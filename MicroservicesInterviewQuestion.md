## **Interview Question**

 # 1 What would you do if two microservices start failing simultaneously? How would you identify the root cause and fix the issue?**

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
# 2 What is a maximum threshold value in a system or microservices architecture?**

---

## **Answer**

A **maximum threshold value** is the **upper limit** set for a specific metric in a system. When this limit is exceeded, it indicates a potential problem, and the system may trigger alerts, throttling, or fail-safe mechanisms.

---

### **Common Examples in Microservices**

1. **CPU Usage Threshold**

   * Example: 80% CPU utilization
   * If exceeded → scale up or alert

2. **Memory Usage Threshold**

   * Prevents out-of-memory crashes

3. **Request Rate (Rate Limiting)**

   * Example: 1000 requests/sec
   * If exceeded → requests may be throttled

4. **Response Time Threshold**

   * Example: 2 seconds
   * If exceeded → considered performance degradation

5. **Error Rate Threshold**

   * Example: 5% failure rate
   * If exceeded → trigger alerts or circuit breaker

---

### **In Microservices Context**

* Helps in:

  * **Auto-scaling**
  * **System protection (avoiding overload)**
  * **Monitoring and alerting**

* Often configured using tools like:

  * Azure Monitor
  * Prometheus

---

### **Real Example**

If a service has:

* Max threshold = **1000 requests/sec**

👉 When traffic goes beyond this:

* System may:

  * Reject new requests (throttling)
  * Scale horizontally (add instances)
  * Trigger alerts

---

### **Short Interview Answer**

> A maximum threshold value is the upper limit set for a system metric such as CPU, memory, request rate, or error rate. When this limit is exceeded, it indicates a potential issue, and the system can trigger alerts, scaling, or protective actions like throttling to maintain stability.

---
Here’s a **proper interview-style question and answer** you can use:

---

# 3 How do you implement a Retry Pattern in microservices?**

---

## **Answer**

The **Retry Pattern** is used in microservices to handle **transient failures** (temporary issues like network glitches, timeouts, or service unavailability) by retrying the failed request after a short delay.

---

### **1. When to Use Retry Pattern**

* Temporary network failures
* Service timeout
* डाउन (temporarily unavailable) dependency
* HTTP status codes like **500, 502, 503, 504**

---

### **2. Key Concepts of Retry Pattern**

#### **a. Retry Count**

* Number of attempts before failing
* Example: 3 retries

#### **b. Delay Between Retries**

* Fixed delay (e.g., 2 seconds)
* Exponential backoff (recommended)

#### **c. Exponential Backoff**

* Wait time increases after each retry
* Prevents overloading system

Example:

* 1st retry → 2 sec
* 2nd retry → 4 sec
* 3rd retry → 8 sec

---

### **3. Implementation in .NET (Best Practice)**

Use libraries like Polly

#### **Example Code**

```csharp
var retryPolicy = Policy
    .Handle<HttpRequestException>()
    .WaitAndRetryAsync(3, retryAttempt =>
        TimeSpan.FromSeconds(Math.Pow(2, retryAttempt)));

await retryPolicy.ExecuteAsync(async () =>
{
    var response = await httpClient.GetAsync("https://service-api");
    response.EnsureSuccessStatusCode();
});
```

---

### **4. Retry with HttpClient Factory**

```csharp
services.AddHttpClient("MyClient")
    .AddPolicyHandler(Policy
        .Handle<HttpRequestException>()
        .WaitAndRetryAsync(3, retryAttempt =>
            TimeSpan.FromSeconds(Math.Pow(2, retryAttempt))));
```

---

### **5. Important Best Practices**

#### ✅ Retry Only for Transient Errors

* Do NOT retry:

  * 400 Bad Request
  * Authentication failures

#### ✅ Use Circuit Breaker with Retry

* Combine with Circuit Breaker to avoid repeated failures
* Example tools: Polly

#### ✅ Add Timeout

* Prevent long waiting

#### ✅ Logging

* Log each retry attempt for debugging

---

### **6. Where to Implement Retry**

* API calls between microservices
* Database calls (if transient)
* Message processing (e.g., Azure Service Bus retry policies)

---

### **7. Real-Time Scenario**

Service A calls Service B:

* Service B is temporarily slow
* Service A retries 3 times with exponential backoff
* If still failing → fallback or circuit breaker triggers

---

## **Short Interview Answer**

> The Retry Pattern is used to handle transient failures by retrying failed operations after a delay. In microservices, it is typically implemented using libraries like Polly with exponential backoff. We configure retry count, delay strategy, and ensure retries are applied only to temporary errors. It is often combined with circuit breakers and logging for better resilience.

