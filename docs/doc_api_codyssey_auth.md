# Codyssey 인증/로그인/세션 API 검토 (미확인)

## 결론
로그인 API의 **안전한 구현 가능 여부를 현재 관찰만으로 확정할 수 없음**. 로그인 화면 네트워크(로그아웃 상태, 마스킹 캡처)가 필요하다.

## 관찰된 사실
- `usr.codyssey.kr`는 SPA. 비인증 접근 시 일정/화면 데이터 대신 HTML 셸이 반환될 수 있음.
- 로그인 후 데이터 API 호출에서 `X-Requested-With: XMLHttpRequest`, `X-XSRF-TOKEN: <redacted>` 관찰.
- 브라우저 컨텍스트에서 `scheduleAllList`를 임의 재호출했을 때 `403 {"code":403,"message":"CSRF_TOKEN_INVALID"}` 발생 → CSRF 보호 정황.
- 데이터 API는 `api.usr.codyssey.kr`를 사용.

## 미확인 항목
| 항목 | 왜 필요한가 |
|---|---|
| 로그인 엔드포인트(URL/Method) | 인증 시작점 |
| 로그인 요청 body 계약 | ID/PW 외 추가 필드(OTP, captcha, device, redirect 등) |
| 로그인 전 CSRF/쿠키 발급 | 자동화 가능 여부 |
| 성공/실패 응답 | 세션 쿠키 설정, 에러 코드, 잠금/지연 |
| SSO/OAuth/MFA/CAPTCHA | 일반 폼 로그인 API 단순 재현 불가 가능성 |
| 로그아웃/세션 갱신/토큰 갱신 | 장기 실행 동기화 안정성 |
| 이용약관/권한 범위 | 승인되지 않은 자동화 위험 |

## 보안/준수 제약
- 비밀번호, OTP, 쿠키, 세션, `X-XSRF-TOKEN` 실값을 저장/공유/커밋하지 않는다.
- 캡차 우회, 인증 우회, 무차별 대입, 속도 제한 회피를 시도하지 않는다.
- 사용자 본인 계정/권한 범위 내 읽기 관찰만 수행한다.

## 다음 테스트(사용자 제공 필요)
1. 로그아웃 상태에서 로그인 페이지 진입.
2. DevTools Network에서 로그인 제출 요청 캡처.
3. 마스킹 후 공유: URL, Method, Request headers(토큰값 제외), Request body 필드명/shape(비밀번호 제외), Response status/body shape, `Set-Cookie` **이름만**.
4. 로그인 직후 `api.usr.codyssey.kr` 첫 호출의 인증 헤더/쿠키 필요 조건 정리.

## 자동 연동 대안(검토)
- 공식 지원 API/SSO가 있으면 우선 사용.
- 브라우저 세션을 사람이 유지하고, 사용자가 명시적으로 승인한 범위에서 읽기 전용 데이터 추출(토큰 저장 금지/단기 세션).
- 서버 자동 로그인은 보안·정책·CSRF·MFA 리스크가 크므로 확정 근거 전에는 구현하지 않음.
