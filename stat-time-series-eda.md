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
1998   20   18   12   16
1999    7   13   15    1
2000    8    4   19   17
2001    2    9   11    3
2002    5   10   14    6

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
> ARMA 모형은 월드 분해정리를 따르는 잘 따라서 시계열 데이터를 모형화하기 적합하다.




