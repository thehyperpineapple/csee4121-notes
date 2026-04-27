# Lecture 7 Question Bank: MapReduce and Spark

## Section 1: OLAP and ETL Fundamentals

**Q1.** Which of the following best describes OLAP systems?
A) Read-write transactional systems with strict isolation
B) Read-only analytical systems with simpler isolation requirements
C) Real-time systems requiring sub-millisecond response
D) In-memory key-value stores

**Q2.** OLAP databases are also commonly known as:
A) Transactional databases
B) Operational databases
C) Data warehouses
D) Document stores

**Q3.** Which of these is an example of an OLAP system?
A) MySQL
B) PostgreSQL
C) BigQuery
D) Redis

**Q4.** What does ETL stand for?
A) Extract, Transmit, Load
B) Extract, Transform, Load
C) Encode, Transform, Link
D) Extract, Translate, Log

**Q5.** In the ETL pipeline, where does cleaning and transformation typically occur?
A) Inside the source databases
B) Using MapReduce and Spark RDDs
C) In the OLTP layer
D) Within the application server

**Q6.** Which of the following is a key challenge for OLAP systems?
A) Maintaining ACID transactions per row
B) Sub-millisecond write latency
C) Allowing users to manipulate large datasets while handling scale and failure
D) Enforcing referential integrity at write time

**Q7.** Streaming processing is best described as:
A) A pure OLTP system
B) A pure OLAP system
C) A hybrid between OLTP and OLAP
D) A replacement for HDFS

---

## Section 2: MapReduce Basics

**Q8.** Why is MapReduce often used instead of SQL/ACID abstractions for large data transformations?
A) SQL cannot perform aggregations
B) ACID abstractions are unnecessarily heavy and hard to scale for large transformations
C) MapReduce supports stronger consistency than SQL
D) SQL doesn't support parallelism at all

**Q9.** Which of the following is NOT an implementation of MapReduce?
A) Hadoop
B) Flink
C) Google's MapReduce
D) Cassandra

**Q10.** MapReduce functions are designed to be:
A) Stateful
B) Idempotent
C) Non-deterministic
D) Sequential only

**Q11.** What does it mean for a function to be idempotent?
A) It runs faster each time it executes
B) Output remains the same even when run multiple times
C) It can only be run once per dataset
D) It modifies its input each time

**Q12.** MapReduce assumes the underlying file system is:
A) NTFS
B) HDFS or equivalent
C) ext4
D) FAT32

**Q13.** In MapReduce, what does the Map function do?
A) Aggregates and summarizes data
B) Extracts something valuable from each record into key-value pairs
C) Sorts the entire dataset
D) Writes final output to disk

**Q14.** What does the Reduce function do?
A) Splits data across nodes
B) Reads data from disk
C) Aggregates, summarizes, filters, or transforms the values for each key
D) Generates random samples

**Q15.** Between Map and Reduce, what intermediate step occurs?
A) Data is encrypted
B) Data is sorted by key
C) Data is replicated 3x
D) Data is compressed

**Q16.** The output of a MapReduce job is:
A) Only the output of the first reducer
B) The union of outputs from all reducers
C) A single file produced by the master
D) The intersection of all mapper outputs

---

## Section 3: MapReduce Optimization and Failures

**Q17.** What is the purpose of locality optimization in MapReduce?
A) To minimize CPU usage
B) To schedule map tasks where HDFS input block replicas reside, reducing network I/O
C) To compress data on disk
D) To balance memory across reducers

**Q18.** Locality optimization eliminates which type of bottleneck?
A) CPU bottleneck
B) Memory bottleneck
C) Network bottleneck
D) Disk bottleneck

**Q19.** How does the master detect worker failures in MapReduce?
A) Workers send completion signals
B) Through periodic heartbeat pings
C) Through user notifications
D) By monitoring CPU temperature

**Q20.** What happens when a worker fails in MapReduce?
A) The entire job restarts from scratch
B) In-progress map/reduce tasks are re-executed
C) The job fails permanently
D) Only completed tasks are re-run

**Q21.** What is a known weakness of the MapReduce master?
A) It uses too much memory
B) It is a single point of failure (resumes from execution log)
C) It cannot communicate with workers
D) It must be run on every node

**Q22.** A famous demonstration of MapReduce robustness involved losing how many machines without job failure?
A) 16 out of 18
B) 160 out of 180
C) 1600 out of 1800
D) 16000 out of 18000

