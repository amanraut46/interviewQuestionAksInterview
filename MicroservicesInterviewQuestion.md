## **Interview Question**

**1 What would you do if two microservices start failing simultaneously? How would you identify the root cause and fix the issue?**

---

## **Answer**

If two microservices fail at the same time, it usually indicates a **shared dependency issue, infrastructure problem, or cascading failure**. My approach would be systematic:

---

### **1. Check Logs and Monitoring First**

* Review centralized logs using tools like Azure Monitor or ELK Stack
* Look for:

  * Common error messages
  * Timestamp correlation
  * Exceptions (timeouts, connection failures, etc.)

---

### **2. Identify Common Dependencies**

Two services failing together often share something:

* Database (e.g., Azure SQL Database)
* Cache (e.g., Redis)
* Messaging system (e.g., Azure Service Bus)
* External APIs

👉 Check if that shared dependency is down, slow, or misconfigured.

---

### **3. Check Infrastructure / Deployment Issues**

* Verify if there was:

  * A recent deployment
  * Configuration change
  * Scaling issue (CPU, memory)
* Check container/orchestration platform like Kubernetes

---

### **4. Look for Cascading Failures**

* One microservice might be calling another:

  * If Service A fails → Service B also fails
* Check service-to-service communication:

  * API timeouts
  * Circuit breaker triggers

👉 Ensure resilience patterns like:

* Retry
* Circuit breaker
* Timeout handling

---

### **5. Validate Network and Connectivity**

* Check:

  * DNS issues
  * Network latency
  * Firewall rules

---

### **6. Fix the Issue Based on Root Cause**

Depending on findings:

* Restart failed services (temporary fix)
* Fix configuration or environment variables
* Scale resources (CPU/memory)
* Fix broken dependency (DB/API)
* Rollback faulty deployment

---

### **7. Prevent Future Failures**

* Implement:

  * Health checks
  * Circuit breaker pattern
  * Distributed tracing (e.g., OpenTelemetry)
  * Alerts & monitoring
* Add fallback mechanisms

---

## **Short Summary (for quick interview answer)**

> If two microservices fail simultaneously, I first check logs and monitoring to identify patterns. Then I look for shared dependencies like database, cache, or messaging systems. I verify recent deployments and infrastructure issues, and check for cascading failures between services. Once the root cause is identified, I fix it by resolving the dependency issue, scaling resources, or rolling back changes. Finally, I implement resilience patterns and monitoring to prevent future failures.

---
