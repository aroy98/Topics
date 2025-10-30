# 🏦 Fintech Software Engineer Interview Q&A (Parts 1–3)

---

## **Part 1: Core Fintech + Technical Questions**

### **1. What is Fintech?**

Fintech combines **finance + technology** to automate and improve financial services such as payments, loans, and investments.

### **2. Core Technologies Used**

Backend (Node.js, Python), Frontend (React), Databases (PostgreSQL), Cloud (AWS), Security (JWT, OAuth2), Monitoring (Grafana).

### **3. What is PCI DSS?**

A global security standard ensuring safe card data handling — encrypt, restrict, and test.

### **4. Payment Flow**

Customer → Payment Gateway → Acquirer → Card Network → Issuer → Response → Merchant.

### **5. Transaction Atomicity**

Ensure ACID; rollback on failure using transactions.

### **6. Payment Gateway vs Processor**

Gateway = interface; Processor = executes transfer.

### **7. API Security**

Use HTTPS, OAuth2, JWT, and encryption.

### **8. Idempotency**

Use a unique key to prevent duplicate payments.

### **9. What is KYC?**

Know Your Customer — verifies identity via PAN/Aadhaar.

### **10. UPI Architecture**

NPCI connects payer and receiver banks via real-time IMPS.

### **11. Database Design Practices**

Use ACID, audit tables, encryption, partitioning.

### **12. Scalable Transaction Service**

Microservices: API Gateway → Queue → Ledger → Notification.

### **13. Ledger DB vs Transaction DB**

Ledger = immutable; Transaction = operational.

### **14. Concurrency Handling**

Row-level locks, optimistic concurrency, or queue.

### **15. AML Role**

Anti-Money Laundering — detect suspicious activity.

### **16. Double-Entry Accounting**

Every transaction affects two accounts: debit + credit.

### **17. Audit Trails**

Maintain append-only logs with timestamps.

### **18. Consistency > Availability**

Ensures accuracy over uptime in financial data.

### **19. Fraud Prevention**

Use ML models, device/IP checks, and OTPs.

### **20. Logging & Monitoring**

ELK for logs, Prometheus for metrics, audit logs for compliance.

---

## **Part 2: Advanced System Design, Cloud, and Security**

### **21. High Availability Payment Service**

Multi-region architecture, ALB, primary-replica DBs, Kafka queues, Redis cache.

### **22. Scaling Fintech Apps**

Use microservices, stateless APIs, sharding, and CDNs.

### **23. Ledger System Design**

Immutable, append-only tables with double-entry accounting.

### **24. ACID vs BASE**

ACID for integrity (transactions), BASE for scalability (analytics).

### **25. Data Privacy**

Encrypt PII (AES-256), mask logs, enforce RBAC, isolate via VPCs.

### **26. Tokenization**

Replace sensitive info with secure random tokens.

### **27. Rate Limiting**

Prevent abuse by tracking user requests per minute using Redis.

### **28. Fraud Detection System**

Rules + ML anomaly detection; process via Kafka → Scoring Engine → Alerts.

### **29. Event-Driven Architecture**

Async and decoupled systems using Kafka/RabbitMQ.

### **30. Cloud Compliance**

Follow SOC2, PCI DSS; enable TLS, CloudTrail, IAM policies.

### **31. Observability**

Logs (ELK), Metrics (Prometheus), Tracing (Jaeger), Alerts (PagerDuty).

### **32. Disaster Recovery**

RPO ≤ 5 min, RTO ≤ 30 min, multi-region backups, failover DNS.

### **33. Testing Fintech Systems**

Unit, integration, performance (JMeter), and security (OWASP ZAP).

### **34. Sandbox Environment**

Used for safe payment testing (Razorpay, Stripe).

### **35. Secret Management**

Use AWS Secrets Manager/Vault; rotate and secure credentials.

---

## **Part 3: Behavioral and Scenario-Based Questions**

### **36. Handling a Payment Outage**

Enabled maintenance, rolled back deployment, restored in 10 min, documented RCA.

### **37. Bug Prioritization**

P1 – Transaction failures; P2 – KYC; P3 – UI bugs.

### **38. Compliance in Development**

Follow PCI DSS, data minimization, and pre-release security reviews.

### **39. Successful Project Example**

Built Kafka + PostgreSQL ledger service handling 20k tx/min.

### **40. Communicating Technical Risk**

Translate to business impact with mitigation steps.

### **41. System Performance Improvement**

Added Redis cache, optimized SQL, async queues.

### **42. Debugging Sensitive Data**

Mask logs, use anonymized dumps, debug in staging.

### **43. Reliable Deployment**

Blue-green deploy, feature flags, auto rollback.

### **44. Post-Incident Actions**

RCA, prevention plan, updated runbooks, transparent comms.

### **45. Staying Updated**

Follow NPCI/RBI, fintech blogs, attend events.

### **46. Feature Development Flow**

Requirements → Design → Secure API → Test → Monitor.

### **47. API Versioning**

Use versioned routes (/api/v2), document via Swagger.

### **48. Cloud Cost Optimization**

Use spot instances, auto-scale, archive logs to Glacier.

### **49. Cross-Team Collaboration**

Standups with QA/Compliance, shared docs, CI/CD reviews.

### **50. Why Fintech?**

It impacts millions — solving reliability, scalability, and compliance challenges through tech is rewarding.

---

✅ *End of Fintech Software Engineer Interview Q&A (Complete Parts 1–3)*