**Q23.** Why does the slowest worker often determine total latency in MapReduce?
A) Because the master waits for explicit confirmation
B) Because the job cannot complete until all tasks finish
C) Because reducers run sequentially
D) Because of disk I/O scheduling

**Q24.** The phenomenon where slow workers extend total job latency motivates which industry metric?
A) 50th percentile latency
B) Average throughput
C) 99th percentile latency
D) Mean time to recovery

**Q25.** Why is the simple "spawn backup copies of every task" approach to stragglers rarely used in practice?
A) It violates idempotency
B) It is very expensive
C) It breaks fault tolerance
D) HDFS does not support it

**Q26.** What is the refined solution to handling stragglers?
A) Always spawn duplicates from the beginning
B) Wait for tasks, and if a task is suspected of taking too long, spawn another one
C) Kill any task that runs longer than 1 second
D) Only re-run tasks at the end of the job

---

## Section 4: Spark and RDDs

**Q27.** What is the main reason MapReduce-style tasks are considered slow?
A) Lack of parallelism
B) Data is written to and read from storage at the start and end of each operation
C) Excessive use of memory
D) Poor compression algorithms

**Q28.** Which of the following limitations applies to MapReduce but not Spark?
A) Cannot run on commodity hardware
B) Iterative algorithms are not supported well; chaining requires lots of unnecessary disk I/O
C) Cannot read from HDFS
D) Does not support key-value pairs

**Q29.** What is the primary strategy Spark uses to be faster than MapReduce?
A) Using GPUs
B) Avoiding persistent storage as much as possible by keeping data in memory
C) Compressing all data
D) Using better hash functions

**Q30.** RDD stands for:
A) Replicated Distributed Database
B) Resilient Distributed Dataset
C) Relational Data Descriptor
D) Robust Direct Disk

**Q31.** Which of the following is NOT a property of RDDs?
A) Read-only / immutable
B) Partitioned collections of records
C) Mutable in place
D) Deterministic

**Q32.** RDDs can be created from:
A) Only stable storage
B) Only other RDDs
C) Stable storage or other RDDs
D) Only Python collections

**Q33.** Which two categories make up Spark's RDD operations?
A) Reads and writes
B) Transformations and actions
C) Queries and updates
D) Inserts and deletes

**Q34.** Which of the following is a transformation in Spark?
A) count
B) collect
C) map
D) save

**Q35.** Which of the following is an action in Spark?
A) filter
B) join
C) collect
D) map

**Q36.** What does "lazy evaluation" mean in Spark?
A) The system only runs at night
B) Transformations are not actually executed until an action is called
C) Operations are executed twice for safety
D) Operations are skipped if the data is large

**Q37.** How does Spark handle fault recovery for RDDs?
A) By replicating every RDD to 3 nodes
B) By tracking lineage information that can be used to recompute lost data
C) By saving all intermediate data to disk
D) By using GPU memory mirrors

---

## Section 5: Spark Programming

**Q38.** What does `sc.parallelize([1, 2, 3])` do?
A) Runs three threads
B) Converts a Python collection into an RDD
C) Splits a file into 3 chunks
D) Loads three files in parallel

**Q39.** Which method loads a text file from HDFS into an RDD?
A) `sc.loadFile()`
B) `sc.openFile()`
C) `sc.textFile()`
D) `sc.readHDFS()`

**Q40.** Given `nums = sc.parallelize([1,2,3])`, what does `nums.map(lambda x: x*x).collect()` return?
A) [1, 2, 3]
B) [1, 4, 9]
C) [2, 4, 6]
D) 14

**Q41.** What does `nums.reduce(lambda x, y: x+y)` do on `[1,2,3]`?
A) Returns [1,2,3]
B) Returns 6 (merges elements with the associative function)
C) Returns 3
D) Returns [3,3,3]

**Q42.** What does `nums.take(2)` return for `[1,2,3]`?
A) The last two elements
B) The first two elements: [1,2]
C) Two random elements
D) Two copies of the first element

**Q43.** Given `pets = sc.parallelize([("cat", 1), ("dog", 1), ("cat", 2)])`, what does `pets.reduceByKey(lambda x, y: x+y)` produce?
A) {(cat, 1), (dog, 1), (cat, 2)}
B) {(cat, 3), (dog, 1)}
C) {(cat, 2), (dog, 2)}
D) {(animals, 4)}

