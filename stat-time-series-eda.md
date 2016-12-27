---
layout: page
title: 데이터 과학 -- 기초 통계
subtitle: 탐색적 시계열 데이터 분석
output:
  html_document: 
    keep_md: yes
  pdf_document:
    latex_engine: xelatex
mainfont: NanumGothic
---



> ## 학습 목표 {.objectives}
>
> * 시계열 데이터 유형을 파악한다.
> * 주요 시계열 모형 및 기본 가정을 살펴본다.


## 시계열 데이터 종류

시계열 데이터의 종류는 크게 보면 두가지 구체적으로 세가지 유형이 있다.

- 등간격 규칙을 갖는 시계열 데이터
- 불규칙 간격을 갖는 시계열 데이터
- 등간격 규칙을 갖으나 결측값이 포함된 시계열 데이터

다음에 나와 있는 시계열 데이터는 이를 특성을 잘 나타내고 있다. 


~~~{.r}
library(zoo)
par(mfrow=c(3,1))

# 규칙을 갖는 시계열 데이터 ---------------------------------
reg_ts_dat <- ts(sample(20), frequency = 4 , start= 1998)
reg_ts_dat
~~~



~~~{.output}
     Qtr1 Qtr2 Qtr3 Qtr4
1998   10   17    8    3
1999    5   12   19   14
2000    4   16    1   20
2001   15    7   18    6
2002   11    2    9   13

~~~



~~~{.r}
plot(reg_ts_dat, type="o", lty=5, lwd=0.8, pch="O", col=2, xlab="", ylab="")

# 불규칙 시계열 데이터 --------------------------------------
## 불균등 관측점을 갖는 시계열 데이터 -----------------------
dat_date <- c("2016-12-05", "2016-11-30", "2016-12-7" ,"2016-12-17" ,"2016-11-15", "2016-12-31", "2016-12-28")
dat_value <- sample(7)
dat_zoo <- zoo(dat_value, as.Date(dat_date))

plot(dat_zoo, type="o", lty=5, lwd=0.7, pch="O", col=2, xlab="", ylab="")

## 규칙을 갖는 시계열이나 결측값이 있는 시계열 데이터 -------
dat <- c(sample(3),NA,sample(3), NA, sample(4))
ts_dat <- ts(dat, start = c(2016, 1), frequency=12)

plot(ts_dat, type="o", lty=5, lwd=0.7, pch="O", col=2, xlab="", ylab="")
~~~

<img src="fig/time-series-eda-1.png" title="plot of chunk time-series-eda" alt="plot of chunk time-series-eda" style="display: block; margin: auto;" />

## 시계열 데이터 기본 가정 및 시계열 모형

시계열 데이터에 대한 기본 가정은 다음과 같다.

- 시계열 관측점은 등간격(equally spaced)이다.
- 시간 인덱스는 이산 시간 관측점을 적용한다.
- 이런 가정은 근사적으로 성립한다.
    - 주식시장 일별 수익률은 주말과 휴일에는 적용되지 않는다.
    - 주요 경제지표(소비자 물가지수 등)는 일별이 아닌 월별로 적용된다.

시계열 모형은 크게 4가지 모형으로 분류된다.

- 백색잡음 (White Noise)
- 랜덤워크 (Random Walk)
- 자기회귀 (Autoregression)
- 이동평균 (Moving Average)


> ### 월드 분해정리 (Wold's decomposition theorem) {.callout}
> 
> 월드는 정상성을 갖는 모든 시계열 데이터는 백색잡음의 선형결합으로 표현될 수 있다는 것을 
증명하여 노벨상을 수상했다.  
> 
> $X_t = W_t + a_1 W_{t-1} + a_2 W_{t-2} + \cdots$  
>  
> $W_i$는 백색잡음, ARMA 모형은 월드 분해정리에 따라 시계열 데이터를 모형화하기 적합하다.

## 시계열 데이터 분석 흐름 [^time-series-concept-example]

