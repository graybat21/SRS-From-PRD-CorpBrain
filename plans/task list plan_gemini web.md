제공해주신 CorpBrain MVP의 소프트웨어 요구사항 명세서(SRS)를 상세히 분석했습니다. 

시니어 Technical Project Manager 및 System Architect의 관점에서, 100% 온프레미스 망분리 요건, Next.js + FastAPI 투트랙 기술 스택, 그리고 UI/UX 디자인과 프론트엔드/백엔드 개발의 분리 원칙을 모두 반영하여 실행 가능한 최소 단위(Feature)로 태스크를 분리했습니다.

아래는 요구사항을 기반으로 도출된 개발 태스크 백로그(Backlog)입니다.

### CorpBrain MVP Task Breakdown List

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 | 복잡도 |
|---|---|---|---|---|---|
| **INF-01** | Infrastructure | Next.js(FE/BE) 및 FastAPI(Parser) 기본 프로젝트 세팅 | 1.5 C-TEC-001, C-TEC-002 | None | L |
| **INF-02** | Infrastructure | 외부 클라우드 원천 차단을 위한 Prisma + 로컬 SQLite DB 스키마 구성 | 1.5 C-TEC-003, 6.2 Entity & Data Model | INF-01 | L |
| **INF-03** | Infrastructure | 단일 서버용 Docker Compose 기반 배포 스크립트 작성 및 restart_policy 구성 | 1.2 Scope, 4.2 REQ-NF-009, REQ-NF-012 | INF-01 | M |
| **DES-01** | UI/UX Design | Tailwind + shadcn/ui 기반 문서 업로드, 하이라이터 화면, 데이터 Export 등 사용자 인터페이스 설계 | 1.5 C-TEC-004, 3.2 Client Applications | None | M |
| **BE-01** | Backend (Next.js) | 수동 파일 업로드 수신 및 FastAPI로의 바이너리 전달 API 구성 (`POST /api/v1/clean`) | 3.3 API Overview, 6.1 | INF-02 | M |
| **BE-02** | Backend (Next.js) | 하이라이트 셀 덮어쓰기 API (`PATCH /api/v1/nodes/{node_id}`) 및 이벤트 로그 기록 | 3.3 API Overview, 4.1 F2 | INF-02 | M |
| **BE-03** | Backend (Next.js) | 문서 검수 최종 승인 API (`PATCH /api/v1/documents/{doc_id}/validate`) | 3.3 API Overview, 4.1 F2 | INF-02 | L |
| **BE-04** | Backend (Next.js) | `validation_passed=true` 문서 대상 JSON/CSV 포맷 변환 및 내보내기 API (`GET /api/v1/export`) | 3.3 API Overview, 4.1 F4 | BE-03 | M |
| **PAR-01** | Parser Core (FastAPI) | 10초 내 손상/난독 파일 판별 및 메모리 누수 방지용 Graceful Shutdown 로직 | 4.1 REQ-FUNC-003 | INF-01 | M |
| **PAR-02** | Parser Core (FastAPI) | 100% 로컬 구동 Rule-based + sLLM 앙상블 파서 엔진 파이프라인 구축 (아웃바운드 0 Byte) | 4.1 REQ-FUNC-001, REQ-FUNC-004 | INF-01 | H |
| **PAR-03** | Parser Core (FastAPI) | A 법무법인 하도급 표준 계약서 1종 맞춤형 표/셀 병합 파싱 로직 적용 (TEDS-Struct 99.9 목표) | 1.5 CON-01, 4.1 REQ-FUNC-005 | PAR-02 | H |
| **PAR-04** | Parser Core (FastAPI) | 추출된 각 Data Node별 0~100% Confidence Score 산출 모듈 구현 | 4.1 REQ-FUNC-006 | PAR-02 | M |
| **PAR-05** | Parser Core (FastAPI) | 크래시/OOM 발생 시 오류 코드 및 타임스탬프 로컬 로그 파일 기록 시스템 | 4.1 REQ-FUNC-014 | INF-01 | L |
| **PAR-06** | Parser Core (FastAPI) | KISA 기준 PII 자동 탐지 및 위치 반환 기능 (탐지율 99% 이상) (Could Have) | 4.1 REQ-FUNC-010 | PAR-02 | H |
| **PAR-07** | Parser Core (FastAPI) | 탐지된 PII 대상 문서당 100ms 이내 AES-256 오토 마스킹 모듈 (Could Have) | 4.1 REQ-FUNC-011 | PAR-06 | M |
| **FE-01** | Frontend (Next.js) | 수동 파일 업로드 UI 컴포넌트 및 파싱 상태/오류 코드 안내 창 구현 | 3.2 Client Applications, 4.1 F1 | DES-01, BE-01 | L |
| **FE-02** | Frontend (Next.js) | 엔진에서 반환된 JSON 기반 구조화 데이터(표 및 텍스트) Viewer 렌더링 | 3.2 Client Applications, 4.1 F1 | DES-01, BE-01 | H |
| **FE-03** | Frontend (Next.js) | Confidence Score 80% 미만 구간 0.5초 이내 붉은색 하이라이터 UI 렌더링 | 4.1 REQ-FUNC-007 | FE-02, PAR-04 | M |
| **FE-04** | Frontend (Next.js) | 하이라이트 된 셀 수동 덮어쓰기(Override) 인풋 박스 및 이벤트 연동 | 4.1 REQ-FUNC-008 | FE-02, BE-02 | M |
| **FE-05** | Frontend (Next.js) | 덮어쓰기 검수 완료 후 최종 검수 승인 버튼 및 성공 상태 전환 UI | 4.1 REQ-FUNC-009 | FE-02, BE-03 | L |
| **FE-06** | Frontend (Next.js) | 승인 완료 데이터 대상 Export (JSON/CSV 선택) 다운로드 버튼 UI 연동 | 4.1 F4 | DES-01, BE-04 | L |

이 백로그는 문서 내의 기능(F1~F5)과 기술적 제약사항(C-TEC)을 모두 반영하였습니다. 기획서에 "Could Have"로 명시된 PII 자동 마스킹 기능(PAR-06, PAR-07)도 우선순위를 고려하여 후순위 배치 시 쉽게 분리할 수 있도록 명시해 두었습니다. 

이 리스트를 Jira 등 이슈 트래커에 바로 임포트하여 스프린트 계획을 수립하시면 됩니다. 개발팀에 전달하기 전, 백로그 레벨에서 추가로 분할이 필요해 보이는 복잡도 높은 태스크(예: PAR-02, PAR-03)가 있다면 말씀해 주십시오.