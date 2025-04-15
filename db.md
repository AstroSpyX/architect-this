# Databases for System Design Interviews

## Introduction

Two types of requirements:

- **Functional** (what problem we need to solve, ex.: sell product to users)
- **Non-functional** (ex: scale)

Choice of database is influenced by non-functional requirements.

### Related Terminology:
(not directly related to DBs but good to be aware)
- **CDN**: Content delivery network (think same image is copied across servers in Europe and South America for low latency delivery)
- **Consistency**: Written data is immediately available when reading
- **OLAP**: Online analytics processing (refers to storage type optimized for heavy analytic queries)
- **OLTP**: Online transaction processing (fast small transaction, e.g. place order, update user record)
- **Sharding**: Spreading data across multiple nodes to spread load across instances
- **TTL**: Time to live (object will self delete in 1 hour)

## CAP Theorem:
Provides theoretical framework that addresses trade-off between three aspects of distributed systems: (C)onsistency, (A)vailability, and (P)artition tolerance

Namely, the theorem states that a distributed system cannot maintain the same high level of Consistency and Availability if partitioned.

In other words, if consistency is more important than availability, the system may go down in case of partial network failure. If availability is more important, then the system will remain available in partitioned state but consistency will be sacrificed. 

**Important Note**: Systems typically choose between CP (consistency/partition tolerance) or AP (availability/partition tolerance). CA systems exist only in theoretical non-partitioned networks.

CAP Theorem finds important application in the domain of Databases. Namely, two main subclasses SQL and NoSQL aim to emphasize different aspects of the system. SQL will remain highly consistent but may become unavailable during partitioning events, but NoSQL DBs will remain available with partial loss of consistency among data across nodes. 

## Main Database Categories

### 1. Relational Databases (RDBMS AKA SQL-type)
- **Characteristics**:
  - Provide ACID capabilities
    - **Atomicity**: Operations can be packaged as a transaction. Either all or none changes are committed to DB
    - **Consistency**: Guarantees that after transaction, the DB remains in a valid state enforcing all rules and constraints
    - **Isolation**: Transactions are not to interfere with each other. Transactions affecting the same data will be executed one by one
    - **Durability**: Once transaction is committed, the data will remain intact even in case of system failure 
  - **Data model**: Designed to handle relational data well (Data fits into a fixed relational structure referred to as tables)
  - **Priority**: Consistency
  - **Scalability considerations**:
    - Vertical scalability (adding more processing power to a single node)
    - Doesn't handle sharding well
    - Might experience degraded performance under high loads or during recovery process (downtime is expected)
- **Examples**: MySQL, PostgreSQL, SQL Server, Oracle
- **Use cases**: Financial data, inventory, transactions requiring strong consistency

### 2. NoSQL Databases
- **Characteristics**:
  - Offer BASE capabilities:
    - **Basically Available**: System will remain available during partial failures
    - **Soft State**: Replicas might temporarily be in an inconsistent state
    - **Eventual Consistency**: Replicas will eventually synchronize, ensuring the data will reflect the latest changes
  - **Data model**: Varies (can store documents, key-value pairs, graph-based data)
  - **Priority**: Availability
  - **Scalability**: Scales horizontally well
  - **Performance under load**: Maintains availability, may sacrifice consistency
  - **Use cases**: Social media, content delivery, OLAP

## NoSQL Database Types

### 1. Document Databases
- **Schemaless**: Can store arbitrarily shaped JSONs
- **Tradeoffs**: Great for flexible and evolving schemas (e.g., product catalog) but indexing and querying depend on document shape
- **Examples**: MongoDB, CouchDB
- **Use cases**: Content management, product catalogs, user profiles

### 2. Key-Value Stores
- Simplest NoSQL database type
- **Data model**: Each item is stored as a key-value pair where the key is unique
- Optimized for high-throughput, low-latency operations
- Highly scalable and distributed
- Limited querying capabilities (primarily by key)
- **Examples**: Redis (also used for caching), DynamoDB, Riak
- **Use cases**: Session storage, shopping carts, user preferences, real-time leaderboards

### 3. Wide-column Stores
- Data stored in column families rather than traditional rows
- Each row can have different columns, providing schema flexibility
- Optimized for high write throughput and storing massive amounts of data
- Better for analytical queries than traditional row-based databases
- **Examples**: Cassandra, HBase, Google's Bigtable
- **Use cases**: Time-series data, event logging, IoT data, systems that need to scale horizontally

