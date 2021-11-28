---
title : "AutoML:Optumna"



excerpt: "Boostcampai model optimziation 4"

categories:
  - ModelOptimization
tags:
  - [Machine Learning,Boostcourse,deep learning ]
# classes : wide
toc: true
toc_sticky: true
---

이 자료는 필자가 만든 자료가 아닌 부스트코스 ai tech 2기 교육자료임을 명시합니다. 이 자료는 상업적으로 이용할 수 없습니다.

## Optuna란?

optuna를 강의에서 사용하게 된 이유는  SOTA 알고리즘이 구현이 되어 있고 , 병렬화가 용이하고 , Conditonal 하이퍼파라미터 구성이 용이하다는 장점이 있습니다. 또한 , 이를 프레임워크가 아닌 라이브러리 형식으로 import  해서 사용이 용이하다는 장점이 있습니다.

(사실은 이거 말고는 다른건 모르기 때문에 잘은 모르겠습니다.)

Optuna의 동작 순서를 보면 아래와 같습니다.

```python
if __name__ == "__main__": 
 study = optuna.create_study(directions=”maximize") 
 study.optimize(objective, n_trials=500) 
 print(f"Best trial {study.best_trial}")
```

1.  study를 만든다.
2. objective 를 정의한다.
3. optimize 한다.

## Optuna Sample Code

