# Codyssey 공지 API (관찰 기반)

## 관찰 화면
- `/main` (헤더 공지 요약), `/notice/noticeGoList`, `/policy/policyGoList` 진입 시 공통적으로 관련 호출 관찰.

## 1. `POST /notice/noticeList`
공지 목록 조회.

- **URL**: `https://api.usr.codyssey.kr/notice/noticeList`
- **Method**: `POST`
- **Content-Type(관찰)**: `application/json`
- **보호**: `X-XSRF-TOKEN: <redacted>` 관찰.
- **관찰된 Body**

| 필드 | 예시 | 비고 |
|---|---|---|
| `orderBy` | `all` | 정렬 |
| `page` | `1` | 페이지 |
| `pagePerRows` | `1` | 화면 맥락에 따라 다름 |
| `sysDivCd` | `STU` | 대상 구분(ADM,STU 등 값이 데이터에 나타날 수 있음) |
| `pstartTitlNm` | `null` | 제목 검색 |
| `mbrNm` | `null` | 작성자 검색 |
| `startDt/endDt` | `null` | 기간 |
| `topFixYn` | `null` | 상단 고정 |

- **응답**: `result.list[]`, `result.paginator`, `result.allCount`

| `list[]` 필드(관찰) | 설명 |
|---|---|
| `pstartSn` | 공지 ID 후보 |
| `pstartTitlNm` | 제목 |
| `pstartCn` | HTML 본문(민감/개인정보 포함 가능성 점검 필요) |
| `mbrNm` | 작성자 표시명 |
| `regDt/mdfcnDt` | 등록/수정 일시 |
| `sysDivCd` | 노출 대상 |
| `topFixYn` | 상단 고정 |
| `inqCnt` | 조회수 |
| `instCd` | 기관 코드 |

## 미확인
- 공지 상세 전용 API(목록에 본문이 포함되어 별도 상세 호출이 없을 수 있음).
- 첨부파일 메타/다운로드 API.
- 운영 규정(`policy`) 화면이 `noticeList` 재사용인지, 별도 필터/엔드포인트인지.
- 검색/페이징 파라미터 전체 계약.
