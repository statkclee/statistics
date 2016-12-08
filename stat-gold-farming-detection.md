---
layout: page
title: 데이터 과학 -- 기초 통계
subtitle: 작업장 탐지
---

> ## 학습 목표 {.objectives}
>
> * 온라인 게임 부정행위에 대해 살펴본다.
> * 온라인 게임 봇 탐지 방법에 대해 이해한다.
> * 온라인 게임 경제 체계에 대해 이해한다.


### 1. MMORPG 역사 [^mmorpg-type]

[^mmorpg-type]: [Play Time: An Overview of the MMORPG Genre](http://www.anthemion.org/playtime_over.html)

MMORPG 게임은 Ultima Online이 대표적인 게임으로 클라이언트 프로그램을 보통 만원 ~ 5만원을 내고 구매해야 하고,
매월 게임에 접속하는데 접속료를 지불하는 형태를 띄고 있다. 경우에 따라서는 클라이언트 프로그램은 무료로 다운로드 받아
설치하고 나서 접속료를 매월 내는 비즈니스 모형과 접속도 무료로 하지만, 게임을 좀더 즐겁게 즐기려면 
아이템을 구매해야 하는 사업모형으로 나타나고 있다.

* 연필과 종이 RPG : Dungeons & Dragons, 1974
* Text adventure games : Adventure, 1975, 
* 머드(MUD, Multi-User Dungeon) : Text adventure games를 서버를 두고 여러명이 게임, MUD1 
* MMORPG : Ultima Online, 2004.  


<iframe width="300" height="200" src="https://www.youtube.com/embed/FPSpQpx-EqU" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/UMim_pYf3Hc" frameborder="0" allowfullscreen></iframe>


### 1. 온라인 게임 부정행위 [^detect-bot]

[^detect-bot]: [강아름, et al. "온라인 게임 봇 탐지를 위한 사용자 행위 분석." 정보보호학회논문지 22.2 (2012): 225-238.](http://ocean.kisti.re.kr/downfile/volume/kiisc/JBBHCB/2012/v22n2/JBBHCB_2012_v22n2_225.pdf)


MMORPG 온라인 게임에서 부정행위는 크게 **게임봇** 과 **사설서버** 로 나눌 수 있다.

* **게임 봇** : 사람을 대신하여 게임을 수행하는 소프트웨어
    * 물리적 형태에 따라 구분: 소프트웨어 형태, USB 혹은 마우스 형태
    * 실행방식에 따른 구분: OOG(Out-of-Game Client) 봇, IG(In-Game Client) 봇
* **사설 서버** : 정품 게임과 동일하거나 수정된 게임을 불법적으로 서비스하는 게임
    * 역공학 기술을 이용하여 제작된 서버
    * 시스템 해킹과 내부 유출을 통해 개발된 실제 서버와 동일한 서버

사설 서버의 경우 실제 게임보다 더 높은 경험치와 포인트, 더 좋은 아이템을 제공하여 정품 게임 사용자를 끌어들인다.


### 2. 온라인 게임 봇 탐지 방법

온라인 게임 봇 탐지 방법은 클라이언트 계층, 네트워크 계층, 서버 계층에서 다양한 방식으로 잡아낼 수 있다.

* **클라이언트 계층 탐지** 
    * 게임 서비스 제공사의 치팅 방지 시스템: 블리자드 WoW Warden System, NCsoft AION NC Guard, 텐센트 TenGuard, 넥슨 Nexon Guard
    * 보안 업체 게임 보안 솔류션: INCA Internet GameGuard, 안랩 Hackshield
    * 게임 내 디자인 활용 탐지: 보이지 않는 NPC(Non-Player Character) 활용 탐지, 보이지 않는 아이템 활용 탐지
    * 사용자 평판 활용 탐지: Kount, iOvation
* **네트워크 계층 탐지**
    * 트래픽 감시 : Time to Live (TTL) 값, Round Trip Time (RTT) 값    
    * 네트워크 프로토콜 변경: 키변경 및 암호 알고리즘 변경
* **서버 계층 탐지** 
    * 게임 내 로그 분석
    * 게임 내 챕차(CAPTCHA) 분석      


### 3. 데이터 분석을 통한 게임 봇 식별

로그 데이터를 기반으로 하여 게임 봇을 식별하는 방법은 사람이 게임 로그를 남기는 특성과 
게임 봇이 게임 로그를 남기는 습성에 차이가 난다는 가정에서 출발한다.
유휴 시간, 소셜 연결에서 사용자 행동이 게임 봇과 차이가 난다고 볼 수 있고, 
이를 근거로 하여 일반 사용자와 게임봇을 식별한다.

이동 지역과 좌표값을 분석하여 일반 사용자와 게임봇을 식별하는 것도 가능하다.
이외에도 네트워크 트래픽을 분석하는 방법과 마우스 키보드 움직임을 데이터화 하는 방식도 존재한다.
챕챠는 일반 사용자와 게임봇을 식별할 수 있는 강력한 방법이 될 수 있지만, 게임에 대한 사용자 몰입을 해친다는 단점도 존재한다.

* **사용자 행동 기반** : 사용자와 게임 봇의 행동 차이를 이용한 방법
    * 유휴 시간 측정
    * 소셜 커넥션 분석
* **이동 경로 기반** : 일반 사용자와 게임 봇의 이동 경로 차이를 가정하여 식별
    * 이동 지역과 좌표값을 분석
* **트래픽 기반** : 네트워크 트래픽을 분석하는 방법
    * 명령 패킷의 타이밍 확인
    * 트래픽의 폭발성 확인
    * 네트워크 상태에 대한 반응 확인
    * 데이터 길이, 트래픽 도착 간격 확인
* **HOP 기반** :  사용자의 키보드나 마우스 움직임 등 게임 외적인 부분을 관찰하여 사람이 할 수 없는 동작을 식별하는 방법   
    * 사용자 입력을 관찰하는 방법
    * 윈도우 이벤트 시퀀스를 분석하는 방법
* **CAPTCHA 기반** : 사람은 쉽게 해결할 수 있으나 컴퓨터는 풀 수 없는 문제를 이용하는 방법
    * 시도 응답 방법

### 4. MMORPG 사용자 유형 [^mmorpg-type]

* **탐험(Exploration)** : 훈련, 치료, 미션 또는 퀘스트 수행, 재료 수집, 경험치 포인트 획득
* **결투(Combat)** : 몬스터 사냥, 사용자간 일대일 결투 (player-versus-player, 'PvP'), 혹은 'player-killing'('PK'), 결투 후에 부활 [^pk-terminology]
* **제작(Craft)** : 무에서 아이템이 생성되거나 재료를 조합해서 새로운 아이템을 생성한다. 에버퀘스트(EverQuest)에서 `metal bits` &rarr; `files` &rarr; `studs` &rarr; `studded armor`
* **사회활동(Socializing)** : 텍스트 메시지를 사용한 커뮤니케이션, 거래, 흥정, 논쟁, 파티, 길드, 종족 등의 그룹 결성, 친구 맺기.



[^pk-terminology]: [Player-Kill의 줄임말. 통신 게임상에서 이용자끼리의 공격이 가능할 경우, 서로 싸워 죽이는 행위를 뜻함](http://kin.naver.com/openkr/detail.nhn?docId=64566)

