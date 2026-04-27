# Lecture 12 - Distributed ML

## Overview

Three main topics:
1. **Distributed Training** - Data parallelism, model parallelism, hybrids
2. **Inference** - Increasingly important; reasoning models multiply complexity
3. **RAG** - Augmenting queries via vector databases (clustering & graph indices)

---

# Part 1: Distributed ML Training

## Training Recap

**High-level idea:** Use gradient descent on an objective function to iteratively update model weights to improve score. State-of-the-art models can have **trillions of weights**.

### Three stages of gradient descent:
1. **Forward propagation** - Apply model to a batch of input samples, run through operators to produce a prediction
2. **Backward propagation** - Run model in reverse to produce error for each trainable weight
3. **Weight update** - Use loss value (output of objective function) to update model weights

**Question:** How do we parallelize this process?

---

## Data Parallelism

**Idea:** Replicate the model on every worker, partition the data across workers.

### Approach 1: Parameter Server

- Workers **push** gradients to parameter servers
- Workers **pull** updated parameters back
- **Inefficiency:** Centralized communication — all workers communicate with parameter servers for weight updates
- **Cannot scale** to large numbers of workers

**Solution:** Decentralize communication via **AllReduce** — perform element-wise reduction across multiple devices.

### AllReduce Variants

- Naïve AllReduce
- Ring AllReduce
- Tree AllReduce
- Butterfly AllReduce

#### Naïve AllReduce
- Each worker sends its local gradients to all other workers
- For N workers, M parameters each: communication = **N × (N-1) × M** parameters
- **Issue:** Same scalability issue as parameter server (each worker talks to all others)

#### Ring AllReduce (most popular)
- Construct a ring of N workers
- Divide M parameters into N slices
- **Step 1 (Aggregation):** Each worker sends one slice (M/N parameters) to the next worker on the ring; repeat N times
  - After step 1, each worker has the aggregated version of M/N parameters
- **Step 2 (Broadcast):** Each worker sends one slice of aggregated parameters to the next worker; repeat N times
- **Total communication:** 2 × M × N parameters
  - Aggregation: M × N
  - Broadcast: M × N

#### Tree / Butterfly AllReduce
- Same idea as Ring AllReduce but with a different topology
- Ring AllReduce remains the most popular in practice

### Issue with Data Parallelism

- Each GPU saves a **replica of the entire model**
- **Cannot train models that exceed GPU device memory**

#### Model size scaling (sample numbers):
| Model | Parameters | Layers | Hidden Dim | Memory Footprint |
|-------|-----------|--------|------------|------------------|
| BERT-Large | 0.32B | 24 | 1024 | 5.12 GB |
| GPT-2 | 1.5B | 48 | 1600 | 24 GB |
| Turing-NLG | 17.2B | 78 | 4256 | 275 GB |
| GPT-3 | 175B | 96 | 12288 | 2800 GB |

GPU memory:
- NVIDIA V100: 16 / 32 GB
- NVIDIA A100: 40 / 80 GB

→ Even an A100 can't hold GPT-3 in memory.

---

## Model Parallelism

**Idea:** Instead of partitioning the data, partition the **model** across multiple nodes.

- Split a model into multiple subgraphs and assign them to different devices
- Transfer intermediate results between devices

### Why Model Parallelism is Hard to Optimize
- **Under-utilization of compute resources** — devices sit idle while waiting on others
- **Low overall throughput** due to poor resource utilization

### Pipeline Model Parallelism

- **Mini-batch:** number of samples processed in each iteration
- Divide a mini-batch into multiple **micro-batches**
- Pipeline the forward and backward computations across micro-batches
- This solves much of the parallelism / under-utilization problem

---

## State-of-the-Art

**Hybrid approach:** Combine data parallelism and model parallelism.

---

# Part 2: Distributed ML Inference

## Why Inference is More Complicated Than It Seems

Historically, inference was simpler than training:
- Single forward pass, just one iteration
- No backward pass or weight updates

**But complications exist:**
- Companies may run **many models simultaneously**
- Training and inference often share the same GPU clusters
- Models can share parts of memory (e.g., **KV cache** in LLMs) to reduce overall memory consumption
- **Goal:** low inference latency while maximizing total throughput

## Reasoning Models / Agents

Examples: OpenAI o1/o3, DeepSeek R1.

### Chain of Thought
- Instead of doing a "one pass" inference, create many inference paths composed of multiple sequences
- Apply a **verifier** model to select the best one
- A single "logical" inference may require **thousands** of individual inferences

### Where it shines
- Generating code
- Generating algorithms / blueprints
- Interacting with the real world

