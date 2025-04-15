# Databases

Two types of requirements:

- **Functional** (what problem we need to solve, ex.: sell product to users)
- **Non-functional** (ex: scale)

Choice of database is influenced by non-functional requirements.

##### Related Terminology:
(not directly related to DBs but good to be aware)
- CDN: content delivery network (think same image is copied across servers in Europe and South America for low latency delivery)
- consistency: written data is immediately available when reading
- OLAP: Online analytics processing (refers to storage type optimized for heavy analytic queries)
- OLTP: Online transaction processing (fast small transaction, e.g. place order, update user record)
- sharding: spreading data across multiple nodes to spread load across instances
- TTL: time to live (object will self delete in 1 hour)

## CAP Theorem:
Provides thoretical framework that addresses trade-off between three aspects of distributed systems: (C)onsistency, (A)valiability, and (P)artitioning

Namely, the theorem states that a distributes system cannot maintain the same high level of Consistency and Availability if partititoned.

In other words, if consistency is more important than availabilty, the system may go down in case of partial network failure. If availability, is more important then, the system will remain available in partiicioned state but consistency will be sacrificed. 

CAP Theorem finds important application in the domain of Databases. Namely, two main subclasses SQL and NoSQL aim to emphacise different aspects of the system. SQL will remains highly consistent but may become unavailable during partitioning events, but NoSQL DBs will remain available with partial loss of consistency among data across nodes. 

## Storage types: two main classes

#### Relational Databases (RDBMS AKA SQL-type)
- **Characteristics**:
  - provide ACID capabilities
    - Atomicity: operations can be packaed as a transaction. Either all or None changes are commit to DB
    - Consistency: guarantees that after transaction, the DB remains in a valid state enforcing all rules and constrains
    - Isolation: transactions are not to interfere with each other. Transactions affecting the same data will be executed one by one
    - Durability: once transaction is commited, the data will remain intact even in case of system failure 
  - Data model: designed to handel relation data well (Data fits into a fixed relational structure reffered as table)
  - Priority: consistency
  - Scalability considerations:
	- Vertical scalability (adding more processing power to a single node)
	- Doesn't handle sharding well
	- Might experience degraded performance under high loads or during recovery process (downtime is expected)
- **Examples**: MySQL, Postgres, SQL Server, Oracle
- Use cases: financial data, inventory

#### NoSQL Databases
- **Characteristics**:
  - offer BASE capabilities:
    - Bascically Available: system will remain available during partial falures
    - Soft State: replicas might temporarily be in an inconsisten state
    - Eventual Consistency: replicas will eventually syncronize, ensuring the data will reflect the latest changes
  - Data model: varies (can store document, key-value pairs, graph based data)
  - Piority: availability
  - Scalability: scales horizontaly well
  - Perforamnce under load: maintains availabilty, may sacrifice consistency
  - Use cases: social media, content delivery, OLP   
#### NoSQL Database sub-classes
### Blob Storage
- Optimized for large binary data (e.g. images, videos, PDFs, ..)
- Files are served as a whole (not used for structured querying - just object retrieval via key)
- Example: For system design interview use S3 (for storage) + CDN (for delivery)
#### Text Search Capabilities
- Provide full-text search and fuzzy matching (configurable edit distance)
- Data consistency may not be guaranteed (not a source of truth)
- Used as a supplement to a primary DB solution
- Examples: SOLR, ElasticSearch (both built on top of Apache Lucene)
#### Time Series Databases
- Extension of Relational DBs with some limitations and optimization
- Data is keyed by timestamps
- Optimized for append-only data and range queries over time intervals
- Poor performance for arbitrary look-ups or joins
- Examples: InfluxDB, Prometheus
- Tradeoff: typically lack strong consistency, eventual consistency is common
#### Document Databases
- Schemaless: can store arbitrarily shaped JSONs
- Tradeoffs: great for flexible and evolving schemas (e.g. product catalog) but indexing and querying depend on document shape
- Examples: MongoDB, CouchDB
#### Datawarehouses
- Examples: Snowflake, BigQuery, Redshift, Hadoop
- Optimized for analytical workloads, batch inserts, and read-heavy aggregation across large data sets (OLAP)
- High latency, not real-time. Cost is proportional to data volume and access frequency
- Typically used for off-line reporting
#### Other Types of Storage


### Other types of storage and subclasses
#### Caching Solutions
- Used to reduce load on resources for infrequently changing data
- Typically come as key-value stores: key is typically built as a composition of API or Query params, values are structured as expected returned data
- Example: Redis or Memcached (Redis is sufficient for system design interview)
- Note: cache must never be used as a source of truth
- Cache invalidation and TTL are important concerns
#### Columnar DBs
- Data is organized in columns under the hood enabling fast aggregation - ideal for analytics
- Ever-increasing data (data keeps building up — example, Amazon Orders, geo locations of Uber Drivers)
- Columnar DB typically references storage format used in data warehousing rather than a separate system (e.g. Postgres is not a columnar Database but it can be configured to use columnar storage)
- Examples: MariaDB
- When to use: when queries are known in advance and performed to run computationally heavy aggregations over fixed set of columns across large range of keys 

### Key-Value Stores
need help with this

#### Wide-column Stores
need help with this

---

### System Design Interview Note:

## Mention why not use a single DB
- Reinforces understanding of tradeoffs and separation of concerns

## Example Breakdown: Amazon DB Choices
- Inventory -> Relational DB (Postgres): fixed schema, ACID compliant
- Product Description -> Document DB (Mongo): flexible schema to handle diverse product types (TV vs T-shirt)
- Orders -> Wide-column store (Cassandra): high write throughput, append-only workload, eventual consistency is acceptable
- Order workflow -> Relational + Event-Driven: Inventory decrement (Postgres), order recording (Cassandra), consider using Kafka to decouple

