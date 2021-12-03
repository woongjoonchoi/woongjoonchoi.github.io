---
title : "Nas from scratch using Optuna"



excerpt: "Boostcampai model optimzation practical"

categories:
  - ModelOptimization
tags:
  - [Machine Learning,Boostcourse,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---
## NAS란?

NAS란 neural architecture search의 약자입니다. NAS는 이미 존재하는 블록 모듈들을 가지고 이들을 적절히 조합해서 새로운 architecture를 찾는 시도입니다. 이를 컴퓨터가 만들다 보니 인간의 직관과 거리가 먼 모델이 나올 수 있습니다. 하지만, 컴퓨터가 더 잘 찾는 여러 사례가 나오고 있기 때문에 가치 있는 연구주제라고 생각이 듭니다. Boost Course에서는 실습 파일로 NAS 가 구현된 코드가 주어졌고, 그렇지 않은 baseline 코드가 있습니다. 저는 그렇지 않는 코드에 도전을 해봤습니다.

## 왜 scratch 를 했는가?

이론강의에서 top-down 방식으로 대략적인 NAS에 대한 개념을 구축했습니다. optuna tutorial 코드를 해보아서 optuna의 메소드에 익숙해졌다고 생각해서 일반적인 image classification training code(baseline code)에 NAS를 구축해보려 시도했습니다. (필자는 baseline에서 op tuna library를 import 한 것을 본 적이 없기에 scratch부터 NAS를 구현했다고 생각했습니다.)



## optuna 파이프라인

![optuna drawio](https://user-images.githubusercontent.com/50165842/144431785-f54f2209-952c-425b-950d-ece1065fb7f0.png)

필자가 생각하는 Optuna의 pipeline입니다. High-level view에서 보면 아래의 3가지 단계를 거칩니다.

1. study를 만든다.
2. objective function을 정의한다.
3. objective function을 optimize 한다.

objective 안에 일반적인 모델의 training pipeline이 들어간다고 필자는 생각했습니다.  필자는 bayesian search알고리즘을 사용했습니다.  필자가 작성한 optuna 파이프라인은 다음과 같습니다.



```python
    
    def objective(trial) :
    test_loss, test_f1, test_acc , n_params= train(
        model_config=model_config,
        data_config=data_config,
        log_dir=log_dir,
        fp16=data_config["FP16"],
        device=device,
        trial =trial
    )
    return test_loss,test_f1,test_acc , n_params
    
    ....
   	study = optuna.create_study(
        study_name="repeat_search", 
        storage="postgresql://optuna:optuna@my_ip:myport/optuna",
        directions=["minimize", "maximize","maximize","minimize"],
        load_if_exists=True)
    study.optimize(objective, n_trials=50, timeout=300)
```

필자는 objective 대상을 4개로 정했습니다. loss, f1, acc, params. 정확성과 latency에서 좋은 performance를 보여주는 모델을 찾는 것이 목표였습니다. 필자는 정확성이 더 중요하다고 생각했기에, 정확성과 관련된 objective 을 3개 쓰고, latency와 관련된 objective를 1개 사용했습니다.



## dataset size 수정



## module 수정

우선은 module block의 개수를 bayesian search로 찾아볼려 했습니다. 이를 위해서 trial을 module 까지 argument로 전달을 해주어야 했습니다. 아래와 같이 코드를 작성해서 전달을 해 주었습니다

```python
model_instance = Model(model_config, verbose=True , trial = trial,log_dir=log_dir)
```

```python
class Model(nn.Module):
    """Base model class."""

    def __init__(
        self,
        cfg: Union[str, Dict[str, Type]] = "./model_configs/show_case.yaml",
        verbose: bool = False,
        trial : optuna.trial.Trial = None,
        log_dir : str = None
    ) -> None:
        
        ...
        
         self.model_parser = ModelParser(cfg=cfg, verbose=verbose ,trial = trial ,log_dir = log_dir)
```

```python
class ModelParser:
    """Generate PyTorch model from the model yaml file."""

    def __init__(
        self,
        cfg: Union[str, Dict[str, Type]] = "./model_configs/show_case.yaml",
        verbose: bool = False,
        trial : optuna.trial.Trial = None,
        log_dir : str = None
    ) -> None:
        
        
        
```

```python
def _parse_model(self , trial, log_dir) -> nn.Sequential:
        """Parse model."""
        layers: List[nn.Module] = []
        log: str = (
            f"{'idx':>3} | {'n':>3} | {'params':>10} "
            f"| {'module':>15} | {'arguments':>20} | {'in_channel':>12} | {'out_channel':>13}"
        )
		...
        
        for i, (repeat, module, args) in enumerate(self.model_cfg):  # type: ignore
            repeat = (
                max(round(repeat * self.depth_multiply), 1) if repeat > 1 else repeat
            )
            if i>=1 and i<=15 and args[-1] == 1:
                n = trial.suggest_int("n_layers_{0}".format(i),low=1, high=5)
                repeat = n* (i//5) *repeat
```

input 과 output layer는 edge case로서 layer의 개수를 1로 고정을 시켰습니다. 그리고, stride값이 2인 블록들도 repeat횟수를 1로 고정시켰습니다. stride값이 2인 블록들은 블록을 거친후 이미지가 size가 줄어드는데 , 각 block들의 input size와 output size를 고정시켰기 때문에 repeat을 1로 고정을 시켰습니다. 

## 버그 발생

여기서 이제 문제가 발생합니다. 

아래의 코드는 train의 시작 부분에서 best_pt를 load하는 부분입니다.

```python
    model_instance = Model(model_config, verbose=True , trial = trial,log_dir=log_dir)
    model_path = os.path.join(log_dir, "best.pt")
    print(f"Model save path: {model_path}")
    if os.path.isfile(model_path):
        model_instance.model.load_state_dict(
            torch.load(model_path, map_location=device)
        )
    model_instance.model.to(device)
```

model_config로 모델을 생성해서 best_tensor를 loading하는 것입니다. 여기까지는 완벽하게 보이는데 한가지 문제점이 있습니다.

```yaml
input_channel: 3

depth_multiple: 1.0
width_multiple: 1.0

backbone:
    # [repeat, module, args]
    [
        # Conv argument: [out_channel, kernel_size, stride, padding_size]
        # if padding_size is not given or null, the padding_size will be auto adjusted as padding='SAME' in TensorFlow
        [1, Conv, [16, 3, 2, null, 1, "HardSwish"]],
        # k t c SE HS s
        [1, InvertedResidualv3, [3, 1, 16, 0, 0, 1]],
        [1, InvertedResidualv3, [3, 4, 24, 0, 0, 2]], # 2-P2/4, 24 # stride 1 for cifar, 2 for others
        [1, InvertedResidualv3, [3, 3, 24, 0, 0, 1]],     
        [1, InvertedResidualv3, [5, 3, 40, 1, 0, 2]], # 4-P3/8, 40
        [1, InvertedResidualv3, [5, 3, 40, 1, 0, 1]],     
        [1, InvertedResidualv3, [5, 3, 40, 1, 0, 1]],     
        [1, InvertedResidualv3, [3, 6, 80, 0, 1, 2]], # 7-P4/16, 80   
        [1, InvertedResidualv3, [3, 2.5, 80, 0, 1, 1]],     
        [1, InvertedResidualv3, [3, 2.3, 80, 0, 1, 1]],     
        [1, InvertedResidualv3, [3, 2.3, 80, 0, 1, 1]],     
        [1, InvertedResidualv3, [3, 6, 112, 1, 1, 1]],     
        [1, InvertedResidualv3, [3, 6, 112, 1, 1, 1]], # 12 -P5/32, 112 
        [1, InvertedResidualv3, [5, 6, 160, 1, 1, 2]],    
        [1, InvertedResidualv3, [5, 6, 160, 1, 1, 1]],     
        [1, InvertedResidualv3, [5, 6, 160, 1, 1, 1]],     
        [1, Conv, [960, 1, 1]],
        [1, GlobalAvgPool, []],
        [1, Conv, [1280, 1, 1]],
        [1, Flatten, []],
        [1, Linear, [6]]
    ]
```

model_config는 고정된 상수 값이였기에 best_model의 config와 다를 수 밖에 없습니다.  model config랑 best_model의 config가 다르니까 architecture가 다르고  , state_dict()를 loading 하게 되면 mapping이 안되는 parameters가 발생하게 됩니다. 

그런데, 여기서부터 문제가 시작되는 것은 아닙니다.

```python
         def save_model(model, path, data, device):
                """save model to torch script, onnx."""
                try:
                    torch.save(model.state_dict(), f=path)
                    ts_path = os.path.splitext(path)[:-1][0] + ".ts"
                    convert_model_to_torchscript(model, ts_path)
                    except Exception:
                        print("Failed to save torch")
    ...
    		print(f"Model not saved. Current best test f1: {best_test_f1:.3f}")
            save_model(
                model=self.model,
                path=self.model_path,
                data=data,
                device=self.device,
            )
```

한번의 trial이 끝난후 best모델을 저장하게 됩니다. model 그 자체를 binary 값으로 저장하는 것이 아닌 model의 parameters 값들을 state_dict() 메소드로 호출해서 저장을 하였습니다. 



```python
    # Create trainer
    trainer = TorchTrainer(
        model=model_instance.model,
        criterion=criterion,
        optimizer=optimizer,
        scheduler=scheduler,
        scaler=scaler,
        device=device,
        model_path=model_path,
        verbose=1,
    )
    best_acc, best_f1 = trainer.train(
        train_dataloader=train_dl,
        n_epoch=data_config["EPOCHS"],
        val_dataloader=val_dl if val_dl else test_dl,
    )

    # evaluate model with test set
    model_instance.model.load_state_dict(torch.load(model_path))
    test_loss, test_f1, test_acc = trainer.test(
        model=model_instance.model, test_dataloader=val_dl if val_dl else test_dl
    )
```

train이 끝난 후 best_model의 state를 loading 하는데 , 마지막 epoch의 모델이 운좋게 best_model이면 버그가 발생하지 않을 것입니다. 그렇지 않다면, 위와 같이 model_config와 best_model의 config가 다르기 때문에 똑같은 버그가 발생할 것입니다.



## 어떻게 해야하는가?

이와 같은 문제를 방지하기 위해서는 어떻게 해야 할까요? 여러가지 해결책이 있을 것입니다. 제가 알고 있는 여러가지 해결책에 대해서 알아보도록 하겠습니다.

### Tensor 대신 binary 파일로 모델 저장.

binary object로 모델 그 자체를 저장하는 방법도 해결책이 될 수 있습니다. binary object로 모델을 저장하게 되면 저희는 그 모델의 구조가 

나중에 다시 적겠습니다. 이 내용은 잘 모르겠습니다.

### yaml 수정

제가 생각한 다른 방법 중 하나는 매번 모델 instance를 생성할 때 마다 yaml파일을 새로 만들어서 저장하는 것입니다. best_model에 대해서만 저장을 해준다면 best_model을 loading 할 때 마다 이 yaml파일로 model_instance를 새로 만들면 위에서 발생한 문제가 발생하지 않을 것입니다.

idea는 떠 올랐지만 구현은 아직 못했습니다.



## 느낀점

scratch 부터 구현해서 해볼려 했지만, 프레임워크나 라이브러리처럼 다른 모델에서 사용할 수준으로 만들기는 어려웠던 거 같습니다.원래의 계획은 kernel,input,stride 까지 search를 해볼려 했는데 , 좀 어려웠습니다. 기회가 된다면 프레임워크나 라이브러리 수준으로 구현해보고 싶습니다.