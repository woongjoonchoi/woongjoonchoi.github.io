---
title : "Flask:Sentiment Classifier tutorial"



excerpt: "Huggingface Deployment using Flask"

categories:
  - Huggingface
tags:
  - [Machine Learning,Huggingface,deep learning ,Flask]
# classes : wide
toc: true
toc_sticky: true
---
## Flask:Sentiment Classifier tutorial

ODQA system을 만들어서 실제로 배포를 하려고 합니다. 하지만, 한 번도 배포를 해본 적이 없기에 우선 web으로 배포를 시도해볼려 합니다. Flask로 deep learning 모델을 배포하는 과정에 대한 배경지식이 없기에 top-down 식으로 빠르게 high-level view를 생성하기 위해서 간단한 sentiment classifier tutorial을 진행했습니다. 이를 이용해서 odqa 시스템 배포로 확장해 나갈 생각입니다.



## Flask app

![image](https://user-images.githubusercontent.com/50165842/144746873-87661625-6cd1-45ed-be3b-9646ef0d185e.png)

제가 한 tutorial의 app의 architecture는 위와 같습니다. predict라는 route를 설정해주고 , root/predict 하는 URL에서 request를 주고받는 식으로 동작합니다. (제가 웹에 대한 지식이 없어서 표현이 부정확 할 수 있습니다. 피드백 부탁드립니다.)



```python
app = Flask(__name__)

...
@app.route("/predict")
def predict() :
...

	return ..
if __name__ == "__main__":
	....
	
	
    app.run()

```

`app.run()` line을 실행하게 되면 local server에 site가 빌드가 됩니다. `@app.route("/predict")` 를 typing하고 그 아래에 predict 라는 함수를 정의하면 root/predict 라는 경로로 들어가게 되면 predict라는 함수를 실행하게 됩니다.





## Predict route

![image](https://user-images.githubusercontent.com/50165842/144747174-1a0a5da4-2d4b-4f65-96d5-6b39ddc90fd4.png)

predict 함수의 동작을 pipeline으로 표현해봤습니다. 

```python
def predict() :
    sentence = request.args.get("sentence")
    positive_prediction = sentence_prediction(sentence)
    negative_prediction = 1- positive_prediction

    # print(sentence)
    response = {}

    #TypeError: Object of type Tensor is not JSON serializable   type change str
    response["response"] = {
        "positive" : str(positive_prediction),
        "negative" : str(negative_prediction),
        "sentence" : str(sentence)
    }
    return flask.jsonify(response)
```

/predict 경로에서 `request.args.get("sentence")` 로 request를 받습니다. `ip/predict?sentence = i love ai  ` 라고 주소창에 입력을 하면 sentence 에 'i love ai' 라는 string이 assign됩니다. 이를 sentence_prediction이라는 함수를 통해 inference를 실행합니다. 결과값을 response라는 dict에 저장하고 `flask.jsonify(response)` 로 return을 해줍니다. 

![image](https://user-images.githubusercontent.com/50165842/144747416-08b47571-916f-46f6-b513-86b99675f03c.png)



## Sentence Prediction

![image](https://user-images.githubusercontent.com/50165842/144747443-57c91d81-1c41-404b-bc74-a7509012292b.png)

```python
def sentence_prediction(sentence) :
    tokenizer = config.TOKENIZER
    max_length = config.MAX_LEN
    review = str(sentence)
    review = " ".join(review.split())

    inputs = tokenizer.encode_plus(
        review,
        None,
        add_special_tokens=True,
        max_length=max_length,
        pad_to_max_length=True,
    )

    ids = inputs["input_ids"]
    mask = inputs["attention_mask"]
    token_type_ids = inputs["token_type_ids"]


    ids = torch.tensor(ids, dtype=torch.long).unsqueeze(0)
    mask = torch.tensor(mask, dtype=torch.long).unsqueeze(0)
    token_type_ids = torch.tensor(token_type_ids, dtype=torch.long).unsqueeze(0)


    ids = ids.to(DEVICE, dtype=torch.long)
    token_type_ids = token_type_ids.to(DEVICE, dtype=torch.long)
    mask = mask.to(DEVICE, dtype=torch.long)



#  output type변경해줘야함 cpu().detach().numpy()
    outputs = MODEL(ids=ids, mask=mask, token_type_ids=token_type_ids)

    outputs = torch.sigmoid(outputs).cpu().detach().numpy()
    return outputs[0][0]
```

sentence_prediction의 파이프라인입니다. model을 evaluation 할 때의 과정과 똑같습니다. request를 통해 받은 sentence를 tokenizing 하고 이를 tensor로 변환한 뒤 Model에 input으로 넘겨줍니다. 이때 , 저는 server에서 Model을 GPU에 올려놨기에 GPU에 있던 output tensor를 CPU로 옮기고, gradient 계산을 안 할 것이기 때문에 `detach`를 사용해서 current graph에서 제거했습니다. 이를 이용해서 softmax값을 계산해 주었습니다.

## Model

```python
MODEL = None
DEVICE = "cuda"


if __name__ == "__main__":
    MODEL = BERTBaseUncased()
    # MODEL = nn.DataParallel(MODEL)
    MODEL.load_state_dict(torch.load(config.MODEL_PATH))
    MODEL.to(DEVICE)
    MODEL.eval()
    app.run()
```

하나의 모델만을 사용하고 변경할 일이 없다고 생각해서 전역변수 MODEL 변수를 선언했습니다.  이 예제에서는 binary 파일로 모델을 저장해서 loading을 했는데, 다음에는 tensor로 저장해서 loading 하는 것이 더 좋을 것 이라고 생각했습니다



## 목표

![image](https://user-images.githubusercontent.com/50165842/144747713-8a968d0a-5066-43f8-8eb6-e678103ce45b.png)

목표는 request orchestrator를 통해서 여러개의 request processor가 db와 통신하는 request-based architecture를 디자인하는 것입니다.  