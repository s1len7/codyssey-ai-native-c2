# Codyssey 알림 API (관찰 기반)

## 관찰
- 공통 헤더: `optionList`, `alarmCntList` 호출 관찰.
- 알림 페이지: `https://usr.codyssey.kr/alarm/alarmList` 접근 가능. 화면에 알림 목록/필터/페이지네이션이 노출됨.

## 1. `POST /alarm/alarmList/optionList`
알림 분류/옵션 코드 목록.

- **URL**: `https://api.usr.codyssey.kr/alarm/alarmList/optionList`
- **Method**: `POST`
- **Body**: 관찰상 빈 요청.
- **응답**: `result.divCdList[]` (코드 테이블 형태)

| 필드(관찰) | 설명 |
|---|---|
| `grupCd` | 그룹 코드(예: `CM005`) |
| `dtlCd` | 상세 코드 |
| `dtlCdNm` | 이름(내 정보, 학습, 커뮤니티 등) |
| `dtlCdDescCn` | 설명 |
| `useYn` | 사용 여부 |
| `engDtlCdNm` | 영문/표시명 |

## 2. `POST /alarm/alarmList/alarmCntList`
알림 건수 및 사용자 상태 요약.

- **URL**: `https://api.usr.codyssey.kr/alarm/alarmList/alarmCntList`
- **Method**: `POST`
- **Body**: 관찰상 빈 요청.
- **응답(관찰)**

| 필드 | 타입(관찰) | 설명 |
|---|---|---|
| `level` | string/number | 레벨 표시 |
| `alarmCnt` | number | 알림 건수 |
| `exp` | string/number | 경험치/포인트성 값 |

## 3. 알림 목록(미확인)
- `/alarm/alarmList` 화면에는 알림 항목이 표시되나, 해당 페이지 재캡처에서 **별도 알림 목록 조회 API를 확정하지 못함**(공통 API + `noticeList`만 관찰).
- 가능성: 다른 경로의 목록 API, 다른 캡처 시점/필터 조건에서 호출, 또는 클라이언트 렌더링/캐시. 추정 금지.

## 미확인
- 알림 목록 조회 API(URL/Method/body/응답 shape).
- 알림 읽음 처리/삭제/이동 링크 처리 API(상태 변경 가능성, 실행 금지).
- `divCdList` 코드가 목록 필터 API에 매핑되는 방식.
- 알림 항목에는 개인식별정보가 포함될 수 있어 문서화 시 마스킹 필요.
