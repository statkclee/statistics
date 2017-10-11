---
layout: page
title: 데이터 과학 -- 기초 통계
---

> ### AI is a Superpower {.callout}
>
> "AI is a superpower!!!", 인공지능을 체득하면 슈퍼파워를 손에 쥘 것이다. [Andrew Ng](https://twitter.com/andrewyng/status/728986380638916609)
>
> 금수저, 은수저 슈퍼파워를 받은 사람과 기계학습을 통달한 흑수저들간의 무한경쟁이 드뎌 시작되었다. 물론, 
> 금수저를 입에 물고 기계학습을 통달한 사람이 가장 유리한 출발을 시작한 것도 사실이다.

통계는 과거 통계 팩키지(SAS, SPSS, 미니탭 등)에 데이터를 넣어 돌리고 결과를 해석하는 학문으로 쉽게 생각하기도 한다. 
수학적인 면에서 보면, 인문학도가 보면 어려운 수학이지만 수학을 전통으로 하는 분들이 보면 쉽게 보이는 것도 사실이다. 
하지만, 통계는 데이터에 기반해서 귀납적 사고방식을 확률로 표현하는 어찌보면 쟈넷윙 박사가 정의한 **컴퓨팅 사고(Computational Thinking)** 에 따르면 
 **수학적 사고(Mathematical Thinking)** 와 더불어 인간 사고체계의 큰 기둥을 이루고 있는 **실험적 사고(Experimental Thinking)** 체계다. 

2차 산업혁명 제조업의 토대가 수학을 기반으로 깔고 물리와 화학을 그 위에 올렸다면, 3차 산업혁명 금융/서비스업 정보화를 넘어,
4차 산업혁명의 기본적인 토양은 통계학과 컴퓨터공학이다. 수학 전공자가 수학적 사고를 통달하는만큼 실험적/통계적 사고를 통달하는 것은 쉬운 일이 아니다. 
하지만 잊지 말아야 할 것은 컴퓨팅 사고도 성큼 다가와 있고, 선진국에서도 일부 사람들만이 하는 이 사고 체계를 하루 빨리 내재화하는 것이 정말 
중요한 핵심 역량이다.


## 학습목차 

1. **확률 모형**
    - [이항분포](stat-binomial.html)
    - [확률 법칙(law of probability)](stat-law-of-probability.html)
    - [이항분포 근사 - 정규분포, 포아송 분포](stat-approx-normal-poisson.html)
    - [독일 V2로켓 영국 폭격 - 포아송 분포](stat-flying-bomb-poisson.html)
1. [실험 유형 구분](stat-coursera-doe.html)
    - [측정 척도(Scales of Measurement)](stat-measurement.html)
    - [A/B 검정(A/B Testing)](stat-ab-testing.html)
    - [실무에서 자주 활용되는 검정($t$-검정)](stat-testing.html)
    - [A/B 검정 -- A안 혹은 B안 선정](ab-testing-in-practice.html)
        - [기호 2번과 3번, 기호 4번과 5번 순위가 의미가 있나?](ab-testing-presidential-election.html)
    - [베이지안 A/B 검정](bayesian-ab-testing-in-practice.html)
        - [올해(2017년) 최고수위 타자는 4할을 넘길 수 있을까?](bayesian-best-hitter-2017.html)
        - [올해(2017년) 최고수위 타자 타율은?](bayesian-best-hitter-ci.html)
        - [1타수 1안타, 2타수 무안타... 타율은?](bayesian-empirical-bayes.html)
        - [장효조, 김태균 누가 더 타율이 높은가?](bayesian-ab-testing.html)
1. [통계 모형(Statistical models)](stat-modeling.html)
    - [통계모형 시각화](stat-model-visualization.html) 
    - [탐색적 시계열 데이터 분석](stat-time-series-eda.html)
    - [시계열 모형 식별 도구](stat-time-series-tools.html)
    - [자기회귀이동평균(ARMA) 모형](stat-time-series-arma.html)
    - [계절 자기회귀이동평균(SARIMA)](stat-time-series-sarima.html)
    - [시계열 데이터 예측(forecast)](stat-time-series-forecast.html)
    - [시계열 예측 자동화](stat-forecast-automation.html)
1. **금융공학(Financial Engineering)**
    - [금융공학을 위한 R 언어 기초](stat-fe-r.html)
    - [금융데이터 가져오기](stat-fe-import.html)
    - [시계열 데이터 다루기](stat-time-series-basics.html)    
    - [깔끔한 금융 시장 분석가(tidyquant)](stat-time-series-tidyquant.html)    
1. [채팅 로그](stat-chatting-log.html)
1. [디지털 경제와 작업장(Gold Farming)](stat-gold-farming.html)
    * [작업장 탐지](stat-gold-farming-detection.html)

### [xwMOOC 오픈 교재](https://statkclee.github.io/xwMOOC/)

- [컴퓨터 과학 언플러그드](http://unplugged.xwmooc.org)  
- [리보그](http://reeborg.xwmooc.org)  
     - [러플](http://rur-ple.xwmooc.org)  
- [파이썬 거북이](http://swcarpentry.github.io/python-novice-turtles/index-kr.html)  
- [정보과학을 위한 파이썬](http://python.xwmooc.org)  
- [소프트웨어 카펜트리 5.3](http://swcarpentry.xwmooc.org)
- [IoT 오픈 하드웨어(라즈베리 파이)](http://raspberry-pi.xwmooc.org/)
    - [$100 오픈 컴퓨터](http://computer.xwmooc.org/)   
    - [$100 오픈 슈퍼컴퓨터](http://computers.xwmooc.org/)
- **데이터 과학**
    - [R 데이터과학](http://data-science.xwmooc.org/)
    - [R 팩키지](http://r-pkgs.xwmooc.org/)
    - [R 도커](http://statkclee.github.io/r-docker/)
    - [통계적 사고](http://think-stat.xwmooc.org/)
    - [R 병렬 컴퓨팅](http://parallel.xwmooc.org/)    
    - [데이터 과학을 위한 저작도구](https://statkclee.github.io/ds-authoring/)
- **기계학습, 딥러닝, 인공지능**
    - [기계학습](http://statkclee.github.io/ml)
    - [고생대 프로젝트](http://statkclee.github.io/trilobite)
- [기호 수학(Symbolic Math)](http://sympy.xwmooc.org/)
- [선거와 투표](http://politics.xwmooc.org/)

