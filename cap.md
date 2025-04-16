# CAP Theorem

## What is it?

The **CAP Theorem** is a theoretical framework that describes the limitations of distributed systems in the presence of **network partitions**. It states that a distributed system can **only guarantee two out of the three** desired properties: **Consistency**, **Availability**, and **Partition Tolerance**.

---

## Terminology

- **Distributed system**: A collection of independent nodes connected by a communication network.

- **Network partition**: A condition in which a subset of nodes becomes unreachable from the rest due to communication failure, effectively splitting the system into isolated groups.

---

## The Three Properties of CAP

- **Consistency (C)**: Every read returns the **most recent write** or an **explicit error**. All nodes see the same data at the same time.

- **Availability (A)**: Every request received by a **non-failing node** results in a **non-error response**, but it may not reflect the most recent write.

- **Partition Tolerance (P)**: The system continues to operate **despite arbitrary message loss, delay, or reordering** between nodes—i.e., it handles network partitions gracefully.

---

## CAP Theorem – Two Formulations

### Formulation I (Original)

> In any distributed system, it is **impossible** to simultaneously guarantee all three properties (C, A, and P).

### Formulation II (Practical View)

> In the presence of a **network partition**, a distributed system must choose **either Consistency or Availability**, but not both.

---

## Trade-offs: What happens during a partition

- **CA (Consistency + Availability)**: Works **only in the absence of partitions**. Assumes a perfectly reliable network, which is unrealistic in real-world distributed systems. Single-node databases (e.g., traditional RDBMS) can be considered CA systems.

- **CP (Consistency + Partition Tolerance)**: The system ensures consistent data across partitions but may **deny requests** (sacrificing availability).  
  **Examples**: MongoDB (with strong consistency), HBase.

- **AP (Availability + Partition Tolerance)**: The system remains operational and responsive during partitions but may return **stale or inconsistent data** (sacrificing consistency).  
  **Examples**: Cassandra, DynamoDB.

---

## Summary

The CAP Theorem is a foundational principle in distributed systems design. It forces engineers to make informed trade-offs based on business and technical requirements:

- **Banking systems** prioritize **Consistency** over Availability.
- **Social media feeds** prioritize **Availability** and responsiveness, even if consistency is eventual.

---

## Remarks

1. **Consistency is not binary.** Many systems support **eventual consistency**, where all nodes converge to the same state over time. Some databases (e.g., Cassandra) offer **tunable consistency** levels—such as `ONE`, `QUORUM`, `ALL`—to balance availability and data accuracy per operation.

2. **Availability varies in degree**. A system can be “Available” in the CAP sense but may not meet SLAs for **high availability (HA)**—i.e., very low downtime.

3. **PACELC** extends CAP by adding latency/consistency trade-offs even **when there is no partition**.  
   > **PACELC**: *If* a partition occurs (P), a system must choose between **Availability (A)** or **Consistency (C)**. *Else* (when no partition), it must choose between **Latency (L)** and **Consistency (C)**.