---
Here’s a **properly framed interview question and answer** you can use:

---
# 4 A SQL query works fine in the local environment but runs very slowly in the deployed (production) environment. How would you troubleshoot and fix it?**

---

## **Answer**

This issue usually happens due to **environment differences, data volume, indexing, or execution plan changes**. I would follow a structured approach:

---

### **1. Compare Data Volume**

* Local DB → small dataset
* Production DB → large dataset

👉 Large data can slow down queries (full table scans, joins, etc.)

---

### **2. Check Indexes**

* Ensure required indexes exist in production
* Missing or different indexes can cause performance issues

Example:

* Index on WHERE / JOIN columns

---

### **3. Analyze Execution Plan**

* Compare execution plans in local vs production
* Look for:

  * Table scans instead of index seeks
  * Expensive joins
  * Missing index recommendations

---

### **4. Parameter Sniffing Issue**

* SQL Server may cache a bad execution plan

👉 Fix:

* Use `OPTION (RECOMPILE)`
* Use optimized queries or stored procedures

---

### **5. Check Environment Differences**

* SQL Server version/config differences
* Hardware differences (CPU, RAM, disk I/O)
* Network latency

Example platform: Microsoft SQL Server

---

### **6. Blocking and Deadlocks**

* In production, multiple users may:

  * Lock tables
  * Cause blocking

👉 Check:

* Active sessions
* Long-running transactions

---

### **7. Update Statistics**

* Outdated statistics can cause bad query plans

👉 Run:

```sql
UPDATE STATISTICS table_name;
```

---

### **8. Check Query Design**

* Avoid:

  * SELECT *
  * Unnecessary joins
  * Functions on indexed columns

---

### **9. Network Latency**

* Query may be fast in DB but slow in app due to:

  * Network delay
  * Large result set transfer

---

### **10. Fix Based on Findings**

* Add/optimize indexes
* Rewrite query
* Update stats
* Fix blocking issues
* Scale database resources

---

## **Short Interview Answer**

> If a SQL query is fast locally but slow in production, I first check data volume and indexes, then compare execution plans. I also look for parameter sniffing, blocking, and outdated statistics. Environment differences like hardware and network latency are also important. Based on findings, I optimize indexes, rewrite the query, or update statistics to improve performance.

---
Here’s a **well-structured interview question and answer** you can use:

---

# 5 During a file upload, the server crashes. How would you handle this situation in a microservices or web application?**

---

## **Answer**

If a server crashes during file upload, it can lead to **data loss, partial uploads, and poor user experience**. To handle this, I would design the system for **fault tolerance and recovery**.

---

### **1. Use Chunked (Resumable) Upload**

* Break file into smaller chunks
* Upload each chunk separately
* If failure happens → resume from last successful chunk

👉 This prevents re-uploading the entire file

---

### **2. Store Upload State**

* Maintain upload progress in:

  * Database
  * Cache (e.g., Redis)

Example:

* FileId, ChunkNumber, Status

---

### **3. Use Temporary Storage**

* Save files in temp location before final processing
* Move to permanent storage only after complete upload

Example cloud storage: Azure Blob Storage

---

### **4. Implement Retry Mechanism**

* Retry failed chunk uploads automatically
* Use exponential backoff

---

### **5. Use Queue-Based Processing**

* After upload, send message to queue
* Process file asynchronously

Example:

* Azure Service Bus

👉 Prevents server overload and crash

---

### **6. Add File Size Limits & Validation**

* Restrict large uploads
* Validate file type and size before processing

---

### **7. Use Load Balancing & Scaling**

* Distribute traffic across servers
* Auto-scale when high load

Example: Kubernetes

---

### **8. Implement Timeout & Cancellation**

* Avoid long-running requests
* Allow user to cancel upload

---

### **9. Logging & Monitoring**

* Track failures using:

  * Azure Monitor

---

### **10. Graceful Recovery**

* On server restart:

  * Resume incomplete uploads
  * Clean up corrupted files

---

## **Real-Time Flow**

1. User uploads file → split into chunks
2. Each chunk uploaded with retry
3. Progress stored in DB/Redis
4. After completion → message sent to queue
5. Background service processes file

---

## **Short Interview Answer**

> To handle server crashes during file upload, I use chunked uploads with resumability, store upload progress, and implement retries. I also use temporary storage and queue-based processing to avoid overloading the server. Additionally, I enable scaling, logging, and monitoring to ensure reliability and quick recovery.

