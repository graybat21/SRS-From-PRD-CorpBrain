---
name: Feature Task
title: "[Feature] PAR-06: KISA 기준 PII 자동 탐지 및 위치 반환 기능"
labels: 'feature, priority:low, parser, could-have'
depends_on: 'PAR-02'
---

## 🎯 Summary
- 기능명: [PAR-06] KISA 기준 PII 자동 탐지 및 위치 반환 기능 (탐지율 99% 이상)
- 목적: 파싱된 텍스트 내에서 개인식별정보(PII: 주민등록번호, 전화번호 등)를 정규식 및 NLP 모델을 활용해 자동으로 찾아내어 해당 위치를 식별한다. (Could Have)

## 🔗 References (Spec & Context)
- SRS 문서: 4.1 F3 (REQ-FUNC-010: PII 오토 마스킹 탐지율)
- SRS 문서: 1.4 References (REF-05: KISA 개인정보 비식별 가이드라인)

## 📋 Task Breakdown
- [ ] 주민등록번호, 여권번호, 전화번호, 이메일 등 KISA 기준 PII 패턴을 매칭하는 정규표현식(Regex) 집합 작성
- [ ] 정규식으로 탐지하기 어려운 문맥적 PII(예: 이름, 주소) 판별을 위해 가벼운 로컬 NER(Named Entity Recognition) 모델 통합 (또는 sLLM 프롬프트 추가)
- [ ] 탐지된 PII의 원문 내 BBox 좌표 또는 텍스트 인덱스를 산출하여 `DATA_NODE` 의 메타데이터로 반환하는 로직 추가

## ✅ Acceptance Criteria
- **Scenario 1: 복합 PII 탐지 정확도 검증**
  - **Given** KISA 표준 테스트 데이터셋(PII 100건 포함)이 문서로 주어졌을 때
  - **When** PII 탐지 모듈을 실행하면
  - **Then** 누락된 PII가 1건 이하(탐지율 99% 이상)여야 하며, 해당 위치 정보가 정확히 반환되어야 한다.

## 🚧 Constraints
- 우선순위 (Could Have): 이 기능은 MVP 핵심 파싱(PAR-02, PAR-03)이 완벽히 동작한 이후 시간적 여유가 있을 때 개발한다.
