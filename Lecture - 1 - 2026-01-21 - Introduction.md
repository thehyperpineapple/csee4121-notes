### Focus of the class:
- How are big data systems designed?
- How to storage data?
- How to query/analyze the data?
- How to ensure uptime/availability to the data?
- How do ML/AI systems work? (inference)
- How to ensure privacy/security/quality?

### Course Objectives
- Broad overview of cloud systems that are used in data science
	- Database topics (DBMS, SQL, NoSQL, Datalakes/warehouses)
	- Computer Systems foundations (throughput vs latency, scalability vs performance)
	- Distributed Systems (sharding, fault tolerance)
	- Systems for Machine Learning (accelerators, distributed training/ inference infrastructure)
	- Basic Security (encryption, privacy)
- Bigquery, RocksDB

Data comes from - Physical Devices, Software Logs, Phones, GPS/Cars, IoT, Social Media
Big Data - extremely large data sets that may be computational analyzed to reveal patterns, trends and associations

### Performance Evaluation
- **Metric** - Something we measure
- **Goal** - Evaluate how good/bad our computer system is performing
Metrics allow us to compare two systems. They are crucial for proving improvements and diagnosing regressions
Ex: Power consumed by DB, CPU cost of running a web backend, Average time to render user page

### Latency vs Throughput
- **Latency** - execution time for single task
- **Throughput** - number of tasks per unit time
Latency = length of pipe, Throughput = number of tasks per unit time

Example: 
- **Latency requirement**: Assuming cars drive at 65mph, so self driving car needs to recognize an object in 0.1 seconds 
- **Throughput requirement**: Object recognition system needs to process 1 million object recognition tasks every second to support 10,000 cars simultaneously

Latency vs Throughput is often a trade off

### More Definitions:
- **Performance:** Units of things per second (Bigger is better)
- **Response time:** of a system Y running Z
$$\text{performance (Y)} = \frac{1}{\text{execution time of Y on Z}}$$
- **Throughput** of system Y running many requests
$$
\text{performance (Y)} = \frac{\text{number of requests}}{\text{unit time}}
$$
- "System X is n times faster than Y" means:
$$
n = \frac{\text{performance (X)}}{\text{performance (Y)}}
$$
To improve systems it really depends
- **Speedup** - Make change to the system and measure how much faster/slower it is
$$
\text{Speedup} = \frac{\text{Execution time before change}}{\text{Execution time after change}}
$$
- Performance improvement depends on
	- How good is the enhancement $\rightarrow$ measured by a factor of S
	- How often is it used $\rightarrow$ measured by a factor of p
	- Primarily used by businesses

- Speedup due to an enhancement E:
$$
\text{Speedup (E)} = \frac{\text{Execution time without E}}{\text{Execution time with E}} = \frac{\text{Performance with E}}{\text{Performance without E}}
$$
$$
\text{Execution time}_{new} = \text{Execution time}_{old}*\left[ (1-p)+\frac{p}{S} \right]
$$
$(1-p) \rightarrow$ fraction of operations that are not affected by E
$\frac{p}{S} \rightarrow$ fraction of operations that are affected by E

This implies
$$
\text{Speedup (E)} = \frac{\text{Execution time}_{old}}{\text{Execution time}_{new}} = \frac{1}{\left[ (1-p)+\frac{p}{S} \right]}
$$
![[Pasted image 20260122224745.png]]
### Amdahl's Law:
Speedup of a system is bounded by the fraction of time not enhanced. They are inversely proportional.

In simple terms - Make the common case fast

**Time Measurements:**
- **Nanosecond (ns):** 1/1,000,000,000 second 
- **Microsecond (us):** 1/1,000,000 second 
- **Millisecond (ms)**: 1/1000 second

#### Potentially Important Access Times:

| Access Type                                                   | Access Time |
| ------------------------------------------------------------- | ----------- |
| CPU Cache Access                                              | 1 ns        |
| Memory Access                                                 | 100 ns      |
| Read small object from random location on a local flash drive | 20us        |
| Read small object within same network in a data center        | 100 us      |
| Run a SQL query on a flash database                           | 1 ms        |
| Read small random object from magnetic disk (HDDS)            | 10 ms       |
| Run SQL query on a disk database                              | 20 ms       |
| Roundtrip time over the internet                              | 30 ms       |
