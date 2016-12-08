---
layout: page
title: 데이터 과학 -- 기초 통계
subtitle: 베이즈 정리
---

> ## 학습 목표 {.objectives}
>
> * 베이즈 정리에 대해서 이해한다.

### 1. 왜 베이즈 정리를 사용할까?

베이즈 정리를 사용하면 $P(A|B)$ 가 주어졌을 때 $P(B|A)$ 확률을 계산할 수 있기 때문에 유용하다.

$P(A,B) = P(B|A) \times P(A)\\
P(A,B) = P(A|B) \times P(B)$

따라서, 

$P(B|A) = \frac{P(A,B)}{P(A)} = \frac{P(A|B) \times P(B)}{PA}$