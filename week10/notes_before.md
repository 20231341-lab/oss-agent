# week09 tools.py 함수 정리

`week09/tools.py`는 영화진흥위원회(KOBIS) 오픈 API의 **일별 박스오피스** 데이터를 가져오는 함수 모음이다.

## 공통 설정

- `requests`로 API를 호출한다.
- API 키는 `secret.py`의 `API_KEY`에서 가져온다.
- `BASE_URL`: KOBIS 일별 박스오피스 조회 주소 (`searchDailyBoxOfficeList.json`)
- 두 함수 모두 `key`와 `targetDt`(날짜)를 파라미터로 넘기고, 타임아웃은 10초다.

## 함수 목록

### 1. `get_boxoffice_rank_1(date)`

해당 날짜의 박스오피스 **1위 영화 제목**을 돌려준다.

- 입력: `date` (문자열, 8자리 날짜. 예: `"20261103"`)
- 출력: 1위 영화 제목 (문자열)
- 동작: 응답의 `boxOfficeResult` → `dailyBoxOfficeList`에서 첫 번째 영화의 `movieNm`을 반환한다.
- docstring이 있다.

### 2. `get_movie_audience(date, title)`

해당 날짜에 특정 영화의 **일일 관객 수**를 돌려준다.

- 입력: `date` (8자리 날짜 문자열), `title` (영화 제목 문자열)
- 출력:
  - 순위 목록에 영화가 있으면 천 단위 쉼표가 붙은 관객 수. 예: `"123,456명"`
  - 없으면 `"'제목' 은(는) 날짜 순위에 없습니다."`
- 동작: 박스오피스 목록을 돌면서 `movieNm`이 `title`과 정확히 일치하는 영화의 `audiCnt`를 찾는다.
- docstring이 없다.

## 참고할 점

- 두 함수가 API 호출 코드(`params`, `requests.get`, JSON 파싱)를 똑같이 반복한다.
- 오류 처리가 없다. 요청이 실패하거나 응답 형식이 다르면, 또는 목록이 비어 있으면(`movies[0]`) 예외가 난다.
- 제목은 완전히 같아야 찾는다. 띄어쓰기 하나만 달라도 "순위에 없습니다"가 나온다.
