---
title : "Categorical variable's representation and interpretation"



excerpt: "one-way table, two-way table, bar graph, distribution"

categories:
  - Statistics
tags:
  - [Mathmathics,Machine Learning,Khan,Statistics ,cateogrical data]
# classes : wide
toc: true
toc_sticky: true
---
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

![image](https://user-images.githubusercontent.com/50165842/157774712-934c24b2-8d87-4899-a297-ed4a4f24ed49.png)



frequency table을 다르게 나타낼 수도 있습니다. 각 column에 대한 percentage(relative-frequency)가 궁금할 수도 있고 , row에 대한 percentage(relative-frequency)가 궁금할 수 도 있습니다.

한 variable을 기준으로 다른 variable의 가능성을 비교할 수 있게 됩니다. 

예를 들어서 , 위 data에서는 sports를 play 하지 않는 학생은 sports를 play 하는 학생들 보다 악기를 play할 가능성이 높습니다.

### interprete two-way table

![image](https://user-images.githubusercontent.com/50165842/157775529-b55e9502-a835-46c4-9df6-0ddbac2f2e11.png)

two-way table을 좀더 보도록 하겠습니다. 

two-way table을 사용하면 한 variable에 대해서 다른 variable이 어떻게 변하는지를 알 수 있습니다 .  이를 해석하는 방법은 여러가지가 있는데 , 이에 대해 다루어보겠습니다.

1.

Computer time이라는 variable에 대해서 Hours/night라는 variable은 아래와 같이 변해갑니다.

- minimal :   5 or fewer   <  5-7   <  7 or more
- Moderate :   5 or fewer   ~= 5-7  ~=  7 or more
- Extreme :    5 or fewer >  5-7  > 7 or more

Computer Time 이 줄어 들수록 잠을 자는 시간이 늘어나므로 , minimal 과 7 or more는 관련이 있다는 결과를 도출해 낼 수 있습니다.



2.

7 or more 에서 Minimal 의 비율은 55 %입니다.  7 or more에서 Extreme의 비율은 20 %입니다. 따라서, Minimal 이 7 or more에서 majority이므로 , 7 or more과 Minimal 이 관계가 있다고 볼 수 있습니다.



3.

7 or more에서 Minimal 의 비율은 55 %입니다. Minimal은 전체의 35.8 %입니다. Minimal이 전체의 대부분이 아니므로 , 다른 이용 시간에서  Minimal 의 비율이 majority가 아닙니다. 따라서 , 7 or more과 Minimal이 관련이 있다고 볼 수 있습니다.

이를 다른 variable을 기준으로 생각하면 Minimal 에서 7 or more의 비율이 51.1 % 이고 , 7 or more은 전체의 33.3 %이므로 Minimal 과 7 or more는 관련이 있다고 볼 수 있습니다. 





## Marginal distribution and conditional distribution

위에서 two-way table을 보았는데 , 이는 2개의 dimension (2개의 variable)을 따르므로 joint distribution이라 볼 수 있습니다.

이번에는 이 distribution 중 2가지 type의 distribution에 대해서 알아보도록 하겠습니다.

### marginal distribution

![image](https://user-images.githubusercontent.com/50165842/157877405-0c44a84d-ee80-4be9-a97b-a54469a481fb.png)

이러한 two-way table이 있을때 초록색으로 highlight 된 부분이 있습니다. 이 부분은 전체에서 , Graduate, Undergraduate , 즉 각 student level 얼마만큼의 사람이 있는지를 보여줍니다.

이와 같이, 전체에 대해서 한가지 variable만을 기준으로 각  bucket에 얼마나 있는지 보는 것을 marginal distribution 이라고 합니다.

 marginal distribution은 1가지 variable에 집중한다 보셔도 됩니다.



### conditional distribution

Q . 각 student level 에 대한 A status variable의 conditional distribution을 구하시오





![image](https://user-images.githubusercontent.com/50165842/157877994-a24d17be-deb3-4a80-b5a7-7608df21d01e.png)



Conditional Distribution이라 함은 a라는  variable이 있고  , b라는 variable에 대하여 어떤 값이 주어졌을 때 이를 percentage로 나타낸 것이, 이를 b에 대한 a의   conditional distribution 이라고 합니다.







# Reference

[ Analyzing categorical data  Statistics and probability Khan Academy](https://www.khanacademy.org/math/statistics-probability/analyzing-categorical-data)

