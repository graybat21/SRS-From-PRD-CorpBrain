---
name: Feature Task
title: "[Feature] INF-02: 외부 클라우드 원천 차단을 위한 Prisma + 로컬 SQLite DB 스키마 구성"
labels: 'feature, priority:high, database, infrastructure'
depends_on: 'INF-01'
---

## 🎯 Summary
- 기능명: [INF-02] 외부 클라우드 원천 차단을 위한 Prisma + 로컬 SQLite DB 스키마 구성
- 목적: 100% 로컬 망분리 환경 준수 및 외부 데이터 전송 원천 차단을 위해 클라우드 DB 대신 로컬 SQLite 기반의 Prisma ORM 환경을 구축하고 초기 스키마를 마이그레이션한다.

## 🔗 References (Spec & Context)
- SRS 문서: 1.5 Constraints and Assumptions (C-TEC-003)
- SRS 문서: 6.2 Entity & Data Model

## 📋 Task Breakdown
- [ ] Next.js 프로젝트 내에 Prisma ORM 설치 (`prisma`, `@prisma/client`)
- [ ] `schema.prisma` 파일 초기화 및 `provider = "sqlite"` 설정
- [ ] SRS 6.2 ERD 기준 `WORKSPACE` 모델 정의
- [ ] SRS 6.2 ERD 기준 `PARSED_DOCUMENT` 모델 정의
- [ ] SRS 6.2 ERD 기준 `DATA_NODE` 모델 정의
- [ ] Prisma 마이그레이션 스크립트 실행 (`npx prisma migrate dev`)하여 로컬 `.db` 파일 생성 확인
- [ ] 서버 시작 시 SQLite DB 연결을 확인하는 Prisma Client 인스턴스 싱글톤 구성

## ✅ Acceptance Criteria
- **Scenario 1: SQLite DB 마이그레이션 성공**
  - **Given** Prisma 스키마 작성이 완료된 상태에서
  - **When** 개발자가 `npx prisma migrate dev` 명령어를 실행하면
  - **Then** 지정된 디렉토리 내에 SQLite `.db` 파일이 정상적으로 생성되고 마이그레이션 내역이 기록되어야 한다.
- **Scenario 2: 외부망 차단 원칙 검증**
  - **Given** 개발 및 운영 DB 환경 구성 시
  - **When** Prisma 환경 변수 및 설정 파일을 점검할 때
  - **Then** 외부 클라우드 URL 접속 정보가 전혀 존재하지 않고, 오직 로컬 파일 시스템 경로(예: `file:./dev.db`)만 명시되어야 한다. (아웃바운드 0 Byte 원칙)

## 🚧 Constraints
- **C-TEC-003**: 개발 및 배포 환경 모두 클라우드 DB 연동을 원천 금지하며, Prisma + 로컬 SQLite만을 사용해야 한다.
