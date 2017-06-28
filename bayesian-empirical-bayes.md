# 데이터 과학 -- 기초 통계



## 1. 타격천재 장효조, 양준혁 그리고 1타수 1안타 선수  

2017년 KBO 한국프로야구 시즌을 보면 
[삼성 최경철](http://www.kbreport.com/leader/main?teamId=&defense_no=&year_from=2017&year_to=2017&split01=&split02_1=&split02_2=&tpa_count=)선수가 
1타수 1안타를 쳐서 타율이 최고 타율을 기록하고 있다.
하지만, 1타수 무안타 타율이 0인 선수도 다수 있다. 이런 경우 타율을 어떻게 의미있게 보정할 수 있을까?

<img src="fig/eb-kbo-hitter.png" alt="경험적 베이즈 적용을 통한 타율계산" width="77%" />

삼성 최경철 선수를 비롯한 상당수 선수는 타석에 올라가 횟수도 매우 적고, 이에 따라 타율도 5할을 넘기는 경우도 심심치 않게 볼 수 있다.
과연 이런 선수가 실제 충분한 타석기회가 주어지면 타율은 얼마나 될까? 이런 문제에 빈도주의 통계를 적용하기가 참 애매하다.

경험적 베이즈(Empirical Bayes) 방법론을 데이터분석 도수상자에 추가하면 흔히 접하게 되는 이러한 문제에 답을 구할 수 있다.

대한민국 야구 데이터가 원데이터 형태가 아닌 웹으로 조회를 통해 볼 수 있게 공개된 경우가 대부분이다. 그래서 경험적 베이즈 방법론을 적용하기 위해,
정확히는 사전분포(Prior)를 구하기 위해 미국 메이져리그 `Lehman` 데이터를 활용한다.

## 2. 경험적 베이즈를 반영한 타율  [^variance-explained-eb]

[^variance-explained-eb]: [Understanding empirical Bayes estimation (using baseball statistics)](http://varianceexplained.org/r/empirical_bayes_baseball/)

경험적 베이즈를 적용하기 위해서 데이터를 적극 활용하는데, 82년부터 타석에 들어선 선수의 안타 데이터가 없어 
사전분포는 미국 메이저리그 야구 기록을 활용하고, 예측하고자 하는 타율 데이터는 현재시점 KBO 프로야구 데이터를 활용한다.

### 2.1. 기본 수학 지식

사전분포가 베타분포이며 우도함수가 이항분포인 경우, 사후분포도 베타분포가 된다. 
즉, 사전분포는 미국 메이저리그 야구 타율분포이며, 우도함수는 2017년 KBO 한국 프로야구 시즌 타석에 들어서서 안타를 친 분포가 된다.
그리고, 이것을 수학적으로 나타내면 다음과 같다.

$$\text{사전분포:} \theta | \alpha_0 , \beta_0 ~ \text{베타분포}(\alpha_0 , \beta_0)$$
$$\text{우도함수:}k|n, \theta ~ \text{이항분포}(\theta , n)$$
$$\text{사후함수:}\theta|k, n, \alpha_0 , \beta_0 ~ \text{베타분포}(\alpha_0 + k, \beta_0 +n-k)$$

따라서, 미국 메이져리그 야구 데이터에서 $\alpha_0$, $\beta_0$를 추정하고, 한국 프로야구 데이터에서 타석에 들어선 안타 데이터가 있다면 
타석이 매우 적은 선수를 포함한 모든 선수에 대한 타율을 설득력있게 추정할 수 있게 된다.


### 2.1. 타율 사전 분포

미국 메이저리그 야구에 100년이 넘는 타자의 야구 기록이 `Lahman` 팩키지에 담겨있어, 
팩키지에 담겨진 타자 데이터를 가져와서 성과 이름을 붙여 전처리한다. 
그런 다음 500 타석을 넘긴 선수에 대한 타율분포를 히스토그램으로 나타내고,
`stat_function` 내부에 베타함수를 넣어 앞서 추정한 $\alpha_0$, $\beta_0$ 값을 넣어 베타함수에 적합시킨다.
보는 사람 관점에서 차이가 날 수도 있지만, 후속 단계를 진행하는 것도 큰 무리가 없어 보인다.


~~~{.r}
# 0. 환경설정 -------------------------------------------
# list_of_packages <- c("Lahman", "ggplot2", "tidyverse", "ggthemes", "extrafont",
#                       "tibble", "rvest", "stringr", "extrafont")
# new_packages <- list_of_packages[!(list_of_packages %in% installed.packages()[,"Package"])]
# if(length(new_packages)) install.packages(new_packages)
# 
# sapply(list_of_packages, require, character.only = TRUE)

# 1. MLB 베타분포 ------------------------------------

## 투수 제외하고 타율 계산  
career <- Batting %>%
  dplyr::filter(AB > 0) %>%
  anti_join(Pitching, by = "playerID") %>%
  group_by(playerID) %>%
  dplyr::summarize(H = sum(H), AB = sum(AB)) %>%
  mutate(average = H / AB)

# 성과 이름을 붙여 가독성 높임.
career <- Master %>%
  tbl_df() %>%
  dplyr::select(playerID, nameFirst, nameLast) %>%
  unite(name, nameFirst, nameLast, sep = " ") %>%
  inner_join(career, by = "playerID") %>%
  dplyr::select(-playerID)

# 타석이 500석 이상 
career_filtered <- career %>%
  filter(AB >= 500)

m <- MASS::fitdistr(career_filtered$average, dbeta,
                    start = list(shape1 = 1, shape2 = 10))

alpha0 <- m$estimate[1]
beta0 <- m$estimate[2]

career_filtered %>%
  dplyr::filter(AB > 500) %>%
  ggplot() +
  geom_histogram(aes(average, y = ..density..), binwidth = .005, color="darkgray", fill="gray") +
  stat_function(fun = function(x) dbeta(x, alpha0, beta0), color = "red",
                size = 1) +
  theme_bw(base_family="NanumGothic") +
  labs(x="타율", y="밀도(Density")
~~~

<img src="fig/eb-prior-mlb-1.png" style="display: block; margin: auto;" />

### 2.2. KBO 타자 타율

데이터를 통해서 우도함수를 구할 수 있는데 이런 경우 KBO 타자 타율 데이터가 필요하다.
다음이나 네이버를 통해 데이터를 긁어온다. 그리고 나서, [KBReport](http://www.kbreport.com/) 사이트에서 
일부 타석이 매우 적은 선수 일부를 데이터에 포함시킨다.


~~~{.r}
# 2. 2017년 KBO리그 ------------------------------------

## 2.1. KBO 통계(6/28일) -------------------------------
kbo_url <- "http://score.sports.media.daum.net/record/baseball/kbo/brnk.daum"
kbo_html <- xml2::read_html(kbo_url)

Sys.setlocale("LC_ALL", "English")
~~~



~~~{.output}
[1] "LC_COLLATE=English_United States.1252;LC_CTYPE=English_United States.1252;LC_MONETARY=English_United States.1252;LC_NUMERIC=C;LC_TIME=English_United States.1252"

~~~



~~~{.r}
kbo_hitter_tbl <- rvest::html_nodes(x=kbo_html, xpath='//*[@id="table1"]')
kbo_hitter <- rvest::html_table(kbo_hitter_tbl)[[1]]
Sys.setlocale("LC_ALL", "Korean")
~~~



~~~{.output}
[1] "LC_COLLATE=Korean_Korea.949;LC_CTYPE=Korean_Korea.949;LC_MONETARY=Korean_Korea.949;LC_NUMERIC=C;LC_TIME=Korean_Korea.949"

~~~



~~~{.r}
# DT::datatable(kbo_hitter)
~~~

### 2.3. KBO 타자 타율 - 경험적 베이즈 추정

베이즈 타율을 계산하는 것은 다음과 같다. 앞서 적률법(Method of Moments)을 통해 $\alpha_0$, $\beta_0$를 
추정했기 때문에 경험적 베이즈를 통해 베이즈 타율을 계산하는 것은 한결 쉽다.
따라서, 강지광, 노경은, 최경철, 이성곤 등 타수가 매우 적은 선수에 대한 타율도 함께 계산할 수 있다.

- `mutate(베이즈_타율 = (안타 + alpha0) / (타수 + alpha0 + beta0))`


~~~{.r}
# 3. KBO 경험적 베이즈(Empirical Bayes) (6/28일) -------------------------------
kbo_hitter_eb <- kbo_hitter %>% 
  dplyr::select(`선수 (팀)`, 타수, 안타, 타율) %>% 
  add_row(`선수 (팀)` = "강지광 (넥센)", 타수 = 2, 안타 = 0, 타율 =0) %>% 
  add_row(`선수 (팀)` = "노경은 (롯데)", 타수 = 2, 안타 = 0, 타율 =0) %>% 
  add_row(`선수 (팀)` = "최경철 (삼성)", 타수 = 1, 안타 = 1, 타율 =1) %>%
  add_row(`선수 (팀)` = "이성곤 (두산)", 타수 = 3, 안타 = 1, 타율 =0.333) %>% 
  mutate(베이즈_타율 = round((안타 + alpha0) / (타수 + alpha0 + beta0), 3))

kbo_hitter_eb  %>% 
  dplyr::arrange(타수) %>% DT::datatable()
~~~

<!--html_preserve--><div id="htmlwidget-41a00e7be1f1a15c49e7" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-41a00e7be1f1a15c49e7">{"x":{"filter":"none","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","35","36","37","38","39","40","41","42","43","44","45","46","47","48","49","50","51"],["최경철 (삼성)","강지광 (넥센)","노경은 (롯데)","이성곤 (두산)","양의지 (두산)","김태균 (한화)","김태군 (NC)","나성범 (NC)","스크럭스 (NC)","최주환 (두산)","심우준 (kt)","박건우 (두산)","강민호 (롯데)","최정 (SK)","김동엽 (SK)","최준석 (롯데)","김헌곤 (삼성)","러프 (삼성)","권희동 (NC)","박경수 (kt)","이승엽 (삼성)","송광민 (한화)","양석환 (LG)","오지환 (LG)","나지완 (KIA)","로사리오 (한화)","한동민 (SK)","김선빈 (KIA)","안치홍 (KIA)","이명기 (KIA)","박용택 (LG)","유한준 (kt)","에반스 (두산)","최형우 (KIA)","모창민 (NC)","김민성 (넥센)","정근우 (한화)","김하성 (넥센)","장민석 (한화)","이정후 (넥센)","민병헌 (두산)","이대호 (롯데)","윤석민 (넥센)","이대형 (kt)","서건창 (넥센)","버나디나 (KIA)","김재환 (두산)","구자욱 (삼성)","하주석 (한화)","손아섭 (롯데)","박해민 (삼성)"],[1,2,2,3,195,203,205,215,215,217,220,225,229,231,231,233,233,234,237,237,237,238,238,238,241,243,244,247,248,249,250,250,251,256,257,257,258,260,263,264,266,267,271,271,274,282,284,285,288,289,293],[1,0,0,1,63,73,57,80,61,71,59,72,71,71,63,69,66,70,66,65,60,76,69,66,73,75,75,93,83,85,84,70,71,87,81,78,81,72,75,87,84,94,85,75,101,85,97,90,87,97,82],[1,0,0,0.333,0.323,0.36,0.278,0.372,0.284,0.327,0.268,0.32,0.31,0.307,0.273,0.296,0.283,0.299,0.278,0.274,0.253,0.319,0.29,0.277,0.303,0.309,0.307,0.377,0.335,0.341,0.336,0.28,0.283,0.34,0.315,0.304,0.314,0.277,0.285,0.33,0.316,0.352,0.314,0.277,0.369,0.301,0.342,0.316,0.302,0.336,0.28],[0.261,0.257,0.257,0.26,0.284,0.299,0.267,0.306,0.269,0.287,0.263,0.285,0.281,0.28,0.265,0.275,0.269,0.276,0.267,0.266,0.256,0.285,0.273,0.267,0.278,0.281,0.28,0.312,0.293,0.296,0.294,0.268,0.27,0.296,0.285,0.279,0.284,0.267,0.271,0.292,0.285,0.302,0.285,0.267,0.311,0.279,0.299,0.286,0.28,0.296,0.269]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>선수 (팀)<\/th>\n      <th>타수<\/th>\n      <th>안타<\/th>\n      <th>타율<\/th>\n      <th>베이즈_타율<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"crosstalkOptions":{"key":null,"group":null},"columnDefs":[{"className":"dt-right","targets":[2,3,4,5]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false},"selection":{"mode":"multiple","selected":null,"target":"row"}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

### 2.4. KBO 타자 타율 시각화 비교

경험적 베이즈를 적용하지 않으면 타수가 매우 적은 선수의 경우 타율이 0, 10할과 같이 양극단에 몰려있게 된다.
하지만, 경험적 베이즈를 적용하면 시각적으로도 비교가 가능한 형태로 나타낼 수 있는 장점이 있다.


~~~{.r}
prior_g <- kbo_hitter_eb %>% 
  dplyr::select(`선수 (팀)`, 타율) %>% 
  ggplot() +
    geom_histogram(aes(타율, y = ..density..), binwidth = .01, color="darkgray", fill="gray", alpha=0.3) +
    theme_bw(base_family="NanumGothic") +
    labs(x="타율", y="밀도(Density")

## 3.2. KBO 베이즈 타율 시각화 (6/28일) -------------------------------

posterior_g <- kbo_hitter_eb %>% 
  dplyr::select(`선수 (팀)`, 타율, 베이즈_타율) %>% 
  gather(구분, 타율,  -`선수 (팀)`) %>% 
  ggplot() +
    geom_histogram(aes(타율, y = ..density.., fill=구분), binwidth = .005, alpha=0.3) +
    theme_bw(base_family="NanumGothic") +
    stat_function(fun = function(x) dbeta(x, alpha0, beta0), color = "red", size = 1) +
    scale_x_continuous(limits=c(0.2, 0.40)) +
    labs(x="타율", y="밀도(Density") +
    theme(legend.position = "top")

gridExtra::grid.arrange(prior_g, posterior_g,  nrow=1)
~~~

<img src="fig/eb-posterior-kbo-viz-1.png" style="display: block; margin: auto;" />
