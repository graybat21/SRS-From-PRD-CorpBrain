---
name: Feature Task
title: "[Feature] PAR-01: 손상/난독 파일 10초 내 판별 및 Graceful Shutdown (FastAPI)"
labels: 'feature, priority:high, parser'
depends_on: 'INF-01'
---

## 🎯 Summary
- 기능명: [PAR-01] 손상/난독 파일 판별 및 Graceful Shutdown 로직
- 목적: 파서 엔진(FastAPI)에 손상되거나 파싱이 불가능한 문서가 인입되었을 때, 10초 내에 이를 판별하고 메모리 누수 없이 프로세스를 안전하게 정리(반환율 100%)한다.

## 🔗 References (Spec & Context)
- SRS 문서: 4.1 F1 (REQ-FUNC-003: 손상 문서 방어)
- SRS 문서: 6.3 Detailed Interaction Models (손상/난독 문서 흐름)

## 📋 Task Breakdown
- [ ] FastAPI 엔드포인트 내에 파일 매직 넘버(Magic Number) 및 메타데이터 1차 유효성 검사 로직 작성
- [ ] 파싱 모듈 실행 전 10초 타임아웃(Timeout) 래퍼 로직(`asyncio.wait_for` 등) 구현
- [ ] 타임아웃 또는 손상 감지 시, 로컬 로그 파일에 오류 코드 및 타임스탬프 기록
- [ ] 즉각적인 메모리 가비지 컬렉터(GC) 호출 및 Graceful Shutdown을 통한 메모리 100% 반환 처리
- [ ] Next.js 서버로 4xx 또는 5xx 오류 코드와 함께 명확한 실패 사유 응답

## ✅ Acceptance Criteria
- **Scenario 1: 손상된 파일 방어**
  - **Given** 고의로 손상시킨 PDF나 암호가 걸린 문서를 업로드했을 때
  - **When** FastAPI 엔진이 파일을 열려고 시도하면
  - **Then** 10초 이내에 파싱 불가를 판별하고, 422 또는 400 에러를 반환해야 한다.
- **Scenario 2: 리소스 반환(메모리 누수 방지)**
  - **Given** 파싱 실패 로직이 발동한 직후
  - **When** 호스트 운영체제(Docker 컨테이너)의 메모리를 측정하면
  - **Then** 해당 요청을 처리하기 위해 할당되었던 메모리가 100% 반환되어야 하며 누수가 없어야 한다.

## 🚧 Constraints
- 무중단성: 하나의 요청 실패가 FastAPI 워커(Worker) 전체의 크래시로 이어져서는 안 된다. 만약 OOM이 임박했다면 스스로 종료 후 Docker `restart_policy` 에 복구를 맡겨야 한다.
- 로깅: 실패 시 디버깅을 위해 로컬 파일 시스템 로그에 스택 트레이스 또는 에러 코드를 반드시 남겨야 한다.
