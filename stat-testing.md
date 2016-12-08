# 데이터 과학 -- 기초 통계





> ## 학습 목표 {.objectives}
>
> * 실무에서 자주 사용하는 검정에 대해 살펴본다.

### 1. 언제 $z-$검정과 $t-$검정을 사용해야 할까?

표본평균과 모평균을 비교할 때 $z-$검정과 $t-$검정을 사용하지만 결정적인 차이는 모집단 표준편차(SD)를 아느냐 모르냐에 따라 달라진다. 
일반적인 경우 모집단 표준편차를 알지 못한다. 


## 1.1. 4가지 검정 비교

$z-$검정, 단일 표본 $t-$검정, 쌍체 $t-$ 검정, 독립 $t-$검정 이 네가지 검정은 평균을 비교하는데 많이 사용되는 통계적 검정이다.
데이터에서 관측값(Observed)을 얻고, 기대값(Expected)을 빼고 이를 표준오차로 나누어서 평균 비교작업을 수행한다.

|                    |          관측값          |        기대값       |      표준오차       |
|--------------------|--------------------------|---------------------|---------------------|
| $z-$검정           |         표본 평균        |     모평균          |     평균 표준오차   |
| 단일 표본 $t-$검정 |         표본 평균        |     모평균          |     평균 표준오차   |
| 쌍체 $t-$ 검정     |   점수 차이 표본 평균    | 점수 차이 모평균    | 평균차이 표준오차   |
| 독립 $t-$검정      |     두 표본평균 차이     |  두 표본평균 차이   | 평균간 차이 표준오차|


### 1.2. 표집 분포

요약 통계량에 대한 가설분포를 표집 분포라고 부른다.  $z-$검정의 경우 표집분포의 평균은 근사적으로 모평균과 같고,
표집분포의 표준편차는 표준오차가 된다. 중심극한정리에 따라 표본크기가 증가함에 따라 표준오차는 감소한다. 

귀무가설 유의성검정(Null Hypothesis Significance Testing, NHST) 얼개에 따르면, 귀무가설은 효과가 없다가 된다.
표준오차는 표본크기가 증가함에 따라 감소하게 된다.

$\textrm{표준오차}\downarrow = \frac{\textrm{모 표준편차}}{\sqrt{\textrm{표본 크기}\uparrow}}$

$p-$값은 확률값(Probability Value)를 줄인 것으로 귀무가설이 참일 때 조건부 확률값이 된다. 
표준오차는 우연에 의한 차이로 볼 수 있는데, 우연의 차이가 아닌 유의성을 주장하려면 적어도 **$2 \times$ 표준편차** 보다 차이가 나야 된다. 

### 1.3. 독립 $t-$검정

신규 기능이 소프트웨어에 추가되었는데 이에 대해 매출 효과가 발생했는지 측정하는데 있어, 
한 사용자 집단은 기존 방식대로 기능을 제공하여 매출을 측정하고, 다른 사용자 집단은 신규 기능이 추가된 소프트웨어를 제공하여
유의적인 매출 증대효과가 있는지 검증할 때 독립 이표본 $t-$검정을 사용한다.

$t-\textrm{값} = \frac {\textrm{관측값} - \textrm{기대값}}{\textrm{표준오차}}$

$t = \frac{\bar {X}_1 - \bar{X}_2}{s_{X_1X_2} \cdot \sqrt{\frac{2}{n}}}$

여기서 $s_{X_1X_2} = \sqrt{\frac{s_{X_1}^2+s_{X_2}^2}{2}}$

> ### $t-$ 검정 편향 문제 {.callout}
> 
> $t-$검정은 표본 크기에 따라 편향되는 문제가 있어 코헨-d (Cohen d)가 일반적인 효과크기 추정치가 된다.
> 
> 코헨-d는 다음과 같이 정의된다.
> 
> $d = \frac{\bar{x}_1 - \bar{x}_2}{s}$
> 
> 여기서, $s = \sqrt{\frac{(n_1-1)s^2_1 + (n_2-1)s^2_2}{n_1+n_2 - 2}}$는 두 표준편차를 합동하여 합친 것이 된다.

