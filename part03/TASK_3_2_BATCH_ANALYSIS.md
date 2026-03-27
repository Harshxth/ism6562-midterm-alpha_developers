5.2 Task 3.2: Dual-Write Consistency Challenge (2 pts)

Task 3.2: Written Analysis: The Dual-Write Challenge
In a distributed environment, executing two independent INSERT statements creates a significant risk of partial failure. If the write to orders_by_customer succeeds but the write to orders_by_product fails due to a network timeout or node crash, the database becomes inconsistent. A user might see their order in their history, but the business inventory would show it as missing.

To handle this in a production environment, you should use a Logged Batch. Unlike a standard batch used for performance, a logged batch is specifically designed to ensure atomicity.

When you submit a logged batch, the coordinator node first records the entire operation in a system table called the batchlog. This log is replicated to other nodes before the actual data writes begin. If the process is interrupted, the cluster uses this log to replay the missing writes automatically. This ensures that Bob Smith’s order is eventually written to both tables or neither, maintaining the data integrity required for the "Query-First" design.

