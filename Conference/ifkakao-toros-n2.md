ifKakao - TOROS N2
=================================

발표자 : 김성진(nick.kim) - 카카오

설명: 카카오 추천팀에서 개발한 근사 최근접 이웃 라이브러리 TOROS N2를 소개합니다.
https://github.com/kakao/n2

발표 속기
---------------------------------

## N2, Nearest Neighbor란?

- 물음, 강아지 글을 읽은 유저에게 어떤 글을 추천할 수 있을까?
1. 이 글과 유사한 글을 추천한다.
2. 이 글을 읽은 사람들이 읽은 글을 추천한다.
3. ...

N2 솔루션은 이 글과 유사한 글을 추천하는 방법에 대해서 집중한다.

강아지 글과 유사한 글에는 대부분 강아지가 있다.

- 유사한 아이템 찾기? 어떻게 할 것인가.
- 유사도를 숫자로 표현하는 방법 => 벡터 모델. 각 아이템을 f-차원의 벡터로 표현한다. 벡터로 표현하면, 각 아이템 간의 거리를 계산할 수 있음.
- 거리 계산이 가능해지면, 거리가 가장 가까운 애들이 유사하구나.라고 판단하는 것이지!

#### 그렇다면 벡터로 어떻게 표현할까?
텍스트는 word 임베딩을 통해서 가능하다.(구현체 word2vec)

이미지도 임베딩 방법이 있다.

( 오늘 발표는 텍스트나 이미지를 임베딩해서 벡터로 표현하는 방법에 대한 발표가 아니다. <- 이거 여러번 언급함. 자주 환기 시켜주니 어려운 주제임에도 불구하고 딴 의문으로 빠지지않고 말하고자하는 내용에 집중할 수 있었다)

평면에 찍을 수 있다. 거리 계산도 가능하다. 거리 계산은 f차원으로 표현하면 이해하기 어려우니 2차원으로 간단하게 표현해보자.

거리가 좀 멀면 유사하지 않다고 볼 수 있다.

여기까지 전제는 벡터 모델링이 잘 되어있다는 것이다.

(벡터 모델링 하는 방법도 궁금하네.)

한 개만 구하지 않고, 글 A와 유사한 것 10개를 찾는다.

## KNN: K개 Nearest neighbor 찾기
이 방법은 좌표 평면에 모든 글과 대상 글의 거리를 계산하는 방식.

=> 가장 간단함, 모든 거리를 계산하는 방식. 이건 브루스포스 방식이니까. 우선순위 큐를 만들어서 모두 계산한다음 다 빼면된다.

=> 이건 너무 느리다. 모든 아이템에 대한 추천 결과를 만들면 0.1 * 100만 = 27시간

=> Brute force 보다 빠른 속도로 찾는 방법은 없을까?

#### 트레이드 오프.
=> 정확도를 버린다. 속도를 취한다.

## Approximate k nearest neighbor search
= 정확도를 버리고 속도를 버린다. AKNN이라고 부른다.
(근사해서 찾기 때문에 approximate.)

- 이 방법은 0.001 * 100만개 = 16분

recall : 정확도. 7초 정도 잃는대신 속도는 100배 이상 빨라집니다.

물론 아이템 적으면 brute force로 해도된다.

즉 AKNN은 대충 찾아서 90% 정확도 100배 빠르게.

## AkNN 패키지들.

### Annoy(spotify에서 만듦)
- Random porjection + tree
- 적은 수의 하이퍼 파라미터
- 리드 온니 매퍼드 데이크 스트럭쳐
- 사용하기 쉬움
- 단점: 최신 라이브러리들에 비해 떨어지는 성능, MMap 지원


### 여기서! MMap이란?
- MMap이 지원되지 않는 경우
    - 쿼리는 빠르게 해야하니까 인덱스를 먼저 만듦.
    - 디스크에 인덱스를 메모리에 올리지 않으니 프로세스가 늘어남에 따라서 리소스관리가 비효율적이다.

- MMap이 지원되는 경우
* process가 os에 물어봐서 disk 메모리에 매핑되어 있는 경우 mem에 올려서 효율적으로 사용한다.


### nmslib
- 속도 정확도 우수한 HNSW 알고리즘 지원
- MMap 미지원


*Annoy는 MMap 지원, 쓰기 쉬우므 성능 아쉼 nmslib 아직 쓰기 힘들다.*

## TOROS N2
- HNSW 알고리즘 지원
- MMAP 지원
- Index build 속도 빠르다
- 설치, 사용 간편, 파이선, 고 바인딩
- nmslib보다 느리다. 써치 속도만 느리다! 인덱스 빌딩은 빠르다.

- 인덱스! 빌드 속도가 빠르다.. => 트레이드 오프가능(강조?)

HNSW ( Hierarchical Hanigable small world graphs)
// TODO 이건 찾아보자... 페이퍼 봐야할 것 같다.

HNSW는 레이어가 여러개 있다. 위에 레이어일수록 노드 갯수가 적다. 아래로 갈수록 노드가 많다. insert할때 엣지를 연결하는건가?

## nmslib vs n2
인덱스 빌드 시간과 서치시간은 교환가능.


## TOROS N2
장점 : HNSW 알고리즘 지원, index build 속도 빠르다. MMAP 지원 등

TODO 앞으로 Search 속도 개선, build 할 때 메모리 더블링 이슈 해결, 항상 k개 결과 보장하도록 개선.
