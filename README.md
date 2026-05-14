# 🌍 The Story: “The City of Money Letters.”

A modern distributed payment-processing architecture explained through a storytelling-style state diagram.

This project demonstrates how a highly scalable and fault-tolerant payment system processes requests safely using:

- API Gateway validation
- Idempotency protection
- Microservices orchestration
- Distributed locks
- Event-driven architecture
- Retry and failover mechanisms
- Immutable ledger tracking
- Queue-based processing
- Observability and monitoring
- Circuit breakers and backpressure handling

---

# 📖 Overview

Imagine a futuristic city where every payment is treated like a secure “money letter.”

Every letter travels through multiple checkpoints before it safely reaches its destination.

The system ensures that:

- no duplicate payments happen,
- failures are retried safely,
- corrupted messages are isolated,
- every transaction is recorded permanently,
- and the entire system remains resilient under heavy load.

This state diagram visualizes the full lifecycle of a payment request in a distributed microservices environment.

---

# 🧠 What This Architecture Demonstrates

## 🔐 Reliability & Safety

The system uses:

- **Idempotency Keys** to prevent duplicate payment processing
- **Distributed Locks** to avoid race conditions
- **Immutable Ledger Updates** for transaction integrity

---

## ⚡ Scalability

The architecture is designed for high throughput using:

- Microservices
- RabbitMQ queues
- Worker-based async processing
- Redis caching

---

## 🛡️ Resilience

The payment flow can recover from failures using:

- Retry logic
- Circuit breakers
- Backpressure protection
- Failover recovery

---

## 📡 Observability

Monitoring is integrated across services so the system can track:

- request flow,
- failures,
- latency,
- retries,
- and processing health.

---

# 🗺️ Payment Processing State Diagram

```mermaid
stateDiagram-v2

[*] --> UserInitiatesPayment

state "🌍 User Initiates Payment" as UserInitiatesPayment
state "🏤 API Gateway Checks Request" as APIGateway
state "🧾 Generate Idempotency Key" as Idempotency
state "🔍 Check Duplicate Request" as DuplicateCheck
state "🧠 Route to Microservices" as Microservices

state "💰 Balance Service" as BalanceService
state "🏦 Bank Connector Service" as BankService
state "📜 History Service" as HistoryService
state "↩️ Refund Service" as RefundService

state "📚 Immutable Ledger Updated" as Ledger
state "🧵 Publish Payment Event" as PublishEvent
state "📬 RabbitMQ Queue" as Queue
state "👷 Worker Consumes Event" as Worker

state "🔁 Retry Processing" as Retry
state "💀 Dead Letter Queue" as DLQ

state "⚡ Redis Cache Lookup" as Cache
state "🔒 Distributed Lock Active" as Lock

state "💳 External Payment Provider" as Provider
state "📡 Observability Tracking" as Observability
state "🧯 Security Validation" as Security

state "⚖️ Backpressure Protection" as Backpressure
state "🚨 Circuit Breaker Triggered" as CircuitBreaker
state "♻️ Failover Recovery" as Failover

state "✅ Payment Success" as Success
state "❌ Payment Failed" as Failed
state "🚫 Duplicate Request Ignored" as DuplicateIgnored

%% Main Flow
UserInitiatesPayment --> APIGateway
APIGateway --> Security
Security --> Idempotency
Idempotency --> DuplicateCheck

DuplicateCheck --> DuplicateIgnored : Already Seen
DuplicateIgnored --> [*]

DuplicateCheck --> Microservices : New Request

%% Microservices Flow
Microservices --> Cache
Cache --> Lock
Lock --> BalanceService

BalanceService --> BankService
BankService --> Provider

Provider --> HistoryService : Approved
Provider --> Retry : Timeout/Error

Retry --> Provider : Retry Request
Retry --> CircuitBreaker : Too Many Failures

CircuitBreaker --> Backpressure
Backpressure --> Failed

CircuitBreaker --> Failover
Failover --> Provider

%% Ledger + Events
HistoryService --> Ledger
Ledger --> PublishEvent
PublishEvent --> Queue
Queue --> Worker

Worker --> Success : Processed Safely
Worker --> DuplicateIgnored : Duplicate Event
Worker --> DLQ : Corrupted Message

%% Observability Everywhere
APIGateway --> Observability
BalanceService --> Observability
BankService --> Observability
Ledger --> Observability
Worker --> Observability

%% End States
Success --> [*]
Failed --> [*]
DLQ --> [*]
