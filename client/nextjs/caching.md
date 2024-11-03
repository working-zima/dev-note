# Caching in Next.js

Mechanism | What | Where | Purpose | Duration
:-: | :-: | :-: | :-: | :-:
Request Memoization | 함수의 반환 값 | Server | React Component 트리에서 데이터를 재사용 | 요청 라이프사이클 동안
Data Cache | 데이터 | Server | 사용자 요청 및 배포 간 데이터 저장 | 지속적 (재검증 가능)
Full Route Cache | HTML 및 RSC 페이로드 | Server | 렌더링 비용 절감 및 성능 향상 | 지속적 (재검증 가능)
Router Cache | RSC 페이로드 | Client | 네비게이션 시 서버 요청 감소 | 사용자 세션 또는 시간 기반

## Full Route Cache

Next 서버측에서 프로젝트가 빌드될 때 특정 페이지의 렌더링 결과를 캐싱하는 기능입니다.
