# Databases

Two types of requirements:

- **Functional** (what problem we need to solve, ex.: sell product to users)
- **Non-functional** (ex: scale)

Choice of database is influenced by non-functional requirements.

## Terms:

- CDN: content delivery network (think same image is copied in Europe and South America for quicker loading time)
- consistency: written data is immediately available when reading
- OLAP: Online analytics processing (heavy analytic queries)
- OLTP: Online transaction processing (fast small transaction, e.g. place order)
- sharding: spreading data across multiple nodes
- TTL: time to live (object will self delete in 1 hour)

## Storage types:

### Relational Databases
- Data fits well into a fixed structure (table)
- Provide ACID capabilities (transactionality and consistency)
- Trade-offs: less horizontally scalable. Monolithic RDBMSs (e.g. Postgres struggle with sharding)
- Data examples: Users, Patients
- DB Examples: MySQL, Postgres, SQL Server, Oracle

### Blob Storage
- Optimized for large binary data (e.g. images, videos, PDFs, ..)
- Files are served as a whole (not used for structured querying - just object retrieval via key)
- Example: For system design interview use S3 (for storage) + CDN (for delivery)

### Text Search Capabilities
- Provide full-text search and fuzzy matching (configurable edit distance)
- Data consistency may not be guaranteed (not a source of truth)
- Used as a supplement to a primary DB
- Examples: SOLR, ElasticSearch (both built on top of Apache Lucene)

### Time Series Databases
- Extension of Relational DBs with some limitations
- Data is keyed by timestamps
- Optimized for append-only data and range queries over time intervals
- Poor for arbitrary look-ups or joins
- Examples: InfluxDB, Prometheus
- Tradeoff: typically lack strong consistency, eventual consistency is common

### Document Databases
- Schemaless: can store arbitrarily shaped JSONs
- Tradeoffs: great for flexible evolving schemas (e.g. product catalog) but indexing and querying depend on document shape
- Examples: MongoDB, CouchDB

### Datawarehouses
- Examples: Snowflake, BigQuery, Redshift
- Optimized for analytical workloads, batch inserts, and read-heavy aggregation across large data sets
- High latency, not real-time. Cost is proportional to data volume and access frequency
- Typically used for off-line reporting

### Caching Solutions
- Used to reduce load on resources for infrequently changing data
- Typically come as key-value stores: key is typically built as a composition of API or Query params, values are structured as expected returned data
- Example: Redis or Memcached (Redis is sufficient for system design interview)
- Note: cache must never be used as a source of truth
- Cache invalidation and TTL are important concerns

### Columnar DBs
- Data is organized in columns under the hood enabling fast aggregation - ideal for analytics
- Ever-increasing data (data keeps building up — example, Amazon Orders, geo locations of Uber Drivers)
- Columnar DB typically references storage format used in data warehousing rather than a separate system

---

## System Design Interview Note:

### Mention why not use a single DB
- Reinforces understanding of tradeoffs and separation of concerns

### Example Breakdown: Amazon DB Choices
- Inventory -> Relational DB (Postgres): fixed schema, ACID compliant
- Product Description -> Document DB (Mongo): flexible schema to handle diverse product types (TV vs T-shirt)
- Orders -> Wide-column store (Cassandra): high write throughput, append-only workload, eventual consistency is acceptable
- Order workflow -> Relational + Event-Driven: Inventory decrement (Postgres), order recording (Cassandra), consider using Kafka to decouple

