---
name: Feature Task
title: "[Feature] PAR-03: 'A 법무법인 하도급 표준 계약서' 맞춤형 표/셀 병합 파싱 로직 적용"
labels: 'feature, priority:critical, parser'
depends_on: 'PAR-02'
---

## 🎯 Summary
- 기능명: [PAR-03] A 법무법인 하도급 표준 계약서 1종 맞춤형 표/셀 병합 파싱 로직 적용 (TEDS-Struct 99.9 목표)
- 목적: MVP의 핵심 검증 대상인 '하도급 표준 계약서'의 복잡한 표 구조(셀 병합, 숨은 선 등)를 완벽하게 복원하여 TEDS-Struct 지표 99.9점을 달성한다.

## 🔗 References (Spec & Context)
- SRS 문서: 1.5 Constraints and Assumptions (CON-01: MVP 범위를 1종 서식으로 제한)
- SRS 문서: 4.1 F1 (REQ-FUNC-005: 1주 내 완전 파싱 PoC 달성)

## 📋 Task Breakdown
- [ ] 'A 법무법인 하도급 표준 계약서'의 공통적인 양식 구조 및 표 메타데이터 분석
- [ ] 복잡한 셀 병합(Rowspan, Colspan)과 경계선 누락 등 Edge Case를 방어하기 위한 템플릿 맞춤형 전처리(Pre-processing) 로직 작성
- [ ] sLLM 프롬프트 엔지니어링 수행 (해당 계약서의 표 구조에 가장 최적화된 Few-Shot 예제 및 컨텍스트 주입)
- [ ] 파싱 결과물에 대해 TEDS-Struct 점수를 측정할 수 있는 내부 평가 스크립트 작성

## ✅ Acceptance Criteria
- **Scenario 1: 하도급 계약서 표 병합 복원율 검증**
  - **Given** 복잡하게 병합된 셀을 포함하는 A 법무법인의 하도급 표준 계약서 10부를 준비하고
  - **When** 파서 엔진을 통해 파싱한 뒤
  - **Then** Ground Truth와 비교하여 추출된 테이블의 행렬 구조가 누락이나 변위 없이 100% 일치해야 하며, TEDS-Struct 측정 스크립트에서 99.9 이상의 점수가 산출되어야 한다.

## 🚧 Constraints
- 개발 범위 제한 (CON-01): 본 태스크에서는 범용적인 문서 파싱을 고려하지 않으며, 오직 'A 법무법인 하도급 표준 계약서' 1종의 예외 케이스(Edge Case)를 해결하는 데 리소스를 집중해야 한다.