**Q44.** What does `pets.groupByKey()` produce on `[("cat", 1), ("dog", 1), ("cat", 2)]`?
A) {(cat, 3), (dog, 1)}
B) {(cat, [1, 2]), (dog, [1])}
C) {(cat, 1), (dog, 1)}
D) {(cat, [1]), (dog, [1, 2])}

**Q45.** Which method writes RDD elements to a text file?
A) `nums.write()`
B) `nums.export()`
C) `nums.saveAsTextFile()`
D) `nums.toDisk()`

---

## Section 6: Spark Internals and SQL

**Q46.** Spark's DAG scheduler primarily provides which of the following capabilities?
A) Replacing the JVM
B) Pipelining functions, data locality awareness, and shuffle avoidance
C) Replacing HDFS with S3
D) Encrypting all data at rest

**Q47.** Which abstraction does Spark SQL use instead of plain RDDs?
A) Tables
B) DataFrames
C) Maps
D) Tuples

**Q48.** A key difference between a DataFrame and an RDD in Spark is:
A) DataFrames are mutable; RDDs are not
B) Each column in a DataFrame has a name
C) DataFrames cannot be partitioned
D) RDDs support SQL but DataFrames do not

**Q49.** What is a User-Defined Function (UDF) in Spark?
A) A built-in function that ships with Spark SQL
B) A custom function defined by the user, applied to each row, that must be registered before use
C) A function only available in Scala
D) A function that runs only on the driver

**Q50.** When writing a UDF in Spark, what edge case must you be careful about?
A) Empty strings
B) NULL values
C) Negative numbers
D) Unicode characters

---

## Section 7: Regex and Streaming

**Q51.** Why is regex useful in PySpark workflows?
A) It replaces SQL entirely
B) It is a scripting language for matching/manipulating strings of text instead of plain Python string functions
C) It is faster than CPU instructions
D) It is the only way to read text files

**Q52.** Why does normal MapReduce/Spark not work well for streaming?
A) They require GPUs
B) Batch processing must wait for entire computation on a large dataset; not intended for long-running, real-time stream processing
C) They cannot read from Kafka
D) They are incompatible with Python

**Q53.** Which of the following is a real-world streaming use case mentioned in the lecture?
A) Uber calculating surge prices
B) Compiling C++ code
C) Static website hosting
D) Database normalization

**Q54.** In Spark Streaming, where is state between batches kept?
A) On disk only
B) In memory using fault-tolerant datasets like RDDs/DataFrames/Datasets
C) On a separate Redis cluster
D) In CPU registers

**Q55.** What is the approximate minimum latency Spark Streaming can achieve with small batch sizes?
A) ~10 ms
B) ~1 second
C) ~10 seconds
D) ~1 minute

**Q56.** Which of the following is NOT listed as a Spark Streaming alternative?
A) Apache Storm
B) Apache Flink
C) Amazon Kinesis
D) Apache Kafka Connect

**Q57.** How does Spark Streaming handle fault recovery?
A) Via full data replication
B) State is stored as RDDs that are deterministically recomputable using lineage; recovery happens in parallel on other nodes
C) By restarting the entire stream
D) By writing every record to S3 synchronously

---

## Section 8: DStreams (Discretized Streams)

**Q58.** What is a DStream conceptually?
A) A direct stream of bytes
B) A series of very small, deterministic batch jobs / a series of RDDs representing a stream of data
C) A continuous in-memory queue
D) A relational database view

**Q59.** What advantage do DStreams inherit from batch processing models like MapReduce?
A) Stronger consistency guarantees
B) Efficient recovery from faults and stragglers via deterministic task re-execution on other nodes
C) Lower memory usage
D) Support for SQL only

**Q60.** Which of the following can NOT be a DStream input source per the lecture?
A) HDFS
B) Kafka
C) Flume
D) Microsoft Word

**Q61.** Which of the following is a valid DStream transformation type?
A) Filter/aggregate operations and new windows / stateful operations
B) Encryption transformations
C) Transactional commits
D) Schema migrations

**Q62.** Which of these is an example of a DStream output operation?
A) `saveAsHadoopFiles`
B) `print`
C) `foreach`
D) All of the above

**Q63.** Why is DStream processing fault tolerant even when a worker dies?
A) Because all data is encrypted
B) Because transformations are not lost (or performed twice) if a worker dies, since computation can be replayed from lineage
C) Because the master saves all data to disk every second
D) Because Spark uses 3-way replication automatically

---

## Section 9: Synthesis and Conceptual