### Agents
- Combine chain of thought with the ability to interact with external sources (vector database, search queries, websites)
- Inference becomes much more resource-intensive and harder to manage
- **Most new AI clusters being built are inference clusters**

## Inference / Serving Systems

Basic interface, complex internals:
- Developers deploy several models, expose an inference API
- Serving system automatically distributes models across GPUs
- Tries to "pack" models on GPUs while maintaining latency constraints

### Memory Efficiency is Essential
- **Hard problem:** we don't know in advance how much memory a model will need to allocate
- Strategy 1: Reduce memory **wastage** within a model (model allocates more than it actually uses)
- Strategy 2: **Share memory across models** (e.g., KV cache) to minimize total consumption

---

# Part 3: RAG and Vector Databases

## RAG Motivation

- LLM context windows are **limited**
- We may want to augment context with **private information** the model wasn't trained on (prior product support chats, internal docs, etc.)

### How RAG works
- Augment the prompt with data from an external "database"
- This database stores **vectors** representing the data, not raw data
- Instead of SQL queries, it finds **K nearest neighbors**

**Memory hierarchy analogy:**
- Context (in GPU memory) ≈ memory
- RAG (typically on a CPU-based node) ≈ disk

## Use Cases
- LLM-backed chatbots
- Context-aware code generation/completion
- Retrieve relevant context/docs via **similarity search**

## Embedding Vectors

Embedding models (e.g., voyage-code-3) map a text chunk into an **embedding vector**:
- The vector encodes **semantic info** of the text chunk
- Relevant texts have similar embedding vectors

## Vector Databases for Similarity Search

Pipeline:
1. Chunk documents
2. Translate chunks to embedding vectors
3. Index the vectors in a database

**Challenge:** How to find similar vectors efficiently?

## Vector Indices

**Problem:** Given a set of vectors and a query vector, efficiently find the **top-k approximate nearest neighbor** vectors.

Two main types:
1. **Clustering indices**
2. **Graph indices**

### Clustering Vector Indices
- Run a clustering algorithm (e.g., k-means) on all vectors
- At query time, only search in the clusters closest to the query vector
- Examples: **IVFF, SPANN**
- **Challenge:** many vectors lie at the boundary of multiple clusters

### Graph Vector Indices
- Build a graph where nodes are vectors and edges connect "similar" vectors
- Examples: **HNSW, NSW, DiskANN**

## Challenges of Vector Indices at Scale

Vector databases can be **huge**:
- Search engines indexing billions of web pages
- Code-completion RAGs covering Microsoft's full codebase

### Example: SPACEV1B dataset (1.4B vectors)
- Vector data: 1.4 × 10⁹ × 100 dim × 1B/dim ≈ **130 GB**
- Neighbor list: 1.4 × 10⁹ × 64 neighbors × 4B/neighbor ≈ **334 GB**

→ **Infeasible to store the entire vector index in memory of a single server.**

## On-Disk Vector Indices
- **On-disk graph indices:** DiskANN, Starling
- **On-disk cluster indices:** SPANN, SPFresh

## Caching Is Important

**Disk bandwidth limits throughput**:
- Samsung 990 EVO 5.0 NVMe SSD 2TB: $130, 5 GB/s
- Seagate FireCuda 540 SSD 2TB: $270, 10 GB/s

**Bandwidth amplification issue:** disk block size ≥ 4KB, but each graph node can be much smaller.
- Example: 100B + 64 × 4B = 356B per node, with 64 neighbors

**Skewed access distribution:** only a few vectors are popular → caching the hot ones helps a lot.

### Caching: Clustering vs. Graph Indices
- Both index types benefit from caching, but the access patterns differ (clusters vs. graph traversal)

## Open Challenges in Vector Databases
- How to efficiently **update / delete** vectors (most vector DBs today are read-only)
- How to efficiently **cache** them
- How to **partition** vector databases across multiple servers

---

# Key Takeaways

- **Data parallelism:** replicate model, partition data — bottlenecked on weight-sync communication; **Ring AllReduce** is the dominant solution
- **Model parallelism:** partition model, replicate data — needed when model > GPU memory; **pipelining over micro-batches** addresses utilization
- **State of the practice:** hybrid data + model parallelism
- **Inference is becoming the dominant cost** — reasoning models turn one logical query into thousands of inferences
- **Memory efficiency** (KV cache sharing, reducing wastage) is the central inference systems problem
- **RAG = vector DB + KNN search**; two index families: **clustering (IVFF, SPANN)** and **graph (HNSW, DiskANN)**
- Vector DBs are too large for a single server's memory → on-disk indices + smart caching of skewed-access hot vectors
