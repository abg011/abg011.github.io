---
layout: post
title: "System Design - Notification Service"
date: 2026-05-20
tags: [system-design, distributed-systems]
---

# Designing a Large-Scale Notification Service — System Design Notes

## Problem Statement

Design a scalable multi-tenant notification platform similar to Twilio or SendGrid that supports:
- Push notifications
- SMS
- Email
- In-app notifications
- Scheduled notifications
- Realtime alerts
- Notification feeds/history

The platform should support:
- Billions of notifications/day
- Multi-tenancy
- High availability
- Low latency for critical notifications
- Retry handling
- Analytics
- User preferences
- Rate limiting

---

# 1. Functional Requirements

## Core Features

### Notification Channels
System should support:
- Push notifications
- Email
- SMS
- In-app notifications

---

### Notification Types
- Transactional notifications
- OTPs
- Marketing notifications
- Scheduled notifications
- Realtime alerts
- Notification digests

---

### APIs
Clients/tenants should be able to:
- Send notifications
- Configure templates
- Configure retry policies
- Configure rate limits
- Configure SLAs/priorities

---

### User Features
Users should be able to:
- View notification feed/history
- Mark notifications read/unread
- Manage preferences
- Opt-out from marketing notifications

---

### Template Support
Notification templates:

```text
Hello {{user_name}},
Your OTP is {{otp}}
```

Templates should support:
- Dynamic placeholders
- Localization
- Channel-specific rendering

---

### Scheduling
Support:
- Immediate notifications
- Delayed notifications
- Recurring notifications

---

### Delivery Tracking
Track:
- SENT
- DELIVERED
- FAILED
- READ
- CLICKED

---

### Reliability
Support:
- Retries
- Exponential backoff
- DLQ (Dead Letter Queue)
- Multi-provider fallback

---

### Idempotency
API requests must be idempotent to prevent duplicate sends.

---

# 2. Non-Functional Requirements

## Scalability
- Billions of notifications/day
- Hundreds of thousands of notifications/sec during peaks

---

## Availability
High availability target:

```text
99.99%
```

---

## Latency
Critical notifications:
- OTPs
- Transactional alerts

should be delivered within seconds.

---

## Fault Tolerance
System should tolerate:
- Queue failures
- Provider failures
- Worker crashes
- Retry storms

---

## Multi-Tenant Isolation
One tenant should not:
- Starve resources
- Delay critical traffic
- Overload providers

---

## Durability
Notification state should not be lost during failures.

---

## Cost Efficiency
Important especially for:
- SMS
- Email providers
- Cross-region traffic

---

# 3. Capacity Estimation

## Assumptions

### Users
- 100M DAU

### Notifications
- 20 notifications/user/day

Total:

```text
2B notifications/day
```

---

## Average QPS

```text
2B / 86400 ≈ 23K/sec
```

Approx:

```text
~25K notifications/sec
```

---

## Peak Traffic

Notification systems are bursty:
- morning campaigns
- sports alerts
- breaking news
- flash sales

Assume:

```text
10x burst factor
```

Peak:

```text
~250K notifications/sec
```

---

## Channel Split

| Channel | Traffic |
|---|---|
| Push | 70% |
| In-app | 20% |
| Email | 8% |
| SMS | 2% |

---

# 4. Storage Estimation

## Notification Payload

Assume:

```text
500B–1KB per notification
```

Raw storage/day:

```text
~1–2 TB/day
```

---

## Replication

With RF=3:

```text
3–6 TB/day
```

---

## Retention

Different notification types have different retention needs:

| Type | Retention |
|---|---|
| OTP | Minutes |
| Marketing | Days |
| Notification feed | Weeks |
| Audit/compliance | Months/Years |

---

## Analytics Amplification

Each notification may generate:
- created event
- queued event
- delivered event
- clicked event
- retry event

Analytics storage may exceed notification payload storage itself.

---

# 5. High-Level Architecture

## Flow

```text
Client
→ Notification API
→ Validation Layer
→ Queueing Layer
→ Channel Workers
→ External Providers
```

---

# 6. Core Components

## Notification API Service

Responsibilities:
- Authentication
- Tenant validation
- Idempotency
- Rate limiting
- Template expansion
- Notification persistence

---

## Validation Layer

Checks:
- User preferences
- Quiet hours
- Opt-outs
- Deduplication
- Tenant quotas

---

## Queueing Layer

Queues partitioned by:
- Channel
- Priority
- Tenant class

Examples:

```text
email.transactional
email.marketing
sms.otp
push.high_priority
```

---

# 7. Why Queue-Based Architecture?

Queues provide:
- Async processing
- Retry isolation
- Backpressure handling
- Burst smoothing
- Fault tolerance

---

# 8. Kafka vs RabbitMQ

