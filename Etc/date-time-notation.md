날짜와 시간 표기법
===================================

## UTC, GMT
UTC는 그레고리력 표기에 따른 협정 세계시이다.

1일은 24시간으로 나뉘며, 1시간은 60분으로, 1분은 60초로 나뉘는 것이 보통이나 약간은 가변적이다.
UTC의 하루는 보통 86,400초이다. (24시간)

UTC는 그리니치 평균시(GMT)로 불리기도 하는데,

UTC와 GMT는 초의 소숫점 단위에서만 차이가 나기 때문에 일상에서는 혼용되어 사용한다.
기술적인 표기에서는 UTC가 사용된다.

참고로 그리니치 평균시(Greenwich Mean Time, GMT)는,
런던(영국의 수도)을 기점으로 하고 웰링턴(뉴질랜드의 수도)을 종점으로 하는 협정 세계시의 빠른 시간이다.

## ISO 8601

ISO 8601은 날짜와 시간의 표기에 관한 국제 표준 규격이다.

ISO 8601에 따라 날짜를 기록하는 방법에는 3가지가 있다.
  - 연월일로 기록하는 방법
  - 연과 연중 일수로 기록하는 방법
  - 연과 주와 주중 일수로 기록하는 방법이다.

### 날짜의 표기
ISO 8601에는 날짜의 표기에 그레고리력을 따른다.

  - 연을 표기할 때는 기원전 1년에 해당하는 0000부터 기원후 9999년에 해당하는 9999까지의 값을 사용할 수 있다.
    단, 율리우스력이 사용되던 0000년부터 1582년까지의 값은 정보 교환 시 상호 합의 하에 그레고리력으로
    환산해야 한다. 0000년 전이나 9999년 후의 표기도 역시 사용하는 주체간의 상호 합의가 필요하다.
    예를 들어, 연도 앞에 빼기표(-)를 붙이면 기원전 1년 이전, 더하기표(+)를 붙이면 기원전 1년 이후를
    가리키는 식으로 합의할 수 있다.
  - 월의 표기는 01부터 12까지의 값을 가지며, 각각 1월부터 12월까지를 나타낸다.
  - 일의 표기는 01부터 시작하며 달에 따라 28부터 31까지의 값을 가진다.
  - 연중 일의 표기는 001부터 시작하며 해에 따라 365에서 366까지의 값을 가진다.
  - 연중 주의 표기는 01부터 시작한다.
  - 주중 일의 표기는 월요일을 1로 시작하여 순서대로 요일에 숫자를 붙이며, 일요일을 7로 한다.

#### 연월일 표기법
YYYY-MM-DD (확장 형식) 또는 YYYYMMDD (기본 형식)으로 표기한다.

```
예) 1981-02-22 또는 19810222 : 1981년 2월 22일
```

#### 연과 연중 일수 표기법
YYYY-DDD (확장 형식) 또는 YYYYDDD (기본 형식)으로 표기한다.

DDD는 연중 날의 번호로 1월 1일이 001이며, 12월 31일은 평년은 365, 윤년은 366이 된다.
```
예) 1981-053 또는 1981053 : 1981년의 53번 째 날
```
#### 연과 주의 주중 일수 표기법
YYYY-Www-D (확장 형식) 또는 YYYYWwwD (기본 형식)으로 표기한다.

ww는 연중 주의 번호로, 해의 첫 주는 01, 마지막 주는 52 또는 53이 된다.

단, 해의 첫 주는 그 해의 첫번째 목요일을 포함한 주로 한다.

D는 요일을 나타내며, 월요일이 1, 일요일이 7이다.
```
예) 1981-W07-7 또는 1981W077 : 1981년의 7번째 주의 일요일
```


### 시간의 표기

시간의 표기에는 쌍점을 쓴 hh:mm:ss (확장 형식) 또는 hhmmss (기본 형식)을 사용한다.

hh는 시를 나타내며 00부터 24까지의 값을 가지고,
mm은 분으로 00부터 59까지의 값을, ss는 초로  00부터 59까지의 값을 갖는다.

날짜와 시간을 함께 표기할 때에는, 날짜와 시간 사이에 T를 넣어 표기한다.
```
예) 1981-02-22T09:00:00 : 1981년 2월 22일 09:00
```


### 시간대의 표기

시간대를 표기할 때에는 Z또는 +/- 기호를 사용한다.

- UTC 시간대에서는 시각 뒤에 Z를 붙인다.
```
예) 1981-02-22T09:00Z 또는 19810222T0900Z : UTC 시간대에서의 1981년 2월 22일 오전 9시
```

- UTC 외의 시간대에서는 시각 뒤에 +- hh:mm, +- hhmm, +- hh 를 덧붙여 쓴다.
```
예) 1981-02-22T09:00:00+09:00 : UTC+9 시간대에서의 1981년 2월 22일 오전 9시
```
+가 붙으면, UTC의 시각보다 더 "빠르다"다는 의미다. 반대로 -는 느리다는 것을 의미하다.
예를 들어, 1981-02-22T09:00+09:00 는 1981-02-22T00:00Z 와 동일하다.
즉, UTC+9 시간대에서는 오전 9시이지만, UTC 시간대에서는 오전 0시이다.


#### 기간의 표기

기간을 나타낼 때에는 시작일시/종료일시로 표기한다.
```
예)
1981-02-22/2007-09-26
1981-02-22T09:00:00+09:00/2007-09-26T17:00:00+09:00
```


#### RFC 3339 Date Formats

ISO8601을 인터넷 프로토콜로 어떻게 다룰 것인지를 규정한 RFC 이다.
ISO8601과 거의 비슷하며, 약간의 차이만 있을 뿐이다.
예를 들면, RFC 3339에서는 'T'의 생략을 허용하지 않고, 날짜와 시간 사이의 공백을 허용한다.
대부분의 경우, 이 둘을 상세하게 분리해서 생각하지 않아도 된다.