Machine Learning 혹은 Deep Learning이라는 어떤 주어진 data의 distribution을 구하는 것입니다. 이를 위해서는 statistics에 대한 이해도가 필요합니다. 물론 , statistics를 모르고도 deep learning을 할 수 있지만, data를 어떻게 분석하고,  model을 해석하는데 statistics는 유용한 tool이 되어줍니다. 따라서, statistics를 공부를 하는것은 machine learning에 있어서 필수적이라 볼 수 있습니다.

khan academy의 statistics 강의를 듣고 정리하는 식으로 공부할려 합니다. 



# Analyzing Categorical data

## Variable ,individual

| Drink             | Type | Calories | Sugars(g) | Caffeine(mg) |
| ----------------- | ---- | -------- | --------- | ------------ |
| Brewed Coffe      | Hot  | 4        | 0         | 260          |
| Caff'e latte      | Hot  | 100      | 14        | 75           |
| Caff'e latte      | Hot  | 170      | 27        | 95           |
| Cappucino         | Hot  | 60       | 8         | 75           |
| Iced brewed coffe | Cold | 60       | 15        | 120          |
| Chai latte        | Hot  | 120      | 25        | 60           |

이러한 coffee data가 예시로 주어졋다고 가정하겠습니다. 

여기서, Individual은 drink가 됩니다. 왜냐하면, 각 drink가 개별적인 data를 나타내기 때문입니다. 

>여기서, individual을 제외한 나머지 column은 variable이고 각 row가 data를 의미합니다.

variable은 두 가지 종류가 있는데 , quantitive 와 categorical 입니다.

> quantitive는  어떤 목록에 fit할 수 없는 값들을 나타냅니다.
>
> categorical은 몇 개의 목록(bucket)을 가지는 값을 의미합니다.

## analyzing One categorical variable

1개의 categroical variable을 여러개의 representaion으로 나타낼 수 있습니다. 그에 대해 알아보고자 합니다.

### pictographs

![image](https://user-images.githubusercontent.com/50165842/157341172-2a5f82e2-8427-4920-bf99-af6f8437380d.png)

pictograph란 "이미지나 심볼을 사용하여 data를 representation하는 방법"을 의미합니다.

통계학을 처음 배울때 , visualizing하기 쉬우므로 많이 사용되는 방법이기도 합니다.

위의 예제에서, symbol 1개는 5개의 다람쥐를 의미합니다. 이를 수식적으로 표현하면 5마리의 다람쥐 / 심볼 입니다.

pictograph 를 읽는 방법은 아래와 같습니다.

> Jazz에 참가한 다람쥐의 symbol수는 5개입니다. 
>
> 5 개의 심볼* (5마리의 다람쥐 / 심볼) 을 계산한다.
>
> 심볼이 약분되어서 5 * 5마리의 다람쥐 = 25마리의 다람쥐 

### bar charts

![image](https://user-images.githubusercontent.com/50165842/157343173-a5d33159-2e5e-4b37-87fc-632ae2e992f4.png)



bar chart의 경우 one-way table이 있는 경우 , bar chart로 변환할 수 있습니다. 

bar chart를 만들 때에는 scale 단위를 잘 정해야 합니다. scale의 단위가 너무 작으면 data를 bar chart내에서 표현할 수 없습니다. 예를들어 , max 값이 40이 제한이라면 50이라는 값을 넘기는 variable이 4개중 3개이기에 의미 있는 정보를 도출할 수 없습니다. 만약에 , max 값이 10000이라면 4개간의 비교를 시각적으로 하기 어려울 것입니다. 따라서, 위의 예제의 경우 이상적인 scale은 min값 0 에 max값 70으로 볼 수 있을 것입니다. 

![image](https://user-images.githubusercontent.com/50165842/157343194-41e847a7-c315-4e14-a142-013ceb985500.png)



### bar chart comparing two sets

bar chart를 이용하면 1개의 set 뿐만 아니라 2개의 set을 비교할 수도 있습니다.





![image](https://user-images.githubusercontent.com/50165842/157555346-be29e9aa-6a5d-4139-843c-706b907a9e5a.png)

예를 들면 , 한 사람의 시험 점수를 기말, 중간 으로 나누어서 표현할 수 있습니다.

### bar chart question

Q . bar chart로 부터 물어볼 수 있는 정보는 무엇이 있을까요?

A. 

-  range
- mid-range
-  median
- 최빈값(mode) 
- average







## Two-way table

여태까지는 1개의 variable만을 table이나 cahrt에 표현하는 방법에 대해서만 알아보았습니다. 하지만,2개의 variable을 한번에 table에 표현할 수도 있습니다. 이번에는 2개의 variable을 table에 표현하는 방법을 알아보도록 하겠습니다 .

> 100명의 사람이 있는데, 남자는 48명 , 여자가 52명이 있습니다. 이 사람들에게 어떤 동물을 선호하는지 조사하였습니다. 남자는 36명이 개를 , 10명이 고양이를 , 2명은 선호하는 동물이 없다고 합니다. 여자는 20명이 개를, 26명이 고양이를, 6명은 선호하는 동물이 없다고 합니다.

위의 조사한 내용을 여러 표현 방법으로 표현해보도록 하겠습니다.



### two-way frequency table

![image](https://user-images.githubusercontent.com/50165842/157670735-afb44c35-328d-4573-a0d4-d5663757bf3f.png)

1. 위 data는 2개의 categorical variable을 가지고 있습니다. 이 variable은 성별, 선호도를 나타냅니다.
2. 성별 variabl이 2개의 bucket, 선호도의 variable이 3개의 bucket을 가지므로 6개의 cell에 value를 넣어야 합니다.
3. 3개의 row에 선호도 variable , 2개의 column이 성별을 나타내도록 합니다.
4. (row,column) 에 해당하는 위치에 해당하는 value를 기입합니다.



위와 같이 count를 채워 넣는 것을 frequency table이라고 합니다.



### venn diagram

![image](https://user-images.githubusercontent.com/50165842/157671681-76015b84-952f-4915-8cd4-521078d35d0e.png)



각 variable이 2개의 bucket을 가진다면 쉽게 venn diagram으로 표현할 수 있습니다.

![image](https://user-images.githubusercontent.com/50165842/157672946-efed14c6-a90c-452f-8578-0f9d4f98086c.png)







### two-way relative frequency table

위의 table을 다르게 나타낼 수도 있습니다. 각 column에 대한 percentage(relative-frequency)가 궁금할 수도 있고 , row에 대한 percentage(relative-frequency)가 궁금할 수 도 있습니다.

## Marginal distribution and conditional distribution



