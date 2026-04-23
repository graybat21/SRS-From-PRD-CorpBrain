---
name: Feature Task
title: "[Feature] INF-03: 단일 서버용 Docker Compose 기반 배포 스크립트 작성 및 restart_policy 구성"
labels: 'feature, priority:high, devops, infrastructure'
depends_on: 'INF-01'
---

## 🎯 Summary
- 기능명: [INF-03] 단일 서버용 Docker Compose 기반 배포 스크립트 작성 및 restart_policy 구성
- 목적: 고객사 단일 온프레미스 서버 배포를 위해 Next.js 앱과 FastAPI 파서를 멀티컨테이너로 패키징하고, 엔진 크래시 상황 시 자동 복구를 위한 정책을 적용한다.

## 🔗 References (Spec & Context)
- SRS 문서: 1.2 Scope
- SRS 문서: 4.2 Non-Functional Requirements (REQ-NF-009, REQ-NF-012)
- SRS 문서: 6.3 Detailed Interaction Models (엔진 크래시 → 로컬 로그 → Docker 자동 복구)

## 📋 Task Breakdown
- [ ] Next.js 어플리케이션용 `Dockerfile` 작성 (생산 환경 빌드 및 실행 최적화)
- [ ] FastAPI 파서 엔진용 `Dockerfile` 작성 (가상환경 분리 불필요, 의존성 설치 및 uvicorn 실행)
- [ ] 최상위 디렉토리에 `docker-compose.yml` 파일 작성
- [ ] `docker-compose.yml` 내에 `web` (Next.js) 및 `parser` (FastAPI) 서비스 정의
- [ ] FastAPI 컨테이너 크래시 및 OOM(Out of Memory)에 대비하여 `restart: always` 또는 `restart: unless-stopped` 정책 추가
- [ ] 내부 서비스 간 격리된 통신을 위해 Docker 내부 Bridge 네트워크 설정
- [ ] SQLite `.db` 파일 및 FastAPI 로그 파일 영속성 보장을 위한 볼륨(Volumes) 마운트 설정

## ✅ Acceptance Criteria
- **Scenario 1: 단일 명령어 기반 전체 서비스 기동**
  - **Given** Docker가 설치된 온프레미스 환경에서
  - **When** `docker-compose up -d` 명령어를 실행하면
  - **Then** Next.js 컨테이너와 FastAPI 컨테이너가 에러 없이 백그라운드에서 정상 기동되어야 한다.
- **Scenario 2: 장애 발생 시 자동 복구 (Auto Recovery)**
  - **Given** FastAPI 컨테이너가 정상 구동 중인 상태에서
  - **When** 의도적으로 FastAPI 컨테이너 프로세스를 강제 종료(크래시 유발, 예: `docker kill`)하면
  - **Then** Docker 데몬의 `restart_policy`에 의해 해당 컨테이너가 즉각적으로 재시작되어야 한다.
- **Scenario 3: 데이터 영속성 검증**
  - **Given** 컨테이너가 가동 중이고 SQLite DB에 임의의 워크스페이스 데이터가 저장된 후
  - **When** Next.js 컨테이너를 재시작(`docker-compose restart web`)하면
  - **Then** 볼륨 마운트 정책에 의해 기존 저장된 데이터가 손실되지 않고 유지되어야 한다.

## 🚧 Constraints
- **REQ-NF-009**: K8s 등 클러스터링 도구는 배제하고, 고객사 단일 서버에서 구동 가능한 Docker Compose 기반이어야 한다.
- **REQ-NF-012**: 비정상 종료 시 시스템 중단을 막기 위해 Docker 자체의 `restart_policy`에 절대적으로 의존하여 자동 재시작을 보장해야 한다.