| System | Best For |
|---|---|
| Kafka | Massive throughput, replay |
| RabbitMQ | Complex routing, retries |
| SQS | Managed simplicity |

Kafka is commonly preferred at very large scale.

---

# 9. Scheduler Service Design

## Requirements
- Hundreds of millions of scheduled notifications
- Arbitrary execution times
- Fault tolerance
- Burst handling

---

## Bucket-Based Scheduling

Store notification IDs in:

```text
1-minute buckets
```

Example:

```text
10:01 bucket → all notifications for 10:01
```

---

## Important Optimization

Store:
- only notification IDs in Redis

Actual payload:
- stored in durable notification store

This reduces Redis memory pressure.

---

## Better Approach: Delayed Queues

Instead of simple polling:
- Redis Sorted Sets
- Timing wheels
- Delayed Kafka queues

Example:

```text
score = execution timestamp
```

Workers continuously fetch:

```text
score <= current_time
```

---

# 10. Retry Architecture

## Exponential Backoff

Instead of immediate retries:

```text
1s → 2s → 4s → 8s
```

Formula:

```text
delay = base × 2^retry_count
```

---

## Jitter

Add randomness:

```text
retry between 6–10s
```

to prevent synchronized retry storms.

---

## Dead Letter Queue (DLQ)

After max retries:

```text
move message to DLQ
```

---

# 11. Multi-Tenant Isolation

## Problem

Suppose:

```text
Amazon sends 500M notifications
```

How prevent:
- OTP delays?
- Queue starvation?
- Provider overload?

---

## Solution

### Rate Limiting
Per:
- Tenant
- Channel
- Provider

---

## Token Bucket Algorithm

Preferred because:
- Allows bursts
- Controls sustained throughput

---

## Separate Priority Queues

Critical traffic:
- OTP
- Security alerts

must never share queues with:
- Marketing campaigns

---

## Retry Isolation

Retry traffic should not starve fresh traffic.

Separate:
- retry queues
- fresh queues

---

# 12. Provider Abstraction Layer

Workers should not directly depend on providers.

Architecture:

```text
Worker
→ Provider abstraction layer
→ FCM/APNS/Twilio/etc
```

Benefits:
- Failover
- Multi-provider routing
- Cost optimization
- Regional routing

---

# 13. Push Notifications vs Realtime Systems

## Important Distinction

| Mechanism | Use Case |
|---|---|
| FCM/APNS | Background/offline notifications |
| WebSockets/Persistent connections | Active realtime delivery |

---

## App Open

Use:
- WebSockets
- MQTT
- Persistent connections

---

## App Closed

Use:
- Firebase Cloud Messaging
- APNS

---

# 14. Persistent Socket vs WebSocket

## Persistent Socket

Generic concept:

```text
long-lived network connection
```

Examples:
- TCP
- MQTT
- WebSocket
- gRPC streams

---

## WebSocket

Specific protocol for:
- bidirectional realtime communication

---

# 15. Hard Problems in Notification Systems

## Fanout

```text
1 event → millions of users
```

---

## Retry Storms

Failures causing:
- massive retry traffic
- cascading failures

---

## Burst Traffic

Example:

```text
500M notifications at 9 AM
```

---

## Ordering Guarantees

Need to determine:
- whether ordering matters
- per-user ordering?
- per-channel ordering?

---

## Idempotency

Prevent duplicate notifications during retries/crashes.

---

## Backpressure

What if:
- producers faster than consumers?

---

# 16. Key Distributed Systems Insights

## Notification systems are fundamentally:

```text
distributed queueing systems
```

---

## Hardest part is often:

```text
operational reliability
```

not raw throughput.

---

## Strong system design focuses on:
- failure handling
- fairness
- isolation
- retries
- observability
- backpressure

—not just databases and APIs.

---

# 17. Important Concepts to Revise Before Interviews

## Must Know
- Kafka partitioning
- Retry queues
- DLQs
- Exponential backoff
- Token bucket rate limiting
- Multi-tenant isolation
- Push vs realtime delivery
- Idempotency
- Backpressure
- Fanout systems

---

# 18. Common Interview Mistakes

## Overengineering Tiny Scale

Avoid:

```text
planet-scale infra for 1K QPS
```

---

## Unrealistic Record Sizes

Notification payloads are usually:

```text
hundreds of bytes
```

—not 10KB.

---

## Ignoring Retries

Retries often dominate traffic during outages.

---

## Treating Storage as One System

Separate:
- hot queues
- analytics
- notification feeds
- retries
- archival storage

---

# Final Takeaway

A strong notification system design interview answer should demonstrate:
- operational thinking
- failure modeling
- distributed systems intuition
- queueing architecture understanding
- fairness/isolation strategies
- practical tradeoff reasoning

The biggest shift from mid-level to senior-level system design is:

```text
thinking about failure, fairness, and operational behavior
instead of just APIs and databases
```

