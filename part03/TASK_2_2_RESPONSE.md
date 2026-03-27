4.2 Task 2.2: Attempt an Invalid Query (2 pts)

bash-3.2$ docker compose exec cassandra-node1 cqlsh
Connected to TechRetailCluster at 127.0.0.1:9042
[cqlsh 6.1.0 | Cassandra 4.1.10 | CQL spec 3.4.6 | Native protocol v5]
Use HELP for help.
cqlsh> SELECT * FROM techretail_orders.orders_by_customer WHERE product_id = 1;
InvalidRequest: Error from server: code=2200 [Invalid query] message="Cannot execute this query as it might involve data filtering and thus may have unpredictable performance. If you want to execute this query despite the performance unpredictability, use ALLOW FILTERING"

Task 2.2: Written Analysis: The Partitioning Problem
Cassandra rejects this query because it violates the core mechanics of a distributed NoSQL system. In the orders_by_customer table, the partition key is customer_id. Cassandra uses a hash of this key to determine exactly which node in the cluster stores that specific user's data. Because your query filters by product_id instead, Cassandra cannot pinpoint the correct node. Fulfilling this request would require a "Full Cluster Scan," checking every node and partition, which is a massive performance bottleneck that does not scale.

The query-first design solves this by duplicating data into specialized tables for specific access patterns. Instead of forcing a relational search on a partitioned table, we create orders_by_product where product_id is the partition key. This ensures every query is a direct, single-partition read, delivering the predictable low latency required for high velocity 2026 data environments.

