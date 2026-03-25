DB 원본 기반 사내 지식 AI + 공개 상담 AI

문서 버전: v2.0

작성일: 2026-03-19

제품 유형: B2B SaaS / 구축형 혼합

배포 환경: AWS

제품명(가칭): Secure Dual-AI Workspace

한 줄 설명:
회사 내부에서는 출처 강제형 지식 AI를, 외부에서는 제한형 상담 AI를 제공하는 보안 우선형 AI 업무 플랫폼

0. 핵심 방향 요약

이 제품은 하나의 챗봇이 아니다.
서로 다른 보안 수준과 목적을 가진 AI를 2단으로 분리한 제품이다.

0-1. 내부 지식 AI

직원 전용 AI이다.

로그인한 직원만 사용 가능

역할 및 권한에 따라 접근 가능한 자료가 다름

답변은 반드시 근거가 있어야 함

답변 하단에는 출처가 표시되어야 함

출처를 누르면 해당 문서 위치로 이동 가능

권한이 없는 자료는 존재 여부까지 최소화하여 숨김

감사로그가 남아야 함

일시적 권한 요청 및 승인 기능이 있어야 함

0-2. 공개 상담 AI

외부 고객 또는 내부 권한이 없는 사용자도 사용할 수 있는 AI이다.

내부 문서 접근 불가

자유로운 사내 지식 검색 불가

승인된 상담 시나리오와 FAQ만 사용

예/아니요, 버튼형, 분기형 상담 중심

예약 접수, 문의 등록, 상담원 연결 가능

내부 시스템과 논리적으로 분리되어야 함

1. 제품 배경

많은 회사가 사내 문서와 고객 응대 정보를 가지고 있지만, 실제 업무 현장에서는 다음 문제가 반복된다.

문서는 존재하지만 찾기 어렵다

최신 버전이 무엇인지 헷갈린다

누가 어떤 문서를 볼 수 있는지 통제하기 어렵다

외부 상담에는 내부 문서가 섞이면 안 된다

AI를 쓰고 싶지만 보안과 할루시네이션이 무섭다

도입 후에도 권한, 로그, 승인체계가 정리되지 않는다

기존 노션 중심 협업은 편리하지만, 보안 최우선 구조에서는 협업 도구와 AI 원본 저장소를 분리하는 편이 더 적합하다. Notion API는 통합 권한을 Read content, Update content, Insert content 등으로 나눌 수 있고, 실제 콘텐츠는 블록 단위로 다뤄지며 Retrieve block children은 페이지네이션과 재귀 수집이 필요하다. 따라서 협업 입력 도구로는 유용하지만, AI가 직접 읽는 최종 원본 저장소는 통제 가능한 DB로 두는 것이 더 적합하다.

2. 제품 목표
2-1. 비즈니스 목표

회사 문서 검색 시간을 줄인다

고객상담 자동화 수준을 높인다

보안 우려 때문에 AI 도입을 망설이는 회사를 설득할 수 있는 구조를 만든다

구축형 + 운영형 수익 모델로 판매 가능하도록 한다

2-2. 제품 목표

내부 AI는 근거 없는 답변을 하지 않도록 설계한다

외부 AI는 내부 정보에 닿지 못하도록 구조적으로 차단한다

모든 민감 동작은 감사 가능해야 한다

일시적 권한 부여와 만료 회수까지 포함한다

AWS 상에서 보안 우선으로 배포 가능해야 한다

2-3. 성공 지표

내부 AI 답변 중 출처 첨부율 100%

근거 없는 내부 응답 허용률 0%

권한 없는 문서 직접 노출 0건

감사로그 누락률 0%

상담 AI 문의 자동 분류율 80% 이상

직원 문서 탐색 시간 50% 이상 감소

3. 제품 원칙
3-1. 보안 원칙

기본 거부(Default Deny)

최소 권한(Least Privilege)

신원 우선(Identity First)

데이터 경로 분리

