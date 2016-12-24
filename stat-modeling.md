---
layout: page
title: 데이터 과학 -- 기초 통계
subtitle: 통계 모형(Statistical models)
output:
  html_document: 
    keep_md: yes
  pdf_document:
    latex_engine: xelatex
mainfont: NanumGothic
---



> ## 학습 목표 {.objectives}
>
> * 모형, 수학모형, 통계 모형을 이해한다.
> * 통계모형을 개발해 나가는 과정을 살펴본다.
> * 집값 예측 모형을 차근차근 개발해 나가는 과정을 R 모형표기법으로 실습한다.


## 1. 통계모형(Statistical Models)

수학적 모형(Mathematical Model)은 숫자, 모형 공식, 등식 등을 활용하여 구성되는 반면에 
통계모형은 수학모형의 한가지 형태로 데이터로부터 만들어지며 불확실성, 무작위 확률 개념을 모형에 담을 수 있다.
통계 모형을 활용하는 곳은 다음과 같다.

- 데이터 숨겨진 패턴을 식별한다.
- 사건을 분류한다.
- 영향을 미치는 것이 다양한데 이를 풀어낸다.
- 증거의 강도를 가늠한다.

미래 결과를 예측하거나, 실험을 통해서 변수들 간의 관계를 연구하거나, 데이터를 탐색해서 변수관계를 파악하는데 통계모형을 적용한다.

> ### 모형(Model)이란? {.callout}
>
> 특정 목적을 표현한 것이 모형이다. (A model is a representation for a purpose) [^model-definition]
> 
> * 표현(representation) : 현실세계 무언가를 표상한 것.
> * 목적(Purpose) : 모형에 대한 특별한 사용처.

