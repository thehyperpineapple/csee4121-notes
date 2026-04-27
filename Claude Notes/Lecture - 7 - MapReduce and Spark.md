# Lecture 7 — MapReduce and Spark

## Topics Covered
- OLAP / analytical databases
- ETL (Extract, Transform, Load)
- MapReduce
- Spark and RDDs (Resilient Distributed Datasets)
- Spark SQL and UDFs
- Regex (intro)
- Stream Processing (Spark Streaming, DStreams)

---

## 1. OLAP (Analytical Databases)

- **Read-only** data, simpler than OLTP.
- Isolation is less of an issue (though streaming breaks this).
- **Challenges:** allow users to manipulate large datasets while dealing with **scale and failure**.
- Also called **data warehouses**:
  - Read-only
  - Updates in batches
  - Read from many sources
  - Long query times
- **Example:** BigQuery.

### Programming Models for OLAP
- **MapReduce** — Mappers and Reducers
- **Spark** — RDDs (Resilient Distributed Datasets)
- **Streaming** — hybrid between OLAP and OLTP

---

## 2. ETL (Extract, Transform, Load)

- Process for **updating OLAP systems**.
- Data is periodically:
  1. **Extracted** from sources (e.g., data lakes)
  2. **Cleaned and transformed**
  3. **Loaded** into the OLAP system (data warehouse)
- **MapReduce and Spark RDDs** are used in the cleaning/transformation stage.

---

## 3. MapReduce

### Why MapReduce?
- Used in place of SQL/ACID abstractions, which are unnecessarily heavy for many tasks and hard to scale, especially for large data transformation.
- More **limited style of programming** that enables:
  - Easy parallel programming and management of very-large-scale data.
  - Invisible hardware/software failure management.

### Implementations
- **Hadoop**, **Flink**, and the original **Google MapReduce**.

### Key Properties
- Functions are **idempotent** — output stays the same when run multiple times.
- Assumes an **HDFS** (or equivalent) file system.

### How It Works (in a nutshell)
1. Start with a collection of inputs of the **same type**.
2. Apply a **map function** to each input in parallel using mappers.
   - Map functions output **key-value pairs**.
3. Sort all key-value pairs by **key**.
4. Apply a **reduce function** to all entries.
   - Reduce is applied to one key and its **list of values**.
5. Each reducer produces output → final job output is the **union** of all reducer outputs.

### Map vs. Reduce
- **Map** — extract something valuable from each record.
- **Reduce** — aggregate, summarize, filter, or transform.

> Note: MapReduce jobs can be **chained**.

### Locality Optimization (Master Scheduling Policy)
- Master asks HDFS for **replica locations** of input file blocks.
- Map tasks are scheduled so that an HDFS input block replica is on the **same machine or rack**.
- Machines read input at **local disk speed**.
- Eliminates network bottleneck (no need to ship data across the cluster).

### Failures in MapReduce
- **Worker failure:**
  - Master detects via periodic **heartbeat pings**.
  - Re-executes in-progress map/reduce tasks.
- **Master failure:**
  - **Single point of failure** — resume from execution log.
- **Robustness example:**
  - Lost 1600 of 1800 machines once but the job still finished.

### Redundant Execution (Stragglers)
- Slow workers (**stragglers**) lengthen completion time.
- The **slowest worker** determines total latency → why systems track **99th percentile latency**.
- Causes: other jobs hogging resources, bad disks transferring slowly.
- **Naive solution:** spawn backup copies of every task; first one wins. Too expensive — never done.
- **Refined solution:** wait for tasks; if one is suspected of taking too long, spawn another.

---

## 4. Spark: In-Memory Analytics

### Why Spark?
MapReduce limitations:
- Slow — data is written/read from storage at the start and end of every operation.
- **Iterative algorithms** are not supported.
- Chaining jobs requires knowing the chain length (hard to loop).
- Lots of unnecessary disk I/O.
- No support for data mining and analytics.

### Key Idea
Spark **avoids persistent storage as much as possible** by keeping data in memory.

### Resilient Distributed Datasets (RDDs)
- A form of **distributed shared memory** that eliminates I/O between iterations.
- **Read-only / immutable**, partitioned collections of records.
- **Deterministic** — formed by specific operations (`map`, `filter`, `join`).
- Can be read from **stable storage** or from **other RDDs**.

### More Expressive than MapReduce
- **Transformations** — `map`, `filter`, `join` (build new RDDs).
- **Actions** — `count`, `collect`, `save` (compute and output results).

### Programming Interface
- **Lazy operations** — transformations are not executed until an action is called.
- **Partitioning** — controls layout across nodes.
- **Persistence** — final output can be stored on disk.

### Example Use Case: Log Mining
- Load error messages from logs into memory; interactively search for patterns.

### Fault Recovery
- RDDs track **lineage** — the sequence of operations used to build them.
- Lost data is **efficiently recomputed** using lineage instead of replication.

