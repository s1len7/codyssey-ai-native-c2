# Codyssey API 역공학 테스트 결과 로그

> 목적: 토큰 부족/중단/다른 에이전트 인계 시 이어서 작업 가능하도록, 테스트 시도·결과·미확인 항목을 기록한다.  
> 보존 문서: `docs/doc_api_codyssey_peer_review_schedule.md`는 수정하지 않음.

## 작업 규칙(사용자 지시)
- API 종류별 문서 분리(`docs/doc_api_codyssey_*.md`) 진행.
- 가능한 것은 실제 관찰/테스트 후 문서화.
- 미완/미지는 테스트 후 결과를 본 파일에 기록.
- 작업 산출물 생성 시마다 커밋/푸시, 마지막에도 푸시.

## 세션/환경
| 항목 | 값 |
|---|---|
| 일시(UTC) | 2026-11-05 |
| 작업 디렉터리 | `/Users/hyeongil.ko8051/codyssey-ai-native-c2` |
| 브랜치/원격 | `main` / `origin https://github.com/s1len7/codyssey-ai-native-c2.git` |
| opencli | `v2.0.2`, daemon/extension connected |
| 브라우저 세션 | `muunzz2v` (사용자가 로그인한 Codyssey 탭) |
| 보안 | 쿠키/세션/CSRF/`X-XSRF-TOKEN`/`mbrId`/이메일 등 민감값은 기록·문서화하지 않음(마스킹). 상태 변경 요청 실행 금지. |

## 완료/진행
- [x] Git 상태 확인, opencli 연결 확인.
- [x] 테스트 결과 로그 파일 생성.
- [ ] API 종류별 문서 생성(진행 중).
- [ ] 미지/미완 항목 추가 테스트.
- [ ] 최종 커밋/푸시.

## 관찰된 API 요약(고유 Method+Path)
| Method | Path | 분류 | 상태 |
|---|---|---|---|
| POST | `/schedule/scheduleAllList/` | schedule | 관찰됨(캘린더) |
| POST | `/schedule/psblScheduleList/` | schedule | 관찰됨 |
| POST | `/ev/request/searchList` | evaluation_request | 관찰됨 |
| GET | `/ev/request/evlStusCdList` | evaluation_request | 관찰됨 |
| GET | `/ev/request/lastEvList` | evaluation_request | 관찰됨 |
| POST | `/ev/request/selectProjectNmList` | evaluation_request | 관찰됨 |
| POST | `/ev/request/selectLrnCorsNmList` | evaluation_request | 관찰됨 |
| POST | `/ev/request/selectLrnUqstnNmList` | evaluation_request | 관찰됨 |
| POST | `/ev/request/evlTotList` | evaluation_request | 관찰됨 |
| POST | `/ev/request/mtlEvlTxnDtoByPkList` | evaluation_request | 관찰됨 |
| POST | `/notice/noticeList` | notice | 관찰됨 |
| POST | `/alarm/alarmList/optionList` | alarm | 관찰됨 |
| POST | `/alarm/alarmList/alarmCntList` | alarm | 관찰됨 |
| POST | `/learning/learningProgress/allLearningList` | learning | 관찰됨 |
| GET | `/point/exp/total/detail` | user_common | 관찰됨 |
| GET | `/rest/user/info/detail` | user_common | 관찰됨 |
| GET | `/api/common/menu/my` | user_common | 관찰됨 |
| GET | `/main/sidebar/new/detail` | user_common | 관찰됨 |
| GET | `/survey/gate/active` | user_common | 관찰됨 |
| POST | `/myinfo/usr/list` | user_common | 관찰됨 |
| POST | `/smallGather/usr/list` | studygroup | 관찰됨 |

## 테스트 상세

### T-001 비인증 `scheduleAllList` 직접 호출 (이전 수행)
- 요청: `POST https://usr.codyssey.kr/schedule/scheduleAllList/` body `bgngYmd=20261001&endYmd=20261231&scheculeType=request&searchType=T`
- 결과: HTTP 200이지만 **SPA HTML 셸(548 bytes)** 반환, JSON 아님. 비인증/프런트 라우팅 영향으로 실 API 응답 미확보.
- 판정: 인증 세션 필요.

### T-002 opencli 로그인 세션 캘린더 관찰
- 페이지: `/ev/schedule/evaluationScheduleList`
- 실제 호출: `POST https://api.usr.codyssey.kr/schedule/scheduleAllList/?mbrId=<redacted>&instCd=<redacted>&bgngYmd=2026.10.26&endYmd=2026.12.06&scheduleType=request`
- 응답: `result.reqList`, `result.timeList`, `result.academicList` 포함. 전문가 특강 3건 `academicList` 확인.
- 판정: 문서화 가능.

### T-003 `scheduleAllList` 파라미터 변형 브라우저 JS 재호출
- 시도: `scheduleType=request+searchType=T`, `request`, `all`, 빈값, 미지정.
- 결과: 모두 `403 {"code":403,"message":"CSRF_TOKEN_INVALID"}`.
- 판정: **CSRF 제약으로 변형 유효성 미확인**. 화면 조작으로 자연 발생 요청만 신뢰.

### T-004 주요 페이지 순회 API 발견
- `/main`, `/notice/noticeGoList`, `/policy/policyGoList`, `/studygroup/studygroupGoList`, `/learning/learningProgress/list`, `/ev/request/evalutionRequestList`, `/ev/request/evalutionRequestWrite`, `/ev/participation/evalutionParticipationList` 순회.
- 판정: 공통/공지/알림/학습/포인트/소모임/평가요청 API 관찰.

### T-005 평가 참여 전용 목록/상세 API (미완료)
- 화면: 참여 목록 4건 노출(`mtlEvlSn-*`, 예정/시작/결과 PASS·FAIL/평가완료).
- 현재 캡처: 전용 참여 목록 엔드포인트 미확정(캘린더 `scheduleAllList`의 A패턴, `mtlEvlTxnDtoByPkList`는 요청 작성 흐름에서 관찰).
- 다음: 참여 페이지 재캡처, 항목 클릭/상세 진입 시 읽기 API 관찰(상태 변경 버튼 실행 금지).

### T-006 로그인 API (미확인)
- 관찰: 로그인 후 API에 `X-Requested-With`, `X-XSRF-TOKEN` 사용. CSRF 오류 재현.
- 미확인: 로그인 엔드포인트 URL/Method/body, CSRF 발급, SSO/MFA/CAPTCHA, `Set-Cookie` 계약(값 미수집).
- 다음: 사용자가 로그아웃 상태에서 로그인 흐름 네트워크를 마스킹 수집하면 `doc_api_codyssey_auth.md` 보강.

## 미지/후속 TODO
- [ ] 평가 참여 목록/상세 전용 API 확정.
- [ ] 학사일정/특강 상세/신청 관련 읽기 API(있다면).
- [ ] 공지 상세 API.
- [ ] 알림 목록 API(카운트 외).
- [ ] `scheduleType`/기간/월 이동/필터의 실제 UI 파라미터 추가 수집.
- [ ] 전문가 평가 유형 실데이터/API 확인.
- [ ] ID 불변성/취소·삭제 표현 규칙(변경 이벤트 관찰 필요).
- [ ] 로그인/로그아웃/토큰 갱신 API 계약(승인된 마스킹 캡처 필요).
