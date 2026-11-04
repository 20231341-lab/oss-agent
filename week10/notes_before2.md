# week09 tools.py 함수 설명

`week09/tools.py`는 영화진흥위원회(KOBIS) 오픈 API로 **일별 박스오피스** 정보를 조회하는 도구 함수 2개를 담고 있다.

## 한눈에 보기

| 함수 | 입력 | 출력 | 용도 |
|---|---|---|---|
| `get_boxoffice_rank_1(date)` | 날짜 | 1위 영화 제목 | 그날 1위 영화 알아보기 |
| `get_movie_audience(date, title)` | 날짜, 영화 제목 | 관객 수 문자열 | 특정 영화의 그날 관객 수 알아보기 |

## 공통 준비

- `import requests` — HTTP 요청용 라이브러리
- `from secret import API_KEY` — API 키는 코드에 직접 쓰지 않고 `secret.py`에서 불러온다.
- `BASE_URL` — KOBIS 일별 박스오피스 API 주소 (`searchDailyBoxOfficeList.json`)

두 함수 모두 같은 순서로 동작한다.

1. `params`에 `key`(API 키)와 `targetDt`(조회 날짜)를 담는다.
2. `requests.get(BASE_URL, params=params, timeout=10)`으로 요청한다.
3. `response.json()`으로 결과를 딕셔너리로 바꾼다.
4. `data["boxOfficeResult"]["dailyBoxOfficeList"]`에서 영화 목록을 꺼낸다.

## 1. `get_boxoffice_rank_1(date)`

- **하는 일:** 지정한 날짜의 박스오피스 1위 영화 제목을 돌려준다.
- **입력:** `date` — 8자리 날짜 문자열 (예: `"20261103"`)
- **출력:** 1위 영화 제목 문자열 (예: `"오디세이"`)
- **핵심 코드:** `movies[0]["movieNm"]` — 목록의 첫 번째 영화가 1위다.
- docstring으로 입력과 출력이 설명되어 있다.

## 2. `get_movie_audience(date, title)`

- **하는 일:** 지정한 날짜에 특정 영화가 동원한 하루 관객 수를 돌려준다.
- **입력:**
  - `date` — 8자리 날짜 문자열
  - `title` — 찾을 영화 제목
- **출력:**
  - 찾으면: 천 단위 쉼표가 붙은 관객 수 (예: `"123,456명"`)
  - 못 찾으면: `"'제목' 은(는) 날짜 순위에 없습니다."`
- **핵심 코드:** `for` 문으로 목록을 돌며 `movie["movieNm"] == title`인 영화의 `audiCnt`를 `int`로 바꾼 뒤 `f"{audi:,}명"`으로 꾸민다.
- docstring이 없다.

## 개선할 수 있는 점

- **중복 코드:** API 호출 부분(1~4단계)이 두 함수에 똑같이 들어 있다. 별도 함수로 빼면 깔끔해진다.
- **오류 처리 없음:** 네트워크 오류, 잘못된 API 키, 잘못된 날짜 형식이면 예외가 난다. 목록이 비어 있으면 `movies[0]`에서 `IndexError`가 난다.
- **제목 비교가 엄격함:** 띄어쓰기나 기호가 조금만 달라도 "순위에 없습니다"가 나온다.
- **docstring 누락:** `get_movie_audience`에도 docstring을 달면 에이전트가 도구 용도를 이해하기 쉽다.
