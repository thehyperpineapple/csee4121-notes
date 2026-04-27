## Network Topology
- Bus - used for utility, gas connections
- Ring - few cables
- Mesh - lot of redundancy
- Star - lot of centralization
- Tree - generally used in data centers (hybridization of tree)
- Hybrid - combination of multiple models

Multipath routing:
internet -> core layer -> aggregation - > access layer (bottom most)

Switch Locations:
- Top of Rack
	- first router after compute node
	- all connected to end of row router
- End of Rack
- Core Rack

**Power Usage Effectiveness (PUE):**
$$
\text{PUE Ratio} = \frac{\text{Total facility power}}{\text{Server or Network Power}}
$$
- Early data centers - 1.7 - 2.0 ratio = Inefficient 
- Now in 2024 - 1.15 (only 15% from optimal)
- Some can be 1.04. 
- Power is usually 25% of monthly operating cost, it is also one of the limiting factor in how large the data center can be
- Better power distribution - fewer transformers
- Liquid cooling for cooling

**Backup Power:**
- Massive amount of batteries to tolerate short glitches in power (until backup generators start back up)
- Glitches could occur due to natural disastrs
- Fuel replenishment 

**Energy Sources:**
- Data centers are being powered by renewable energy

**Fault tolerance:**
- Redundancy is solution to tolerate faults incase things break
- Make independent copies of all data
- Multiple independant network connections
- Copies of every service -> releases, update cycles, resource use

**Potential failures for a new data center:**
- hard drives failures, individual machine failures, blips for dns
- router failures, reloads
- network maintenances
- racks go wonky, failures
- network rewiring
- rack-move
- PDU (power delivery unit) failure, overheating

**Comparing AI datacenters to traditional ones:**
- **Similarity**
	- Same rack/row topology
	- Cooling still a big problem (GPU immersion cooling)
- Differences:
	- Compute: AI centers need thousands of GPUs, small ratio of CPUs/GPUs
	- Memory: Don't need as much traditional CPU memory, reauires lots of on-GPU high bandwidth networking both within a server and aceoss servers
	- Network: needs more demanding network requirements. Requires high bandwidth both within and across servers

**Where to build:**
- Plentiful, inexpensive electricity
- Good network connections
- Inexpensive land
- Geographically near users
- Available labor pool
- Politics

**General application guidance**
- organizer jobs and tasks into tiers
- localizers and colocates
- minimizes bandwidth usage
- uses right models (pubsub, RPCs, batch workflows)
- respects quotas
- provides telemetry for obeservation and adjustment

Easy to lose sight that our code runs somewhere physically. Cloud is not an abstract concept. Huge physical sites consuming power equivalent to entire cities

# Relational Model
- Entities and Relations
- Example Schema: Course Management System
	- Student(cuid: string, name: string, gpa: float)
	- Courses(cid: string, c-name: string, room: string)
	- Enrolled(cuid: string, cid: string, grade: string)

- **Relational Model Schemas:**
	- Organizing principle of data + operations
	- Relational model:
		- Strict structure
	- Schema: Blueprint of tables
		- Logical Schema - describes types, names
		- Physical Schema - describes data layout
		- Virtual Schema (Views) - derived tables

**Logical Data Independence:** Protection from logical structure changes in data
**Physical Data Independence:** Protection from Physical Layout Changes

**Python List Operations:**

| Operation        | What does it do                                                         |
| ---------------- | ----------------------------------------------------------------------- |
| Basic Types      | int, long, string                                                       |
| Map              | map(function, list) applies function to input list                      |
| Filter           | filter(function, list) returns sub list that satisfies filter condition |
| Reduce/Aggregate | runs a computation on a list and returns a result                       |
**SQL Queries on Tables**
Tables can be perceived as a <u>list of rows</u>
Basic Types $\rightarrow$ int32, int64, char\[n], float32, float64