### Lineage Example
```python
msgs = textFile.filter(lambda s: s.startswith("ERROR")) \
               .map(lambda s: s.split("\t"))
```
$$HDFS\ file \rightarrow Filtered\ RDD \rightarrow Mapped\ RDD$$

### Basic Actions
```python
nums = sc.parallelize([1,2,3])      # Python collection → RDD

# Load text from FS, HDFS, or S3
sc.textFile("file.txt")
sc.textFile("directory/*.txt")
sc.textFile("hdfs://namenode:9000/path/file")

squares = nums.map(lambda x: x*x)            # {1,4,9}
even    = squares.filter(lambda x: x%2 == 0) # {4}

nums.collect()                       # [1,2,3]
nums.take(2)                         # [1,2]
nums.count()                         # 3
nums.reduce(lambda x,y: x+y)         # associative merge
nums.saveAsTextFile("hdfs://file.txt")
```

### Key-Value Operations
Spark's distributed reduce transformations operate on RDDs of key-value pairs.

```python
pets = sc.parallelize([("cat", 1), ("dog", 1), ("cat", 2)])
pets.reduceByKey(lambda x, y: x+y)  # {(cat, 3), (dog, 1)}
pets.groupByKey()                   # {(cat, [1,2]), (dog, [1])}
pets.sortByKey()                    # {(cat,1), (cat,2), (dog,1)}
```

### Under the Hood: DAG Scheduler
- Handles **general task graphs**.
- Automatically **pipelines functions**.
- **Data locality aware**.
- **Partitioning aware** to avoid shuffles.

---

## 5. SQL on Spark

- **Spark SQL** allows running SQL queries on Spark.
- Uses **DataFrames** instead of RDDs.
- Each column has a **name**.

```python
df.collect()  # returns records as a list
```

---

## 6. User-Defined Functions (UDFs) in Spark

- Custom operations defined by the user, applied to **each row**.
- Must consider the **NULL case**.
- UDFs need to be **registered** before use.

---

## 7. Regex (Brief Intro)

- Scripting language for **matching and/or manipulating strings of text**, in place of using Python string functions.
- Supported by **Python and PySpark**.
- Useful in many programming contexts.

---

## 8. Stream Processing

> *Hybrid between OLAP and OLTP.*

### Motivation
- Large amounts of data require **real-time views** (e.g., social media).
- Need to process large amounts of data quickly.
- **Not pure OLTP** — updates don't happen in place.
- **Not pure OLAP** — it's real-time and updates are granular.
- Streaming is a **relatively new class** of data system.

### Why MapReduce / Plain Spark Won't Work
- **Batch processing** has to wait for the whole dataset to compute.
- Not intended for **long-running, real-time** processing.

### Real-World Examples
- **Uber** — calculating surge prices.
- **LinkedIn** — aggregating updates into one email.
- **Netflix** — understanding user behavior to improve personalization.
- **TripAdvisor** — daily earnings calculations and fraud detection.

### Spark Streaming
- State between batches is kept in memory in **fault-tolerant datasets** (RDD / DataFrame / Dataset).
- **Batch sizes** can be reduced to as low as **0.5s**, achieving **~1s latency**.
- Combines **streaming and batch** workloads.
- **Alternatives:** Apache Storm, Apache Flink, Amazon Kinesis, Google Dataflow.

### Fault Recovery
- State is stored as an **RDD**.
- Deterministically re-computable parallel collection.
- Remembers **lineage** of operations.
- Fault / straggler recovery is done **in parallel on other nodes**.
- **Fast recovery without full data replication.**

---

## 9. Discretized Stream Processing (DStreams)

- Run a streaming computation as a **series of very small, deterministic batch jobs**.
- Inherits batch-processing recovery: divide job into deterministic tasks and rerun failed/slow tasks in parallel on other nodes.
- Same recovery techniques applied at lower time scales — transformations are **not lost or performed twice** if a worker dies.
- A **DStream** = series of **RDDs** representing a stream of data.
- Created from:
  - Live streaming data
  - Transforming other DStreams
- **Sources:** HDFS, Kafka, Flume, Twitter.

### Transformations
- Build new streams from existing streams via:
  - **Filter / aggregate** operations
  - **New windows** and **stateful** operations

### Output Operations
- Send data to the outside world: `saveAsHadoopFiles`, `print`, `foreach`.

---

## Key Takeaways

- **OLAP / data warehouses** = read-only, batch-updated analytical systems.
- **MapReduce** = simple, fault-tolerant, parallel programming model — but disk-heavy and not iterative.
- **Spark / RDDs** = in-memory, lineage-based fault tolerance, supports iteration and richer ops.
- **DAG scheduler** pipelines functions and exploits locality + partitioning.
- **Spark SQL + DataFrames + UDFs** make Spark accessible to SQL users.
- **Stream processing** sits between OLTP and OLAP — Spark Streaming uses **DStreams** (mini-batches of RDDs) for low-latency, fault-tolerant real-time computation.
