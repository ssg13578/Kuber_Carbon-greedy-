# Kuber_Carbon
전체적인 흐름
1. 데이터 수집 (Celery Beat - 5분마다 자동 실행)
   └── WattTime API → 실시간 탄소 강도 가져오기
   └── Prometheus → 각 노드의 CPU/메모리 사용량 가져오기
   
2. 탄소 배출량 계산 (carbon_collector.py)
   └── 탄소 강도 × 전력 사용량 = 노드별 탄소 배출량
   
3. DB에 저장 (PostgreSQL)
   └── 각 클러스터(노드)의 상태 업데이트
   
4. 최적 클러스터 선택 (carbon_scheduler.py)
   └── DB에서 탄소 배출량이 가장 낮은 클러스터 찾기
   └── 결과만 반환 (실제 마이그레이션은 아직 구현 안됨)

| 컨테이너                    | 역할                                   |
| ----------------------- | ------------------------------------ |
| **app**                 | `main.py` 실행 — FastAPI 서버            |
| **redis_kuber**         | Celery 브로커(Broker) 및 결과 저장소(Backend) |
| **db**                  | PostgreSQL — 클러스터 상태/결과 저장           |
| **worker**              | `celery_app.py` 실행 — Celery 워커       |
| **beat** *(일부 구성에선 포함)* | Celery Beat — 주기적인 Task 실행 트리거       |