**Q64.** Compared to MapReduce, why is Spark generally better suited for iterative algorithms?
A) It has more reducers per job
B) It keeps intermediate data in memory rather than writing to disk between iterations
C) It uses Python instead of Java
D) It runs on GPUs by default

**Q65.** Which statement best characterizes the relationship between MapReduce and Spark?
A) Spark is a strict subset of MapReduce
B) Spark provides a more expressive interface (transformations + actions) and avoids unnecessary disk I/O between stages
C) MapReduce is always faster than Spark
D) Spark cannot handle failures, but MapReduce can

**Q66.** Lazy evaluation primarily helps Spark by:
A) Caching credentials
B) Allowing the DAG scheduler to optimize and pipeline operations before any execution
C) Skipping any operation involving NULLs
D) Avoiding the need for lineage

**Q67.** Which property of RDDs enables Spark's fault recovery without full data replication?
A) Mutability
B) Lineage tracking of deterministic operations
C) Encryption
D) Random sampling

**Q68.** Why are streaming systems considered "not exactly OLTP and not exactly OLAP"?
A) They are batch-only
B) Updates do not happen in place (unlike OLTP), but updates are real-time and granular (unlike OLAP)
C) They run only on a single machine
D) They use SQL exclusively

**Q69.** Which of the following statements about MapReduce locality optimization is TRUE?
A) Map tasks are scheduled randomly to balance load
B) The master asks HDFS for replica locations and schedules map tasks where input block replicas live
C) Reducers must be on the same machine as mappers
D) Locality optimization requires GPUs

**Q70.** In Spark, which combination correctly classifies operations?
A) `map`/`filter`/`join` are actions; `count`/`collect`/`save` are transformations
B) `map`/`filter`/`join` are transformations; `count`/`collect`/`save` are actions
C) All of them are actions
D) All of them are transformations

**Q71.** What is the role of "partitioning" in RDDs?
A) Splitting transactions into smaller commits
B) Determining the physical layout of an RDD across nodes
C) Compressing data with gzip
D) Encrypting data per partition

**Q72.** What is the role of "persistence" in Spark RDDs?
A) Permanently storing every transformation
B) Allowing final output to be stored on disk (as needed)
C) Locking data to a single node
D) Disabling lineage

**Q73.** A log mining workflow ("load error messages once, then interactively search for many patterns") highlights which Spark advantage?
A) Strong ACID guarantees
B) In-memory caching enables fast repeated queries on the same dataset
C) Better hardware compatibility than MapReduce
D) Built-in support for SQL joins

**Q74.** Which of the following best summarizes why DStreams reuse RDD-style recovery?
A) They use the same lineage-based deterministic re-execution model on smaller time-scale batches
B) They use full replication to a backup node
C) They write every record synchronously to HDFS
D) They use a separate fault-recovery engine unrelated to RDDs

**Q75.** Which of the following best describes the MapReduce execution flow?
A) Reduce → Sort → Map → Output
B) Map (per input in parallel) → Sort by key → Reduce (per key + values) → Union of outputs
C) Map → Map → Map → Output
D) Sort → Reduce → Map → Output

---

## Answers

| Q | A | Q | A | Q | A | Q | A | Q | A |
|---|---|---|---|---|---|---|---|---|---|
| 1  | B | 16 | B | 31 | C | 46 | B | 61 | A |
| 2  | C | 17 | B | 32 | C | 47 | B | 62 | D |
| 3  | C | 18 | C | 33 | B | 48 | B | 63 | B |
| 4  | B | 19 | B | 34 | C | 49 | B | 64 | B |
| 5  | B | 20 | B | 35 | C | 50 | B | 65 | B |
| 6  | C | 21 | B | 36 | B | 51 | B | 66 | B |
| 7  | C | 22 | C | 37 | B | 52 | B | 67 | B |
| 8  | B | 23 | B | 38 | B | 53 | A | 68 | B |
| 9  | D | 24 | C | 39 | C | 54 | B | 69 | B |
| 10 | B | 25 | B | 40 | B | 55 | B | 70 | B |
| 11 | B | 26 | B | 41 | B | 56 | D | 71 | B |
| 12 | B | 27 | B | 42 | B | 57 | B | 72 | B |
| 13 | B | 28 | B | 43 | B | 58 | B | 73 | B |
| 14 | C | 29 | B | 44 | B | 59 | B | 74 | A |
| 15 | B | 30 | B | 45 | C | 60 | D | 75 | B |