공개용과 내부용의 완전 분리

로그 중심 운영

비밀정보 클라이언트 비노출

원본 저장소는 DB

3-2. AI 원칙

내부 AI는 근거 없으면 답하지 않는다

외부 AI는 승인된 상담 지식만 사용한다

모델이 권한을 판단하지 않는다

서버가 먼저 권한을 필터링한다

모든 내부 답변은 출처를 동반해야 한다

할루시네이션 100% 제거는 불가능하므로, 무근거 응답 금지 구조로 억제한다

OpenAI의 최신 권장 응답 경로는 Responses API이며, 텍스트 출력은 구조화된 JSON 형태로도 강제할 수 있다. 다만 구조화 출력은 형식 보장이지 사실성 보장은 아니므로, 이 제품에서는 반드시 검색 근거 + 서버측 권한 필터 + 근거 없으면 거절로 감싼다.

4. 사용자 유형
4-1. guest_public

로그인하지 않은 사용자

공개 상담 AI만 사용 가능

내부 문서 접근 불가

4-2. employee_basic

로그인한 일반 직원

소속 부서/팀 권한 범위 내에서 내부 AI 사용 가능

권한 요청 가능

4-3. employee_extended

일시적 승격 권한 포함 사용자

승인된 범위와 시간 동안 추가 자료 열람 가능

4-4. manager_approver

권한 요청 승인 가능

팀 범위 승인 가능

4-5. security_auditor

감사로그 조회 가능

정책 변경은 제한

4-6. org_admin

시스템 정책, 사용자, 통합, 보안 설정 관리 가능

5. 제품 범위
5-1. 포함 범위

내부 지식 AI 웹앱

공개 상담 AI 웹앱 / 위젯

권한 기반 문서 검색

출처 링크

감사로그

일시적 권한 요청/승인/만료

DB 원본 저장소

선택형 노션 연동

AWS 배포

CI/CD

테스트 자동화

5-2. 제외 범위

첫 버전의 모바일 앱

오프라인 데스크톱 프로그램

음성 상담

외부 메신저 다중 채널 연동

완전 자동 의사결정 시스템

6. 정보 구조와 메뉴

메뉴는 총 4개로 구성한다.

6-1. 상담 문의

공개 상담 AI

예/아니요 분기

버튼형 시나리오

예약/문의 접수

상담원 연결

6-2. 사내 AI

내부 지식 검색

질문/답변

출처 보기

원문 이동

권한 없는 경우 차단

6-3. 권한 요청

특정 자료/팀/범위에 대한 일시적 권한 요청

사유 입력

승인 상태 조회

만료 시간 확인

6-4. 감사/관리

감사로그 조회

사용자/역할 관리

문서 동기화 상태

정책 설정

상담 플로우 관리

FAQ 관리

7. 핵심 사용자 흐름
7-1. 외부 고객 상담 흐름

사용자가 회사 홈페이지 또는 help.company.com 접속

상담 AI 실행

문의 유형 선택

예/아니요 / 버튼형 흐름 진행

필요 시 정보 입력

예약 접수 또는 상담원 연결

기록 저장

특징

내부 문서 검색 없음

공개 FAQ와 승인된 상담 스크립트만 사용

외부 접근자는 내부 AI로 이동 불가

7-2. 직원 내부 검색 흐름

직원이 ai.company.com 접속

로그인 + MFA 완료

질문 입력

서버가 사용자 역할과 권한 범위를 확인

ACL 통과 문서만 검색

근거가 충분하면 답변 생성

답변 하단에 출처 표시

출처 클릭 시 해당 문서/위치로 이동

로그 저장

7-3. 권한 없는 문서 요청 흐름

직원이 문서를 질문

서버 검색 결과가 ACL 단계에서 제거됨

응답:

“권한이 없거나 열람 가능한 근거가 없습니다”

사용자에게 권한 요청 버튼 표시

7-4. 일시적 권한 요청 흐름