---
Here’s a **clean and professional interview-style answer**:

---

# 6 How do you handle multiple users updating the same row in a database using C#?**

---

## **Answer**

When multiple users try to update the same row simultaneously, it can lead to **data inconsistency or lost updates**. This problem is handled using **concurrency control mechanisms**.

There are mainly two approaches:

---

## **1. Optimistic Concurrency (Recommended)**

### **Concept**

* Assume conflicts are rare
* Allow multiple users to read and update
* Before saving, check if data has changed

---

### **How to Implement in C# (Entity Framework)**

Use a **RowVersion / Timestamp column**

```csharp id="p9o1k2"
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }

    [Timestamp]
    public byte[] RowVersion { get; set; }
}
```

---

### **Handling Conflict**

```csharp id="z8x7c6"
try
{
    context.SaveChanges();
}
catch (DbUpdateConcurrencyException ex)
{
    // Handle conflict
    // Reload data or inform user
}
```

---

### **What Happens**

* If another user updates the row → RowVersion changes
* Your update fails → exception thrown
* You can:

  * Reload latest data
  * Ask user to retry

---

## **2. Pessimistic Concurrency**

### **Concept**

* Lock the row while updating
* Prevent others from modifying

---

### **How to Implement**

* Use SQL locks (e.g., `WITH (UPDLOCK, ROWLOCK)`)

Example:

```sql id="z3m8n1"
SELECT * FROM Products WITH (UPDLOCK)
WHERE Id = 1;
```

---

### **Drawback**

* Can reduce performance
* May cause deadlocks

---

## **3. Using Transactions**

* Wrap operations in a transaction
* Ensure atomic updates

Example:

```csharp id="l2k9m0"
using (var transaction = context.Database.BeginTransaction())
{
    // read + update
    context.SaveChanges();
    transaction.Commit();
}
```

---

## **4. Best Practice**

* Prefer **Optimistic Concurrency** in most applications
* Use **Pessimistic** only when conflicts are frequent
* Always handle exceptions gracefully

---

## **Short Interview Answer**

> To handle multiple users updating the same row, I use concurrency control. In C#, optimistic concurrency using a RowVersion column is preferred, where conflicts are detected during update and handled using exceptions. Pessimistic locking can also be used but may impact performance. Transactions ensure data consistency.

---
Here’s a **properly framed interview question and answer** you can use:

---

# 7 What would you do if message sending fails in a service bus? How do you handle it in a microservices architecture?**

---

## **Answer**

If message sending fails in a messaging system like Azure Service Bus, it can impact communication between microservices. To handle this, I would implement **reliability and fault-handling mechanisms**.

---

### **1. Implement Retry Mechanism**

* Retry sending the message for **transient failures**
* Use **exponential backoff** to avoid overload

Example:

* Retry 3–5 times with increasing delay

---

### **2. Use Dead Letter Queue (DLQ)**

* If message fails after retries → move to **Dead Letter Queue**
* Prevents message loss
* Allows later analysis and reprocessing

---

### **3. Ensure Idempotency**

* Duplicate messages may occur due to retries
* Design consumers to handle duplicates safely

Example:

* Use unique message ID

---

### **4. Logging and Monitoring**

* Log all failures and retries
* Monitor using tools like:

  * Azure Monitor

---

### **5. Use Transactions (If Needed)**

* Ensure message sending and DB update happen together
* Prevent inconsistent state

---

### **6. Fallback Mechanism**

* If sending fails:

  * Store message in DB (Outbox Pattern)
  * Retry later via background job

---

### **7. Circuit Breaker Pattern**

* If service bus is down:

  * Stop sending messages temporarily
  * Resume after recovery

---

### **8. Validate Configuration**

* Check:

  * Connection string
  * Queue/topic name
  * Permissions

---

### **9. Network & Throttling Handling**

* Handle:

  * Network failures
  * Rate limits (throttling)

---

### **10. Use Outbox Pattern (Best Practice)**

* Save message in DB first
* Background worker sends message to Service Bus
* Ensures **no message loss**

---

## **Real-Time Flow**

1. App tries to send message
2. If fails → retry
3. If still fails → store in DB (outbox)
4. Background service retries
5. If permanently fails → move to DLQ

---

## **Short Interview Answer**

> If message sending fails in Service Bus, I implement retries with exponential backoff, and if it still fails, I use a Dead Letter Queue to avoid message loss. I also use the Outbox pattern to store messages temporarily and retry later. Additionally, I ensure idempotency, logging, and monitoring to handle failures reliably.

---





