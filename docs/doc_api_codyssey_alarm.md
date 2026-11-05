# Codyssey 알림 API (관찰 기반)

## 관찰
여러 페이지 공통 헤더/사이드바 로드 시 알림 관련 API가 호출됨.

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

## 미확인
- 실제 알림 목록 조회 API(건수 외 목록/읽음 처리/상세).
- 읽음 처리/삭제 등 상태 변경 API(실행 금지).
- `divCdList` 코드가 알림 필터 API에 어떻게 사용되는지.
