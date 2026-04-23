---
name: Feature Task
title: "[Feature] PAR-05: 크래시/OOM 발생 시 로컬 로그 기록 시스템 구축"
labels: 'feature, priority:medium, parser'
depends_on: 'INF-01'
---

## 🎯 Summary
- 기능명: [PAR-05] 크래시/OOM 발생 시 오류 코드 및 타임스탬프 로컬 로그 파일 기록 시스템
- 목적: 파서 엔진에서 치명적인 오류(Out of Memory 등)가 발생하여 강제 종료될 경우, 디버깅을 위한 최소한의 로그를 로컬 파일 시스템에 기록한다.

## 🔗 References (Spec & Context)
- SRS 문서: 4.1 F5 (REQ-FUNC-014: 로컬 로그 기반 오류 복구)

## 📋 Task Breakdown
- [ ] FastAPI 내 글로벌 Exception Handler 및 Python `logging` 모듈 설정
- [ ] 예외 발생 시 스택 트레이스, 발생 시각, 오류 코드를 JSON 형식으로 포맷팅하는 로직 구현
- [ ] 파일 입출력을 통해 `./logs/parser_error.log` 에 덧붙이기(Append) 쓰기 로직 작성 (컨테이너 볼륨에 마운트되도록 구성)
- [ ] 메모리 부족(MemoryError) 감지를 위해 가벼운 리소스 모니터링 데몬 스레드 추가 (선택 사항)

## ✅ Acceptance Criteria
- **Scenario 1: 코드 레벨 크래시 발생 시 로컬 파일 기록**
  - **Given** FastAPI 엔진이 정상 작동 중일 때
  - **When** 의도적인 코드 크래시(예: `raise Exception("test crash")`)가 발생하면
  - **Then** 지정된 로컬 로그 파일 경로에 해당 에러의 발생 시각과 상세 코드가 정상적으로 Append 되어야 한다.
- **Scenario 2: 하드 OOM(Out of Memory) 킬 방어 또는 로깅**
  - **Given** 단일 문서 파싱 중 메모리 사용량이 컨테이너 제한(Limit)에 도달할 때
  - **When** 운영체제(Docker)가 프로세스를 강제 종료(OOM Kill)하기 직전 혹은 직후에
  - **Then** 메모리 모니터링 데몬이 이를 사전에 감지하여 로컬 로그를 남기거나, 불가능할 경우 Docker의 자동 재시작(`restart_policy`) 이벤트 로그를 통해 원인을 추적할 수 있도록 체계를 갖춰야 한다.

## 🚧 Constraints
- 모니터링 제약: 외부 SaaS형 로깅/경보 시스템(PagerDuty, Datadog 등) 연동은 절대 금지되며 오직 로컬 텍스트 파일로만 남겨야 한다.