### 4. Graph Databases
- Optimized for highly connected data with complex relationships
- **Data model**: Nodes (entities) connected by edges (relationships)
- Queries are expressed in terms of traversing the graph
- Excellent for relationship-heavy data where the connections are as important as the data
- **Examples**: Neo4j, Amazon Neptune, JanusGraph
- **Use cases**: Social networks, recommendation engines, fraud detection, knowledge graphs

## Specialized Data Storage Solutions

### 1. Blob Storage
- Optimized for large binary data (e.g., images, videos, PDFs, etc.)
- Files are served as a whole (not used for structured querying - just object retrieval via key)
- **Example**: For system design interview use S3 (for storage) + CDN (for delivery)
- **Use cases**: Media storage, file repositories, backup storage

### 2. Text Search Engines
- Provide full-text search and fuzzy matching (configurable edit distance)
- Data consistency may not be guaranteed (not a source of truth)
- Used as a supplement to a primary DB solution
- **Examples**: SOLR, ElasticSearch (both built on top of Apache Lucene)
- **Use cases**: Site search, product search, log analysis

### 3. Time Series Databases
- Extension of Relational DBs with some limitations and optimization
- Data is keyed by timestamps
- Optimized for append-only data and range queries over time intervals
- Poor performance for arbitrary look-ups or joins
- **Examples**: InfluxDB, Prometheus, TimescaleDB
- **Tradeoff**: Typically lack strong consistency, eventual consistency is common
- **Use cases**: IoT data, monitoring metrics, financial market data

### 4. Data Warehouses
- **Examples**: Snowflake, BigQuery, Redshift, Hadoop
- Optimized for analytical workloads, batch inserts, and read-heavy aggregation across large data sets (OLAP)
- High latency, not real-time. Cost is proportional to data volume and access frequency
- Typically used for offline reporting
- **Use cases**: Business intelligence, data analytics, historical reporting

### 5. Caching Solutions
- Used to reduce load on resources for infrequently changing data
- Typically come as key-value stores: key is typically built as a composition of API or Query params, values are structured as expected returned data
- **Example**: Redis or Memcached (Redis is sufficient for system design interview)
- **Note**: Cache must never be used as a source of truth
- Cache invalidation and TTL are important concerns
- **Use cases**: Frequently accessed data, session storage, API response caching

### 6. Columnar Databases
- Data is organized in columns under the hood enabling fast aggregation - ideal for analytics
- Ever-increasing data (data keeps building up — example, Amazon Orders, geo locations of Uber Drivers)
- Columnar DB typically references storage format used in data warehousing rather than a separate system (e.g., PostgreSQL is not a columnar Database but it can be configured to use columnar storage)
- **Examples**: MariaDB ColumnStore, Amazon Redshift, Google BigQuery
- **When to use**: When queries are known in advance and performed to run computationally heavy aggregations over fixed set of columns across large range of keys
- **Use cases**: Business intelligence, reporting, analytics

## System Design Interview Tips

### Explain Why Not to Use a Single DB
- Reinforces understanding of tradeoffs and separation of concerns
- Demonstrates awareness of different data access patterns
- Shows knowledge of distributed systems principles

### Additional Tips
- Be ready to explain which properties of each database type are most relevant to your specific design problem
- Discuss trade-offs between consistency, availability, and partition tolerance for your specific use case
- Consider data access patterns: read-heavy vs. write-heavy workloads
- Discuss database migration strategies if the system needs to evolve
- Think about how to handle database failures and recovery

### Example Breakdown: Amazon DB Choices
- **Inventory** → Relational DB (PostgreSQL): Fixed schema, ACID compliant
- **Product Description** → Document DB (MongoDB): Flexible schema to handle diverse product types (TV vs T-shirt)
- **Orders** → Wide-column store (Cassandra): High write throughput, append-only workload, eventual consistency is acceptable
- **Order workflow** → Relational + Event-Driven: Inventory decrement (PostgreSQL), order recording (Cassandra), consider using Kafka to decouple
- **Search functionality** → ElasticSearch: For fuzzy matching and full-text search across products
- **Shopping cart** → Key-value store (Redis): Fast, temporary storage with expiration
- **Recommendations** → Graph database (Neo4j): To model complex relationships between users and products