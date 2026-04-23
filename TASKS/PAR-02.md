---
name: Feature Task
title: "[Feature] PAR-02: 100% 로컬 구동 Rule-based + sLLM 앙상블 파서 엔진 파이프라인 구축"
labels: 'feature, priority:critical, parser'
depends_on: 'INF-01'
---

## 🎯 Summary
- 기능명: [PAR-02] 100% 로컬 구동 Rule-based + sLLM 앙상블 파서 엔진 파이프라인 구축
- 목적: 외부 클라우드 의존성 없이(아웃바운드 0 Byte), 정규식/규칙 기반 1차 파싱과 사내망 구동 sLLM을 결합하여 무결점에 가까운 표/양식 데이터 추출 파이프라인의 뼈대를 완성한다.

## 🔗 References (Spec & Context)
- SRS 문서: 4.1 F1 (REQ-FUNC-001: 정형 데이터 포맷 변환, REQ-FUNC-004: 아웃바운드 0 Byte)
- SRS 문서: 6.3 Detailed Interaction Models (Parser Core 내부 흐름)

## 📋 Task Breakdown
- [ ] 100% 오프라인 구동이 보장되는 오픈소스 문서 파싱 라이브러리(PyMuPDF, pdfplumber 등) 셋업 및 플러거블(Pluggable) 아키텍처 패턴 적용
- [ ] 파서의 1단계: Rule-based 엔진을 이용한 문서 내 표 영역(BBox) 감지 및 원시 텍스트 추출 로직 구현
- [ ] 파서의 2단계: 사내망에 이미 구축된 sLLM 모델(예: Llama 3 8B, Qwen 등)로 프롬프트를 전송하여 원시 텍스트를 구조화(JSON)하는 API 연동 로직 작성 (로컬 내 gRPC/REST 통신)
- [ ] 1단계와 2단계의 결과를 병합하여 일관된 스키마(표의 행/열, 텍스트)를 갖춘 `DATA_NODE` 배열 객체 생성 로직 작성

## ✅ Acceptance Criteria
- **Scenario 1: 완전한 로컬 환경(망분리)에서의 파싱 성공**
  - **Given** 외부 인터넷 접속이 완전히 차단된 개발/테스트 환경에서
  - **When** 샘플 문서가 Parser 파이프라인에 입력되면
  - **Then** 외부 API 호출 시도 없이 온전히 자체 sLLM 및 Rule-based 로직만으로 문서를 파싱하여 구조화된 JSON 결과를 반환해야 한다.
- **Scenario 2: 플러거블 구조 확인**
  - **Given** 새로운 파싱 라이브러리를 추가하거나 sLLM 모델을 교체해야 할 때
  - **When** 인터페이스 기반(Adapter 패턴)으로 코드를 주입하면
  - **Then** 기존 파서 파이프라인의 핵심 로직 수정 없이 교체가 정상적으로 작동해야 한다.

## 🚧 Constraints
- 보안 제약: REQ-FUNC-004에 따라 코드 내 어떠한 외부 클라우드 서비스(OpenAI, Anthropic 등) 호출 라이브러리도 포함해서는 안 되며, 아웃바운드는 0 Byte여야 한다.
- 아키텍처 제약: 특정 파싱 라이브러리에 완전히 종속되지 않도록 인터페이스(플러거블)를 분리해야 한다.