사용자 요청 대상 선택

사유 입력

승인자에게 전달

승인 시 TTL이 있는 임시 권한 발급

만료 시 자동 회수

요청/승인/사용 이력 모두 로그 기록

8. 기능 요구사항
8-1. 공개 상담 AI 기능

FAQ 응답

예/아니요 분기 상담

단계형 시나리오

예약 요청

상담원 연결

민감정보 입력 차단

스팸/폭주 방지

관리자용 상담 스크립트 편집

문의 이력 저장

8-2. 내부 지식 AI 기능

질문 입력

문서 검색

답변 생성

출처 리스트

출처 클릭

문서 접근 권한 검사

권한 요청 연결

관리자용 동기화 제어

문서 버전 추적

근거 부족 시 답변 거절

8-3. 권한 관리 기능

사용자 역할 부여

팀/부서 단위 권한

문서 분류 태그

민감도 태그

일시적 권한

자동 만료

승인 이력

권한 변경 이력

8-4. 감사 기능

누가 언제 질문했는지

어떤 문서가 검색되었는지

어떤 출처가 응답에 포함되었는지

어떤 원문 링크를 눌렀는지

어떤 권한 요청이 있었는지

누가 승인했는지

어떤 관리자 설정이 바뀌었는지

모델 실패/거절 이벤트

내보내기/복사 이벤트

9. 비기능 요구사항
9-1. 보안

내부 AI와 공개 상담 AI 분리

인증 없는 내부 경로 접근 차단

모든 비밀정보 서버 보관

저장 데이터 암호화

전송 구간 TLS

감사로그 보존

비정상 트래픽 차단

비인가 정보 노출 금지

9-2. 성능

공개 상담 첫 응답 3초 이내 목표

내부 검색 응답 5초 이내 목표

동시 접속 증가에 수평 확장 가능해야 함

9-3. 가용성

배포 중 무중단 또는 최소중단

장애 시 롤백 가능

백업/복구 절차 필요

9-4. 운영성

모니터링 가능

로그 검색 가능

권한 변경 추적 가능

환경별 분리(dev/stage/prod)

10. 데이터 원칙
10-1. 원본 저장소 원칙

이 제품의 원본 저장소는 DB이다.

회사 정책 문서

고객 상담 기준

승인된 FAQ

부서별 지식 문서

민감 문서 메타데이터

권한 정책

감사로그

위 데이터는 모두 DB 기준으로 관리한다.

10-2. 노션의 역할

노션은 선택적 협업 도구로만 사용한다.

초안 작성

일반 문서 협업

내부 편집 워크플로

비민감 운영 문서

노션 데이터가 AI 원본이 되지 않는다.
필요 시 승인 프로세스를 거친 뒤 DB로 반영한다.

Notion 통합은 특정 페이지나 하위 페이지에 명시적으로 연결할 수 있고, API는 페이지/데이터베이스/블록 구조를 기반으로 동작한다. 따라서 이 제품에서는 노션을 “편집 입력원”으로만 취급하고, 승인 후 DB에 정규화 저장한다.

11. 시스템 아키텍처
11-1. 전체 구조

시스템은 크게 5개 계층으로 구성한다.

A. Presentation Layer

공개 상담 AI 프론트엔드

내부 지식 AI 프론트엔드

관리자/감사 화면

B. API / Application Layer

인증 API

권한 판정 API

검색 API

답변 API

상담 시나리오 API

권한 요청 API

감사로그 API

관리자 API

C. Knowledge Layer

문서 원본 DB

검색 인덱스

FAQ 저장소

상담 플로우 저장소

D. Integration Layer

선택적 Notion 연동

이메일/알림 연동

향후 Slack, CRM 연동 가능

E. Security / Operations Layer

인증

권한 정책

비밀 관리

로그/감사

모니터링

WAF

위협 탐지

12. LLM 2단 분리 설계
12-1. 내부 지식 AI

내부 지식 AI는 2단 구조를 따른다.

