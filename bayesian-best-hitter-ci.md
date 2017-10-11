# 데이터 과학 -- 기초 통계



## 1. 베이지안 신뢰구간 [^variance-explained-credible-interval]

[^variance-explained-credible-interval]: [Understanding credible intervals (using baseball statistics)](http://varianceexplained.org/r/credible_intervals_baseball/)

빈도주의 통계학에서 **신뢰구간(Confidence Interval)**이라고 얘기하는 개념이 베이지안에서는 **신뢰구간(Credible Interval)**이다.
적절한 용어가 통계학회에서도 제시하고 있지 않아 (혹은 제시하고 있는데 홍보가 덜되서 모르거나... 어쨌든...) 용어는 신뢰구간을 사용하지만,
실무에서 사용하는 의미는 동일하다.


## 2. 올해(2017년) 최고 수위타자 타율은?

전반기를 지나고 있는 현재시점(2017년 6월) 최고타자 수위타자 타율은 어떻게 될까? 이를 빈도주의 통계 및
베이즈 통계를 활용하여 95% 신뢰수준으로 구해본다.


### 2.1. 환경설정 및 데이터 준비

[위키 KBO 수위타자](https://ko.wikipedia.org/wiki/KBO_%EB%A6%AC%EA%B7%B8_%ED%83%80%EC%9C%A8_%EA%B4%80%EB%A0%A8_%EA%B8%B0%EB%A1%9D) 순위를 
긁어온다. 80년대부터 최근까지 수위타자 타율의 변화를 시각화한다.


~~~{.r}
# 0. 환경설정 -------------------------------------------
# list_of_packages <- c("Lahman", "ggplot2", "tidyverse", "ggthemes", "extrafont",
#                       "tibble", "rvest", "stringr", "extrafont")
# new_packages <- list_of_packages[!(list_of_packages %in% installed.packages()[,"Package"])]
# if(length(new_packages)) install.packages(new_packages)
# 
# sapply(list_of_packages, require, character.only = TRUE)

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



~~~{.r}
# 2. 최고 타자 타율 시각화 ------------------------------------

hitter_tbl %>% 
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

<img src="fig/bayesian-ci-import-1.png" style="display: block; margin: auto;" />

### 2.2. 최고 수위타자 사전분포

최고 수위타자에 대한 사전분포를 추정한다. 타석에 들어서서 안타를 치는 것은 이항분포가 되니,
이에 적합한 사전분포는 베타분포로 필요한 것이지만, 베타분포에 대한 $\alpha, \beta$ 모수를 추정하면 된다.
경험적 베이즈 방법을 활용하여 사전분포 베타분포에 대한 모수를 추정한다. 적률법을 활용하여 추정한다.
데이터가 적은 경우 수렴이 안되는 상황이 발생할 수 있으니 그점을 유념한다.


~~~{.r}
# 3. 베타분포 ------------------------------------
## 3.1. 최고타자 사전분포 모수추정 ------------------------
hitter_avg_decade <- hitter_tbl %>% 
  mutate(시대 = cut(연도, breaks = c(0, 1990, 2000, 2010, 2020), labels=c("80년", "90년", "00년", "10년"))) %>% 
  dplyr::filter(시대 %in% c("10년"))

## 3.1. 적률법 ------------------------
alpha_beta <- MASS::fitdistr(hitter_avg_decade$"타율", dbeta,
                    start = list(shape1 = 560, shape2 = 970))

alpha0 <- alpha_beta$estimate["shape1"]
beta0 <- alpha_beta$estimate["shape2"]

## 3.2. 최고타자 사전분포 시각화 ------------------------
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

<img src="fig/bayesian-ci-prior-1.png" style="display: block; margin: auto;" />

### 2.3. 현재 수위타자 경쟁

사전분포에 대한 선행작업이 완료되었다면, 다음으로 현재 수위타자 경쟁을 하고 있는 데이터를 긁어온다.
[다음 스포츠 야구 타자 통계](http://score.sports.media.daum.net/record/baseball/kbo/brnk.daum)웹사이트에서 
25명 데이터를 긁어와서 이중 수위타자가 될 가능성이 높은 10명만 추려본다.


~~~{.r}
## 3.4. 최고타자 타율 데이터 가져오기 ------------------------
baseball_url <- "http://score.sports.media.daum.net/record/baseball/kbo/brnk.daum"
baseball_html <- xml2::read_html(baseball_url)

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
## 3.5. 최고타자 타율 신뢰구간 ------------------------
baseball_bayes_df <- baseball_hitter %>% 
  mutate(선수 = str_extract_all(`선수 (팀)`, '^[^\\s]*'),
          팀 = str_extract_all(`선수 (팀)`,  "\\([^()]+\\)")) %>% 
  dplyr::select(선수, 팀, 타수, 안타, 타율) %>% 
  dplyr::filter(row_number() <=10 ) %>% 
  tbl_df %>% unnest()
~~~

### 2.4. 베이즈 타율 95% 신뢰구간

베이지안으로 추정한 타율은 사전분포에서 추정한 $\alpha, \beta$가 있다면 타수와 안타 데이터를 바탕으로 
추정이 가능하다. 또한 사후분포의 $\alpha, \beta$를 갱신하면 95% 신뢰구간도 수월하게 계산할 수 있다.
빈도주의 기법과 비교하여 베이지안 기법의 차이점도 함께 비교해본다.


~~~{.r}
baseball_bayes_df <- baseball_bayes_df %>% 
  mutate(alpha1 = 안타 + alpha0,
         beta1 = 타수 - 안타 + beta0,
         베이즈_타율 =  round((안타 + alpha0) / (타수 + alpha1 + beta0), 3), 
         low  = qbeta(.025, alpha1, beta1),
         high = qbeta(.975, alpha1, beta1))

frequentist_df <- baseball_bayes_df %>%
  group_by(선수) %>%
  do(tidy(binom.test(.$안타, .$타수))) %>%
  select(선수, 타율=estimate, low = conf.low, high = conf.high) %>%
  mutate(method = "빈도주의 신뢰구간")

bayes_df <- baseball_bayes_df %>%
  select(선수, 타율=베이즈_타율, low, high) %>%
  mutate(method = "베이지안 신뢰구간")

comparison_df <- bind_rows(frequentist_df, bayes_df) %>% 
  ungroup()

DT::datatable(comparison_df)
~~~

<!--html_preserve--><div id="htmlwidget-639c1cf7ac4d9af126b6" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-639c1cf7ac4d9af126b6">{"x":{"filter":"none","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20"],["김선빈","김재환","김태균","나성범","서건창","손아섭","안치홍","이대호","이명기","최형우","김선빈","나성범","서건창","김태균","이대호","최형우","손아섭","김재환","이명기","안치홍"],[0.376,0.340277777777778,0.360576923076923,0.36697247706422,0.363309352517986,0.341216216216216,0.337301587301587,0.356617647058824,0.338582677165354,0.347328244274809,0.349,0.35,0.346,0.35,0.346,0.346,0.343,0.343,0.345,0.345],[0.315748502809848,0.285717132731992,0.29533185172091,0.302904264050001,0.306702220746974,0.287346703426274,0.279168567787393,0.299696186212758,0.280607956656038,0.289789176195788,0.345031707040431,0.343562954784893,0.343412246810503,0.342738875043327,0.342386942136696,0.341030061738057,0.339968312488038,0.339863547324404,0.339823444652705,0.339658422992401],[0.439229667193431,0.398164525123692,0.429859214116186,0.434712814763957,0.422865137801155,0.398301582380721,0.399314582245336,0.416701827818296,0.400375918446429,0.408366206138715,0.389802846704099,0.388708349834409,0.387789187847578,0.387993622189172,0.386811663493874,0.385544005263416,0.384032487461485,0.384022961918695,0.384405844905407,0.384261679765331],["빈도주의 신뢰구간","빈도주의 신뢰구간","빈도주의 신뢰구간","빈도주의 신뢰구간","빈도주의 신뢰구간","빈도주의 신뢰구간","빈도주의 신뢰구간","빈도주의 신뢰구간","빈도주의 신뢰구간","빈도주의 신뢰구간","베이지안 신뢰구간","베이지안 신뢰구간","베이지안 신뢰구간","베이지안 신뢰구간","베이지안 신뢰구간","베이지안 신뢰구간","베이지안 신뢰구간","베이지안 신뢰구간","베이지안 신뢰구간","베이지안 신뢰구간"]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>선수<\/th>\n      <th>타율<\/th>\n      <th>low<\/th>\n      <th>high<\/th>\n      <th>method<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"crosstalkOptions":{"key":null,"group":null},"columnDefs":[{"className":"dt-right","targets":[2,3,4]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false},"selection":{"mode":"multiple","selected":null,"target":"row"}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

### 2.4. 베이즈 타율 95% 신뢰구간 시각화

빈도주의 방법을 통한 95% 신뢰구간을 시각화하면 우선 신뢰구간이 상당히 넓은 점과 함께, 꿈의 타율
4할을 넘을 수 있는 선수가 다수 발견된다. 
반면에 95%신뢰구간 상한으로 베이지안 추정을 통해 갈음해 보면 4할을 넘는 선수가 없다. 
참고로 점선은 최고 수위타자 2010년대 평균 타율이다. 


~~~{.r}
## 3.6. 최고타자 타율 신뢰구간 시각화 ------------------------

comparison_df %>% 
  mutate(선수 = reorder(선수, 타율)) %>%
  ggplot(aes(타율, 선수, color = method, group = method)) +
  geom_point(size=3.5) +
  geom_errorbarh(aes(xmin = low, xmax = high)) +
  geom_vline(xintercept = alpha0 / (alpha0 + beta0), color = "red", lty = 2) +
  labs(x="", y="", color = "") +
  theme(legend.position = "top")
~~~

<img src="fig/bayesian-ci-posterior-viz-1.png" style="display: block; margin: auto;" />
