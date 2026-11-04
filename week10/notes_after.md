작성: Claude Code / 확인: (곽동현)

# week09/tools.py 함수 정리

- 영화진흥위원회(KOBIS) 오픈 API의 **일별 박스오피스** 데이터를 조회하는 함수 모음임

## 공통 설정

- `requests` 라이브러리로 HTTP 요청을 보냄
- API 키는 `secret.py`의 `API_KEY`에서 가져옴 (코드에 직접 노출하지 않기 위함)
- `BASE_URL`: KOBIS 일별 박스오피스 조회 API 주소 (`searchDailyBoxOfficeList.json`)

## 함수 목록

### 1. `get_boxoffice_rank_1(date)`

- 특정 날짜의 박스오피스 **1위 영화 제목**을 반환함

| 항목 | 내용 |
|------|------|
| 입력 | `date` (문자열) – 8자리 날짜, 예: `"20261103"` |
| 출력 | (문자열) 1위 영화 제목, 예: `"오디세이"` |

- 동작 순서
  1. `key`, `targetDt`를 파라미터로 API에 GET 요청함 (timeout 10초)
  2. 응답 JSON에서 `boxOfficeResult` → `dailyBoxOfficeList` 목록을 꺼냄
  3. 목록의 첫 번째 항목(`movies[0]`)의 `movieNm`(영화 제목)을 반환함

### 2. `get_movie_audience(date, title)`

- 특정 날짜에 특정 영화의 **일일 관객 수**를 반환함

| 항목 | 내용 |
|------|------|
| 입력 | `date` (문자열) – 8자리 날짜 / `title` (문자열) – 영화 제목 |
| 출력 | (문자열) 천 단위 쉼표가 붙은 관객 수, 예: `"123,456명"` |

- 동작 순서
  1. `get_boxoffice_rank_1`과 같은 방식으로 그날의 박스오피스 목록을 가져옴
  2. 목록을 돌면서 `movieNm`이 `title`과 정확히 일치하는 영화를 찾음
  3. 찾으면 `audiCnt`(당일 관객 수)를 정수로 바꿔 `"123,456명"` 형식으로 반환함
  4. 끝까지 못 찾으면 `"'제목' 은(는) 날짜 순위에 없습니다."` 메시지를 반환함

## 참고 / 개선할 점

- 두 함수가 API 호출 코드(요청 → JSON → 목록 추출)를 똑같이 반복함. 공통 함수로 빼면 중복 감소 가능함
- `get_movie_audience`에는 docstring이 없음. 입력/출력 설명 추가가 필요함
- 날짜 형식이 잘못됐거나 API 오류가 나면 `KeyError`/`IndexError` 발생 가능함 (예외 처리 없음)
- 영화 제목이 완전히 일치해야 찾을 수 있음 (띄어쓰기·부제 차이에 주의 필요함)