[^time-series-concept-example]: [Stationarity Tests in R, checking mean, variance and covariance](http://stackoverflow.com/questions/33522442/stationarity-tests-in-r-checking-mean-variance-and-covariance)

시계열 데이터를 분석하기 위해서 가장 먼저 문자형과 숫자형으로 준비된 데이터프레임을 시간자료구조를 포함한 
적절한 데이터프레임으로 변환하는 과정이 필요하다. 
시계열 데이터프레임이 준비되면 시각화를 통해 시계열 데이터 상태를 파악하고 정상 시계열 데이터로 변화하는 과정에 전념한다.

<img src="fig/stat-time-series-workflow.png" alt="시계열 데이터 분석 흐름" width="77%" />


> ### 정상성(Stationarity)과 약정상성(Weak Stationarity) {.callout}
> 
> 정상성을 갖는 시계열 데이터는 랜덤한 움직임을 갖지만 시점마다 유사하게 동작하는 특징이 있어 시간에 따라 안정된 분포를 갖는다.
> 약정상성은 시간에 따라 일정한 평균, 분산, 공분산을 갖는 확률과정으로 정의된다. 즉, 시계열 데이터 $Y_1 , Y_2 , \cdots $이 다음을 세가지 
> 조건을 충족하면 된다.
> 
> - 시계열 $Y_t$ 모든 시점 $t$에 대해 평균이 $\mu$로 같다.
> - 시계열 $Y_t$ 모든 시점 $t$에 대해 분산이 $\sigma^2$로 같다.
> - 시계열 $Y_t$, $Y_s$ 모든 $h$에 대해 공분산이 $|t-s| = h$로 같다; 예를 들어, 6단위 만큼 차이가 나서 $Cov(Y_1, Y_7 ) = Cov(Y_11 , Y_17 )$.
> 
> 특히, 정상성을 갖는 데이터는 절약성 원칙(Principle of Parsimony)에 따라 적은 모수만을 사용하여 모형을 만들 수 있는 장점이 있다.



~~~{.r}
par(mfrow=c(2,3))

# 백색잡음 시계열 -----------------------------------------------------------
y_t <- rnorm(100, mean=0, sd=1)

ts.plot(y_t, main="백색잡음(White Noise)",xlab="시간(time)", col="blue", lwd=2)
abline(h=0)

# 추세를 갖는 시계열--------------------------------------------------
e <- rnorm(100, 0, 10)
trend_yt <- 0.5*seq(1,100) + e

ts.plot(trend_yt, lwd=2, col="blue", main="추세를 갖는 시계열")
abline(a=0, b=0.5)

# 랜덤워크 시계열 -----------------------------------------------------------

e <- rnorm(100)
rw_yt <- cumsum(e)
ts.plot(rw_yt, lwd=2, col="blue", main="랜덤워크 시계열")
abline(h=0)

# 분산이 커지는 시계열 -----------------------------------------------------------
e <- sapply(1:100, function(x) rnorm(1, sd = 2*x/100))
var_yt <- 0.1*seq(1,100) + e

ts.plot(var_yt, lwd=2, col="blue", main="추세와 분산이 커지는 시계열")
abline(a=0, b=0.1)

# 평균이 증가하는 시계열 -----------------------------------------------------------
mean_yt <- c(rnorm(50), rnorm(50, 5))

ts.plot(mean_yt, lwd=2, col="blue", main="평균이 변화하는 시계열")
abline(a=0, b=0) 
abline(a=5, b=0)


# 분산이 변화하는 시계열 -----------------------------------------------------------
var_change_yt <- c(rnorm(150,0,1), rnorm(200,0,10), rnorm(150,0,1))

ts.plot(var_change_yt, lwd=2, col="blue", main="분산이 변화하는 시계열")
abline(a=0, b=0) 
~~~

<img src="fig/time-series-concept-1.png" title="plot of chunk time-series-concept" alt="plot of chunk time-series-concept" style="display: block; margin: auto;" />

|   백색잡음   | 추세를 갖는 시계열 |   랜덤워크 시계열   |
|--------------|--------------------|---------------------|
| 추세와 분산이 커지는 시계열 | 평균이 변화하는 시계열 | 분산이 변화하는 시계열 |