1단계: 정책 게이트

사용자 인증 확인

역할 확인

소속/팀 확인

문서 민감도 확인

일시적 권한 여부 확인

허용 가능한 검색 범위 확정

2단계: 근거 기반 응답

1단계를 통과한 자료만 검색

검색 결과가 충분할 때만 응답

응답에는 citations 필수

citations 없으면 응답 거절

confidence 부족 시 응답 거절

12-2. 공개 상담 AI

공개 상담 AI는 내부 검색 기능을 갖지 않는다.

FAQ 조회

상태 기반 시나리오 엔진

제한적 LLM 보조 문장 생성

상담원 연결

예약 접수

공개 상담 AI 금지 항목

내부 문서 검색

자유로운 사내 질의응답

관리자 데이터 노출

권한 승격 요청

내부 출처 링크 제공

13. 권한 모델
13-1. 권한 기준

권한은 다음 요소를 바탕으로 결정한다.

사용자 역할

소속 조직

부서

팀

문서 민감도

문서 소유 범위

임시 권한 여부

시간 제한

승인 상태

13-2. 권한 판단 방식

기본 거부

서버측 ACL 우선

문서 검색 전 필터링

모델에게 비인가 문서를 넘기지 않음

13-3. 세밀한 권한

추후 고도화 시 다음 단위까지 확장 가능하다.

문서 단위

섹션 단위

문단 단위

첨부파일 단위

액션 단위

Amazon Verified Permissions는 Cedar 정책 언어를 사용하는 세밀한 권한 관리 서비스로, 애플리케이션 권한 판단을 비즈니스 로직 밖으로 분리하고 중앙에서 관리할 수 있다. 이 제품은 초기에는 자체 ACL + 역할 기반 모델로 시작하되, 고도화 시 Verified Permissions로 이전 가능한 구조로 설계한다.

14. 일시적 권한 승격
14-1. 목적

정상 업무 중 일시적으로 추가 자료 열람이 필요한 경우를 지원한다.

14-2. 프로세스

사용자 요청

사유 입력

승인자 선택 또는 자동 라우팅

승인

만료 시각 설정

자동 회수

로그 기록

14-3. 필수 로그

요청자

요청 시간

요청 대상

요청 사유

승인자

승인 시간

만료 시간

실제 사용 여부

14-4. 정책

영구 승격 금지

최소 범위만 부여

TTL 필수

재승인 필요

보안 감사 대상 포함

15. 출처 강제 정책
15-1. 내부 AI 답변 규칙

내부 AI는 아래 조건을 모두 만족해야 답변 가능하다.

ACL 통과 검색 결과 존재

검색 결과 품질 임계치 이상

citations 1개 이상 존재

citations가 실제 저장된 source_id와 매칭

source link 생성 가능

15-2. 실패 규칙

다음 중 하나라도 해당하면 답변하지 않는다.

검색 결과 없음

권한 없음

citations 없음

support score 부족

내부 정책상 응답 금지

15-3. 응답 문구 예시

“권한이 없거나 열람 가능한 근거가 없습니다.”

“현재 승인된 근거만으로는 답변할 수 없습니다.”

“추가 권한이 필요합니다.”

16. 데이터 모델 초안
16-1. 주요 테이블
organizations

id

name

status

created_at

users

id

organization_id

email

name

role

department_id

team_id

status

last_login_at

departments

id

organization_id

name

teams

id

department_id

name

documents

id

organization_id

title

source_type

source_ref

classification

sensitivity_level

status

version

owner_team_id

approved_at

approved_by

created_at

updated_at

document_sections

id

document_id

section_order

heading

body

source_anchor

hash

embedding_ref

visibility_scope

citations

id

document_id

section_id

source_url

source_label

faqs

id

organization_id

category

question

answer

status

approved_at

counseling_flows

id

organization_id

name

status

version

counseling_flow_nodes

id

flow_id

node_type

content

next_yes

