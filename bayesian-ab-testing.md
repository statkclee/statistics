# 데이터 과학 -- 기초 통계



## 1. 베이지안 A/B 검정 [^bayesian-ab-testing-baseball]

[^bayesian-ab-testing-baseball]: [Understanding Bayesian A/B testing (using baseball statistics)](http://varianceexplained.org/r/bayesian_ab_baseball/)

2011년 젊은 나이에 위암투병 후 세상을 떠난 [장효조](https://ko.wikipedia.org/wiki/%EC%9E%A5%ED%9A%A8%EC%A1%B0) 선수와 일본에서 돌아와 여전히 현역으로 
수위타자를 넘보고 있는 [김태균](https://ko.wikipedia.org/wiki/%EA%B9%80%ED%83%9C%EA%B7%A0_(1982%EB%85%84)) 선수 중 누가 더 우수한 선수인가? 
이런 질문은 야구에 관심이 있거나, 사자와 호랑이가 싸우면 누가 이기나? 
마징가와 건담이 싸우면 누가 이길까? 이런 질문이 될 수도 있지만, 야구 데이터를 가지고 
최고 타자를 검증해보자.
 
<img src="fig/kbo_ab_testing.png" alt="베이지안 검정 틀" width="77%" />

결국, 장효조 선수의 타율이 김태균 선수 타율보다 높은 확률이 얼마인가를 가지고, 특정 확률값
예를 들어 70% 이상 되면 역대 최고 타자로 장효조 선수를 인정하는 것으로 정리하는 것도 한 방법이다.

사전분포는 82년부터 [연도별 최고타율](https://ko.wikipedia.org/wiki/KBO_%EB%A6%AC%EA%B7%B8_%ED%83%80%EC%9C%A8_%EA%B4%80%EB%A0%A8_%EA%B8%B0%EB%A1%9D)을 
획득한 선수의 타율을 사전분포로 두고 베타분포의 모수 $\alpha , \beta$를 
추정하고, 이를 장효조, 김태균 선수의 타율과 결합하여 사후분포를 추정하여 누가 더 타율이 좋은지 평가한다.


## 2. 단계별 베이지안 검정

### 2.1. 데이터 가져오기

베이지안 데이터 분석에 필요한 팩키지를 가져와서 설치한다.
장효조, 김태균 선수 야구기록을 위키에서 긁어와서 데이터프레임으로 준비한다.


~~~{.r}
# 0. 환경설정 -------------------------------------------
# list_of_packages <- c("ggplot2", "tidyverse", "ggthemes", "pwr", "extrafont", "shiny", "broom", 
#                       "tibble", "rvest", "stringr", "extrafont")
# new_packages <- list_of_packages[!(list_of_packages %in% installed.packages()[,"Package"])]
# if(length(new_packages)) install.packages(new_packages)
# 
# sapply(list_of_packages, require, character.only = TRUE)

# 1. 대한민국 최고 타자 데이터 --------------------------
## 1.1. 타자 장효조 -------------------------------------
Sys.setlocale("LC_ALL", "English")
~~~



~~~{.output}
[1] "LC_COLLATE=English_United States.1252;LC_CTYPE=English_United States.1252;LC_MONETARY=English_United States.1252;LC_NUMERIC=C;LC_TIME=English_United States.1252"

~~~



~~~{.r}
chj_url <- "https://ko.wikipedia.org/wiki/%EC%9E%A5%ED%9A%A8%EC%A1%B0"
chj_xml <- read_html(chj_url)

chj_tbl <- html_nodes(chj_xml, xpath='//*[@id="mw-content-text"]/div/table[3]')
chj_tbl <- html_table(chj_tbl, fill=TRUE)[[1]]

Sys.setlocale("LC_ALL", "Korean")
~~~



~~~{.output}
[1] "LC_COLLATE=Korean_Korea.949;LC_CTYPE=Korean_Korea.949;LC_MONETARY=Korean_Korea.949;LC_NUMERIC=C;LC_TIME=Korean_Korea.949"

~~~



~~~{.r}
DT::datatable(chj_tbl)
~~~

<!--html_preserve--><div id="htmlwidget-caee46fff5210e1d4e07" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-caee46fff5210e1d4e07">{"x":{"filter":"none","data":[["1","2","3","4","5","6","7","8","9","10","11"],["1983","1984","1985","1986","1987","1988","1989","1990","1991","1992","KBO 통산 : 10년"],["삼성","삼성","삼성","삼성","삼성","삼성","롯데","롯데","롯데","롯데","KBO 통산 : 10년"],["27","28","29","30","31","32","33","34","35","36","KBO 통산 : 10년"],[92,89,107,95,88,96,94,96,122,82,961],[386,365,422,367,335,334,393,364,424,242,3632],[317,309,346,304,284,283,333,324,346,204,3050],[61,56,66,55,51,41,40,36,56,23,485],[117,100,129,100,110,89,101,89,120,54,1009],[19,19,24,20,16,12,8,13,17,9,157],[3,7,1,2,4,2,3,4,10,0,36],[18,7,11,6,2,4,1,1,4,0,54],[62,44,65,41,58,35,31,22,54,25,437],[22,8,17,16,7,5,12,8,11,3,109],[2,8,8,7,4,5,15,4,7,1,61],[58,49,57,59,41,49,56,34,70,33,506],[39,25,23,30,26,27,30,33,31,25,289],[0.369,0.324,0.373,0.329,0.387,0.314,0.303,0.275,0.347,0.265,0.331],[0.475,0.424,0.467,0.436,0.461,0.419,0.407,0.347,0.452,0.365,0.427],[0.618,0.498,0.543,0.467,0.493,0.413,0.354,0.349,0.488,0.309,0.459],[1.093,0.923,1.01,0.903,0.954,0.833,0.762,0.696,0.941,0.674,0.886],[196,154,188,142,140,117,118,113,169,63,1400],[1,2,6,6,1,2,2,5,4,2,31],[6,5,4,1,3,2,3,2,0,1,27],[0,0,8,0,1,0,0,4,2,1,16],[5,2,6,3,6,0,1,0,4,3,30],[6,8,6,7,3,0,0,0,6,1,37]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>연\n도<\/th>\n      <th>소\n속<\/th>\n      <th>나\n이<\/th>\n      <th>출\n장<\/th>\n      <th>타\n석<\/th>\n      <th>타\n수<\/th>\n      <th>득\n점<\/th>\n      <th>안\n타<\/th>\n      <th>2\n루\n타<\/th>\n      <th>3\n루\n타<\/th>\n      <th>홈\n런<\/th>\n      <th>타\n점<\/th>\n      <th>도\n루<\/th>\n      <th>도\n실<\/th>\n      <th>볼\n넷<\/th>\n      <th>삼\n진<\/th>\n      <th>타\n율<\/th>\n      <th>출\n루\n율<\/th>\n      <th>장\n타\n율<\/th>\n      <th>O\nP\nS<\/th>\n      <th>루\n타<\/th>\n      <th>병\n살\n타<\/th>\n      <th>몸\n맞<\/th>\n      <th>희\n타<\/th>\n      <th>희\n플<\/th>\n      <th>고\n4<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"crosstalkOptions":{"key":null,"group":null},"columnDefs":[{"className":"dt-right","targets":[4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false},"selection":{"mode":"multiple","selected":null,"target":"row"}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

~~~{.r}
## 1.2. 타자 김태균 ------------------------
Sys.setlocale("LC_ALL", "English")
~~~



~~~{.output}
[1] "LC_COLLATE=English_United States.1252;LC_CTYPE=English_United States.1252;LC_MONETARY=English_United States.1252;LC_NUMERIC=C;LC_TIME=English_United States.1252"

~~~



~~~{.r}
ktg_url <- "https://ko.wikipedia.org/wiki/%EA%B9%80%ED%83%9C%EA%B7%A0_(1982%EB%85%84)"
ktg_xml <- read_html(ktg_url)

ktg_tbl <- html_nodes(ktg_xml, xpath='//*[@id="mw-content-text"]/div/table[7]')
ktg_tbl <- html_table(ktg_tbl, fill=TRUE)[[1]]

Sys.setlocale("LC_ALL", "Korean")
~~~



~~~{.output}
[1] "LC_COLLATE=Korean_Korea.949;LC_CTYPE=Korean_Korea.949;LC_MONETARY=Korean_Korea.949;LC_NUMERIC=C;LC_TIME=Korean_Korea.949"

~~~



~~~{.r}
DT::datatable(ktg_tbl)
~~~

<!--html_preserve--><div id="htmlwidget-16d753cbfa97f04a39c1" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-16d753cbfa97f04a39c1">{"x":{"filter":"none","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18"],["2001","2002","2003","2004","2005","2006","2007","2008","2009","2010","2011","2012","2013","2014","2015","2016","KBO 통산 : 14년","NPB 통산 : 2년"],["한화","한화","한화","한화","한화","한화","한화","한화","한화","지바\n롯데","지바\n롯데","한화","한화","한화","한화","한화","KBO 통산 : 14년","NPB 통산 : 2년"],["19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","KBO 통산 : 14년","NPB 통산 : 2년"],[88,105,133,129,124,124,118,115,95,141,31,126,101,118,133,144,1653,172],[289,344,573,556,529,511,491,484,389,614,119,513,430,508,524,652,6793,733],[245,298,479,473,461,423,393,410,336,527,104,416,345,422,408,529,5638,631],[51,25,67,76,73,66,62,81,63,68,7,61,41,66,61,94,887,75],[82,76,153,153,146,123,114,133,111,141,26,151,110,154,129,193,1828,167],[13,11,24,26,33,27,13,27,15,22,5,24,24,30,28,39,334,27],[2,0,2,1,2,0,0,1,0,0,0,0,0,0,0,0,8,0],[20,7,31,23,23,13,21,31,19,21,1,16,10,18,21,23,276,22],[54,34,95,106,100,73,85,92,62,92,14,80,52,84,104,136,1157,106],[2,2,3,2,3,2,2,2,0,0,0,3,0,0,3,1,25,0],[0,1,2,1,1,0,1,0,0,0,0,1,3,2,1,0,13,0],[40,41,79,70,60,82,90,64,45,74,12,81,73,70,98,108,1001,86],[72,103,106,99,73,89,70,67,71,140,23,69,67,73,80,97,1136,163],[0.335,0.255,0.319,0.323,0.317,0.291,0.29,0.324,0.33,0.268,0.25,0.363,0.319,0.365,0.316,0.365,0.324,0.259],[0.436,0.347,0.424,0.412,0.401,0.405,0.42,0.417,0.417,0.357,0.336,0.474,0.444,0.463,0.457,0.475,0.431,0.346],[0.649,0.362,0.572,0.529,0.547,0.447,0.483,0.622,0.545,0.429,0.327,0.536,0.475,0.564,0.539,0.569,0.533,0.378],[1.085,0.709,0.996,0.94,0.947,0.852,0.903,1.039,0.961,0.786,0.663,1.01,0.92,1.027,0.996,1.044,0.963,0.724],[159,108,274,250,252,189,190,255,183,226,34,223,164,238,220,301,3006,260],[4,13,13,11,20,18,13,8,12,24,4,11,14,18,19,11,185,28],[4,2,9,6,6,2,2,5,6,4,2,11,8,11,12,9,93,6],[0,1,4,0,0,0,0,0,0,0,0,0,0,0,1,0,6,0],[0,2,2,7,2,4,6,5,2,9,1,5,4,5,5,6,55,10],[2,2,6,3,5,1,4,4,4,1,0,8,7,1,12,10,69,1]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>연\n도<\/th>\n      <th>소\n속<\/th>\n      <th>나\n이<\/th>\n      <th>출\n장<\/th>\n      <th>타\n석<\/th>\n      <th>타\n수<\/th>\n      <th>득\n점<\/th>\n      <th>안\n타<\/th>\n      <th>2\n루\n타<\/th>\n      <th>3\n루\n타<\/th>\n      <th>홈\n런<\/th>\n      <th>타\n점<\/th>\n      <th>도\n루<\/th>\n      <th>도\n실<\/th>\n      <th>볼\n넷<\/th>\n      <th>삼\n진<\/th>\n      <th>타\n율<\/th>\n      <th>출\n루\n율<\/th>\n      <th>장\n타\n율<\/th>\n      <th>O\nP\nS<\/th>\n      <th>루\n타<\/th>\n      <th>병\n살\n타<\/th>\n      <th>몸\n맞<\/th>\n      <th>희\n타<\/th>\n      <th>희\n플<\/th>\n      <th>고\n4<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"crosstalkOptions":{"key":null,"group":null},"columnDefs":[{"className":"dt-right","targets":[4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false},"selection":{"mode":"multiple","selected":null,"target":"row"}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

### 2.2. 두 야구선수 기록 정리

장효조, 김태균 선수 데이터를 가져와서 타율검증에 필요한 데이터만 
발라내서 `ab_df`라는 데이터프레임으로 준비한다.


~~~{.r}
## 1.3. 대한민국 최고 타자 데이터 비교 ------------------------------------

chj_df <- chj_tbl %>% mutate(이름 = "장효조") %>% 
  dplyr::select(이름, 타수=`타\n수`, 안타=`안\n타`, 타율=`타\n율`) %>% 
  dplyr::filter(타수 >=3000)

ktg_df <- ktg_tbl %>% mutate(이름 = "김태균") %>% 
  dplyr::select(이름, 타수=`타\n수`, 안타=`안\n타`, 타율=`타\n율`) %>% 
  dplyr::filter(타수 >=3000)

(ab_df <- bind_rows(chj_df, ktg_df))
~~~



~~~{.output}
    이름 타수 안타  타율
1 장효조 3050 1009 0.331
2 김태균 5638 1828 0.324

~~~

### 2.3. 사전 분포

베타분포를 사전분포로 두고, 모수 $\alpha , \beta$를 
82년부터 최고타율 데이터를 활용하여 경험적 베이즈 방법론을 적용하여 추정한다.
그리고 나서, `ggplot2`를 활용하여 시각화한다. 


~~~{.r}
# 2. KBO 최고타율 사전분포 ------------------------------------
## 2.1. 최고타율 데이터 가져오기 ------------------------
Sys.setlocale("LC_ALL", "English")
~~~



~~~{.output}
[1] "LC_COLLATE=English_United States.1252;LC_CTYPE=English_United States.1252;LC_MONETARY=English_United States.1252;LC_NUMERIC=C;LC_TIME=English_United States.1252"

~~~



~~~{.r}
hitter_url <- "https://ko.wikipedia.org/wiki/KBO_%EB%A6%AC%EA%B7%B8_%ED%83%80%EC%9C%A8_%EA%B4%80%EB%A0%A8_%EA%B8%B0%EB%A1%9D"
hitter_xml <- read_html(hitter_url)

hitter_tbl <- html_nodes(hitter_xml, xpath='//*[@id="mw-content-text"]/div/table[3]')
hitter_tbl <- html_table(hitter_tbl, fill=TRUE)[[1]]

Sys.setlocale("LC_ALL", "Korean")
~~~



~~~{.output}
[1] "LC_COLLATE=Korean_Korea.949;LC_CTYPE=Korean_Korea.949;LC_MONETARY=Korean_Korea.949;LC_NUMERIC=C;LC_TIME=Korean_Korea.949"

~~~



~~~{.r}
DT::datatable(hitter_tbl)
~~~

<!--html_preserve--><div id="htmlwidget-d925df50910d33b6705e" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-d925df50910d33b6705e">{"x":{"filter":"none","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","35"],[1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016],["백인천","장효조","이만수","장효조","장효조","장효조","김상훈","고원부","한대화","이정훈","이정훈","양준혁","이종범","김광림","양준혁","김기태","양준혁","마해영","박종호","양준혁","장성호","김동주","브룸바","이병규","이대호","이현곤","김현수","박용택","이대호","이대호","김태균","이병규","서건창","테임즈","최형우"],["MBC 청룡","삼성 라이온즈","삼성 라이온즈","삼성 라이온즈","삼성 라이온즈","삼성 라이온즈","MBC 청룡","빙그레 이글스","해태 타이거즈","빙그레 이글스","빙그레 이글스","삼성 라이온즈","해태 타이거즈","쌍방울 레이더스","삼성 라이온즈","쌍방울 레이더스","삼성 라이온즈","롯데 자이언츠","현대 유니콘스","LG 트윈스","KIA 타이거즈","두산 베어스","현대 유니콘스","LG 트윈스","롯데 자이언츠","KIA 타이거즈","두산 베어스","LG 트윈스","롯데 자이언츠","롯데 자이언츠","한화 이글스","LG 트윈스","넥센 히어로즈","NC 다이노스","삼성 라이온즈"],[0.412,0.369,0.34,0.373,0.329,0.387,0.354,0.327,0.334928,0.348,0.36,0.341,0.393,0.337,0.346,0.344,0.342,0.372,0.34,0.355,0.343,0.342,0.343,0.337,0.336,0.337,0.357,0.372,0.364,0.357,0.363,0.348,0.37,0.381,0.376],["역대 유일의 4할 타율이자 역대 1위의 타율","당시 안타 1위","최초의 타자 트리플 크라운 달성","","최초 2년 연속 타격왕","","","역대 최저 타율 타격왕","타율 2위 이강돈(빙그레 이글스)의 기록은 .334862으로 0.000066 차이","","","최초의 신인 선수 타격왕","역대 2위","","","","","","양손 타자 (스위치 히터) 최초의 타격왕","","","","최초의 외국인 선수 타격왕","최다안타1위","역대 2번째 타자 트리플 크라운, 장타율 1위 달성","","역대 최연소(만 20세) 타격왕","2000년대 최고 타율 타격왕","역대 3번째 타자 트리플 크라운, 타격 7관왕 달성","1992년 이정훈 이후 19년만의 2년 연속 타격왕","","최고령 타격왕","한국 프로야구 최초 200안타 달성","한국 프로야구 최초 40-40 달성",""]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>연도<\/th>\n      <th>이름<\/th>\n      <th>소속<\/th>\n      <th>타율<\/th>\n      <th>비고<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"crosstalkOptions":{"key":null,"group":null},"columnDefs":[{"className":"dt-right","targets":[1,4]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false},"selection":{"mode":"multiple","selected":null,"target":"row"}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

~~~{.r}
## 2.2. 사전분포 최고타자 베타분포 모수추정 ------------------------
alpha_beta <- MASS::fitdistr(hitter_tbl$"타율", dbeta,
                    start = list(shape1 = 560, shape2 = 970))

alpha0 <- alpha_beta$estimate["shape1"]
beta0 <- alpha_beta$estimate["shape2"]

## 2.3. 최고타자 사전분포 시각화 ------------------------
gg_x <- seq(0.3, 0.45, .001)
beta_dens <- dbeta(gg_x, alpha0, beta0) 

beta_df <- data.frame(gg_x, beta_dens) 

beta_df %>% 
  ggplot(data = ., aes(x=gg_x, y=beta_dens)) +
    geom_line() +
    scale_x_continuous(limits=c(0.3, 0.45)) +
    theme_bw(base_family="NanumGothic") +
    theme(legend.position = "top") +
    scale_color_brewer(palette="Dark2") +
    labs(x="타율", y="밀도 (Density)") +
    geom_vline(aes(xintercept=alpha0/(alpha0+beta0)),
               color="blue", linetype="dashed", size=1)
~~~

<img src="fig/ab-bayesian-two-players-prior-2.png" style="display: block; margin: auto;" />

### 2.4. 장효조, 김태균 타율 사후분포 

사후분포 타율을 추정하는 방식은 4가지를 제시하고 있다.

- 모의시험 시뮬레이션 (Simulation)
- 수치적분 (Numerical Integration)
- 닫힌 공식 해법 (Closed-form Solution)
- 닫힌 공식 정규분포 근사화 (Closed-form approximation)

4가지 접근방법 모두 장단점이 있다. 

#### 2.4.1. 모의시험 시뮬레이션 (Simulation)

모의시험은 수식이나 기타 다른 설명이 없이 
컴퓨터가 열심히 일을 해서 정답을 가져다 주는 것이니 그냥 가져다 쓰면된다.


~~~{.r}
# 3. 최고 타자는 누구인가? ------------------------------
ab_eb_df <- ab_df %>%
  mutate(eb_estimate = (안타 + alpha0) / (타수 + alpha0 + beta0)) %>%
  mutate(alpha1 = 안타 + alpha0,
         beta1 = 타수 - 안타 + beta0)

## 3.1. 모의시험 시뮬레이션 ------------------------------

chj_param <- ab_eb_df %>% dplyr::filter(이름 =="장효조") %>% 
  dplyr::select(alpha1, beta1)
chj_sim <- rbeta(1e6, chj_param$alpha1, chj_param$beta1)

ktg_param <- ab_eb_df %>% dplyr::filter(이름 =="김태균") %>% 
  dplyr::select(alpha1, beta1)
ktg_sim <- rbeta(1e6, ktg_param$alpha1, ktg_param$beta1)

(sim_res <- mean(chj_sim > ktg_sim))
~~~



~~~{.output}
[1] 0.782726

~~~

장효조, 김태균 타율 사후분포에서 1,000,000번 두 타자를 경쟁시켜 누가 
더 타율이 높은지 평균을 낸다. 장효조 선수가 78.3% 더 타율이 높게 나온다.

#### 2.4.2. 수치적분

두번째 방법은 수치적분을 사용한 방식으로 A/B 검정이 많이 사용되어 
[Agile A/B testing with Bayesian Statistics and Python](https://web.archive.org/web/20150419163005/http://www.bayesianwitch.com/blog/2014/bayesian_ab_test.html)에서
영감을 받아 David Robinson이 구한현 방식이다. 모수 $\alpha , \beta$가 큰 경우 시간이 다소 걸린다는 점을 주의한다.


~~~{.r}
## 3.2. 수치 적분 ------------------------------
x <- seq(.315, .355, .0002)

crossing(chj_x = x, ktg_x = x) %>%
  mutate(chj_density = dbeta(chj_x, chj_param$alpha1, chj_param$beta1),
         ktg_density = dbeta(ktg_x, ktg_param$alpha1, ktg_param$beta1),
         joint = chj_density * ktg_density) %>%
  ggplot(aes(chj_x, ktg_x, fill = joint)) +
  geom_tile() +
  geom_abline(color="darkgray") +
  theme_bw(base_family="NanumGothic") %>% 
  scale_fill_gradient2(low = "white", high = "red") +
  labs(x = "장효조 타율",
       y = "김태균 타율",
       fill = "결합 밀도") +
  theme(legend.position = "none")
~~~

<img src="fig/ab-bayesian-two-players-integration-1.png" style="display: block; margin: auto;" />

~~~{.r}
d <- .00002
limits <- seq(.315, .55, d)
sum(outer(limits, limits, function(x, y) {
  (x > y) *
    dbeta(x, chj_param$alpha1, chj_param$beta1) *
    dbeta(y, ktg_param$alpha1, ktg_param$beta1) *
    d ^ 2
}))
~~~



~~~{.output}
[1] 0.7632884

~~~

#### 2.4.3. 닫힌 공식 해법(Closed-form Solution)

베이지안 A/B 검정에 대한 닫힌 공식 해법이 존재한다.
이를 반영하여 누가 더 타율이 높은지 검정한다.

즉, 장효조 선수와 김태균 선수의 타율 정보가 있을 때, 
장효조 선수 타율이 김태균 선수 타율보다 높을 확률을 다음과 같이 정의할 수 있다.
즉, A가 B보다 나을 확률은 [Miller 해법](http://www.evanmiller.org/bayesian-ab-testing.html#binary_ab_derivation)에 따라 다음과 같이 계산할 수 있다.

$$p_A \sim \mbox{Beta}(\alpha_A, \beta_A)$$

$$p_B \sim \mbox{Beta}(\alpha_B, \beta_B)$$

$${\rm Pr}(p_B > p_A) = \sum_{i=0}^{\alpha_B-1}\frac{B(\alpha_A+i,\beta_A+\beta_B)}{(\beta_B+i) B(1+i, \beta_B) B(\alpha_A, \beta_A) }$$

여기서 $B$는 베타함수가 된다. 
A/B 검정에서 많이 사용되는 베이지안 사전분포는 베타분포고, 응답율을 이항분포를 가정할 경우, 사후분포는 베타분포가 되고 모수는 다음과 같다. 

$$ ~ Beta (\alpha + s, \beta + n - s)$$

- $\alpha:$ 베타분포 첫번째 모수
- $\beta:$ 베타분포 두번째 모수
- $n:$ 시행 횟수
- $s:$ 성공 횟수



~~~{.r}
## 3.3. 닫힌 공식 해법(Closed-form Solution) -----------------------------

h <- function(alpha_a, beta_a,
              alpha_b, beta_b) {
  j <- seq.int(0, round(alpha_b) - 1)
  log_vals <- (lbeta(alpha_a + j, beta_a + beta_b) - log(beta_b + j) -
                 lbeta(1 + j, beta_b) - lbeta(alpha_a, beta_a))
  1 - sum(exp(log_vals))
}

h(chj_param$alpha1, chj_param$beta1,
  ktg_param$alpha1, ktg_param$beta1)
~~~



~~~{.output}
[1] 0.7816925

~~~

#### 2.4.3. 닫힌 공식 해법(Closed-form Solution)

마지막 방법은 [John D. Cook(2012), Fast approximation of Beta inequalities
](http://www.johndcook.com/fast_beta_inequality.pdf) 방법을 활용한 것으로, 
모수 $\alpha , \beta$가 큰 경우 베타분포가 정규분포로 근사되어 차이가 크게 나지 않는 성질을 
활용하는 방법이다. 반대로 모수 $\alpha , \beta$가 작은 경우 적합이 잘 되지 않아 오차가 크게 날 수 있으니
주의한다.


~~~{.r}
## 3.4. 닫힌 공식 정규분포 근사화(Closed-form approximation) -----------------------------
h_approx <- function(alpha_a, beta_a, 
                     alpha_b, beta_b) {
  u1 <- alpha_a / (alpha_a + beta_a)
  u2 <- alpha_b / (alpha_b + beta_b)
  var1 <- alpha_a * beta_a / ((alpha_a + beta_a) ^ 2 * (alpha_a + beta_a + 1))
  var2 <- alpha_b * beta_b / ((alpha_b + beta_b) ^ 2 * (alpha_b + beta_b + 1))
  pnorm(0, u2 - u1, sqrt(var1 + var2))
}

h_approx(chj_param$alpha1, chj_param$beta1,
         ktg_param$alpha1, ktg_param$beta1)
~~~



~~~{.output}
[1] 0.7822859

~~~

4가지 분석결과 모두 장효조 선수가 김태균 선수와 동시대 경쟁을 할 경우 거의 80% 정도 
장효조 선수 타율이 김태균 선수 타율보다 높게 나올 것으로 추정된다.
