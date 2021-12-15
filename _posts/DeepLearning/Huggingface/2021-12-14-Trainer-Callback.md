---
title : "Huggingface:TrainerCallback"



excerpt: "Huggingface TrainerCallback"

categories:
  - Huggingface
tags:
  - [Machine Learning,Huggingface,deep learning ,Callback]
# classes : wide
toc: true
toc_sticky: true
---
## TrainerCallback

Huggingface에서 Trainer라는 Class는 모델의 학습을 굉장히 쉽게 해줍니다. 하지만, logging 방식이 정해져 있기 때문에, 상황에 따라 logging을 다르게 할 수 있는 방법을 찾다가 TrainerCallback이라는 것을 알게 되었습니다. Huggingface에서 TrainerCallback이라는 Class를 제공해줍니다.  TrainerCallback class를 subclassing해서 다양한 Callback Class들이 제공되고 있습니다. subclassing을 통해 다양한 callback method를 override 할 수 있기 때문에 inheritance에 대한 개념을 인지한다면 scratch부터 짜는 것처럼  짤 수 있다고 생각합니다. 이번 글에 나오는 코드는 huggingface의 docs에서 가져온 코드입니다. 지극히, 초보적인 Python Coder 관점에서 바라본 것이라 중급자나 고급자분들께서는 의아하실 수도 있다고 생각합니다. 좋게 봐주시면 감사하겠습니다. (자세한 정의는 나중에 좀 더 자세하게 다루도록 하겠습니다.)

## Callback 이 어떻게 구현되었는지

Huggingface 에서 TrainerCallback이 어떻게 작동하는지 알아보도록 하겠습니다. TrainerCallback이라는 부모 클래스를 여러 개의 다른 callback 클래스들이 subcalssing을 해서  구현되어있습니다.

```python
class DefaultFlowCallback(TrainerCallback):
    """
    A :class:`~transformers.TrainerCallback` that handles the default flow of the training loop for logs, evaluation
    and checkpoints.
    """
```

callback의 예시 중 하나인 DefaultFlowCallback을 가져와 보았습니다. TrainerCallback을 subclassing 했다는 것을 알 수 있습니다. Huggingface에서 제공하는 Callback은 저 개인적으로는 두 종류로 분류할 수 있다고 생각합니다. Huggingface에서 기본적으로 제공하는 Callback Class와 외부 서비스와 통합이 된 integration callback class들이 있다고 생각합니다. Huggingface repository를 들어가시면 trainer_callback.py 와 integrations.py 두 개로 나뉘어서 저장되어있음을 알 수 있습니다 . integrations.py에는 wadnb,mlfow,azure등 여러 metric logging 서비스와 통합이 되어있음을 알 수 있습니다. 

- Default Callback
  - DefaultFlowCallback
  - ProgressCallback
  - PrinterCallback
  - EarlyStoppingCallback
- Integration Callback
  - TensorboardCallback
  - WandbCallback
  - CometCallback
  - AzureMLCallback
  - MLFlowCallback
  - CodeCarbonCallback

현재 repo 에 정의된 callback 클래스들입니다.

## Callback 추가 및 삭제

Callback 클래스를 추가하게 되면 특정 조건을 만족할 때마다 callback이 호출되게 됩니다. Integration Callback의 경우 추가하는 방법은 Trainer를 instantiate 할 때 report_to argument에서 지정해주게 됩니다. 예를 들어, WandBCallback을 추가하고 싶으면 "wandb" 라는 argument를 넘겨주면 됩니다. Default Callback들의 경우 DefaultFlowCallback 은 자동으로 추가가 됩니다. 수동으로 Callback들을 추가하려면 Trainer의 add_callback이라는 method를 사용하게 된다면  Callback을 추가할 수 있습니다. Trainer의 remove_callback이라는 method를 사용하게 되면 Callback을 삭제할 수 있습니다.

## Callback Method

Huggingface에서 한번 Trainer를 instantiate 하게 된다면 CallbackHandler,TrainerState,TrainerControl 가 Trainer instance의 attribute로 지정이 됩니다. callback handler로 여러 method를 호출하게 됩니다.다른 프레임워크는 잘 모르지만, Huggingface의 경우 on_init_end, on_train_begin , on_step_end , ..등등으로 callback의 method들이 훈련하는 도중에 특정 시점에 호출이 됩니다.  TrainerState로 train 했을 때의 metric, best_checkpoint, epoch,step 등등을 넘기고 TrianserControl로 should_evaluation ,should_save, should_log 등등의 boolean 값으로 호출된 method를 수행할 지 정하게 됩니다. 



예시를 보겠습니다.

