# 데이터 과학 -- 기초 통계



## 1. 대한민국 최고 수위 타자 [^beta-distribution-with-baseball]

[^beta-distribution-with-baseball]: [Understanding the beta distribution (using baseball statistics)](http://varianceexplained.org/statistics/beta_distribution_and_baseball/)

위키피디어 웹사이트에 대한민국 최고 타자에 대한 데이터가 올라가 있다. 

[KBO 리그 타율 관련 기록](https://ko.wikipedia.org/wiki/KBO_%EB%A6%AC%EA%B7%B8_%ED%83%80%EC%9C%A8_%EA%B4%80%EB%A0%A8_%EA%B8%B0%EB%A1%9D)을 살펴보면 
마지막 4할타자인 백인천 전감독의 기록이 눈에 띈다. 1990년에는 소수점 6자리로 수위타자가 갈린 기록도 독특히다.

이러한 데이터를 바탕으로 올해 4할 타자가 나올 것인지 가능성을 점검해 보자.



~~~{.r}
# 0. 환경설정 -------------------------------------------
list_of_packages <- c("ggplot2", "tidyverse", "ggthemes", "pwr", "extrafont", "shiny", "broom", 
                      "tibble", "rvest", "stringr", "extrafont")
new_packages <- list_of_packages[!(list_of_packages %in% installed.packages()[,"Package"])]
if(length(new_packages)) install.packages(new_packages)

sapply(list_of_packages, require, character.only = TRUE)
~~~



~~~{.output}
  ggplot2 tidyverse  ggthemes       pwr extrafont     shiny     broom 
     TRUE      TRUE      TRUE      TRUE      TRUE      TRUE      TRUE 
   tibble     rvest   stringr extrafont 
     TRUE      TRUE      TRUE      TRUE 

~~~



~~~{.r}
# 1. 대한민국 최고 타자 데이터 ------------------------------------
# devtools::install_github('stefano-meschiari/latex2exp')
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

> ### rvest R 한글 클로링 오류 workaround {.callout}
>
> `Error in utils::type.convert(out[, i], as.is = TRUE, dec = dec) : 
  invalid multibyte string at '<ed><8c><a8>'` 이런 오류가 rvest를 사용하여 
> 한글 웹 페이지를 긁어올 때 흔히 발생된다. R의 고질적인 문제지만 해법은 
> 누군가 정규직으로 3--4달 가량 이러한 인코딩 문제를 해결하면 되지만 아직 아무도 
> 나서고 있지는 않다. 
> 
> 어째든, 웹페이지를 긁어올 때는 `Sys.setlocale("LC_ALL", "English")` 영어로,
> 긁어와서 분석 시작할 때는 `Sys.setlocale("LC_ALL", "Korean")` 한국어로 분석하면 된다.

### 1.1. 최고 수위타자 표

데이터 분석을 하면서 늘 느끼는 것이지만, 정적 표 데이터를 다이나믹 `DT` 팩키지를 활용하여 표현하면 
단순 데이터에서는 볼 수 없는 새로운 사실을 많이 볼 수 있다. 이런 점이 R의 크나 큰 매력인데 명령어 한줄로 해결이 된다.



~~~{.r}
DT::datatable(hitter_tbl)
~~~

<!--html_preserve--><div id="htmlwidget-a11a558d769922b20e59" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-a11a558d769922b20e59">{"x":{"filter":"none","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","35"],[1982,1983,1984,1985,1986,1987,1988,1989,1990,1991,1992,1993,1994,1995,1996,1997,1998,1999,2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016],["백인천","장효조","이만수","장효조","장효조","장효조","김상훈","고원부","한대화","이정훈","이정훈","양준혁","이종범","김광림","양준혁","김기태","양준혁","마해영","박종호","양준혁","장성호","김동주","브룸바","이병규","이대호","이현곤","김현수","박용택","이대호","이대호","김태균","이병규","서건창","테임즈","최형우"],["MBC 청룡","삼성 라이온즈","삼성 라이온즈","삼성 라이온즈","삼성 라이온즈","삼성 라이온즈","MBC 청룡","빙그레 이글스","해태 타이거즈","빙그레 이글스","빙그레 이글스","삼성 라이온즈","해태 타이거즈","쌍방울 레이더스","삼성 라이온즈","쌍방울 레이더스","삼성 라이온즈","롯데 자이언츠","현대 유니콘스","LG 트윈스","KIA 타이거즈","두산 베어스","현대 유니콘스","LG 트윈스","롯데 자이언츠","KIA 타이거즈","두산 베어스","LG 트윈스","롯데 자이언츠","롯데 자이언츠","한화 이글스","LG 트윈스","넥센 히어로즈","NC 다이노스","삼성 라이온즈"],[0.412,0.369,0.34,0.373,0.329,0.387,0.354,0.327,0.334928,0.348,0.36,0.341,0.393,0.337,0.346,0.344,0.342,0.372,0.34,0.355,0.343,0.342,0.343,0.337,0.336,0.337,0.357,0.372,0.364,0.357,0.363,0.348,0.37,0.381,0.376],["역대 유일의 4할 타율이자 역대 1위의 타율","당시 안타 1위","최초의 타자 트리플 크라운 달성","","최초 2년 연속 타격왕","","","역대 최저 타율 타격왕","타율 2위 이강돈(빙그레 이글스)의 기록은 .334862으로 0.000066 차이","","","최초의 신인 선수 타격왕","역대 2위","","","","","","양손 타자 (스위치 히터) 최초의 타격왕","","","","최초의 외국인 선수 타격왕","최다안타1위","역대 2번째 타자 트리플 크라운, 장타율 1위 달성","","역대 최연소(만 20세) 타격왕","2000년대 최고 타율 타격왕","역대 3번째 타자 트리플 크라운, 타격 7관왕 달성","1992년 이정훈 이후 19년만의 2년 연속 타격왕","","최고령 타격왕","한국 프로야구 최초 200안타 달성","한국 프로야구 최초 40-40 달성",""]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>연도<\/th>\n      <th>이름<\/th>\n      <th>소속<\/th>\n      <th>타율<\/th>\n      <th>비고<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"crosstalkOptions":{"key":null,"group":null},"columnDefs":[{"className":"dt-right","targets":[1,4]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false},"selection":{"mode":"multiple","selected":null,"target":"row"}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->


~~~{.r}
# 2. 최고 타자 타율 시각화 ------------------------------------

hitter_tbl %>% 
  # mutate(IMF전후 = ifelse(연도 >= 1998, "IMF후", "IMF전")) %>% 
  mutate(시대 = cut(연도, breaks = c(0, 1990, 2000, 2010, 2020), labels=c("80년", "90년", "00년", "10년"))) %>% 
  ggplot(data = ., aes(x=타율, color=시대)) +
    geom_histogram(aes(y=..density..), binwidth=0.005, colour="gray", fill="darkgray") +
    geom_density(alpha=.1, aes(fill=시대)) +
    scale_x_continuous(limits=c(0.3, 0.45)) +
    theme_bw(base_family="NanumGothic") +
    theme(legend.position = "top") +
    scale_color_brewer(palette="Dark2") +
    labs(x="타율", y="밀도 (Density)") +
    geom_vline(aes(xintercept=mean(타율)),
             color="blue", linetype="dashed", size=1)
~~~

<img src="fig/best-hitter-all-seasons-ggplot-1.png" style="display: block; margin: auto;" />

80년대는 최초이자 마지막 4할 타자 백인천 전감독도 있고 해서 최고타자 타율의 퍼짐의 분포가 상당하다. 하지만,
90년대, 2000년대 들어서며 안정화에 들어섰고, 특히 2010년대를 보면 90년대와 2000년대와 비교해서 볼 때 상당히 높아진 것이 확인된다.


## 2. 대한민국 최고 수위 타자 사전분포

올해 최고타자 타율이 얼마나 될 것인지 베이지안 방법론을 통해서 추정을 할 경우, 먼저 사전분포를 정해야 한다.
타석에 들어서는 것은 시행횟수로 안타를 치는 것은 성공으로 가정하면 이항분포를 활용할 수 있다.
켤레 사전분포(conjugate prior)로 베타분포를 두고 생성함수 확률분포로 이항분포를 가정하면 사후분포도 베타분포가 되는데 
$\alpha$, $\beta$ 모수는 이를 반영하여야 된다.

계산의 편의성을 위해 베타분포는 2010년대 최고타율 분포를 맞출 수 있도록 $\alpha$, $\beta$를 데이터에서 추정한다. [^stackexchange-beta-parameters]
$\alpha$, $\beta$를 추정하는데 적률법(Method of Moments)을 비롯하여 다양한 방법이 존재한다.
적률법을 적용할 경우 `MASS::fitdistr` 함수를 활용하여 $\alpha$, $\beta$를 추정한다.


[^stackexchange-beta-parameters]: [Calculating the parameters of a Beta distribution using the mean and variance](https://stats.stackexchange.com/questions/12232/calculating-the-parameters-of-a-beta-distribution-using-the-mean-and-variance  )


~~~{.r}
## 3.1. 최고타자 사전분포 모수추정 ------------------------
hitter_avg_decade <- hitter_tbl %>% 
  mutate(시대 = cut(연도, breaks = c(0, 1990, 2000, 2010, 2020), labels=c("80년", "90년", "00년", "10년"))) %>% 
  dplyr::filter(시대 %in% c("10년"))

## 3.1. 적률법 ------------------------
hitter_mom <- MASS::fitdistr(hitter_avg_decade$"타율", dbeta,
                    start = list(shape1 = 560, shape2 = 970))
hitter_mom
~~~



~~~{.output}
    shape1     shape2 
  559.7318   970.2151 
 (323.0754) (560.1108)

~~~



~~~{.r}
## 3.2. 평균과 분산을 활용 ------------------------
# https://stats.stackexchange.com/questions/12232/calculating-the-parameters-of-a-beta-distribution-using-the-mean-and-variance  
estBetaParams <- function(mu, var) {
  alpha <- ((1 - mu) / var - 1 / mu) * mu ^ 2
  beta <- alpha * (1 / mu - 1)
  return(params = list(alpha = alpha, beta = beta))
}

## 3.3. 최고타자 사전분포 시각화 ------------------------

hitter_beta <- estBetaParams(0.3658333, 0.0001509667)

gg_x <- seq(0.3, 0.45, .001)
beta_dens <- dbeta(gg_x, hitter_beta$alpha, hitter_beta$beta) 

beta_df <- data.frame(gg_x, beta_dens) 

beta_df %>% 
  ggplot(data = ., aes(x=gg_x, y=beta_dens)) +
    geom_line() +
    scale_x_continuous(limits=c(0.3, 0.45)) +
    theme_bw(base_family="NanumGothic") +
    theme(legend.position = "top") +
    scale_color_brewer(palette="Dark2") +
    labs(x="타율", y="밀도 (Density)") +
    geom_vline(aes(xintercept=hitter_beta$alpha/(hitter_beta$alpha+hitter_beta$beta)),
               color="blue", linetype="dashed", size=1)
~~~

<img src="fig/best-hitter-all-prior-1.png" style="display: block; margin: auto;" />

2010년대 최고타율에 맞춰 평균도 맞췄고, 퍼짐의 정도도 반영한 것이 시각적으로 확인된다. 4할을 넘길 확률은 아주 작은 것으로 보이고,
마찬가지로 3할대 초반 타율로 최고타자로 등극하기도 극히 확률적으로 희박하다는 전문가의 의견도 충분히 반영가능하다.


## 3. 2017년 현재 타격 순위 

다음 스포츠, 야구, KBO리그 타자순위 6월 27일 기준 타격 순위 데이터를 [웹사이트](http://score.sports.media.daum.net/record/baseball/kbo/brnk.daum)에서 
긁어온다. 


~~~{.r}
## 3.4. 최고타자 타율 데이터 가져오기 ------------------------
baseball_url <- "http://score.sports.media.daum.net/record/baseball/kbo/brnk.daum"
baseball_html <- xml2::read_html(baseball_url)

## import KBO League Team Ranking
Sys.setlocale("LC_ALL", "English")
~~~



~~~{.output}
[1] "LC_COLLATE=English_United States.1252;LC_CTYPE=English_United States.1252;LC_MONETARY=English_United States.1252;LC_NUMERIC=C;LC_TIME=English_United States.1252"

~~~



~~~{.r}
hitter_table <- rvest::html_nodes(x=baseball_html, xpath='//*[@id="table1"]')
baseball_hitter <- rvest::html_table(hitter_table)[[1]]
Sys.setlocale("LC_ALL", "Korean")
~~~



~~~{.output}
[1] "LC_COLLATE=Korean_Korea.949;LC_CTYPE=Korean_Korea.949;LC_MONETARY=Korean_Korea.949;LC_NUMERIC=C;LC_TIME=Korean_Korea.949"

~~~



~~~{.r}
DT::datatable(baseball_hitter)
~~~

<!--html_preserve--><div id="htmlwidget-0e82775b188c95477186" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-0e82775b188c95477186">{"x":{"filter":"none","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","35","36","37","38","39","40","41","42","43","44","45","46","47"],[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,17,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47],["김선빈 (KIA)","나성범 (NC)","서건창 (넥센)","김태균 (한화)","이대호 (롯데)","김재환 (두산)","이명기 (KIA)","최형우 (KIA)","박용택 (LG)","손아섭 (롯데)","안치홍 (KIA)","이정후 (넥센)","최주환 (두산)","양의지 (두산)","박건우 (두산)","송광민 (한화)","민병헌 (두산)","구자욱 (삼성)","모창민 (NC)","정근우 (한화)","윤석민 (넥센)","강민호 (롯데)","로사리오 (한화)","한동민 (SK)","최정 (SK)","김민성 (넥센)","나지완 (KIA)","하주석 (한화)","버나디나 (KIA)","러프 (삼성)","최준석 (롯데)","양석환 (LG)","장민석 (한화)","스크럭스 (NC)","김헌곤 (삼성)","에반스 (두산)","유한준 (kt)","박해민 (삼성)","권희동 (NC)","김태군 (NC)","오지환 (LG)","김하성 (넥센)","이대형 (kt)","박경수 (kt)","김동엽 (SK)","심우준 (kt)","이승엽 (삼성)"],[73,55,71,54,71,71,62,72,68,73,67,74,66,60,60,65,69,74,70,70,73,66,62,71,68,69,71,72,70,64,70,71,70,58,71,66,71,74,73,69,71,72,73,67,68,71,67],[277,239,315,236,296,324,275,316,289,336,282,295,246,230,257,265,304,330,279,294,299,256,278,284,286,286,289,316,313,274,270,262,284,256,265,289,274,323,283,240,274,297,289,275,253,229,268],[247,215,274,203,267,284,249,256,250,289,248,264,217,195,225,238,266,285,257,258,271,229,243,244,231,257,241,288,282,234,233,238,263,215,233,251,250,293,237,205,238,260,271,237,231,220,237],[93,80,101,73,94,97,85,87,84,97,83,87,71,63,72,76,84,90,81,81,85,71,75,75,71,78,73,87,85,70,69,69,75,61,66,71,70,82,66,57,66,72,75,65,63,59,60],[20,19,16,12,7,17,11,19,12,17,15,15,9,12,15,16,12,23,15,12,12,8,14,17,7,17,14,11,15,15,7,18,7,13,11,9,7,8,11,7,10,20,6,15,9,14,10],[0,1,2,0,0,2,3,2,1,3,2,3,3,0,2,0,0,7,3,0,1,1,1,1,0,1,2,7,4,0,0,2,0,0,2,0,0,6,1,0,4,1,3,1,1,1,3],[2,13,4,8,12,16,3,17,3,7,9,2,5,9,6,5,8,14,8,8,6,16,18,22,27,8,10,6,11,14,9,6,1,17,6,12,8,2,9,1,6,10,0,11,15,3,13],[42,49,46,49,46,47,39,56,40,36,46,25,38,44,32,42,39,54,51,32,41,42,55,53,62,51,46,32,50,58,45,47,19,49,36,39,42,21,44,18,31,52,21,44,46,20,43],[43,51,48,29,35,49,37,48,37,56,52,57,38,31,40,37,45,59,34,49,42,35,51,43,50,30,42,42,55,41,30,32,36,43,35,43,27,43,37,20,33,46,38,36,39,28,30],[3,9,9,0,0,2,2,0,3,11,4,5,3,1,10,2,1,4,3,4,0,0,5,1,1,0,0,5,16,2,0,3,7,1,7,0,0,22,1,0,8,6,17,0,2,13,1],[24,23,38,30,29,39,17,57,34,45,29,29,26,33,28,17,34,42,17,31,24,25,33,38,51,22,44,19,27,38,35,21,11,38,26,35,21,21,40,24,31,31,15,34,20,8,26],[27,49,25,30,39,60,30,35,46,43,31,33,27,24,37,47,45,61,47,34,38,52,31,48,51,49,52,52,59,61,46,51,46,73,26,50,37,38,53,25,71,35,44,60,34,50,41],[0.377,0.372,0.369,0.36,0.352,0.342,0.341,0.34,0.336,0.336,0.335,0.33,0.327,0.323,0.32,0.319,0.316,0.316,0.315,0.314,0.314,0.31,0.309,0.307,0.307,0.304,0.303,0.302,0.301,0.299,0.296,0.29,0.285,0.284,0.283,0.283,0.28,0.28,0.278,0.278,0.277,0.277,0.277,0.274,0.273,0.268,0.253],[0.425,0.431,0.441,0.436,0.416,0.42,0.381,0.456,0.408,0.424,0.401,0.395,0.394,0.419,0.392,0.356,0.388,0.4,0.354,0.386,0.365,0.375,0.388,0.398,0.427,0.35,0.405,0.345,0.358,0.394,0.385,0.345,0.313,0.387,0.352,0.367,0.335,0.324,0.377,0.352,0.355,0.349,0.314,0.363,0.328,0.294,0.321],[0.482,0.651,0.485,0.537,0.513,0.585,0.446,0.629,0.428,0.488,0.52,0.432,0.465,0.523,0.484,0.45,0.451,0.593,0.49,0.453,0.432,0.563,0.597,0.656,0.688,0.471,0.502,0.451,0.5,0.543,0.442,0.458,0.323,0.581,0.425,0.462,0.404,0.369,0.447,0.327,0.429,0.477,0.321,0.485,0.515,0.382,0.485],[0.907,1.082,0.927,0.973,0.929,1.004,0.826,1.085,0.836,0.912,0.922,0.826,0.86,0.942,0.877,0.806,0.839,0.993,0.844,0.84,0.796,0.938,0.985,1.054,1.115,0.82,0.907,0.797,0.858,0.937,0.827,0.803,0.636,0.968,0.777,0.829,0.739,0.692,0.824,0.679,0.784,0.826,0.635,0.848,0.843,0.676,0.806]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>순위<\/th>\n      <th>선수 (팀)<\/th>\n      <th>경기<\/th>\n      <th>타석<\/th>\n      <th>타수<\/th>\n      <th>안타<\/th>\n      <th>2타<\/th>\n      <th>3타<\/th>\n      <th>홈런<\/th>\n      <th>타점<\/th>\n      <th>득점<\/th>\n      <th>도루<\/th>\n      <th>사사구<\/th>\n      <th>삼진<\/th>\n      <th>타율<\/th>\n      <th>출루율<\/th>\n      <th>장타율<\/th>\n      <th>OPS<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"crosstalkOptions":{"key":null,"group":null},"columnDefs":[{"className":"dt-right","targets":[1,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false},"selection":{"mode":"multiple","selected":null,"target":"row"}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

서건창 선수와 김선빈 선수가 3할7푼으로 공동선두를 달리고 있다. 차이가 있다면 타수에서 차이가 난다.

## 4. '17년 타율 예측

현재 타율을 기존 2010년대 사전분포를 반영하면 다소 놀라울 수 있는데 이대호가 나성범을 능가하는 것이 
시각적으로 확인된다. 이런 점은 베이지안 분석을 통해서 발견할 수 있는데 이유는 나성범 선수의 타석이 
상대적으로 이대호 선수보다 적어 이를 감안하면 나름 설득력이 있다. 수식으로 표현하면 수식으로 이해하시는 분께 더 편할 수도 있다. 범타는 타석수 - 안타수로 계산한다.

$$Beta (\alpha_0 + \text{안타}, \beta_0 + \text{범타} )$$


~~~{.r}
## 3.5. 최고타자 타율 시각화 ------------------------

gg_x <- seq(0.3, 0.45, .001)
seo_dens  <- dbeta(gg_x, hitter_beta$alpha+100, hitter_beta$beta+170)
kim_dens  <- dbeta(gg_x, hitter_beta$alpha+90, hitter_beta$beta+153)
na_dens  <- dbeta(gg_x, hitter_beta$alpha+77, hitter_beta$beta+134)
lee_dens  <- dbeta(gg_x, hitter_beta$alpha+93, hitter_beta$beta+169)

best_hitter_df <- data.frame(gg_x, seo_dens, kim_dens, na_dens, lee_dens) 

best_hitter_df %>% 
  gather(player, hit_avg, -gg_x) %>% 
  ggplot(data = ., aes(x=gg_x, y=hit_avg, group=player)) +
  geom_line(aes(color=player), size=1.3) +
  scale_x_continuous(limits=c(0.3, 0.45)) +
  theme_bw(base_family="NanumGothic") +
  theme(legend.position = "top") +
  scale_color_brewer(palette="Dark2") +
  labs(x="타율", y="밀도 (Density)") +
  scale_color_manual(name="수위 타자명: ", 
                     values=c("#931a1a", "#ff0000", "#195292", "#ff7f00"),
                     labels=c("서건창", "김선빈", "나성범", "이대호"))
~~~

<img src="fig/best-hitter-2017-bayesian-1.png" style="display: block; margin: auto;" />

올해 확률 4할을 넘길 확률은 어떻게 될까? 이 문제 답을 하기 위해서 앞선 사후분포확률에서 4할 이상 칠 확률은 오히려 김선빈 서건창선수보다 약간 앞선다.


~~~{.r}
## 3.6. 4할을 넘을 확률 ------------------------
seo_400  <- 1-pbeta(0.4, hitter_beta$alpha+100, hitter_beta$beta+170)
kim_400  <- 1-pbeta(0.4, hitter_beta$alpha+90, hitter_beta$beta+153)
na_400   <- 1-pbeta(0.4, hitter_beta$alpha+77, hitter_beta$beta+134)
lee_400  <- 1-pbeta(0.4, hitter_beta$alpha+93, hitter_beta$beta+169)

big_four_400 <- data.frame("서건창" = seo_400, "김선빈"=kim_400, "나성범"=na_400, "이대호"=lee_400)
 
big_four_400 %>% gather(선수, 타율4할) %>% 
  dplyr::mutate(타율4할 = scales::percent(타율4할))
~~~



~~~{.output}
    선수 타율4할
1 서건창 0.1730%
2 김선빈 0.1826%
3 나성범 0.1623%
4 이대호 0.0919%

~~~
