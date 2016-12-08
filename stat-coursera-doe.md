---
layout: page
title: 데이터 과학 -- 기초 통계
subtitle: 실험 유형 구분
---

> ## 학습 목표 {.objectives}
>
> * 실험 유형을 구분한다.

### 1. 실험 유형 [^coursear-doe]

[^coursear-doe]: [Designing, Running, and Analyzing Experiments](https://www.coursera.org/learn/designexperiments)


#### 1.1. 비율 검정

| 표본 | 반응 범주 |                     검정                                 |
|------|-----------|----------------------------------------------------------|
|  1   |     2     | 단일 표본 $\chi^2$ 검정, 이항 검정(Binomial Test)    |
|  1   |   2 이상  | 단일 표본 $\chi^2$ 검정, 다항 검정(multinomial Test)     |
| 1이상|   2 이상  | $N$ 표본 $\chi^2$ 검정, G-검정, 피셔의 정확검정      |


#### 2. 분산 분석과 일반화 선형모형

| 요인  |   수준  | 군간(B)/군내(W) |     모수 검정      |          비모수 검정                 |
|-------|---------|-----------|--------------------------|--------------------------------------|
|  1    |   2     | 군간(B)   | 독립표본 $t-$검정        | 만-위트니 $U$ 검정(Mann-Whitney)     |
|  1    |  2 이상 | 군간(B)   | 일원 분산분석            | 크루스칼-왈리스 검정(Kruskal-Wallis) |
|  1    |    2    | 군간(W)   | 쌍체 $t-$ 검정            | 윌콕슨 부호순위 검정(Wilcoxon signed-rank test)|
|  1    |  2 이상 | 군간(W)   | 일원 반복측정 분산분석   | 프리드만의 검정 (Friedman's test)    |
| 1이상 |  2 이상 | 군간(B)   | 요인 분산분석, 선형모형  | Aligned Rank Transform(ART), 일반화선형모형(GLM)|
| 1이상 |  2 이상 | 군간(W)   | 요인 반복측정 분산분석, 선형혼합모형  | Aligned Rank Transform(ART), 일반화선형혼합모형(GLMM)|


### 2. 비율 검정

#### 2.1. 사용자 선호도(User Preference) 

* 단일 표본 비율 검정(One-Sample Test of Proportions)
* 단일표본 비율검정 (More One-Sample Tests of Proportions)
* 이표본 비율검정(Two-Sample Tests of Proportions)





#### A/B 테스트 -- $t-$검정

* $t-$검정

#### 작업 완수 시간 -- 분산분석

* 일원 분산분석 (One-way Analysis of Variance)
    * 군간(Between) 요인 분석

#### 사용자 검색 -- 반복 측정

* 반복측정 분산분석(Repeated Measures ANOVA)
    * 군내(Within) 요인 분석

#### 핸드폰 자판 입력 -- 요인 실험 설계 

* 요인 실험 설계 (Factorial Design of Experiment)
    * 한가지 이상 요인에 대한 실험 설계

#### 일반화 선형 (혼합) 모형

* 일반화 선형 모형(Generalized Linear Models, GLM)
* 일반화 선형 혼합 모형(Generalized Linear Mixed Models, GLMM)


