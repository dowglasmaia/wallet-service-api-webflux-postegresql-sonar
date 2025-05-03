# 🚀 Wallet Service API

A digital wallet application that allows users to manage transactions, view statements, check balances, make purchases, and issue refunds. Built using a non-blocking reactive model with Java 17 and Spring WebFlux, following Clean Code and SOLID principles, and implementing the Bridge and Strategy design patterns.

* Reactive programming provides a modern and efficient approach to building highly responsive, resilient, and scalable systems. In the context of Spring WebFlux, it enables the creation of reactive APIs capable of handling a high volume of simultaneous requests by using resources efficiently through a non-blocking, event-driven model.
* This architecture is especially suited for applications that integrate multiple data sources, process large volumes of real-time events, or aim to enhance API responsiveness under high load. The ability to compose operations declaratively and robust error handling makes reactive programming a powerful tool in developing modern services.

---

## 📜 Table of Contents

* [⚡ Technologies](#-technologies)
* [📑 Features](#-features)
* [📦 Project Structure](#-project-structure)
* [🚀 Solution Design](#-solution-design)
* [📚 Documentation](#-documentation)
* [🔌 Design Patterns](#-design-patterns)
* [🛠️ Development](#️-development)
* [🛠️ Transactions and Concurrency Control](#️-transactions-and-concurrency-control)
* [🔍 Observability](#-observability)
* [🚀 Execution and Configuration](#-execution-and-configuration)
* [🌐 References](#-references)

---

## ⚡ Technologies

![Docker](https://img.shields.io/badge/-Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![Java 17](https://img.shields.io/badge/-Java%2017-007396?style=flat-square&logo=java&logoColor=white)
![Spring WebFlux](https://img.shields.io/badge/-Spring%20WebFlux-6DB33F?style=flat-square&logo=spring&logoColor=white)
![Kafka](https://img.shields.io/badge/-Kafka-231F20?style=flat-square&logo=apache-kafka&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/-PostgreSQL-336791?style=flat-square&logo=postgresql&logoColor=white)
![SonarQube](https://img.shields.io/badge/-SonarQube-4E9BCD?style=flat-square&logo=sonarqube&logoColor=white)
![Jaeger](https://img.shields.io/badge/-Jaeger-00B7FF?style=flat-square&logo=jaeger&logoColor=white)
![OpenTelemetry](https://img.shields.io/badge/-OpenTelemetry-CF6300?style=flat-square&logo=opentelemetry&logoColor=white)
![Grafana](https://img.shields.io/badge/-Grafana-F46800?style=flat-square&logo=grafana&logoColor=white)
![Grafana Loki](https://img.shields.io/badge/-Grafana%20Loki-1F75FE?style=flat-square&logo=grafana&logoColor=white)
![Prometheus](https://img.shields.io/badge/-Prometheus-E6522C?style=flat-square&logo=prometheus&logoColor=white)
![Swagger](https://img.shields.io/badge/-Swagger-85EA2D?style=flat-square&logo=swagger&logoColor=black)

---

## 📑 Features

* **Transaction Statement**: Retrieve a full list of account transactions.
* **Balance Inquiry**: Check the current account balance.
* **Withdraw Funds**: Withdraw funds from the account.
* **Purchase**: Make purchases which deduct from the balance.
* **Purchase Refund**: Cancel previous purchases and return the amount to the account.
* **Asynchronous Messaging**: After successful persistence operations, the API sends an asynchronous message to the Audit API.

---

## 📦 Project Structure

```plaintext
src
├── main
│   ├── java
│   │   └── com.dowglasmaia.wallet
│   │       ├── config
│   │       ├── controller
│   │       ├── entity
│   │       ├── enums
│   │       ├── exceptions
│   │       ├── messagemodel
│   │       ├── repository
│   │       ├── service
│   │       │   ├── impl
│   │       │   ├── mapper
│   │       ├── strategy
│   │       └── WalletApplication.java
│   └── resources
│       ├── application.yml
│       └── ...
└── test
    └── java
        └── com.dowglasmaia.wallet
            └── ...
```

---

## 🚀 Solution Design

Includes architecture diagram(s) (e.g., Gateway integration).

---

## 📚 Documentation

* **[Swagger/OpenAPI Contract](src/main/resources/openapi/MS-WALLET.yaml)**
* **[Postman Collection - API](src/main/resources/collection/Wallet%20-%20Transaction%20-%20API.postman_collection.json)**
* **[Postman Collection - GATEWAY](src/main/resources/collection/API-GATEWAY-DOWGLAS-MAIA.postman_collection.json)**
* **[API Gateway Repository](https://github.com/dowglasmaia/traefik-gateway-config)**
* **[Audit Service Repository](https://github.com/dowglasmaia/audit-transaction-service-sboot-mongodb)**

---

## 🔌 Design Patterns

### Bridge Pattern

Used to decouple abstractions and implementations, allowing both to vary independently.

* **Abstraction**: `TransactionService` interface
* **Implementations**: `CreditAdjustmentTransactionServiceImpl`, `CreateTransactionServiceImpl`, etc.

### Strategy Pattern

Defines a family of algorithms, encapsulates each one, and makes them interchangeable.

* **Context**: `TransactionContext`
* **Strategies**: Used to calculate new balances for operations like deposit, withdrawal, and refund.

---

## 🛠️ Development

### Principles

* **Clean Code**: Easy to read and maintain.
* **SOLID**: Object-oriented design principles with focus on single responsibility.
* **Reactive Architecture**: Non-blocking implementation using Spring WebFlux.

### Testing

* **JUnit**: Unit testing.
* **Mockito**: Mocking for tests.

---

## 🛠️ Transactions and Concurrency Control

### Database

Transactional handling ensures atomicity and consistency. Optimistic locking is used to prevent race conditions.

**Optimistic Locking Implementation:**

1. **Using @Version Annotation**:

```java
@Version
private Long version;
```

2. **Conflict Handling in `CreateTransactionServiceImpl`**:

```java
return saveTransaction(account, transactionEntity.getOperationType(), transactionEntity.getAmount())
      .then(repository.save(account))
      .onErrorMap(OptimisticLockingFailureException.class, ex -> {
          return new BusinessException("Concurrent update error", HttpStatus.CONFLICT);
      });
```

### Benefits

* **Avoids Silent Conflicts**
* **Higher Performance** (vs. pessimistic locking)
* **Scalable in High-Concurrency Environments**

---

## 🔍 Observability

### OpenTelemetry + Jaeger

* **OpenTelemetry**: Automatic instrumentation for metrics.
* **Jaeger**: Distributed tracing for transaction tracking and performance insights.

---

## 🚀 Execution and Configuration

### Prerequisites

* **Docker**: [Install Docker](https://docs.docker.com/get-docker/)

### Run the Application

```bash
docker-compose up
```

### Run the Gateway

```bash
docker-compose up
```

### Database Configuration

```properties
url: jdbc:postgresql://localhost:5432/walletDB
username: maia
password: maiapw
```

**SQL Scripts** for DB creation included.

---

## 🔍 SonarQube Configuration

* Create an **Access Token** at `http://localhost:9000`
* Run locally:

```bash
mvn clean verify sonar:sonar -Dsonar.projectKey=wallet-service-api ...
```

* Run using Docker:

```bash
docker container run ... sonarsource/sonar-scanner-cli ...
```

---

## 🌐 References

* [OpenTelemetry Docs](https://opentelemetry.io/docs)
* [Jaeger](https://www.jaegertracing.io/docs)
* [Spring WebFlux](https://docs.spring.io/spring-framework/reference/web/webflux.html)
* [Kafka](https://kafka.apache.org/documentation/)
* [PostgreSQL](https://www.postgresql.org/docs/)
* [Grafana](https://grafana.com/)
* [SonarQube](https://www.sonarsource.com/)

---
