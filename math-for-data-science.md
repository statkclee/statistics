# 데이터 과학 -- 기초 통계



## 1. 데이터과학과 수학 {#data-science-math}

데이터는 누구나 다루는 시대가 되었는데 언제나 데이터를 효과적으로 다루고자 하면 언제나 등장하는 것이 수학이다.
그렇다고 수학을 전공할 것도 아니고, 데이터 과학에 꼭 필요한 수학적 지식은 무엇일까? 
이에 대해서 듀크 대학 교수님들이 [코세라, Data Science Math Skills](https://www.coursera.org/learn/datasciencemathskills/)라는 과목을 개설하여 
교육서비스를 제공하고 있다.

- 집합 이론(Set theory)
- 실수의 성질
- 구간 표기법과 부등식을 갖는 대수
- 합계와 시그마($\Sigma$) 표기법
- 데카르트 평면과 기울기, 거리 공식
- $x-y$ 평면 위에 (역)함수를 표현
- 곡선에 대한 접선과 순간변화율에 대한 미분 개념
- 지수, 로그, 자연로그함수
- 베이즈 정리를 포함하는 확률론

고등학교과정을 충실히 이수했다면 이미 알고 있는 지식이라, 오랜동안 사용하기 않아서 잊었다면 다시한번 
다져가는 것도 데이터를 과학적으로 다뤄보고자 할 때 등장하는 수학 공포증을 날려보낼 수도 있을 것으로 판단된다.

## 2. 집합론 [^wiki-set] {#set-theory}

[^wiki-set]: [위키백과 - 집합(Set)](https://ko.wikipedia.org/wiki/%EC%A7%91%ED%95%A9)

**집합(set)**은 서로 구별되는 대상들을 순서와 무관하게 모은 것으로 정의된다.
이때 집합에 속하는 각각의 대상들은 집합의 **원소(element)**라고 하고,
집합의 크기를 **기수(Cardinality)**로 원소의 집합에 대한 포함관계를 $\in$으로 표현한다. 
세상에 존재하는 거의 모든 것들은 집합의 원소가 될 수 있으며, 여기에는 숫자나 대수, 사람, 글자, 집합, 국가와 같은 개념들이 포함된다. 집합을 표현하는 방법은 일반적으로 알파벳의 대문자로 집합을 표기하고, 원소는 소문자로 표기한다.

### 2.1. 집합 표현법과 집합연산 {#set-notation}

집합을 기술하는 방법은 **원소나열법**과 **조건제시법**이 있다.

원소 나열법은 집합에 포함된 원소를 쭉 나열하는 것이다.

- $\{1, 2, 3\}$, $\{흰색, 검은색\}$, ...
- 1부터 10까지 자연수 $\{1, 2, 3, ..., 10\}$, 1부터 11까지 홀수 $\{1, 3, 5, ..., 11\}$

두번째는 원소를 일일이 사례를 들어 표현하는 대신에 논리적 관계를 기술하여 집합을 표현하는 조건제시법으로 다음과 같은 형태로 표현한다.

$$\{(\text{집합의 모든 원소의 형태})|(\text{원소의 조건})\}$$

- {x|x는 $1\le x \le 10$인 자연수} = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
- {x+y|x는 1 또는 2, y는 3 또는 4} = {4, 5, 6}
- {(x,y)|x $\in$ {1,2}, y $\in$ {1,2}} = {(1,1), (1,2), (2,1), (2,2)}

<img src="fig/type-one-type-two-error.png" alt="1종, 2종 오류" width="77%" />

예를 들어, 검사결과 임신이 되어 양성이 나온 사람(P)과 그렇지 않은 사람(N)을 집합으로 표현해 보자.

- $P = \{ x \in X | \text{x는 임신 검사결과 임신이 된 사람} \}$
- $N = \{ x \in X | \text{x는 임신 검사결과 임신이 되지 않은 사람} \}$

전체집합은 임신 검사결과 임신으로 판정된 사람과 임신이 되지 않은 것으로 판정된 사람이 되고, 임신이 된 것으로 판명된 사람과 동시에 임신이 되지 않은 것으로 판정된 사람은 없어 공집합($\phi$)이 된다.
또한, 임신 검사결과 오류가 있을 수 있어 집합기호를 통해 True Positive, False Positive, False Negative, True Nagative 4가지 경우로 표현할 수 있다.

보통 데이터과학에서 관심이 있는 사건을 $Y$로 표현하고 $Y$가 발현된 경우 1, 그렇지 않는 경우 0으로 인코딩한다.
$Y$를 임신상태로 표현하면, 1은 임신인 상태, 0은 임신이 되지 않은 상태를 각각 나타낸다.

- 모집단 사람($H$, 임신한 사람과 임신하지 않은 사람)
    - $임신상태 \cup 비임신 = H$ : 임신한 사람과 임신하지 않은 사람을 모두 합치면 전체집합이 된다.
    - $임신상태 \cap 비임신 = \phi$ : 임신한 사람과 동시에 임신하지 않은 사람은 존재하지 않아 공집합이 된다.
- 검사결과(임신검사 결과 임신 혹은 임신되지 않음으로 나옴) 
    - $음성 \cup 양성 = H$ : 임신검사결과 임신되었다는 결과와 임신이 되지 않았다는 결과를 모두 합하면 전체집합이 된다.
    - $음성 \cap 양성 = \phi$ : 임신검사결과 임신되었다는 결과와 동시에 임신이 되지 않았다는 결과는 나올 수가 없어 공집합이 된다.
- 모집단 사람과 검사결과를 조합하게 되면 다음 4가지 경우의 수가 나온다.
    - $비임신상태 \cap 음성$(False Nagative): 비임신상태인데, 검사결과 음성이 나와 임신이 아닌 것으로 판정된 경우
    - $임신상태 \cap 양성$(True Positive): 임신상태인데 검사결과 음성이 나와 임신이 된 것으로 판정된 경우.    
    - $비임신상태 \cap 양성$(False Positive, **1종 오류**): 비임신상태인데, 검사결과 양성이 나와 임신이 된 것으로 판정된 경우.
    - $임신상태 \cap 음성$(True Nagative, **2종 오류**): 임신상태인데 검사결과 음성이 나와 임신이 안 된 것으로 판정된 경우.
    
앞서 정의한 것을 기반으로 다음과 같이 전체 사람중에 임신상태인 사람과 비임신상태 사람의 비율을 계산할 수 있다.

- $\frac{|임신상태|}{|사람|}$ = 전체 사람중에서 임신상태인 사람의 비율
- $\frac{|비임신상태|}{|사람|}$ = 전체 사람중에서 비임신상태인 사람의 비율

또한, True Positive, False Positive, False Negative, True Nagative 경우를 집합으로 표현할 수 있다.

- $\frac{임신상태 \cap 양성}{|임신상태|}$ = True Postive로 임신상태인 사람중에서 임신검사결과 양성으로 판정된 사람의 비율
- $\frac{비임신상태 \cap 양성}{|비임신상태|}$ = False Postive로 비임신상태인 사람중에서 임신검사결과 양성으로 판정된 사람의 비율
- $\frac{임신상태 \cap 음성}{|임신상태|}$ = True Nagative로 임신상태인 사람중에서 임신검사결과 음성으로 판정된 사람의 비율
- $\frac{비임신상태 \cap 음성}{|비임신상태|}$ = False Positive로 비임신상태인 사람중에서 임신검사결과 음성으로 판정된 사람의 비율

데이터 과학에서 예측모형 성능평가와 관련된 사항은 [기계학습 알고리즘 성능평가](http://statkclee.github.io/ml/ml-assessment.html)를 참조한다.

### 2.2. R 코드 {#r-set-notation}

`sets` 팩키지와 `VennDiagram` 팩키지를 활용하여 집합연산을 R코드로 수행해본다. 

- [sets: Sets, Generalized Sets, Customizable Sets and Intervals](https://cran.r-project.org/web/packages/sets/)
- [VennDiagram: Generate High-Resolution Venn and Euler Plots](https://cran.r-project.org/web/packages/VennDiagram/index.html)

먼저 집합 $A,B$를 생성한다. 집합 $A = \{ A, B, \cdots, J \}$, 
집합 $B = \{ E, F, \cdots, O \}$가 된다. 집합의 크기/기수는 `length` 함수로 구한다.
`sets` 팩키지의 합집합, 교집합, "두 집합의 상대 여집합의 합"을 내장된 연산자로 각기 계산한다.


~~~{.r}
# 0. 환경설정 ----------
library(sets)
library(VennDiagram)
library(RAM)
library(eulerr)

# 1. 집합 정의 ---------
A <- LETTERS[1:10]
B <- LETTERS[5:15]

A_set <- as.set(A)
B_set <- as.set(B)

# 2. 집합 크기/기수(Cardinality) -------

length(A)
~~~



~~~{.output}
[1] 10

~~~



~~~{.r}
length(B)
~~~



~~~{.output}
[1] 11

~~~



~~~{.r}
# 3. 기본 집합 연산 --------------------
# 합집합
A_set | B_set
~~~



~~~{.output}
{"A", "B", "C", "D", "E", "F", "G", "H", "I", "J", "K", "L", "M",
 "N", "O"}

~~~



~~~{.r}
# 교집합
A_set & B_set
~~~



~~~{.output}
{"E", "F", "G", "H", "I", "J"}

~~~



~~~{.r}
# 두 집합의 상대 여집합의 합(Symmetric Difference)
A_set %D% B_set
~~~



~~~{.output}
{"A", "B", "C", "D", "K", "L", "M", "N", "O"}

~~~

`draw.pairwise.venn` 함수를 활용하여 앞에서 정의한 집합을 벤다이어그램으로 다음과 같이 시각화할 수 있다. 


~~~{.r}
# 4. 벤다이어그램 ---------------------
## 4.1. 종합

draw.pairwise.venn(
  area1 = length(A_set),
  area2 = length(B_set),
  cross.area = length(A_set & B_set),
  category = c("집합 A", "집합 B"),
  cat.pos = c(0, 180),
  euler.d = TRUE,
  sep.dist = 0.03,
  fill = c("light blue", "pink"),
  alpha = rep(0.5, 2),
  lty = rep("blank", 2)
)
~~~

<img src="fig/r-set-venn-diagram-1.png" style="display: block; margin: auto;" />

~~~{.output}
(polygon[GRID.polygon.11], polygon[GRID.polygon.12], polygon[GRID.polygon.13], polygon[GRID.polygon.14], text[GRID.text.15], text[GRID.text.16], text[GRID.text.17], text[GRID.text.18], text[GRID.text.19]) 

~~~



~~~{.r}
## 4.2. 원소도 함께 표현
group.venn(list(집합A=A, 집합B=B), label=TRUE, 
           fill = c("orange", "blue"),
           cat.pos = c(0, 0),
           lab.cex=1.3)
~~~

<img src="fig/r-set-venn-diagram-2.png" style="display: block; margin: auto;" />

실제 임신여부와 검사결과는 0 혹은 1로 표현된다.
이를 난수로 생성하여 표를 만들고 시각화를 하게 되면 다음과 같다.


~~~{.r}
# 5. 제1종, 제2종 오류 ----------------------
## 5.1. 데이터 준비 -------------------------

pregnant_TF <- rbinom(100, 1, 0.3)
test_PN     <- rbinom(100, 1, 0.3)

table(test_PN, pregnant_TF)
~~~



~~~{.output}
       pregnant_TF
test_PN  0  1
      0 61 12
      1 23  4

~~~



~~~{.r}
pregnant_df <- data.frame(pregnant_TF, test_PN)

## 5.2. 시각화 -------------------------
pregnant_fit <- euler(pregnant_df)

plot(pregnant_fit, auto.key = TRUE, counts=TRUE, labels = c("1종 오류", "2종 오류"))
~~~

<img src="fig/r-set-type-i-type-ii-1.png" style="display: block; margin: auto;" />


## 3. 합 $\Sigma$ 표기법 {#sigma-notation}

<iframe width="300" height="180" src="https://www.youtube.com/embed/5jwXThH6fg4" frameborder="0" allowfullscreen></iframe>

$\Sigma$ 표기법을 통해 합을 표현하는 것이 일반적이다.

- $\sum_{i=1}^{10} i = 1+2+3+ \cdots +10$
- $\sum_{i=1}^{100} i = 1+2+3+ \cdots +99+100$
- $\sum_{i=1}^{50} \pi i^2 = \pi \times0^2 + \pi \times 1^2 + \pi \times 2^2 +\cdots + + \pi \times 50^2$

$\Sigma$ 표기법을 통해 수식을 변형하는 것도 가능하다.

- $\begin{align}
     \sum_{i=1}^{3} (i^2 + 7i) &= (1^2 + 7\times 1) + (2^2 + 7\times 2) + (3^2 + 7\times 3) \\
                               &= (1^2 + 2^2 + 3^2) + (7\times 1 + 7\times 2 +7\times 3) \\
                               &= \sum_{i=1}^{3} (i^2) +  \sum_{i=1}^{3} (7i)
   \end{align}$

### 3.1. 평균과 분산 계산 {#mean-and-variance}
                               
산술평균은 가장 일반적으로 수식으로 데이터의 중심을 파악하고자 할 때 많이 사용된다.

$$\bar{x} = \frac{1}{n} \cdot \sum_{i=1}^n{x_i}$$
반면에, 분산은 데이터의 퍼짐을 측정로 자주 사용된다.

$$\sigma^2 = \frac{\sum_{i=1}^{n}(x_i-\overline{x})^2}{n}$$

분산에 제곱근을 취해 표준편차를 정의해서 제곱한 것을 걷어내어 평균에서 떨어진 거리를 파악하는데 직관적으로 활용한다. 

$$\sigma = \sqrt{\frac{\sum_{i=1}^{n}(x_i-\overline{x})^2}{n}}$$

### 3.1. 평균과 분산 {#r-mean-variance}

앞서 정의한 수학적 내용을 R 코드로 표현하면 다음과 같다.

- 합: `sum(dat)`
- 평균: sum(dat)/length(dat)
- 표본분산: sum((dat - mean(dat))^2) / (length(dat) -1)

> 모분산을 알수 없기 때문에 자유도 1을 떼어내서 표본분산을 계산한다. 


~~~{.r}
# 1. 합과 평균, 분산 ----------
## 데이터
dat <- seq(from=1, to=10, by=1)

## 합계
sum(dat)
~~~



~~~{.output}
[1] 55

~~~



~~~{.r}
## 평균
sum(dat)/length(dat) == mean(dat)
~~~



~~~{.output}
[1] TRUE

~~~



~~~{.r}
## 분산
sum((dat - mean(dat))^2) / (length(dat) -1)  == var(dat)
~~~



~~~{.output}
[1] TRUE

~~~

## 4. 함수 [^wiki-function] {#math-function}

[^wiki-function]: [위키백과 - 함수(function)](https://ko.wikipedia.org/wiki/%ED%95%A8%EC%88%98)

함수는 특정 집합에서 다른 집합으로 매핑하는 역할을 수행한다.
즉, 함수 $f$는 다음과 같은 튜플 $(X,Y,\operatorname{graph}f)$로 표현할 수 있으며,
집합 $X$를 정의역, 집합 $Y$를 공역이라고 부르며, 
$\operatorname{graph}f$는 곱집합 $X\times Y$의 부분집합이 되고, $f$의 그래프라고 부르며 
$f:X \rightarrow Y$ 같이 표현한다.

여기서 중요한 것은 임의의 $x\in X$에 대해서 $y\in Y$가 유일하게 존재해야 된다는 점이다. 즉,

- 임의의 $x\in X$에 대하여, $(x,y)\in\operatorname{graph}f$인 $y\in Y$가 유일하게 존재한다.

$f:X \rightarrow Y$로 함수를 표현하면 정의역 $x \in X$ 원소는 $f(x) \in Y$ 집합의 원소로 포함된다.

함수를 표현하는 방식은 크게 세가지로 나눠진다.

- 열거법: 대응 규칙이 유한 개일 때만 완전하게 표현된다.
    - $\{아빠, 엄마, 동생, 나\} \rightarrow  \{1월 1일, 2월 15일, \cdots  12월 30일, 12월 31일\}$
- 공식이나 알고리즘을 통한 표현법
    - $f:\{1,2,3\} \rightarrow  \{4,5,6,7\}$
    - $f:x \rightarrow  x+3$
- 함수의 그래프를 통한 표현법

데이터과학에서 지도학습(Supervised Learning)을 통한 예측모형을 개발하는 경우 함수로 다음과 같이 표현할 수 있다.
즉, 정의역에 속한 $(X_1, X_2,\cdots, X_n)$ 집합원소를 수익 $\mathbb{R}$에 매핑하는 함수가 지도학습 모형이 된다.

$\text{수익}: (X_1, X_2,\cdots, X_n) \rightarrow \mathbb{R}$

### 4.1. 함수 R 코드 {#r-code-function}

$y=2\cdot x -1$ 함수와 $y=x^2$ 함수를 그래프로 표현하면 다음과 같다.


~~~{.r}
#library(hrbrthemes)
#library(extrafont)
#library(tidyverse)
#loadfonts()

x <- seq(from=-5, to=5, by=0.5)
y <- 2 * x -1

df <- data.frame(x, y)

ggplot(df, aes(x, y)) +
  geom_point() +
  geom_line() +
  stat_function(fun=function(x)x^2, geom="line", aes(colour="square")) +
  theme_ipsum_rc(base_family = "NanumGothic") +
  theme(legend.position = "none",
        axis.line.x=element_blank(),
        axis.ticks.x=element_blank(),
        axis.title.x=element_blank(),
        panel.grid.minor.x=element_blank(),
        panel.grid.major.x=element_blank(),
        panel.grid.minor.y=element_blank(),
        panel.grid.major.y=element_blank()) +
  geom_vline(xintercept=0) +
  geom_hline(yintercept = 0) +
  labs(x="", y="") +
  annotate("text", 3, 5, vjust = -1, label = "y=2x-1", parse = FALSE) +
  annotate("text", 4, 15, vjust = -1, label = "y=x^2", parse = FALSE)
~~~

<img src="fig/r-function-code-1.png" style="display: block; margin: auto;" />

### 4.2. 함수 적용사례 - 남자신장 {#r-function-height}

[연령별 학생 평균 키(행정구역/성별)](http://gsis.kwdi.re.kr:8083/statHtml/statHtml.do?orgId=338&tblId=DT_1LEA012#) 데이터를 받아 2015년 기준 남자 연령별로 신장을 매핑하는 함수를 구해보자.

먼저 데이터를 데이터프레임형태로 가공한다. 그리고 나서, 연령을 정의역으로 갖고 신장을 공역으로 갖는데 3차 다항식 함수로 매핑해보자.

$$f: \text{연령} \rightarrow \text{신장}$$
$\text{연령} \rightarrow \text{신장}$으로 매핑하는 함수는 $f(\text{연령}) = \beta_0 + \beta_1 \cdot \text{연령} + \beta_2 \cdot \text{연령}^2 + \beta_3 \cdot \text{연령}^3$으로 정의한다.



~~~{.r}
height_df <- tribble(~연령, ~신장,
                       6	, 120.3,
                       7	, 125.5,
                       8	, 131.8,
                       9	, 136.6,
                       10, 	143.0,
                       11, 	148.9,
                       12, 	156.6,
                       13, 	163.5,
                       14, 	168.5,
                       15, 	171.4,
                       16, 	173.0,
                       17, 	173.4,
                       18, 	173.5)


height_lm <- lm(신장 ~ 연령 + I(연령^2) + I(연령^3), data=height_df)

ggplot(height_df, aes(연령, 신장)) +
  geom_point() +
  stat_function(fun=function(x) coef(height_lm)[1] + coef(height_lm)[2]*x + coef(height_lm)[3]*x^2 + coef(height_lm)[4]*x^3, geom="line", aes(colour="square")) +
  theme_ipsum_rc(base_family = "NanumGothic") +
  theme(legend.position = "none",
        axis.line.x=element_blank(),
        axis.ticks.x=element_blank(),
        axis.title.x=element_blank(),
        panel.grid.minor.x=element_blank(),
        panel.grid.major.x=element_blank(),
        panel.grid.minor.y=element_blank(),
        panel.grid.major.y=element_blank()) +
  geom_vline(xintercept = 6) +
  geom_hline(yintercept = 110) +
  labs(x="연령", y="신장")
~~~

<img src="fig/height-funciton-1.png" style="display: block; margin: auto;" />

상기 함수를 수식으로 표현하면 다음과 같다.

- 신장=132.1 -9.6연령 1.6연령^2 -0.1연령^3

