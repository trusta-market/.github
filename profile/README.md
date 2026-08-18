# Trusta Market

실제 운영 환경을 가정하여 설계한 MSA 기반 중고 거래 플랫폼입니다.

서비스 분리 이후 발생하는 데이터 정합성, 장애 격리, 운영 복잡도, 성능 병목을 직접 설계하고 검증하는 것을 목표로 합니다.

## Architecture

Trusta Market은 Multi Repository 구조로 서비스를 분리합니다.

```text
Client
  │
  ▼
API Gateway
  │
  ▼
Business Services
  ├── user-service
  ├── product-service
  ├── inspection-service
  ├── order-service
  ├── payment-service
  ├── wallet-service
  ├── delivery-service
  └── notification-service
        │
        ├── Kafka
        └── PostgreSQL
              └── schema-per-service
```

- 각 서비스는 자신의 데이터를 소유합니다.
- 서비스 간 데이터베이스 직접 접근은 허용하지 않습니다.
- 서비스 간 데이터 전파와 비동기 처리는 Kafka 이벤트를 사용합니다.

## Repositories

### Platform

| Repository | Responsibility |
|---|---|
| [api-gateway](https://github.com/trusta-market/api-gateway) | 외부 요청 진입점 |
| [config-server](https://github.com/trusta-market/config-server) | 중앙 설정 관리 |
| [eureka-server](https://github.com/trusta-market/eureka-server) | 서비스 디스커버리 |
| [trusta-local-infra](https://github.com/trusta-market/trusta-local-infra) | 로컬 인프라 설정 |
| [trusta-zipkin](https://github.com/trusta-market/trusta-zipkin) | Zipkin 배포 설정 |

### Business Services

| Repository | Responsibility |
|---|---|
| [user-service](https://github.com/trusta-market/user-service) | 회원 관리 |
| [product-service](https://github.com/trusta-market/product-service) | 상품 관리 |
| [inspection-service](https://github.com/trusta-market/inspection-service) | 검수 프로세스 |
| [order-service](https://github.com/trusta-market/order-service) | 주문 처리 |
| [payment-service](https://github.com/trusta-market/payment-service) | 결제 처리 |
| [wallet-service](https://github.com/trusta-market/wallet-service) | 포인트 관리 |
| [delivery-service](https://github.com/trusta-market/delivery-service) | 배송 관리 |
| [notification-service](https://github.com/trusta-market/notification-service) | 알림 처리 |

### Shared and Supporting

| Repository | Responsibility |
|---|---|
| [common](https://github.com/trusta-market/common) | 공통 모듈 |
| [mock-carrier-service](https://github.com/trusta-market/mock-carrier-service) | 배송 연동 테스트용 Mock 서비스 |

## Technology Stack

| Area | Technology |
|---|---|
| Backend | Java 21, Spring Boot 3.5.13, Spring Cloud |
| Persistence | Spring Data JPA, PostgreSQL, Cloud SQL |
| Messaging | Apache Kafka |
| Security | Spring Security |
| Infrastructure | Docker, Kubernetes (GKE) |
| Observability | Prometheus, Grafana, Google Cloud Monitoring |

## Key Engineering Points

### Data Ownership

서비스별 데이터 소유권을 분리하되, 운영 복잡도를 고려하여 schema-per-service 구조를 선택했습니다.

### Failure Isolation

서비스 장애가 전체 시스템으로 전파되지 않도록 다음 요소를 적용합니다.

- Retry
- Dead Letter Queue
- Idempotent Processing
- Graceful Shutdown
- Readiness / Liveness Probe

### Observability

애플리케이션, JVM, 데이터베이스, Kafka 지표를 수집하여 장애와 병목을 확인합니다.

### Performance Verification

성능은 추측이 아닌 측정을 통해 검증합니다.

- k6 기반 고정 TPS 부하 테스트
- P95 / P99 Latency
- Throughput 및 Error Rate
- JVM, HikariCP, PostgreSQL, Kafka 리소스 사용량

성능 개선은 동일한 조건에서 개선 전후를 다시 측정하는 방식으로 검증합니다.