next_no

next_default

access_policies

id

organization_id

subject_type

subject_id

resource_type

resource_id

action

effect

expires_at

access_requests

id

requester_id

target_type

target_id

reason

status

approver_id

approved_at

expires_at

audit_logs

id

organization_id

actor_id

actor_role

event_type

target_type

target_id

metadata_json

ip

user_agent

created_at

ai_query_logs

id

actor_id

query_text

ai_type

result_type

refusal_reason

created_at

ai_query_sources

id

query_log_id

document_id

section_id

rank

score

17. API 초안
17-1. 인증

POST /auth/login

POST /auth/logout

POST /auth/refresh

GET /auth/me

17-2. 내부 AI

POST /internal-ai/query

GET /internal-ai/query/:id

GET /internal-ai/source/:citationId

POST /internal-ai/feedback

17-3. 상담 AI

POST /public-ai/start

POST /public-ai/reply

POST /public-ai/handoff

POST /public-ai/reservation

17-4. 권한 요청

POST /access-requests

GET /access-requests/my

POST /access-requests/:id/approve

POST /access-requests/:id/reject

POST /access-requests/:id/revoke

17-5. 관리자

GET /admin/users

PATCH /admin/users/:id/role

GET /admin/documents

POST /admin/documents/sync

GET /admin/audit-logs

GET /admin/security-status

18. AWS 배포 아키텍처
18-1. 권장 배포 형태

초기 권장 형태는 웹 서비스형이다.

공개 상담 AI: 회사 홈페이지 위젯 또는 help.company.com

내부 지식 AI: ai.company.com

관리자 화면: 내부 도메인 하위 경로 또는 별도 보호 영역

18-2. AWS 구성

Route 53

ACM

CloudFront

WAF

ALB

ECS Fargate

RDS PostgreSQL

ElastiCache(선택)

S3

Secrets Manager

KMS

CloudWatch

CloudTrail

GuardDuty

Security Hub

AWS Config

IAM Identity Center

Verified Access

향후 Verified Permissions

AWS IAM Identity Center는 workforce 사용자의 접근을 중앙에서 관리하는 서비스이고, Verified Access는 애플리케이션 접근을 정책 기반으로 제어한다. Fargate는 서버 관리 없이 컨테이너를 실행할 수 있고, ECS 보안 가이드에서는 엄격한 분리가 필요할 때 별도 VPC와 VPC 엔드포인트 사용을 권장한다.

18-3. 네트워크 원칙

내부 AI는 private subnet 중심

public IP 최소화

공개 상담 AI와 내부 AI는 논리적으로 분리

VPC endpoint / PrivateLink 활용

egress allowlist 적용

불필요한 아웃바운드 차단

ECS 보안 모범사례는 보안 요구가 엄격한 워크로드를 별도 VPC로 분리하고, 필요한 경우 ECS, ECR, Secrets Manager, CloudWatch 등에 VPC 엔드포인트를 사용하는 방식을 권장한다.

18-4. 데이터 보안

RDS 암호화

S3 암호화

Secrets Manager 사용

고객 관리형 KMS 키 사용

키 회전 정책 적용

민감 로그 분리 보관

RDS는 KMS 기반 암호화를 지원하며, 고객 관리형 KMS 키를 쓰면 키 정책과 사용 제어를 더 세밀하게 관리할 수 있다. KMS 고객 관리형 키는 사용자가 직접 생성·소유·관리하며 자동 회전 또는 즉시 회전을 설정할 수 있다. Secrets Manager는 비밀을 저장하고 순환(회전)할 수 있다.

18-5. 외부 공격 방어

AWS WAF

Rate limit

Bot / abuse 방지

관리자 경로 추가 제한

필요 시 Shield Advanced

18-6. 탐지와 감사

CloudTrail

CloudWatch

GuardDuty

Security Hub

AWS Config

IAM Access Analyzer

