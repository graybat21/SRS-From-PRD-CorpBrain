---
name: Feature Task
title: "[Feature] PAR-04: 추출된 Data Node별 Confidence Score 산출 모듈 구현"
labels: 'feature, priority:high, parser'
depends_on: 'PAR-02'
---

## 🎯 Summary
- 기능명: [PAR-04] 추출된 각 Data Node별 0~100% Confidence Score 산출 모듈 구현
- 목적: 프론트엔드의 붉은색 하이라이터 렌더링에 사용될 신뢰도(Confidence) 점수를 sLLM의 Logprob(로그 확률) 값이나 규칙 기반 검증을 통해 산출하여 각 Node에 부여한다.

## 🔗 References (Spec & Context)
- SRS 문서: 4.1 F2 (REQ-FUNC-006: Confidence Score 산출)

## 📋 Task Breakdown
- [ ] sLLM 응답에서 생성된 토큰들의 Logprob(확률 값)을 추출하는 로직 구현 (또는 sLLM이 Logprob을 지원하지 않을 경우, 원본 텍스트와 파싱된 JSON 간의 Levenshtein 거리 계산 등 대안 유사도 산출식 구현)
- [ ] Rule-based 단계의 BBox 인식 정확도 지표와 텍스트 유사도 지표를 가중 평균하여 최종 `0.0 ~ 1.0` (0~100%) 범위의 Float 점수로 변환하는 함수 작성
- [ ] 최종 변환된 Confidence 점수를 파서의 결과물 배열(각 `DATA_NODE`) 객체에 할당 (`confidence_score` 필드)
- [ ] 결과 JSON 내 원본 문서에서의 위치를 나타내는 `highlight_ranges` (BBox 좌표 등) 함께 반환하도록 구조체 보강

## ✅ Acceptance Criteria
- **Scenario 1: Data Node별 Float 점수 할당 확인**
  - **Given** 문서 파싱이 완료되어 JSON 배열이 생성되었을 때
  - **When** 응답 데이터를 조회하면
  - **Then** 모든 `DATA_NODE` 객체 내에 `confidence_score` 필드가 존재하며, 값의 범위는 0.0에서 1.0 사이여야 한다.
- **Scenario 2: 저신뢰도 점수의 정확성 (수작업 확인)**
  - **Given** 의도적으로 얼룩지거나 화질이 좋지 않은 문서를 업로드했을 때
  - **When** 파서가 해당 부분을 판독하고 결과를 반환하면
  - **Then** 해당 오류가 발생할 가능성이 높은 셀의 `confidence_score`가 0.8(80%) 미만으로 산출되어야 한다.

## 🚧 Constraints
- 성능 제약: Confidence 점수를 산출하기 위해 과도한 연산(예: 별도의 모델을 띄워 이중 추론)을 해서는 안 되며, 파싱 전체 응답 Latency 지연(p95 ≤ 1.5초) 제약 내에서 처리되어야 한다.
