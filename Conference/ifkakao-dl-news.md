ifKakao - 딥러닝을 활용한 뉴스 메타 태깅
=================================

발표자 : 김기도(olaf.kido) - 카카오

설명: 사용자에게 알맞은 뉴스를 전달하기 위해서는 언론사에서 전달해주는 기본 정보 이외에도 컨텐츠의 다양한 특성을 파악하여 이를 사용자와 연결 짓는 것이 중요합니다. 다양한 특성 정보들 중에서 기사의 유형이나 핵심 주제 같은 것들은 컨텐츠 본문을 자연어 처리해서만 얻을 수 있기 때문에 분석하기가 매우 까다롭습니다. 본 발표에서는 Deep Learning 기술을 사용한 '뉴스 메타 태깅 시스템'의 개발 사례를 소개합니다. 이 사례를 통해 분석 모델 학습부터 운영 시스템 개발 과정에서의 고민과 Lessons Learned를 공유하도록 하겠습니다.

발표 속기
---------------------------------
### 뉴스 메타데이터(News Metadata)
-> 뉴스 기사의 특징을 설명하는 정보들
- 언론사 제공 속성 : 언론사, 기자, 카테고리
- 콘텐츠 분석 정보 : 형태, 내용, 이미지 분석

콘텐츠 분석 정보 <- 기계학습, 딥러닝

왜? 필요한가. 다양한 메타데이터를 확보하여 사용자에게 필요한 콘텐츠 제공에 활용.

### 뉴스 메타 태깅 시스템.
메타 데이터 생성과 활용이 잘 되려면 데이터의 유통이 간단해야함.

Convolution Neural Network로 기사 형태 분류하기를 과제로 작년부터 스터디를 시작.

### 기사 형태 분류
* 사실 전달형 기사(A)  해설/묘사형 기사(B)가 있음.

대부분 사실 전달형 기사이다.

해설 묘사형이 정보량이 많고 오래 읽을 만한 내용의 기사들을 찾는 특징 중 하나.

이걸 분류해서 큐레이션하면 사용자가 좋아하지않을까?

### Text Classification by character based Convolution Neural Network : CNN

뉴스 기사 콘텐츠를 있는 그대로 분석에 활용. 형태소 분석을 하지않고 캐릭터를 있는 그대로 한다.

CNN : 캐릭터 단위로 뉴스 기사를 학습할 수 있다는 것
- 기사 제목이나 본문의 특징적 기호를 활용할 수 있고.
- 한글 한자를 로마자로 변환하기 때문에 알파벳 스페이스 안에서 처리 가능하고
- 문자와 단어의 순서 보전하여 기사의 문투를 학습할 수 있다.

### 문제점! 기사 형태 분류 정보가 없다.

기사 형태 분류 정보가 없다. -> 딥러닝으로 만들자.  -> 지도 학습을 하려면 정답셋이 필요하다. -> 기사 형태 분류 정보가 없다....

미미의 고민 : 어떻게하면 정답셋을 만들 수 있을까?

fact 1. 사실 전달 기사는 대체로 기사가 짧다.

이걸로 분류해보자. => 이렇게 하면 오분류된 데이터가 있다.

=> 하지만 오분류 패턴이 일관되지 않다면 패턴 학습 대세에 영향 적음

= 랜덤하게 잘 만들어진 오분류는 크게 없다.

평가가 어렵다. 그래서 사람이 들어간다.

##### 전수 조사를 통해 라벨링한다. (허겅)

- 하루 총 뉴스 데이터는 44만건,
- 해설 묘사형 기사 수가 너무 적다.
- 학습 결과가 별로다.
=> 해설 기사를 높이니 학습 테스트가 괜찮았다.

### 모델 앙상블
학습 모델마다 특성이 다르므로 이를 결합하여 결과 변동성을 줄임

### 뉴스 메타 태깅 시스템
- 뉴스 발행 후 메타데이터를 생성하고 유통시키는 플랫폼

#### 설계 목표:
- 메타 태그 생성의 비동기성: 다양한 조직과 플랫폼이 메타 데이터 생성에 참여
- 메타 태그 생성 기술의 다양성: 기계학습, 딥러닝 플랫폼의 다양성을 수용할 수 있어야
- 메타데이터 생성 서비스 메타 데이터 생성 서비스(기사형태분류) 태그 토픽에 발행한다. 다시 발행된 tag들을 카프카에 보내고 원래 컨텐츠와 병합한다. 하모니를 통해서.


### Inferrer API
- 기계학습 / 딥러닝 모델을 서비스하는 API
- 각 모델 단위로 API 개발
- 도커라이징하여 학습 모델 + 코드 버전관리
- 모델 추론 이외의 기능은 최소화 할 것

### 앙상블 API
- 인퍼러 API 호출하고 응답함
- 각 모델에 필요한 입력값을 생성.
- Ensemble API는 게이트웨이 역할을 한다.
- 스프링 웹 플럭스 기반

=> spring-webflux: 개발 여러 소스 정보를 취합한다. 병렬-비동기 처리 구조의 이점을 가지고 있다. 들어온 데이터를 feature로 만들어서 inferrer API를 호출해서 머지한다.

#### Message Flow Control

결국엔 아래와 같은 구조를 가지는 뉴스 메타 태깅 시스템 아키텍쳐.

```
Filter -> Invoke Ensembel -> Post process -> produce
```

Apache Nifi가 적합!

//TODO: Nifi 자세히 알아보기

### Apache Nifi

- Data Flow 개발이 간단
- 각 처리 단계별로 in/out 처리량과 데이터 모니터링
- nifi registry와 연동하면 Data Flow 형상 관리가능.

### 향후 과제
- 딥러닝 모델 품질관리,
    - 오분류 사례 수집 및 모델 재학습
    - 현재는 시간별 분류 결과량 트래킹

- 다양한 메타데이터 추가
    - 기사 내용의 특징 분석
    - 기사 내 사진, 차트, 인포그래픽 분석
    - 음성 뉴스 및 동영상을 활용한 다양한 실험
