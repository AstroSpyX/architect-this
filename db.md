# Databases

Two types of requirements:

- **Functional** (what problem we need to solve, ex.: sell product to users)
- **Non-functional** (ex: scale)

Choice of database is influenced by non-functional requirements.

## Storage types:

### Relational Databases
- Data fits well into fixed structure (table)
- Provide ACID capabilities (transactionality and consistency)
- Example: Users, Patients

### Blob Storage
- Typically images/videos/PDF documents/other files
- Files are served as a whole (content of files is not accessed)
- For system design interview: great example is CDN + S3

### Text Search Capabilities
- Provide fuzzy search
- Data consistency may not be guaranteed (not a source of truth, just a search)
- Examples: SOLR, ElasticSearch (both built on top of Apache Lucene)

### Time Series Databases
- Extension of Relational DBs with some limitations
- Data is keyed by timestamps
- Optimized for sequential updates (no good support for random updates or reads); instead, give me data within a range
- Examples: InfluxDB, Prometheus

### Document Databases
- These do not have a fixed schema (can store JSON with arbitrary attributes and data types)

### Datawarehouses
- Typically used for analytics on the entire company data, not transactional
- Typically used for off-line reporting
- Example: Snowflake and Redshift

### Caching Solutions
- Used to reduce load on resources for infrequently changing data
- Typically come as key-value stores: key is built as a composition of API or Query params, values are structured as expected data is structured
- Example: Redis or Memcached (Redis is enough for system design interview)

### Columnar DBs
- Very few queries
- Ever-increasing data (data keeps building up — example, Amazon Orders, geo locations of Uber Drivers)

---

## Typically it is expected to provide solutions with multiple system design interviews.

### Example: Database choices for Amazon

- **Relational DB** for storing Amazon Inventory (product ID, seller ID) — limited number of well-structured columns
- **MongoDB** for Product Description (product TV has drastically different attributes than product T-Shirt)
- **Cassandra** for orders (since orders are increasing non-linearly as more users place orders)
- Once a user places an order, we remove the item from inventory (Postgres) and add the order to order storage (Cassandra)