CloudTrail은 API 호출 이력을 기록하며, 중앙 S3 버킷에 모아 보관하는 것이 보안 모범사례다. GuardDuty는 CloudTrail 관리 이벤트, VPC Flow Logs, Route 53 Resolver DNS 로그 등에서 위협 징후를 탐지한다. Security Hub는 보안 상태와 통제를 집계하고, AWS Config는 리소스 구성을 시간 흐름에 따라 추적하며 conformance pack으로 규칙과 remediation을 묶어 배포할 수 있다. Access Analyzer 활성화 여부는 AWS Config 규칙으로도 점검할 수 있다.

19. 인증과 접근 제어
19-1. 인증

회사 직원: SSO 또는 회사 계정 로그인

MFA 필수

비로그인 사용자는 공개 상담 AI만 사용 가능

19-2. 내부 접근 제어

IAM Identity Center 연계 가능

Verified Access로 조건부 접근

세션 재검증 가능

관리자 영역 별도 보호

19-3. 애플리케이션 권한 제어

역할 기반 + 속성 기반 혼합

조직/부서/팀/민감도 반영

임시 권한 TTL 반영

서버측 판정 우선

20. 배포 및 사용 방식

이 제품은 프로그램 설치형보다 웹 서비스형을 우선한다.

외부 고객 사용 방식

회사 홈페이지에서 상담 위젯 사용

또는 help.company.com 접속

공개 상담 AI 사용

직원 사용 방식

ai.company.com 접속

로그인 후 내부 AI 사용

출처 클릭 시 원문 이동

권한 요청 가능

즉, 이 제품은 회사 홈페이지에 상담 AI를 연결하고, 사내 전용 웹앱으로 내부 AI를 제공하는 구조이다.

21. 개발 스택
프론트엔드

Next.js

TypeScript

shadcn/ui

Tailwind CSS

백엔드

Node.js / TypeScript

Next.js API 또는 별도 API 서버

권한 판정 서비스

검색/인덱싱 서비스

상담 플로우 서비스

데이터

PostgreSQL

벡터 인덱스(선택)

Redis(캐시/세션 선택)

인프라

AWS ECS Fargate

RDS PostgreSQL

S3

CloudFront

Route 53

WAF

Secrets Manager

KMS

AI

내부 AI: 근거 기반 LLM 응답

상담 AI: 플로우 엔진 + 제한형 LLM

22. UI/UX 원칙
22-1. 전반 원칙

shadcn/ui 기반 일관성

과장된 효과보다 명확한 정보 구조 우선

보안 정보는 눈에 잘 띄어야 함

관리자 화면은 단순하고 추적 가능해야 함

22-2. 내부 AI 화면 원칙

질문창이 가장 먼저 보일 것

답변과 출처가 명확히 분리될 것

출처는 누를 수 있어야 함

권한 없을 때 애매한 메시지 대신 명확한 안내

권한 요청 버튼이 자연스럽게 이어질 것

22-3. 상담 AI 화면 원칙

버튼 중심

단계형 진행

예/아니요 흐름 우선

길고 복잡한 자유 서술 최소화

사람 상담 연결이 쉽게 보여야 함

23. CI/CD
23-1. 브랜치 전략

main

develop