두 집단간 평균의 차이를 검증할 때 또 확인해야 되는 사항이 등분산성 검정이다. 
두 집단의 분산이 동일해야 여기에서 추론하는 결론이 유의미할 수 있다.
등분산성 가정을 검정하는 방법이 [레빈 검정(Levene's test)](https://en.wikipedia.org/wiki/Levene%27s_test)이다. 
따라서 독립 $t-$검정을 시작하기 전에 레빈 검정을 통해 등분산성이 만족되는지 사전에 확인하고 이게 충족되면 $t-$검정 혹은 분산분석으로 넘어가고 
그렇지 않는 경우 비모수 검정이나 일반화된 웰치 $t-$검정(Welch's $t-$test)로 넘어간다.
웰치 $t-$검정의 또다른 이름은 이분산(unequal variance) $t-$ 검정으로 $t-$ 검정을 일반화한 것이다.

즉, 등분산성 가정이 만족되지 않고, 두집단간 표본크기가 불균형된 경우 웰치 $t-$검정을 우선 검토한다.

$W = \frac{(N-k)}{(k-1)} \frac{\sum_{i=1}^k N_i (Z_{i\cdot}-Z_{\cdot\cdot})^2} {\sum_{i=1}^k \sum_{j=1}^{N_i} (Z_{ij}-Z_{i\cdot})^2}$



~~~{.r}
suppressPackageStartupMessages(library(readr))
t.dat <- read_csv("data/t-test-data.csv", col_names = TRUE)
~~~



~~~{.output}
Parsed with column specification:
cols(
  cond = col_character(),
  pre = col_integer(),
  post = col_integer(),
  gain = col_integer(),
  train = col_integer()
)

~~~



~~~{.r}
# 레빈 등분산 검정
suppressPackageStartupMessages(library(car))
levene.test(t.dat$gain, t.dat$cond)
~~~



~~~{.error}
Warning: 'levene.test' is deprecated.
Use 'leveneTest' instead.
See help("Deprecated") and help("car-deprecated").

~~~



~~~{.error}
Warning in leveneTest.default(...): t.dat$cond coerced to factor.

~~~



~~~{.output}
Levene's Test for Homogeneity of Variance (center = median)
      Df F value Pr(>F)
group  1  2.5024  0.122
      38               

~~~

$t-$검정을 R에서 수행하는 방법은 `t.test` 함수를 사용하고 인자로 `var.equal`을 설정하여 명시적으로 수행한다.


~~~{.r}
# 독립 t-검정
suppressPackageStartupMessages(library(dplyr))
t.dat.t08 <- t.dat %>% filter(cond == "t08")
t.dat.t19 <- t.dat %>% filter(cond == "t19")

t.test(t.dat.t08$gain, t.dat.t19$gain, var.equal = TRUE)
~~~



~~~{.output}

	Two Sample t-test

data:  t.dat.t08$gain and t.dat.t19$gain
t = -8.9677, df = 38, p-value = 6.443e-11
alternative hypothesis: true difference in means is not equal to 0
95 percent confidence interval:
 -5.209412 -3.290588
sample estimates:
mean of x mean of y 
     1.35      5.60 

~~~



~~~{.r}
# 코헨 d
suppressPackageStartupMessages(library(lsr))
cohensD(t.dat.t08$gain, t.dat.t19$gain, method = "pooled")
~~~



~~~{.output}
[1] 2.835822

~~~

### 1.4. 쌍체 $t-$검정

독립 $t-$검정과 비교하여 종속 $t-$검정이라고 하고 동일한 사용자가 반복해서 특정 요인에 차이가 있는지 검정할 때 사용된다.
각 사용자마다 전후 평균 차이를 검정한다.

유의성 검정은 표본 크기에 따라 편향이 발생하게 된다. 표본 크기가 커지게 되면 표준오차가 작아지게 된다.
표본 크기가 클 때, 관측된 작은 차이도 "통계적 유의성"을 갖게 된다. **효과크기(effect size)**는 효과의 크기를 추정한다. 

**코헨 d** 는 표준오차가 아닌 표준 편차로 나눈다.


~~~{.r}
# 쌍체 t-검정
t.test(t.dat$post, t.dat$pre, paired = TRUE)
~~~



~~~{.output}

	Paired t-test

data:  t.dat$post and t.dat$pre
t = 8.4158, df = 39, p-value = 2.662e-10
alternative hypothesis: true difference in means is not equal to 0
95 percent confidence interval:
 2.639805 4.310195
sample estimates:
mean of the differences 
                  3.475 

~~~



~~~{.r}
# 코헨 d t-검정
cohensD(t.dat$post, t.dat$pre, method = "paired")
~~~



~~~{.output}
[1] 1.330659

~~~

## 2. A/B검정에 활용되는 일반적 통계량 


|      가정한 분포      |        사례           |      일반적 검정      |
|-----------------------|-----------------------|-----------------------|
|    정규 분포          | 구매고객 평균 매출    |    웰치 $t-$검정      |
|    이항 분포          |     클릭율(CTR)       |   피셔의 정확검정     |
|    포아송 분포        | 구매고객 평균 매매량  |     E-검정            |
|    다항 분포          | 제품별 구매 수량      |   카이 제곱 검정      |
|   분포 가정 없음      |                       |   만-위트니 U 검정    |


### 2.1. 피셔의 정확검정을 통한 광고 연결률 효과 분석 [^fisher-exact-ctr]

[^fisher-exact-ctr]: [The Only* Statistical Significance Test You Need](http://www.lunametrics.com/blog/2014/07/01/statistical-significance-test/)

광고 A [광고 연결률(CTR, click through rates)](http://terms.naver.com/entry.nhn?docId=819411&cid=42344&categoryId=42344)이 1.1%, 광고 B 클릭율이 1.3% 으로 나왔다면 
1.3% > 1.1% 로직으로 광고 A가 더 좋을까? 그럴수도 그렇지 않을 수도 있다.

사용자 1,000명 대상 광고 효과를 A/B 검정하면 유의적이지 않지만, 100,000 명 대상으로 광고효과를 검정하면 유의적으로 파악된다.
동일한 1.3%, 1.1% 광고 연결률이지만 사용대상자 숫자에 따라 통계적 추론을 달라진다.


~~~{.r}
# 1. 피셔의 정확검정(Fisher's exact test)--------------------------
## 1,000 번 사례
ad_1000 <-  matrix(c(11, 13, 989, 987), nrow = 2,
                  dimnames = list(ads   = c("Ad A", "Ad B"),
                                  click = c("Click", "No Click")))
ad_1000
~~~



~~~{.output}
      click
ads    Click No Click
  Ad A    11      989
  Ad B    13      987

~~~



~~~{.r}
fisher.test(ad_1000, alternative = "two.sided")
~~~



~~~{.output}

	Fisher's Exact Test for Count Data

data:  ad_1000
p-value = 0.8378
alternative hypothesis: true odds ratio is not equal to 1
95 percent confidence interval:
 0.3407936 2.0539927
sample estimates:
odds ratio 
  0.844515 

~~~



~~~{.r}
## 100,000 번 사례
ad_100000 <-  matrix(c(1100, 1300, 98900, 98700), nrow = 2,
                  dimnames = list(ads   = c("Ad A", "Ad B"),
                                  click = c("Click", "No Click")))
ad_100000
~~~



~~~{.output}
      click
ads    Click No Click
  Ad A  1100    98900
  Ad B  1300    98700

~~~



~~~{.r}
fisher.test(ad_100000, alternative = "two.sided")
~~~



~~~{.output}

	Fisher's Exact Test for Count Data

data:  ad_100000
p-value = 4.335e-05
alternative hypothesis: true odds ratio is not equal to 1
95 percent confidence interval:
 0.7782170 0.9162075
sample estimates:
odds ratio 
 0.8444445 

~~~

