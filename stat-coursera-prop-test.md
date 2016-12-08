---
layout: page
title: 데이터 과학 -- 기초 통계
subtitle: 단일 표본 비율검정 -- 웹사이트 선호도 분석
---

> ## 학습 목표 {.objectives}
>
> * 사용자 선호도 비율을 검정한다.
> * 단일 표본 비율 검정을 수행한다.
> * 단일 표본 수준이 3개 이상인 경우 비율 검정을 수행한다.


### 1. 비율 검정

| 표본 | 반응 범주 |                     검정                                 |
|------|-----------|----------------------------------------------------------|
|  1   |     2     | 단일 표본 $\chi^2$ 검정, 이항 검정(Binomial Test)    |
|  1   |   2 이상  | 단일 표본 $\chi^2$ 검정, 다항 검정(multinomial Test)     |
| 1이상|   2 이상  | $N$ 표본 $\chi^2$ 검정, G-검정, 피셔의 정확검정      |

#### 1.1 단일 표본 비율검정 -- 웹사이트 A,B 선호도 분석

웹사이트 두개를 개발해 두고, 사용자에게 A 웹사이트와 B 웹사이트에 대한 선호도를 조사한다.
통상 A 웹사이트는 기존 웹사이트가 되고, B 웹사이트는 새로운 UI, 기능이 추가된 웹사이트다.

