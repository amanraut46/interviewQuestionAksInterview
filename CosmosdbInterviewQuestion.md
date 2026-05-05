# 1⚡ How to Optimize Performance of Cosmos DB
- **Partitioning:** Choose a good partition key to evenly distribute data and avoid hot partitions.
- **Indexing:** Customize indexing policies; exclude unnecessary fields to reduce overhead.
- **Request Units (RUs):** Provision RUs based on workload; use autoscale for variable traffic.
- **Query Optimization:** Use filters on partition keys, avoid cross-partition queries when possible.
- **Caching:** Implement caching at the application level to reduce repeated queries.
- **Bulk Operations:** Use bulk executor library for large inserts/updates.