Map + Filter in multiple tables
```sql
SELECT c1, c2
FROM t1,t2
WHERE condition 
```
Reduce/Aggregate
```sql
SELECT SUM(c1*c2) 
FROM T 
WHERE condition 
GROUP BY c3;
```

### Other Data Models
1. Key-Value:
	- Every piece of data is stored as a key mapped to a value
	- No schemas, No relationships. 
	- Great of caching, session storage and simple lookups
	- Ex: Redis, DynamoDB
2. Document:
	- Stores semi-structured documents (JSON, BSON) that might have nested fields
	- Each document can have different fields
	- Good for content management, user profiles and applications that need schema flexibility
	- Ex: MongoDB, CouchDB, Firestore
3. Wide-Column / Column-Family:
	- Data organized in columns rather than rows, rows can have different columns
	- Optimized for large dataset querying where it's required to read specific columns across many rows
	- Common for time-series analytics and write-heavy workloads
	- Ex: Cassandra, HBase, Google Bigtable
4. Graph:
	- Data is modeled as nodes (entities) and edges (relationships) with properties on both.
	- Traversing relationships is a first class operation rather than an expensive join
	- Ideal for social networksm fraud detection and recommendation engines
	- Ex: Neo4js, Amazon Neptune, TigerGraph
5. Time-Series:
	- Optimized for timestamped data points
	- Efficient compression and time-based queries
	- Used for metrics, IoT sensor data and monitoring
	- Ex: InfluxDB
6. Vector:
	- Stores high-dimensional embeddings and supports similarity search (nearest neighbor queries)
	- Essential for AI applications like semantic search and RAG systems
	- Examples: Pinecone, Milvus, pgvector

# SQL - Structured Query Language
- Language for querying and manipulating data
- Very high-level programming language
- Data Manipulation Language (DML): Query one or more table (Insert/delete/modify tuples)
- Data Definition Language (DDL): Define relational schema (create/alter/delete tables)

**Relation / Table:** Multi-set of tuples/rows having attributes specified by the schema
**Attribute / Column:** Typed data entry present in each tuple in the relation. Must have an <u>atomic</u> type in standard SQL

Atomic types - Char(), Varchar(), int, bigint, smallint, float, money, datetime

Number of tuples (rows) - Cardinality of the relation
Number of attributes (columns) - Arity of the relation

Schema of table - table name, attributes and types
Key - attribute who's values are unique. Keys are underlined. Also acts as unique identifier
```sql
Product(Pname: string, Price: float, Category: string, Manufacturer: string)
```

In this case key is $\rightarrow$ <u>Pname</u>: String

Key is in implicit constraint (a rule that the database must always follow) on which tuples can be in the relation
You cannot have two _different_ rows that share the same key.

**Declaring a schema:**
```sql
Students(cuid: string, name: string, gpa: float) 

CREATE TABLE Students ( 
	cuid CHAR(20), 
	name VARCHAR(50), 
	gpa float, 
	PRIMARY KEY (cuid), 
)
```

Columns can be constrained to be `NOT NULL`
Schemas and Constraints are how databases understand semantics of data
Keys are most important

### API (Application Programming Interface) Schema:
- Translation layer between external clients and internal database schema
- Can hide sensitive database details or expose computed/aggregated data not directly stored
- Db schema can change without breaking API if translation layer is updated accordingly
- Request/Response formats, endpoints, parameters
- Uses language agnostic formats (JSON ,XML, Protocol Buffers) with type systems
- Changes must maintain backward compatibility or use versioning

### Protocol Buffers
Protocol Buffers (Protobuf) are Google's language-neutral, platform-neutral, extensible mechanism for **serializing structured data**
- API Schema definition language
- Provides libraries for convenience
- Comparable to JSON and XML but better
- Binary on the wire
- There is no schema (flexible)
- Operational reality may not be beautiful but it centralizes complexity of consistency
	- Not request/response but streaming, make it RPC like instead of REST-like (RPC - Remote Procedure Call - lets one program on one computer to execute a function or procedure on another computer as though it was a local call)