데이터는 [Practical Statistics for HCI](http://depts.washington.edu/aimgroup/proj/ps4hci/) 사이트에 공개된 SPSS 파일을 R로 불러와서 실험 검증에 사용한다.

~~~ {r}
##==============================================================================
## 01. 데이터 가져오기
##==============================================================================
library(haven)
spss.dat <- file.choose()
pref.df <- read_spss(spss.dat)

head(pref.df)
~~~

~~~ {.output}
Source: local data frame [6 x 2]

  Subject Preference
    (dbl)     (lbll)
1       1          1
2       2          2
3       3          2
4       4          1
5       5          1
6       6          1
~~~

웹선호도에 대한 코딩이 `1`, `2`로 되어 있다. 이를 `A`, `B` 로 코딩을 바꾼다.

~~~ {r}
##==============================================================================
## 02. 데이터 전처리 및 정제
##==============================================================================
library(dplyr)
library(plyr)

pref.df$Subject <- sapply(pref.df$Subject, as.factor)
pref.df$Preference <- sapply(pref.df$Preference, as.factor)

pref.df <- pref.df %>% mutate(Preference = revalue(Preference, c('1'='A', '2'='B')))
~~~

~~~ {r}
##==============================================================================
## 03. 실험설계 통계분석
##==============================================================================
# 3.1. 기초 통계 및 시각화
summary(pref.df)
plot(pref.df$Preference)
~~~

~~~ {.output}
    Subject   Preference
 1      : 1   A:21      
 2      : 1   B: 9      
 3      : 1             
 4      : 1             
 5      : 1             
 6      : 1             
 (Other):24      
~~~

~~~ {r}
#-------------------------------------------------------------------------------
# 3.2 실험설계 단일표본 비율검정: One-Sample Proportion Test
#-------------------------------------------------------------------------------
# 근사 카이스퀘어 검정
prefs <- xtabs( ~ Preference, data=pref.df)
prefs
chisq.test(prefs)
~~~

$p-$값 0.05를 기준으로 본다면, 웹사이트 A와 B 선호도에 유의성이 있다는 결론에 도달하게 된다.

~~~ {.output}
    Chi-squared test for given probabilities

data:  prefs
X-squared = 4.8, df = 1, p-value = 0.02846
~~~

~~~ {r}
# 정확 이항 검정 (Exact Binomial Test)
binom.test(prefs)
~~~

$\chi^2$ 검정은 표본수가 많아지게 되면 정확도가 높아지는 근사검정이지만 정확 이항 검정(Exact Binomial Test)은 컴퓨팅 계산에 부담이 되지만, 근사 검정이 아니고 정확 검정이다. 귀무가설 웹사이트 A와 B의 선호도가 같다는 95% 신뢰구간에 0.5가 포함되지 않고 있다. 

~~~ {.output}
    Exact binomial test

data:  prefs
number of successes = 21, number of trials = 30, p-value = 0.04277
alternative hypothesis: true probability of success is not equal to 0.5
95 percent confidence interval:
 0.5060410 0.8526548
sample estimates:
probability of success 
                   0.7 
~~~


#### 1.2. 단일 표본 비율검정 -- 웹사이트 A,B,C 선호도 분석

웹사이트가 A,B 두가지 아니고 3개 이상인 경우 A,B,C 웹사이트 간에 선호도 차이가 있는지 검정한다.

~~~ {r}
##==============================================================================
## 01. 데이터 가져오기
##==============================================================================
library(haven)
spss.dat <- file.choose() # prefs3.sav
pref.df <- read_spss(spss.dat)

head(pref.df)
~~~

~~~ {.output}
Source: local data frame [6 x 2]

  Subject Preference
    (dbl)     (lbll)
1       1          3
2       2          3
3       3          3
4       4          1
5       5          1
6       6          3
~~~

웹선호도에 대한 코딩이 `1`, `2`, `3`으로 되어 있다. 이를 `A`, `B`, `C`로 코딩을 바꾼다.

~~~ {r}
##==============================================================================
## 02. 데이터 전처리 및 정제
##==============================================================================
library(dplyr)
library(plyr)

pref.df$Subject <- sapply(pref.df$Subject, as.factor)
pref.df$Preference <- sapply(pref.df$Preference, as.factor)

pref.df <- pref.df %>% mutate(Preference = revalue(Preference, c('1'='A', '2'='B', '3'='C')))
~~~

~~~ {r}
##==============================================================================
## 03. 실험설계 통계분석
##==============================================================================
# 3.1. 기초 통계 및 시각화
summary(pref.df)
plot(pref.df$Preference)
~~~

~~~ {.output}
    Subject   Preference
 1      : 1   C:13      
 2      : 1   A:13      
 3      : 1   B: 4      
 4      : 1             
 5      : 1             
 6      : 1             
 (Other):24   
~~~

~~~ {r}
#-------------------------------------------------------------------------------
# 3.2 실험설계 단일표본 비율검정: One-Sample Proportion Test
#-------------------------------------------------------------------------------
# 근사 카이스퀘어 검정
prefs <- xtabs( ~ Preference, data=pref.df)
prefs
chisq.test(prefs)
~~~

$p-$값 0.05를 기준으로 본다면, 웹사이트 A, B, C 선호도에 유의성이 없다는 결론에 도달할 수 있지만, 0.05에 
가깝기 때문에 유보한다.

~~~ {.output}
    Chi-squared test for given probabilities

data:  prefs
X-squared = 5.4, df = 2, p-value = 0.06721
~~~

다항분포 검정을 통한 정확 다항검정에서는 0.05에 더욱 가까운 $p-$ 값이 검출되었다.

~~~ {r}
# 다항분포 검정
library(XNomial)
xmulti(prefs, c(1/3, 1/3, 1/3), statName = "Prob")
~~~

~~~ {.output}
P value (Prob) = 0.05117
~~~

다중비교는 세가지 웹사이트 간에 차이가 있다는 것은 이해가 되나 어느 것에서 차이가 나는지 검출할 때 사용한다.

~~~ {r}
# 다중 비교
aa <- binom.test(sum(pref.df$Preference=='A'), nrow(pref.df), p=1/3)
bb <- binom.test(sum(pref.df$Preference=='B'), nrow(pref.df), p=1/3)
cc <- binom.test(sum(pref.df$Preference=='C'), nrow(pref.df), p=1/3)
p.adjust(c(aa$p.value, bb$p.value, cc$p.value), method="holm")
~~~

웹사이트 A, C 선호도에는 차이가 없지만, B에는 유의성이 있는 것으로 보인다. 

~~~ {.output}
[1] 0.4996989 0.0583577 0.4996989
~~~
