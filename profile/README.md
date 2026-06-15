# Trusta Market

실제 운영 환경을 가정하여 설계한 MSA 기반 중고 거래 플랫폼입니다.

이 프로젝트는 단순한 기능 구현이 아닌, 서비스 분리 이후 발생하는 데이터 정합성, 장애 복원력, 운영 복잡도, 성능 병목 문제를 직접 설계하고 검증하는 것을 목표로 합니다.

---

## Why This Project Exists

모놀리식 아키텍처는 초기 개발 생산성이 높지만 서비스 규모가 커질수록 다음과 같은 문제가 발생합니다.

- 배포 영향 범위 증가
- 서비스 간 결합도 증가
- 장애 전파
- 데이터 소유권 불명확
- 독립적인 확장 한계

Trusta Market은 이러한 문제를 해결하기 위해 MSA를 적용하였으며, 단순히 서비스를 분리하는 것에 그치지 않고 운영 환경에서 발생하는 실제 문제들을 검증하는 데 초점을 맞추고 있습니다.

---

## Architecture Principles

### Service Owns Its Data

각 서비스는 자신의 데이터를 소유합니다.

서비스 간 데이터베이스 직접 접근을 금지하며 이벤트 기반으로 데이터를 동기화합니다.

### Failure Isolation

하나의 서비스 장애가 전체 시스템 장애로 확산되지 않도록 설계합니다.

- Retry
- Dead Letter Queue
- Graceful Shutdown
- Health Check
- Readiness / Liveness Probe

### Observability First

문제를 예방하는 것보다 빠르게 탐지하고 분석하는 것을 중요하게 생각합니다.

- Metrics
- Monitoring
- Performance Testing
- Capacity Planning

### Simplicity over Complexity

기술 도입 자체보다 운영 복잡도 대비 효과를 우선 고려합니다.

- 서비스별 독립 데이터베이스 대신 Schema-per-Service 선택
- 필요한 범위 내에서 Event-Driven Architecture 적용
- 과도한 분산 트랜잭션 지양

---

## System Architecture

### Architecture Diagram

(추가 예정)

---

## Repository Strategy

본 프로젝트는 Multi Repository 전략을 사용합니다.

### Why Multi Repository?

각 서비스를 독립적으로 관리하기 위함입니다.

- 독립 배포
- 독립 CI/CD
- 서비스별 권한 분리
- 서비스별 릴리즈 주기 분리

### Trade-offs

- Repository 관리 비용 증가
- Cross Repository 변경 복잡성 증가
- 공통 코드 관리 비용 증가

이러한 비용을 감수하더라도 서비스 독립성을 확보하는 것이 더 중요하다고 판단하였습니다.

---

## Platform Repositories

### Infrastructure

| Repository | Responsibility |
|---|---|
| `config-server` | 중앙 설정 관리 |
| `eureka-server` | 서비스 디스커버리 |
| `api-gateway` | 외부 요청 진입점 |
| `trusta-local-infra` | 로컬 인프라 설정 |
| `trusta-zipkin` | zipkin 배포 관련 설정 |

### Business Services

| Repository | Responsibility |
|---|---|
| `user-service` | 회원 관리 |
| `product-service` | 상품 관리 |
| `inspection-service` | 검수 프로세스 |
| `order-service` | 주문 처리 |
| `payment-service` | 결제 처리 |
| `wallet-service` | 포인트 관리 |
| `delivery-service` | 배송 관리 |
| `notification-service` | 알림 처리 |

---

## Data Architecture

### Database Strategy

- Cloud SQL (PostgreSQL)
- Schema-per-Service
- 서비스 간 직접 DB 접근 금지
- 이벤트 기반 데이터 전파

### Why Schema-per-Service?

서비스별 데이터베이스를 완전히 분리하는 경우 운영 복잡도가 크게 증가합니다.

현재 프로젝트는 다음 균형점을 선택했습니다.

- 데이터 소유권 확보
- 운영 단순성 유지
- 비용 효율성 확보

---

## Technology Stack

### Backend

- Java 21
- Spring Boot 3.5.13
- Spring Cloud
- Spring Data JPA
- Spring Security

### Messaging

- Apache Kafka

### Database

- PostgreSQL
- Cloud SQL

### Infrastructure

- Docker
- Kubernetes (GKE)

### Monitoring

- Prometheus
- Grafana
- Google Cloud Monitoring

---

## Reliability

운영 환경에서 발생할 수 있는 장애 상황을 고려하여 다음 전략을 적용합니다.

### Application Layer

- Retry
- Timeout
- Graceful Shutdown

### Messaging Layer

- Dead Letter Queue
- Consumer Error Handling
- Idempotent Processing

### Infrastructure Layer

- Readiness Probe
- Liveness Probe
- Rolling Update

---

## Observability

### Application Metrics

- Application Log
- HTTP Throughput
- Response Time
- Error Rate

### JVM Metrics

- Heap Usage
- GC Pause

### Database Metrics

- HikariCP Status
- HikariCP Pending Requests
- HikariCP Acquire Time
- HikariCP Usage Time

### Infrastructure Metrics

- CPU Usage
- Memory Usage
- Kafka Lag

---

## Performance Verification

성능은 추측이 아닌 측정을 통해 검증합니다.

### Load Testing

**Tool**

- k6

**Verification Targets**

- Throughput
- P95 / P99 Latency
- Database Resource Usage
- Infrastructure Resource Usage

---

## Engineering Topics

프로젝트 진행 과정에서 다음 주제를 집중적으로 학습하고 검증하였습니다.

- Microservice Architecture
- Event Driven Architecture
- Transaction Boundary
- Distributed Consistency
- Database Capacity Planning
- Connection Pool Tuning
- Kubernetes Resource Management
- Failure Isolation
- Observability

---

## Current Limitations

현재 프로젝트는 학습 및 검증 목적의 플랫폼으로 다음과 같은 한계가 존재합니다.

- Single Region Deployment
- 단일 Cloud SQL 인스턴스 사용
- 제한적인 Auto Scaling 정책
- 단순화된 이벤트 재처리 전략

---

## Future Improvements

- Multi Region Deployment
- CDC 기반 이벤트 전파
- Advanced Capacity Planning
- Production Grade Auto Scaling

---

## Goal

저희는 서비스를 만드는 개발자가 아니라,

운영 가능한 시스템을 설계하고 검증할 수 있는 엔지니어가 되는 것을 목표로 합니다.
