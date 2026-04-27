### OLAP (aka analytical databases)
- Read only data, simpler
- Isolation less of an issue (tho streaming breaks this)
- Challenges: How to allow users to manipulate large datasets and deal with scale and failure
- Programming Models:
	- MapReduce (Mappers and Reducers)
	- Spark: RDDs - Resilient Distributed Datasets
	- Streaming: Hybrid between OLAP and OLTP
- aka Data warehouses. read-only, updates in batches, read from many sources, long query times
- Example: bigquery

### ETL (Extract Transform and Load): Updating OLAP Systems
- Data periodically extracted from sources like data lakes, cleaned and transformed, written to OLAP (Data warehouse)
- MapReduce and Spark RDDs - Cleaning and Transformation stage

### MapReduce
- Used inplace of SQL/ACID abstractions as they are unnecessarily heavy for many tasks, hard to scale, especially for large data transformation
- MapReduce is more limited style of programming for
	- Easy parallel programming and management of very-large-scale data
	- Invisible hw and sw failure management
- Several implementations including Hadoop, Flink and the original Google Implementation just called MapReduce
- Function is idempotent (output remains the same even after it's run multiple times)
- Assumes it's a hdfs or equivalent file system
**In a Nutshell:**
- Starts with collection of inputs with the same type
- Apply a map function to each input in parallel using the mapper
	- Map functions puts data into key value pairs
- It is then sorted by all the key-value pairs by key
- Apply a reduce function to all the entries. 
	- Application of the reduce function to one key and it's list of values
- Each reducer produces some output and output of the entire job is the union of what is produced by each reducer
- **Map** - extract something valuable form each record
- **Reduce** - Aggregate, summarize, filter or transform
- Look at Chaining MapReduce
- **Locality Optimization - Master Scheduling Policy:**
	- Asks HDFS for replica locations and input file blocks
	- Map tasks scheduled so hdfs input block replica are on the same machine or same rack
	- This ensures machines read input at local disk speed
	- So we don't need to have data over the cluster in the network, eliminating network bottleneck
- **Failures in MapReduce:**
	- Worker failure:
		- Master detects if workers failed by periodically pinging them (heartbeat)
		- Re-executes in-progress map/reduce tasks
	- Master failure:
		- Single point of failure, resume from execution log
	- Robust:
		- Lost 1600 out of 1800 machines once but finished fine
- **Redundant Execution:**
	- Slow workers/stragglers lengthen completion time
	- Slowest worker determines total latency. hence why many systems measure 99th percentile latency
	- Other jobs consuming resources on machine and bad disks with errors transfer data very slowly
	- A simple solution would be to spawn backup copies of tasks. Whichever one finishes first "wins", treat slow executions as failed as execute. It's very expensive and never done
	- Refined solution: Wait for tasks, if it's suspected of taking too long, spawn another one

### Spark: In-Memory Analytics
> *MapReduce based tasks are slow as data is written and read from storage in the start and end of each operation. Iterative algos are not supported. Need to chain jobs, need to know how many to chain (hard to loop), lot of unnecessary disk I/O when chaining. No support for data mining and analytics*
- Spark avoids persistent storage as much as possible by keeping it in memory
- Resilient Distributed Datasets (RDDs):
	- form of distributed shared memory that eliminates i/o reads and writes from disk intermediate data b/n iterations
	- Read only / immutable, paritioned collections of record memory
	- Deterministic and formed by specific operations (map,filter, join)
	- Can be read from stable storage or other RDDs
- More expressive interface than MapReduce (transformations(map, filter, join) and actions(count, collect, save))
- Programming Interface:
	- Lazy operations: transformations not done until action
	- operations on rdds: transformations (build new rdds/ includes mapreduce) and actions(compute and output results to a file or py collection)
	- Partitioning - layout across nodes
	- Persistence - final output can be stored on disk
- Example use case: Log Mining
	- Load Error messages from logs into memory and then interactively search for various patterns
- Fault recovery: RDDs track lineage information that can be used to efficiently recompute lost data
`msgs = textFile.filter(lambda s: s.startsWith(“ERROR”)) .map(lambda s: s.split(“\t”`
$HDFS\ file \rightarrow Filtered\ RDD \rightarrow Mapped\ RDD$

Some basic actions
```python
nums = sc.parallelize([1,2,3]) # Py collection to RDD

#Load text file from FD, HDFS or S#
sc.textFile("file.txt")
sc.textFile("directory/*.txt")
sc.textFile("hdfs://namenode:9000/path/file")

squares = nums.map(lambda x:x*x) # Pass each element through function {1,4,9}
even = squares.filter(lambda x:x%2 ==0) # Keep elements passing a predicate {4}

nums.collect() # Retrieve RDD contents [1,2,3]
nums.take(2) # Return first k elements [1,2]
nums.count() # Counts number of elements 3
nums.reduce(lambda x,y: x+y) # Merge elements with an associative function
nums.saveAsTextFile("hdfs://file.txt") # Write elements to a text file
```

Some key-value operations (spark's distributed reduce transformations operate on RDDs of key-value pairs)
```python
pets = sc.parallelize([("cat", 1), ("dog", 1), ("cat", 2)])
pets.reduceByKey(lambda x, y: x+y) # {(cat, 3), (dog, 1)}
pets.groupByKey() # {(cat, [1,2]), (dog, [1])}
pets.sortByKey() # {(cat,1), (cat,2), (dog,1)}
```

- **Under the Hood: a DAG scheduler**
	- General task graphs
	- Automatically pipelines functions
	- Data locality aware
	- Partitioning aware to avoid shuffles

- **SQL on Spark**
	- Spark SQL allows us to use SQL on Spark
	- Uses DataFrames instead of RDDs
	- Each column has name
	```python
	df.collect() # returns records as list
	```

- **User-Defined Functions (UDF) in Spark**
	- Custom operations that is defined by the user that is applied to each row. Make sure to consider NULL case. UDF functions need to be registered.
	- Example:
	![[Pasted image 20260426130515.png]]

**Regex:**
- Scripting language for matching and/or manipulating strings of text instead f using Python string functions. Supported by Python and PySpark
- Useful in many programming contexts

### Streaming Processing
> *Hybrid between OLAP and OLTP*
- Large amount of data that calls for needing real-time views of data (social media stuf) and process large amounts of data
- Not exactly OLTP because updates don't happen in place. Not exactly OLAP because it is realtime (updates are granular)
- Streaming is a relatively new class of data system 
- MapReduce and normal Spark wouldn't normally work.
	- Batch Processing: would need to wait for entire computation on large dataset
	- Not intended for long-running and real-time stream processing
- Example:
	- Uber calculating surge prices
	- LinkedIn aggregating updates into one email
	- Netflix understanding user behavior to improve personalization
	- Tripadvisor calculating earning per day and fraud detection
- **Spark Streaming**
	- State between batches kept in memory in fault tolerant datasets (as RDD/Dataframe/Dataset)
	- Batch sizes can be reduced as low as 0.5s to acheive ~ 1s latency
	- Combines streaming and batch workloads
	- Alternatives: Apache Storm, Apache Flink, Amazon Kinesis, Google Dataflow 
- **Fault Recovery**
	- State stored as RDD
	- Deterministically re-computable parallel collection
	- Remembers lineage of operations used to create them
	- Fault / Straggler recovery is done in parallel on other nodes
	- Fast recovery from faults without full data replication

#### Discretized Stream Processing / DStream
- Run streaming computation as a series of very small, deterministic batch jobs
- Batch processing models (MapReduce), recover from faults and stragglers efficiently. Divide job into deterministic tasks and rerun failed/slow tasks in parallel on other nodes
- Same recovery techniques at lower time scales, transformations are not lost (or performed twice) if a worker dies
- Series of RDDs representing a stream of data
- Can be created from live streaming data/ transforming other DStreams
- Many data sources can be inputs: HDFS, kafka, flume, twitter
- Transformations
	- Build new streams from existing streams through
		- Filter/aggregate operations
		- New windows and stateful operations
- Output operations
	- Send data to outside world (saveAsHadoopFiles, print, foreach)