![image](https://user-images.githubusercontent.com/50165842/143770131-541dc3a0-1a62-46a0-b0bb-a0ad22441879.png)

logical view로 표현하며 위와 같이 됩니다.

![image](https://user-images.githubusercontent.com/50165842/143770157-48764504-df95-413a-8613-8417cdfd2b07.png)

이를 코드블럭으로 대치 해서 표현하면 위와 같이 됩니다.

### objective

```python
def objective(trial: optuna.trial.Trial, device) -> Tuple[float, int, float]:
    """Optuna objective.
    Args:
        trial
    Returns:
        float: score1(e.g. accuracy)
        int: score2(e.g. params)
    """
    
    ...
    ...
    hyperparams = search_hyperparam(trial)

    model = Model(model_config, verbose=True)
    trainer = TorchTrainer(
        model,
        criterion,
        optimizer,
        scheduler,
        device=device,
        verbose=1,
        model_path=RESULT_MODEL_PATH,
    )
    trainer.train(train_loader, hyperparams["EPOCHS"], val_dataloader=val_loader)
    loss, f1_score, acc_percent = trainer.test(model, test_dataloader=val_loader)
    params_nums = count_model_params(model)

    model_info(model, verbose=True)
    return f1_score, params_nums, mean_time
```

```python
    study = optuna.create_study(
        directions=["maximize", "minimize", "minimize"],
        study_name="automl101",
        sampler=sampler,
        storage=rdb_storage,
        load_if_exists=True,
    )
    study.optimize(lambda trial: objective(trial, device), n_trials=500)
```



대략 pseudocode로 적어보면 위와 같이 나오게 됩니다.   obejctvie를 정의하고 위에서 봐왔던 대로 이를 optimize 합니다.

### architecture config

![image](https://user-images.githubusercontent.com/50165842/143770199-fc6d8681-27ec-4d2c-aefe-082f98e3ecdd.png)

위 와 같이 논문들은 architecture들을 표의 형태로 표현을 합니다. 따라서, 이 모델의 module(block)들을 configure 파일로 만들 수 있지 않을까 라는 생각이 들 수 있습니다.

![image](https://user-images.githubusercontent.com/50165842/143770267-beed1e83-51b1-462a-a79e-1fce82714625.png)

![image](https://user-images.githubusercontent.com/50165842/143770292-6c64858f-c82e-49b3-ad47-401b7255126b.png)

### hyperparameter config

hyperparameter serach를 위한 configuration으로 강의에서 소개해준 것은 3가지가 있다. 필자는 wandb에서 hyperparameter search를 했을 때, categorical, continuous, integer ,log_uniform , exponential_uniform으로 다 커버가 됬었다. 강의에서는 log,discrete등등을 소개 안해주었으나 필자가 따로 찾아서 정리했다.

#### categorical suggest

![image](https://user-images.githubusercontent.com/50165842/143770755-523bcb73-faac-46f7-a855-661306026fbc.png)

```python
        m2_activation = trial.suggest_categorical(
            "m2/activation", ["ReLU", "Hardswish"]
        )
        ...
    model_config["depth_multiple"] = trial.suggest_categorical(
        "depth_multiple", [0.25, 0.5, 0.75, 1.0]
    )
     
        
        pseudo code로 작성한거니 tab키나 이런것들은 신경쓰지 말자.
```



suggest_categorical은 docs에 위 사진 같이 정의되어 있다. 첫번째 argument가 name parameter고 , 두번째 parameter가 선택 대상이다. string type 인지 numerical type인지 명시를 안해줘도 되는 듯 합니다.

#### continuous suggest

![image](https://user-images.githubusercontent.com/50165842/143770820-3d71c4ab-b134-4d89-af5a-88489ce7cbc5.png)

```python
m2_t = round(trial.suggest_float("m2/v3_t", low=1.0, high=6.0, step=0.1), 1)
        
```

low,high 를정해주면 됩니다. step=0.1인건 잘 모르겠습니다. log=False에서 logdomain에서 search가 가능합니다.,

얘는 아마 baeysian search로 찾을 것입니다.

#### integer suggest

```python
m2_kernel = trial.suggest_int("m2/kernel_size", low=1, high=5, step=2)
```

low,high가 주어지면 주어진 구간을 bayesian search로 탐색을 할 것입니다.

#### log uniform suggest

![image](https://user-images.githubusercontent.com/50165842/143771000-22825590-c643-4cb8-b7eb-3e5fbd613b7f.png)

log domain의 continous parameter를 sapmling 해줍니다.

#### discrete uniform suggest

![image](https://user-images.githubusercontent.com/50165842/143771363-04c22e53-8a1c-49af-bf8b-d252698fedf3.png)

```python
subsample = trial.suggest_discrete_uniform("subsample", 0.1, 1.0, 0.1)
```

q 간격의 discrete sampling을 해줍니다.

#### uniform suggest

![image](https://user-images.githubusercontent.com/50165842/143771209-e6731755-5a67-461b-a48e-c9f22e090a89.png)

```python
momentum = trial.suggest_uniform("momentum", 0.0, 1.0)
```



linear domain에서 low ,high 범위 내의 continuous parameter를 sample해준답니다. continous parameter가 필요할 때 사용하면 될듯 합니다.

#### conditional

```python
m2 = trial.suggest_categorical(
        "m2", ["Conv", "DWConv", "InvertedResidualv2", "InvertedResidualv3", "Pass"]
    )
    m2_args = []
    m2_repeat = trial.suggest_int("m2/repeat", 1, 5)
    m2_out_channel = trial.suggest_int("m2/out_channels", low=16, high=128, step=16)
    m2_stride = trial.suggest_int("m2/stride", low=1, high=UPPER_STRIDE)
    # force stride m2
    if n_stride == 0:
        m2_stride = 2
    if m2 == "Conv":
        # Conv args: [out_channel, kernel_size, stride, padding, groups, activation]
        m2_kernel = trial.suggest_int("m2/kernel_size", low=1, high=5, step=2)
        m2_activation = trial.suggest_categorical(
            "m2/activation", ["ReLU", "Hardswish"]
        )
        m2_args = [m2_out_channel, m2_kernel, m2_stride, None, 1, m2_activation]
    elif m2 == "DWConv":
        # DWConv args: [out_channel, kernel_size, stride, padding_size, activation]
        m2_kernel = trial.suggest_int("m2/kernel_size", low=1, high=5, step=2)
        m2_activation = trial.suggest_categorical(
            "m2/activation", ["ReLU", "Hardswish"]
        )
        m2_args = [m2_out_channel, m2_kernel, m2_stride, None, m2_activation]
    elif m2 == "InvertedResidualv2":
        m2_c = trial.suggest_int("m2/v2_c", low=16, high=32, step=16)
        m2_t = trial.suggest_int("m2/v2_t", low=1, high=4)
        m2_args = [m2_c, m2_t, m2_stride]
    elif m2 == "InvertedResidualv3":
        m2_kernel = trial.suggest_int("m2/kernel_size", low=3, high=5, step=2)
        m2_t = round(trial.suggest_float("m2/v3_t", low=1.0, high=6.0, step=0.1), 1)
        m2_c = trial.suggest_int("m2/v3_c", low=16, high=40, step=8)
        m2_se = trial.suggest_categorical("m2/v3_se", [0, 1])
        m2_hs = trial.suggest_categorical("m2/v3_hs", [0, 1])
        # k t c SE HS s
        m2_args = [m2_kernel, m2_t, m2_c, m2_se, m2_hs, m2_stride]
    if not m2 == "Pass":
        if m2_stride == 2:
            n_stride += 1
            if n_stride >= MAX_NUM_STRIDE:
                UPPER_STRIDE = 1
        model.append([m2_repeat, m2, m2_args])
```

이렇게 conditional 하게 hyperparmeter를 configuration 할 수 있습니다.

### module안보단 architecture에 집중하자.

![image](https://user-images.githubusercontent.com/50165842/143771574-3c14d2c3-1e34-4485-9a96-9e15a6f638fb.png)

Nas 논문을 언급하자면, 여기서 말하자는 내용은 Enigneering 관점에서 ***<u>module(micro)을 연구할 것이 아니라 , module들을 어떻게 잘 연결할지를 연구</u>***하자는 것입니다.



### 예시1

![image](https://user-images.githubusercontent.com/50165842/143771733-d607f35e-64e0-4daf-9f04-09e7699d65d5.png)

아 모델은 보면 low-level의 feature 뽑을 떄랑, high level feature뽑을 때가 같습니다. 이를 layer가 깊어질수록 high-level feature을 더 많이 뽑도록 하게 할 수 있습니다.

### 예시2

![image](https://user-images.githubusercontent.com/50165842/143771891-f7896d54-8902-4863-ae11-802d7d8649e6.png)

일괄적으로 n번 repeat 하는대신 n1,n2,n3번 repeat하도록 할 수 있습니다.

## Model code

이제 yaml파일로 부터 어떻게 모델을 생성하는지 알아보도록 하겠습니다.



### model parser

![image](https://user-images.githubusercontent.com/50165842/143772004-b7202ac8-bbec-497b-a9ba-196db4ea07b6.png)

```python
model = Model(model_config, verbose=True)
```

위 사진과 같은 yaml file을 Model이라는 class로 넘겨줍니다.

Model이라는 class는 아래와 같이 정의되어 있다고 하겠습니다.

```python
class Model(nn.Module):
    """Base model class."""

    def __init__(
        self,
        cfg: Union[str, Dict[str, Type]] = "./model_configs/show_case.yaml",
        verbose: bool = False,
    ) -> None:
        """Parse model from the model config file.

        Args:
            cfg: yaml file path or dictionary type of the model.
            verbose: print the model parsing information.
        """
        super().__init__()
        self.model_parser = ModelParser(cfg=cfg, verbose=verbose)
        self.model = self.model_parser.model

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        """Forward."""
        return self.forward_one(x)

    def forward_one(self, x: torch.Tensor) -> torch.Tensor:
        """Forward onetime."""

        return self.model(x)
```

Model_Parser라는 클래스를 살펴보겠습니다.

```python
class ModelParser:
    """Generate PyTorch model from the model yaml file."""

    def __init__(
        self,
        cfg: Union[str, Dict[str, Type]] = "./model_configs/show_case.yaml",
        verbose: bool = False,
    ) -> None:
        """Generate PyTorch model from the model yaml file.

        Args:
            cfg: model config file or dict values read from the model config file.
            verbose: print the parsed model information.
        """

        self.verbose = verbose
        if isinstance(cfg, dict):
            self.cfg = cfg
        else:
            with open(cfg) as f:
                self.cfg = yaml.load(f, Loader=yaml.FullLoader)

        self.in_channel = self.cfg["input_channel"]

        self.depth_multiply = self.cfg["depth_multiple"]
        self.width_multiply = self.cfg["width_multiple"]
        self.model_cfg: List[Union[int, str, float]] = self.cfg["backbone"]  # type: 				ignore
        ....
		self.model = self._parse_model()
```

yaml파일을 받으면 line별로 yaml 파일을 읽습니다. 

![image](https://user-images.githubusercontent.com/50165842/143772193-0206a248-b07b-4f00-b4cf-872fb3b7d129.png)



예시를 들면 위 첫 3줄이 각각, in_channel,depth_multiply,width_multiply에 assign 됩니다.



### module generator

```python
        for i, (repeat, module, args) in enumerate(self.model_cfg):  # type: ignore
            repeat = (
                max(round(repeat * self.depth_multiply), 1) if repeat > 1 else repeat
            )
            module_generator = ModuleGenerator(module, in_channel)(  # type: ignore
                *args,
                width_multiply=self.width_multiply,
            )
```

mode_cfg 를 config의 'backbone' 에 위에서 assign 시켰습니다. repeat,module_name , module_args를 반복문을 돌며 unpacking 한다음 이를 ModuleGenerator로 넘겨줍니다. 이를 그림으로 표현하면 아래와 같습니다.

![image](https://user-images.githubusercontent.com/50165842/143772410-c45209aa-9875-413e-84aa-58ce8cc1ae28.png)

아래의 그림에선 repeat에 대해 얘기합니다.

![image](https://user-images.githubusercontent.com/50165842/143772470-68093404-4f83-4610-a289-3c1c0530b2f7.png)

repeat이라는 개념은 mobilenetv2에서 나왔다고 합니다. 훈련된 모델을 작은 devcice나 웹 , 진짜 큰서버 등등 여러 application에서 쓸 수 있을 겁니다.  ***<u>edge device에서는 module이 너무 클 수도 있기 때문에</u>*** , depth_multiple이라는 것을 이용해서 repeat 횟수를 줄이는 것입니다.  

예를 들어 , depth_multiple이 0.5이면 repeat 횟수를 절반으로 줄입니다.



여기에서는 언급이 되어있지 않지만, width_multiple은 모델의 output size를 조절해줍니다. 예를들어 ,width_multiple이 2.0이면 output 은 64가아닌 128이 됩니다. 마스터님께서는 이것이 모델을 조율 하는데 효과적이라고 생각을 하신답니다.



```python
            module_generator = ModuleGenerator(module, in_channel)(  # type: ignore
                *args,
                width_multiply=self.width_multiply,
            )
            m = module_generator(repeat=repeat)

            layers.append(m)
            in_channel = module_generator.out_channel    
```

위에 있던 module_generator 부분을 떼어온것인데 이제 argument를 넘겨주면 module을 생성합니다. 

layers 에 모듈들을 append 해주면 모델이 차곡차곡 쌓아져서 생성됩니다. 그리고 , out_channle을 다음 in_channl로 바꿔주고 iterative 하게 반복을 해줍니다.

##  Module 구현체

### AbstactClass(module generaotr)

![image](https://user-images.githubusercontent.com/50165842/143773218-d2790084-a701-44f5-9fe4-64288ade678f.png)

ModuleAbstract 클래스를 정의해보도록 하겠습니다. 최소한의 기능으로 out_channel을 결정해주기 위해서 out_channel과 callable 정의만 해주면 됩니다.



### Module 추가 과정

![image](https://user-images.githubusercontent.com/50165842/143773588-eedf911e-f5db-45b0-9de9-a918949574c6.png)

여기서 제시된 inverted residual block을 module로 추가해보도록 하겠습니다.

```python
class InvertedResidualv2Generator(GeneratorAbstract):
    """Bottleneck block generator."""

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

    @property
    def out_channel(self) -> int:
        """Get out channel size."""
        return self._get_divisible_channel(self.args[0] * self.width_multiply)

    @property
    def base_module(self) -> nn.Module:
        """Returns module class from src.common_modules based on the class name."""
        return getattr(__import__("src.modules", fromlist=[""]), self.name)

    def __call__(self, repeat: int = 1):
        """call method.
        InvertedResidualv2 args consists,
        repeat(=n), [c, t, s] // note original notation from paper is [t, c, n, s]
        """
        module = []
        _, t, s = self.args  # c is equivalent as self.out_channel
        inp, oup = self.in_channel, self.out_channel
        for i in range(repeat):
            stride = s if i == 0 else 1
            module.append(
                self.base_module(inp=inp, oup=oup, expand_ratio=t, stride=stride)
            )
            inp = oup
        return self._get_module(module)
```

repeat 만큼 block을 module 에 append 해준후 get_module로 넘겨줍니다.