---
name: Feature Task
title: "[Feature] PAR-07: 탐지된 PII 대상 AES-256 오토 마스킹 모듈"
labels: 'feature, priority:low, parser, could-have'
depends_on: 'PAR-06'
---

## 🎯 Summary
- 기능명: [PAR-07] 탐지된 PII 대상 문서당 100ms 이내 AES-256 오토 마스킹 모듈
- 목적: PAR-06에서 탐지된 PII 데이터를 AES-256 규격으로 안전하게 암호화(마스킹)하여 원본의 유출을 방지한다. (Could Have)

## 🔗 References (Spec & Context)
- SRS 문서: 4.1 F3 (REQ-FUNC-011: AES-256 마스킹 시간)

## 📋 Task Breakdown
- [ ] Python `cryptography` 라이브러리를 활용한 AES-256-CBC (또는 GCM) 암호화/복호화 유틸리티 클래스 구현
- [ ] `.env` 파일로부터 안전하게 Secret Key를 로드하여 암호화에 사용하는 로직 구성
- [ ] PAR-06에서 식별된 PII 문자열을 마스킹 문자(예: `***`)로 치환하고, 필요시 원본은 DB 내 별도 필드에 암호화하여 저장(`pii_masked=true` 마킹)하는 로직 작성
- [ ] 암호화 처리 과정의 소요 시간을 측정하고 100ms 이내에 완료되도록 최적화

## ✅ Acceptance Criteria
- **Scenario 1: AES-256 암호화 및 복호화 무결성**
  - **Given** 평문 PII 문자열과 Secret Key가 주어졌을 때
  - **When** 암호화 함수를 실행하면
  - **Then** 결과물이 올바른 형태의 AES 암호문이어야 하며, 동일한 키로 복호화했을 때 평문과 100% 일치해야 한다.
- **Scenario 2: 문서 단위 마스킹 성능 검증**
  - **Given** 50개의 PII가 포함된 단일 문서가 주어졌을 때
  - **When** 오토 마스킹 파이프라인을 실행하면
  - **Then** 해당 문서의 전체 PII를 마스킹 처리하는 데 소요된 시간이 총 100ms 이하여야 한다.

## 🚧 Constraints
- 우선순위 (Could Have): 이 기능은 PAR-06 선행 완료 후 시간이 허용될 때만 진행하며, 지연 시간(100ms 이내) 기준을 준수하지 못할 경우 탑재를 보류한다.