[^model-definition]: [Starfield, Smith, and Blelock, "How to Model It: Problem Solving for the Computer Age"](https://cran.r-project.org/web/packages/statisticalModeling/vignettes/modeling.html)

<img src="fig/stat-modeling-elements.png" alt="통계모형 구성요소" width="77%" />

### 1.1. 통계모형의 구성요소

모형(Model)은 특정목적을 표현한 것이며, 수학모형은 수학 구성요소를 조합해서 생성되고, 통계모형은 데이터를 기반으로 이를 학습시켜 만들어진다.
통계모형은 세가지 구성요소로 구성된다.

- 데이터 : 데이터프레임으로 깔끔한 데이터(Tidy Data)로 구성이 되어야 되고, 변수와 관측점 그리고 값으로 구성되는데 컴퓨터가 인식할 수 있을 뿐만 아니라 개념적으로도 일관된 체계를 갖춰야 된다.
- 함수: 데이터에서 패턴을 뽑아낼 수 있는 모형 훈련함수(training function)와 모형을 평가할 수 있는 평가함수(evaluation function) 한쌍으로 구성되고, 회귀모형, 나무모형, 신경망, 군집모형, 딥러닝 모형이 여기에 포함된다.
- 공식: 함수가 정해졌다면 변수들간의 관계를 R 공식에 맞춰 표현한다.

> ### 키보드 자판으로 통계모형을 R로 표현하는 방법 {.callout}
> 
> 수학공식을 R공식으로 변환해서 표현해야 되는 사유는 자판을 통해 수식을 입력해야 한다는 한계에 기인한다.
> 따라서, 자판에 있는 키보드의 특수기호를 잘 활용하여 가장 가독성이 좋고 입력이 용이하게 나름대로 R에서 
> 구현한 방식은 다음과 같다.
> 
> 1. 주효과에 대해 변수를 입력으로 넣을 `+`를 사용한다.
> 1. 교호작용을 변수간에 표현할 때 `:`을 사용한다. 예를 들어 `x*y`는 `x+y+x:z`와 같다.
> 1. 모든 변수를 표기할 때 `.`을 사용한다. 
> 1. 종속변수와 예측변수를 구분할 때 `~`을 사용한다. `y ~ .`은 데이터프레임에 있는 모든 변수를 사용한다는 의미가 된다.
> 1. 특정변수를 제거할 때는 `-`를 사용한다. `y ~ . -x`는 모든 예측변수를 사용하고, 특정 변수 `x`를 제거한다는 의미가 된다.
> 1. 상수항을 제거할 때는 `-1`을 사용한다.
> 
> 
> | R 공식구문 | 수학 모형 | 설명 |
> |------------|---------------|-----------------------------|
> |`y~x`       | $y_i = \beta_0 + \beta_1 x_i + \epsilon_i$ | `x`를 `y`에 적합시키는 1차 선형회귀식 |
> |`y~x -1`       | $y_i = \beta_1 x_i + \epsilon_i$ | `x`를 `y`에 적합시 절편 없는 1차 선형회귀식 |
> |`y~x+z`       | $y_i = \beta_0 + \beta_1 x_i + \beta_2 z_i +\epsilon_i$ | `x`와 `z`를 `y`에 적합시키는 1차 선형회귀식 |
> |`y~x:z`       | $y_i = \beta_0 + \beta_1 x_i \times z_i +\epsilon_i$ | `x`와 `z` 교호작용 항을 `y`에 적합시키는 1차 선형회귀식 |
> |`y~x*z`       | $y_i = \beta_0 + \beta_1 x_i + \beta_2 z_i + \beta_1 x_i \times z_i +\epsilon_i$ | `x`와 `z`, 교호작용항을 `y`에 적합시키는 1차 선형회귀식 |


교육수준에 따른 가계소득을 예측하는 경우 `income ~ educ` 공식을 적용할 경우 $\text{income} = \alpha + \beta \times \text{education}$, 
$\text{가계 소득} = \alpha + \beta \times \text{교육 수준}$이 되고, 가계소득은 교육수준의 함수가 된다, 가계소득은 교육수준으로 설명된다, 
가계소득은 교육수준으로 모형화되고, 가계소득을 교육수준으로 설명할 수 있다 등등으로 언급된다.


~~~{.r}
inc_lm <- lm(income ~ educ, data=income)
~~~

> ### 공변량(Covariate)? {.callout}
>
> 공변량은 그 자체로 관심을 갖는 변수는 아니지만, 종속변수에 영향을 주는 독립변수다. [^covariate-wiki]
> 
> 예를 들어, Woodworth (1987)는 특정 지점의 평균해수면 높이를 예측하고자 했고, 시간에 따른 해수면높이를 예측하고자 해서 
> 주된 독립 관심변수는 시간이지만, 해수면 대기압을 함께 독립변수에 넣어 평균해수면 높이를 예측하는 경우 훨씬 더 좋은 예측 모형을 개발할 수 있었다.
> 이 경우 대기압이 공변량이 된다. 경우에 따라서는 공변량이 주된 독립 관심변수로 바뀌는 경우도 종종 있다.

[^covariate-wiki]: [Covariate](https://en.wikipedia.org/wiki/Covariate)

### 1.2. 통계모형 구축 흐름

통계모형 개발과정은 데이터 과학 프로세스와 크게 차이나지 않는다. 
다만, 일반적인 통계모형을 개발할 경우 다음과 같은 과정을 거치게 되고, 지난한 과정이 될 수도 있다.

1. 데이터를 정제하고, 모형에 적합한 데이터(R과 모형 팩키지와 소통이 될 수 있는 데이터형태)가 되도록 준비한다.
1. 변수에 대한 분포를 분석하고 기울어짐이 심한 경우 변수변환도 적용한다.
1. 변수와 변수간에, 종속변수와 설명변수간에 산점도와 상관계수를 계산한다. 특히 변수간 상관관계가 $r > 0.9$ 혹은 근처인 경우 변수를 빼거나 다른 방법을 강구한다.
1. 동일한 척도로 회귀계수를 추정하고 평가하려는 경우, `scale()` 함수로 척도로 표준화한다.
1. 모형을 적합시킨 후에 잔차를 보고, 백색잡음(whitenoise)인지 확인한다. 만약, 잔차에 특정한 패턴이 보이는 경우 패턴을 잡아내는 모형을 새로 개발한다.
    1. `plot()` 함수를 사용해서 이상점이 있는지, 비선형관계를 잘 잡아냈는지 시각적으로 확인한다.
    1. 다양한 모형을 적합시키고 `R^2` 와 `RMSE`, 정확도 등 모형평가 결과가 가장 좋은 것을 선정한다.
    1. 절약성의 원리(principle of parsimony)를 필히 준수하여 가장 간결한 모형이 되도록 노력한다.
1. 최종 모형을 선택하고 모형에 대한 해석결과와 더불어 신뢰구간 정보를 넣어 마무리한다.    

<img src="fig/stat-modeling-workflow.png" alt="통계모형 개발 작업흐름" width="77%" />

### 1.3. 모형 설계(Model Design)

모형 설계 과정은 탐색적 데이터 분석을 통해 충분한 데이터에 대한 이해를 바탕으로 목적에 맞는 모형을 개발해 나간다.
모형설계 과정에서 중요한 의사결정을 하게 되는데 중요 모형 설계 변수에는 다음이 포함된다.

- 적합한 훈련 데이터
- 종속변수와 설명변수 선정: 독립 관심변수와 공변수(Covariate)
- 모형 아키텍쳐
    - 선형회귀모형: lm(), glm()
    - 반복분할(recursive partitioning): rpart()
    - 신경망(neural network): nnet()
    - 최근접이웃(k-nn) : knn()

결국 모형을 개발한다는 것은 데이터와 모형 개발자의 선택에 따라 이루어지는 과정의 결과물이 된다.

### 1.4. 모형평가(Evaluate Model)

훈련데이터를 달리하거나, 모형 아키텍쳐를 달리 선택하거나, 공식을 달리하면 무수히 많은 다양한 모형 출력결과를 얻게 된다.
그 수많은 모형 중에서 어떤 것을 선택할 것인가? 이 문제에 답을 주는 것이 모형평가과정이다. 
예측하고자 하는 변수가 연속형이냐 범주형이냐에 따라 차이가 있기는 하지만, 기본적으로 오차가 최소화되는 모형이 가장 좋은 모형이 되지만,
과적합에 대한 문제, 모형의 복잡성에 대한 문제, 컴퓨터 계산 자원 소모에 대한 문제, 연산시간에 대한 다양한 조건이 만족되고 나서 
오차가 최소화되는 모형이 적합한 모형으로 평가된다.

<img src="fig/stat-model-evaluation.png" alt="통계모형 평가" width="77%" />


## 2. R로 통계공식 표기하는 방법 [^chicago-formula]

[^chicago-formula]: [Statistical Formula Notation in R](http://faculty.chicagobooth.edu/richard.hahn/teaching/formulanotation.pdf)

|  기호  |              의미                    |
|--------|--------------------------------------|
|   +    | 해당변수 포함                        |
|   -    | 해당변수 제외                        |
|   :    | 두변수 교호작용 포함                 |
|   *    | 해당변수와 교호작용 포함             |
|   `|`  | 조건 반영 해당변수 포함              |
|   ^    | 모든 변수와 모든 교호작용 포함       |
|  `I`   | 해당 수식으로 구성된 변수 새로 추가  |
|   1    | 상수항                               |

세가지 변수를 공식에 넣어 예측을 하는 경우 교호작용까지 고려하여 공식에 넣게 되면 다음과 같다.

$y_i = \beta_0 + \beta_1 X_i + \beta_2 Z_i + \beta_3 W_i + \beta_4 X_i \times Z_i + \beta_5 X_i \times W_i + \beta_6 W_i \times Z_i +\epsilon_i$


~~~{.r}
formula_notation_1 <- 'Y ~ X + Z + W + X:Z + X:W + Z:W'
formula_notation_2 <- 'Y ~ X * Z * W - X:Z:W'
formula_notation_3 <- 'Y ~ (X + Z + W)^2'

income_lm <- lm(formula_notation_1, data=income_df)
income_lm <- lm(formula_notation_2, data=income_df)
income_lm <- lm(formula_notation_3, data=income_df)
~~~

## 3. 집값 예측 사례

처음에 벽난로 숫자(`fireplaces`)로 집값을 예측하고, 평수(`living_area`)를 추가하고,
벽난로 숫자와 평수간의 교호작용도 추가하고, 땅값(`land_value`)도 추가하고, 
침실숫자(`bedrooms`)도 넣어가며 좀더 정밀한 집값 예측모형을 이해하면서 만들어 나간다.


~~~{.r}
library(statisticalModeling)
data(Houses_for_sale)

house_mod <- lm(price ~ fireplaces, data = Houses_for_sale)
gmodel(house_mod)
~~~

<img src="fig/house-prediction-1.png" title="plot of chunk house-prediction" alt="plot of chunk house-prediction" style="display: block; margin: auto;" />

~~~{.r}
house_mod <- lm(price ~ fireplaces + living_area, data = Houses_for_sale)
gmodel(house_mod)
~~~

<img src="fig/house-prediction-2.png" title="plot of chunk house-prediction" alt="plot of chunk house-prediction" style="display: block; margin: auto;" />

~~~{.r}
house_mod <- lm(price ~ fireplaces * living_area, data = Houses_for_sale)
gmodel(house_mod)
~~~

<img src="fig/house-prediction-3.png" title="plot of chunk house-prediction" alt="plot of chunk house-prediction" style="display: block; margin: auto;" />

~~~{.r}
house_mod <- lm(price ~ fireplaces * living_area + land_value, data = Houses_for_sale)
gmodel(house_mod)
~~~

<img src="fig/house-prediction-4.png" title="plot of chunk house-prediction" alt="plot of chunk house-prediction" style="display: block; margin: auto;" />

~~~{.r}
house_mod <- lm(price ~ fireplaces * living_area + land_value + bedrooms, data = Houses_for_sale)
gmodel(house_mod)
~~~

<img src="fig/house-prediction-5.png" title="plot of chunk house-prediction" alt="plot of chunk house-prediction" style="display: block; margin: auto;" />
