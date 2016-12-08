---
layout: page
title: 데이터 과학 -- 기초 통계
subtitle: 측정 척도(Scales of Measurement)
---

> ## 학습 목표 {.objectives}
>
> * 통계학에서 다루는 측정에 대해 살펴본다.
> * 측정 척도에 대한 이론적 배경을 이해한다.


## 측정 변수의 구분 [^stevens-measurement] [^winner-measurement]


<img src="fig/stat-stevens-measurement.png" alt="측정 분류" width="77%" />

명목형, 순서형, 구간형, 비율형 네가지로 측정 척도를 구분한다.
측정 척도에 따라 유의미한 통계량도 함께 정해진다. 자세한 사항은  [Stevens, Stanley Smith. "On the theory of scales of measurement." (1946).](http://psychology.okstate.edu/faculty/jgrice/psyc3214/Stevens_FourScales_1946.pdf) 논문을 참조한다.


[^stevens-measurement]: [Stevens, Stanley Smith. "On the theory of scales of measurement." (1946).](http://psychology.okstate.edu/faculty/jgrice/psyc3214/Stevens_FourScales_1946.pdf)

[^winner-measurement]: Wiener, Norbert. "A new theory of measurement: a study in the logic of mathematics." Proceedings of the London Mathematical Society 2.1 (1921): 181-205.

### 범주형, 순서형 자료형

``` {r data-type-ordinal}
# 범주형
animals_vector <- c("Elephant", "Giraffe", "Donkey", "Horse")
factor_animals_vector <- factor(animals_vector)
factor_animals_vector

# 순위형
temperature_vector <- c("High", "Low", "High","Low", "Medium")
factor_temperature_vector <- factor(temperature_vector, order = TRUE, levels = c("Low", "Medium", "High"))
factor_temperature_vector
```

``` {r data-type-factor-level}
# "M", "F" 수준
survey_vector <- c("M", "F", "F", "M", "M")
factor_survey_vector <- factor(survey_vector)
levels(factor_survey_vector)

# "Female", "Male" 로 변환
levels(factor_survey_vector) <- c("Female", "Male")
levels(factor_survey_vector)
```

``` {r data-type-factor-summary}
# 문자형 벡터와 요인 벡터
survey_vector <- c("M", "F", "F", "M", "M")
factor_survey_vector <- factor(survey_vector)

# 문자형 벡터 요약
summary(survey_vector)

# 요인 벡터 요약
summary(factor_survey_vector)
```


### 데이터프레임 생성

``` {r df-create}
# 벡터를 정의한다.
name <- c("Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune")
type <- c("Terrestrial planet", "Terrestrial planet", "Terrestrial planet", 
          "Terrestrial planet", "Gas giant", "Gas giant", "Gas giant", "Gas giant")
diameter <- c(0.382, 0.949, 1, 0.532, 11.209, 9.449, 4.007, 3.883)
rotation <- c(58.64, -243.02, 1, 1.03, 0.41, 0.43, -0.72, 0.67)
rings <- c(FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, TRUE, TRUE)

# 벡터를 합쳐서 데이터프레임을 생성
planets_df <-data.frame(name, type, diameter, rotation, rings)
```