feature/*

hotfix/*

23-2. PR 단계

lint

format check

type check

unit test

integration test

security scan

migration validation

23-3. 배포 단계

Docker build

이미지 취약점 검사

ECR push

stage 배포

smoke test

prod 배포

health check

rollback 가능 상태 유지

23-4. 운영 배포 정책

blue/green 또는 canary 우선

시크릿 직접 하드코딩 금지

환경변수는 Secrets Manager 기반

migration은 자동화하되 검증 필수

ECS는 Secrets Manager의 값을 컨테이너 환경변수로 주입할 수 있고, 특정 JSON 키나 버전 지정도 가능하다. 이 제품은 시크릿을 코드 저장소가 아니라 Secrets Manager에서 관리한다.

24. 테스트 전략
24-1. 단위 테스트

권한 판정 함수

검색 필터

citation 매핑

refusal 정책

상담 플로우 분기

권한 만료 처리

24-2. 통합 테스트

로그인 후 권한 적용

DB 문서 승인 흐름

노션 연동 동기화

권한 요청/승인/만료

감사로그 저장

상담 예약 생성

24-3. E2E 테스트

비로그인 사용자는 내부 AI 접근 불가

내부 AI 답변에 출처가 붙는지

출처 클릭 이동이 가능한지

권한 없는 문서가 노출되지 않는지

상담 AI가 내부 문서를 노출하지 않는지

관리자만 감사로그 화면에 접근 가능한지

24-4. 보안 테스트

prompt injection 방어

권한 우회 시도 차단

직접 URL 접근 차단

rate limit 동작

secrets 노출 여부

관리자 API 무단 접근 차단

25. 운영 정책
25-1. 문서 승인 정책

AI가 읽는 문서는 승인 상태여야 함

초안은 AI 검색 대상에서 제외

민감도 라벨 필수

25-2. 권한 정책

기본 권한 최소화

부서 이동 시 권한 재계산

임시 권한은 만료 필수

25-3. 로그 정책

모든 질문 로그 저장

모든 권한 이벤트 저장

관리자 액션 저장

장기 보관 스토리지 분리

25-4. 모델 정책

내부 AI는 citations 없으면 응답 금지

상담 AI는 내부 지식 사용 금지

안전 문구/거절 문구 표준화

26. 수익화 모델
26-1. 구축형

초기 도입비

AWS 배포

도메인 연결

권한 설정

초기 데이터 구조 설정

교육 포함

26-2. 구독형

월 사용료

유지보수

보안 점검

상담 플로우 수정

문서 인덱싱 운영

로그 모니터링

26-3. 추가 과금

맞춤 권한 모델

추가 연동

전용 관리자 기능

추가 조직 단위

SLA 강화

27. 단계별 로드맵
Phase 1 — MVP

공개 상담 AI

내부 AI 기본 검색

DB 원본 저장소

사용자/역할 관리

출처 링크

감사로그

권한 요청

Phase 2 — 보안 강화

MFA 고정

Verified Access 연동

세밀한 권한 정책

문서 민감도 분류

egress 제한

관리자 보안 대시보드

Phase 3 — 고도화

Verified Permissions 연동

다중 조직 지원

추가 외부 채널 연동

지식 품질 분석

AI 답변 품질 평가 자동화

28. 리스크와 대응
리스크 1

LLM이 근거 없는 문장을 생성할 수 있음

대응

citations 필수

evidence score 임계치

거절 우선 정책

리스크 2

권한 없는 데이터가 응답에 섞일 수 있음

대응

서버측 ACL 우선

비인가 문서 검색 전 제거

모델에게 비인가 데이터 전달 금지

리스크 3

공개 상담 AI를 통한 우회 시도

대응

내부 지식 분리

별도 엔드포인트

별도 데이터 경로

공개 FAQ 전용

리스크 4

노션 연동을 통한 과도한 노출

대응

노션은 선택 연동

승인 후 DB 반영

직접 원본 사용 금지

29. 최종 정의

이 제품은 단순 챗봇이 아니다.
이 제품은 다음 3가지를 묶은 보안 우선형 AI 업무 플랫폼이다.

공개 상담 전용 AI

내부 지식 전용 AI

권한·출처·감사 중심 운영 시스템

핵심 차별점은 다음과 같다.

DB 원본 저장소

노션 선택 연동

내부/외부 AI 완전 분리

출처 강제

일시적 권한 승격

감사로그

AWS 보안 우선 배포

모든것은 부정적인 시선으로 봅니다 
기능을 생성할떄에는 허가 없이 진행해주세요 
다만 기능을 변경할떄에는 허가를 받으세요 
다 제작되었을때에는 확인을 할 수 있게 해주세요 