```python

                    model.zero_grad()
                    self.state.global_step += 1
                    self.state.epoch = epoch + (step + 1) / steps_in_epoch
                    self.control = self.callback_handler.on_step_end(args, self.state, self.control)
```

예시를 보면, 위 코드는 step이 끝날 때 마다 callback_handler 가 on_step_end 라는 method를 호출하는 것을 볼 수 있습니다 .



```python
    def on_step_end(self, args: TrainingArguments, state: TrainerState, control: TrainerControl):
        return self.call_event("on_step_end", args, state, control)
```

이런 식으로 on_step_end method가 call_event라는 method를 호출하게 됩니다.

```python
    def call_event(self, event, args, state, control, **kwargs):
        for callback in self.callbacks:
            result = getattr(callback, event)(
                args,
                state,
                control,
                model=self.model,
                tokenizer=self.tokenizer,
                optimizer=self.optimizer,
                lr_scheduler=self.lr_scheduler,
                train_dataloader=self.train_dataloader,
                eval_dataloader=self.eval_dataloader,
                **kwargs,
            )
            # A Callback can skip the return of `control` if it doesn't change it.
            if result is not None:
                control = result
        return control
```

call_event에서 Trainer에 추가된 모든 callback들에 대하여 getattr을 통해서 event 에 해당하는 method가 있는지 확인을 합니다 . 여기에서는 이 method가 'on_step_end' 가 되겠습니다. TrainerControl의 boolean값들을 result를 통해 변경해줍니다. (모든 event가 그렇지는 않겟지만, on_step_end 의 경우 boolean 값을 변경해줍니다.)

```python

                    model.zero_grad()
                    self.state.global_step += 1
                    self.state.epoch = epoch + (step + 1) / steps_in_epoch
                    self.control = self.callback_handler.on_step_end(args, self.state, self.control)
                    self._maybe_log_save_evaluate(tr_loss, model, trial, epoch, ignore_keys_for_eval)
```

```python
    def _maybe_log_save_evaluate(self, tr_loss, model, trial, epoch, ignore_keys_for_eval):
        if self.control.should_log:
            logs: Dict[str, float] = {}

            # all_gather + mean() to get average loss over all processes
            tr_loss_scalar = self._nested_gather(tr_loss).mean().item()

            # reset tr_loss to zero
            tr_loss -= tr_loss

            logs["loss"] = round(tr_loss_scalar / (self.state.global_step - self._globalstep_last_logged), 4)
            logs["learning_rate"] = self._get_learning_rate()

            self._total_loss_scalar += tr_loss_scalar
            self._globalstep_last_logged = self.state.global_step
            self.store_flos()

            self.log(logs)

        metrics = None
        if self.control.should_evaluate:
            metrics = self.evaluate(ignore_keys=ignore_keys_for_eval)
            self._report_to_hp_search(trial, epoch, metrics)

        if self.control.should_save:
            self._save_checkpoint(model, trial, metrics=metrics)
            self.control = self.callback_handler.on_save(self.args, self.state, self.control)
```



이후 다시 trainer의 stackframe으로 돌아오게 됩니다. 그리고 ,maybe_log_save_evaluate 라는 method를 통해서 control의 attribute의 따라서 logging을 하게 됩니다. 아직 시도를 해보지는 않았지만, Trainer Class의 _maybe_log_save_evaluate 랑 train method를 overriding을 하면 logging을 상황에 맞게 할 수 있습니다. 하지만, 단순히 train  data에 대한 metric이 궁금한 수준이라면 , 저는 eval set을 train의 subset으로 사용해서 overfitting 여부를 확인하는게 더 효율적이라고 생각합니다 

## Custom Callback

```python
class MyCallback(TrainerCallback):
    "A callback that prints a message at the beginning of training"

    def on_train_begin(self, args, state, control, **kwargs):
        print("Starting training")
```

```python
class PrinterCallback(TrainerCallback):
    """
    A bare :class:`~transformers.TrainerCallback` that just prints the logs.
    """

    def on_log(self, args, state, control, logs=None, **kwargs):
        print(logs)
        print(state.log_history)
        _ = logs.pop("total_flos", None)
        if state.is_local_process_zero:
            print(logs)
```

기존의 Callback들을 그대로 사용해도 되지만 customize해서 나만의 callback 클래스를 만들 수 있습니다. 두번째 예시는 , 필자가 PrinterCallback을 새로 정의한 예시입니다 .하지만, method를 override를 할 때는 callback handler class에 정의되어 있는 method를 해주셔야 합니다. 위에서 봤듯이, callblackhandler에서 정의된 method에서 call_event를 호출을 합니다. 만약에 ,callbackhandler에  정의되지 않은 method를 callback class에서 override를 한다면 호출되지 않을 것입니다.



