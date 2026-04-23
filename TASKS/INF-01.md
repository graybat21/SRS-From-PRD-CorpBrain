---
name: Feature Task
title: "[Feature] INF-01: Next.js(FE/BE) 및 FastAPI(Parser) 기본 프로젝트 세팅"
labels: 'feature, priority:high, infrastructure'
depends_on: 'none'
---

## 🎯 Summary
- 기능명: [INF-01] Next.js(FE/BE) 및 FastAPI(Parser) 기본 프로젝트 세팅
- 목적: CorpBrain MVP의 프론트엔드/일반 백엔드와 핵심 AI 파서 엔진을 분리하는 투트랙(Two-track) 모노레포 또는 멀티레포 구조의 초기 환경을 구축한다.

## 🔗 References (Spec & Context)
- SRS 문서: 1.5 Constraints and Assumptions (C-TEC-001, C-TEC-002)

## 📋 Task Breakdown
- [ ] 프로젝트 디렉토리 구조 설계 (예: `/apps/web` 및 `/apps/parser` 구조 생성)
- [ ] 프론트엔드 및 일반 서버 로직용 Next.js (App Router) 프로젝트 초기화
- [ ] UI/UX 개발을 위한 Tailwind CSS, shadcn/ui 의존성 설치 및 초기 설정
- [ ] 핵심 파서 엔진 구동을 위한 FastAPI (Python) 독립 프로젝트 초기화
- [ ] FastAPI 프로젝트 내 파이썬 가상환경 설정 및 기본 의존성(`fastapi`, `uvicorn` 등) `requirements.txt` 작성
- [ ] Next.js 서버(Next.js Server Actions)와 FastAPI 간의 기본 통신 테스트(Ping 엔드포인트) 구현

## ✅ Acceptance Criteria
- **Scenario 1: 투트랙 서버 독립 구동 확인**
  - **Given** 로컬 개발 환경에서
  - **When** Next.js 개발 서버(`npm run dev`)와 FastAPI 서버(`uvicorn`)를 각각 실행하면
  - **Then** 에러 없이 두 서버가 지정된 포트(예: 3000, 8000)에서 정상 구동되어야 한다.
- **Scenario 2: 서버 간 내부 통신 검증**
  - **Given** Next.js와 FastAPI 서버가 모두 구동 중일 때
  - **When** Next.js의 특정 API Route나 Server Action을 통해 FastAPI의 `/ping` 엔드포인트를 호출하면
  - **Then** FastAPI로부터 HTTP 200 상태 코드와 정상 응답(예: `{"status": "ok"}`)을 반환받아야 한다.

## 🚧 Constraints
- **C-TEC-001**: 프론트엔드 및 일반 백엔드 API는 반드시 Next.js (App Router) 기반으로 작성해야 함.
- **C-TEC-002**: 일반 DB 입출력은 Next.js에서 처리하되, 핵심 AI 로직 및 파서 엔진은 반드시 FastAPI(Python) 독립 컨테이너로 분리되어야 함.
