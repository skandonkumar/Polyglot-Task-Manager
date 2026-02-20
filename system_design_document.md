# System Design Document

## Polyglot Task Management API

**Objective:**  
A highly available, multi-language microservices architecture serving a unified API, utilizing a shared cache and distinct database technologies.

---

## Architecture Overview

This system employs a polyglot microservices pattern with language-specific backends paired to their optimal persistence layers. A unified API gateway routes requests to backend services, while a shared caching layer reduces database load.

---

## Architecture Components & Responsibilities

### 1. API Gateway

**Service:** `Nginx` (Ingress Controller)

Acts as the entry point for all incoming requests. Routes traffic based on HTTP headers or path prefixes to the respective backend services.

- Routes: `/api/java`, `/api/node`, `/api/python`
- Provides request distribution and load balancing

### 2. Caching Layer

**Service:** `Redis` (Shared StatefulSet)

Deployed as a single shared cache to reduce database load across all backends.

- **Pattern:** Read-through cache
- **Behavior:** All three backends attempt to read from Redis before querying their respective databases
- **Deployment:** Single shared StatefulSet

### 3. Backend Services (Compute Layer)

| Service | Framework | Use Case | Strengths |
|---------|-----------|----------|-----------|
| **Java** | Spring Boot | High-throughput processing | Highly typed, concurrent execution |
| **Node.js** | Express/Fastify | API serialization | Non-blocking I/O, rapid JSON handling |
| **Python** | Flask/FastAPI | Data validation | Synchronous/Asynchronous hybrid, rapid prototyping |

### 4. Database Layer (Persistence)

| Backend | Database | Type | Use Case | Strengths |
|---------|----------|------|----------|-----------|
| **Java** | Cassandra | Wide-column store | High-throughput writes | Distributed, scalable writes |
| **Node.js** | MongoDB | Document store | Flexible schemas | Schema-less JSON data, flexible queries |
| **Python** | PostgreSQL | Relational | ACID transactions | Structured data, transaction support |

---

## Technology Stack Summary

- **API Gateway:** Nginx
- **Cache:** Redis
- **Languages:** Java, Node.js, Python
- **Databases:** Cassandra, MongoDB, PostgreSQL