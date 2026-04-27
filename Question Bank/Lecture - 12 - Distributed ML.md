# Lecture 12 - Distributed ML: Question Bank

## Section 1: Distributed Training Fundamentals

**1.** Which of the following is NOT one of the three stages of gradient descent described in the lecture?
a) Forward propagation
b) Backward propagation
c) Weight update
d) Parameter compression

**2.** What does forward propagation produce?
a) Error for each trainable weight
b) A prediction
c) Updated model weights
d) Aggregated gradients

**3.** What does backward propagation produce?
a) A prediction from a batch of inputs
b) Updated model weights
c) Error for each trainable weight
d) An embedding vector

**4.** State-of-the-art models can have how many weights according to the lecture?
a) Millions
b) Billions
c) Trillions
d) Thousands

**5.** Which of the following best describes the purpose of the weight update stage?
a) Generates predictions on batches
b) Uses the loss value to update model weights
c) Computes embeddings
d) Performs k-means clustering

---

## Section 2: Data Parallelism & Parameter Server

**6.** What is the basic idea of data parallelism?
a) Partition model, replicate data
b) Replicate model, partition data
c) Replicate both model and data
d) Partition both model and data

**7.** In a parameter server architecture, what do workers do?
a) They send raw data to parameter servers
b) They push gradients and pull updated parameters
c) They perform clustering on parameter vectors
d) They run k-means on the gradients

**8.** What is the main inefficiency of the parameter server architecture?
a) It uses too much disk
b) Centralized communication that doesn't scale
c) It cannot handle backward propagation
d) It can only train small models

**9.** What technique decentralizes communication in distributed training?
a) Parameter server
b) AllReduce
c) Pipelining
d) RAG

**10.** AllReduce performs what kind of operation?
a) Element-wise reduction across multiple devices
b) Random sampling across devices
c) Encryption of model weights
d) K-means clustering of gradients

---

## Section 3: AllReduce Variants

**11.** Which of the following is NOT an AllReduce variant mentioned in the lecture?
a) Naïve AllReduce
b) Ring AllReduce
c) Tree AllReduce
d) Linear AllReduce

**12.** In Naïve AllReduce, what is the total communication cost for N workers and M parameters each?
a) N × M
b) 2 × M × N
c) N × (N-1) × M
d) M × log(N)

**13.** What is the main scalability issue with Naïve AllReduce?
a) Each worker communicates with all other workers
b) It requires a centralized server
c) It cannot handle backward propagation
d) It cannot fit the model in GPU memory

**14.** In Ring AllReduce, M parameters are divided into how many slices?
a) M slices
b) N slices (where N is the number of workers)
c) 2 slices
d) log(N) slices

**15.** What happens during Step 1 (Aggregation) of Ring AllReduce?
a) Each worker sends one slice to the next worker on the ring; repeated N times
b) Each worker sends all parameters to a central server
c) Each worker performs k-means clustering
d) Each worker sends gradients to all other workers

**16.** After Step 1 (Aggregation) in Ring AllReduce, what does each worker have?
a) The full set of all parameters
b) The aggregated version of M/N parameters
c) None of the parameters
d) A copy of the entire model

**17.** What happens during Step 2 (Broadcast) of Ring AllReduce?
a) Each worker sends one slice of aggregated parameters to the next worker; repeated N times
b) Workers push gradients to a parameter server
c) Each worker recomputes gradients
d) The system performs forward propagation

**18.** What is the total communication cost of Ring AllReduce?
a) N × (N-1) × M
b) 2 × M × N
c) M × N²
d) M × log(N)

**19.** What is the breakdown of communication cost in Ring AllReduce?
a) Aggregation: M × N, Broadcast: M × N
b) Aggregation: M × N², Broadcast: M
c) Aggregation: M, Broadcast: N
d) Aggregation: 2M, Broadcast: 2N

**20.** Which AllReduce variant is the most popular according to the lecture?
a) Naïve AllReduce
b) Ring AllReduce
c) Tree AllReduce
d) Butterfly AllReduce

**21.** Tree AllReduce and Butterfly AllReduce are similar to Ring AllReduce except:
a) They use a different communication topology
b) They don't actually aggregate gradients
c) They are restricted to two workers
d) They do not work for deep neural networks

---

## Section 4: Model Sizes and Memory

**22.** According to the lecture, what is the memory footprint of GPT-3 (175B parameters)?
a) 5.12 GB
b) 24 GB
c) 275 GB
d) 2800 GB

**23.** What is the memory footprint of BERT-Large (0.32B parameters) according to the lecture?
a) 5.12 GB
b) 24 GB
c) 275 GB
d) 80 GB

**24.** What memory capacity does an NVIDIA A100 GPU have?
a) 16 / 32 GB
b) 40 / 80 GB
c) 100 / 200 GB
d) 2800 GB

**25.** What memory capacity does an NVIDIA V100 GPU have?
a) 8 / 16 GB
b) 16 / 32 GB
c) 40 / 80 GB
d) 80 / 160 GB

**26.** What is the main issue with data parallelism for large models?
a) It requires too many parameters
b) Each GPU saves a replica of the entire model, which may exceed GPU memory
c) It cannot perform backward propagation
d) It only works on CPUs

---

## Section 5: Model Parallelism

**27.** What is the basic idea of model parallelism?
a) Partition data, replicate model
b) Partition model across multiple nodes
c) Replicate both model and data
d) Use only a single GPU

**28.** What must be transferred between devices in model parallelism?
a) Raw training data
b) Intermediate results
c) Embedding vectors only
d) Random samples

**29.** What is one major drawback of vanilla model parallelism?
a) Under-utilization of compute resources
b) It uses too little memory
c) It requires no communication
d) It cannot use GPUs

**30.** What is a "mini-batch" in pipeline model parallelism?
a) A subset of model weights
b) The number of samples processed in each iteration
c) A small embedding vector
d) A fraction of one parameter

**31.** What is a "micro-batch" in pipeline model parallelism?
a) A piece of a mini-batch used to pipeline computation
b) A single sample in the dataset
c) A slice of a parameter vector
d) A subset of GPU memory

**32.** How does pipeline model parallelism improve over vanilla model parallelism?
a) By eliminating backward propagation
b) By pipelining forward and backward computations across micro-batches
c) By replicating the entire model on each GPU
d) By compressing the gradients

**33.** What is the "state of the practice" approach to distributed training?
a) Pure data parallelism
b) Pure model parallelism
c) A hybrid of data and model parallelism
d) Single GPU training

---

## Section 6: Distributed Inference

**34.** Historically, why was inference simpler than training?
a) It only requires forward propagation, no backward pass or weight updates
b) It uses less data
c) It runs on CPUs only
d) It does not require a model

**35.** Which of the following complicates modern inference?
a) Companies running many models simultaneously on shared GPU clusters
b) Models sharing memory like the KV cache
c) Reasoning models requiring many inference paths
d) All of the above

**36.** What is the goal of an inference serving system?
a) Maximize training throughput
b) Low inference latency while maximizing total utilization/throughput
c) Minimize the number of users
d) Maximize disk usage

**37.** What is one example of memory that can be shared across models in LLM inference?
a) The KV cache
b) The optimizer state
c) The training dataset
d) The objective function

**38.** Which models are listed as examples of reasoning models?
a) BERT and GPT-2
b) OpenAI o1/o3 and DeepSeek R1
c) ResNet and VGG
d) Word2Vec and GloVe

**39.** What is the basic idea of chain-of-thought reasoning at inference time?
a) Use a single forward pass with no verification
b) Create many "inference" paths with multiple sequences and use a verifier model to select the best
c) Run only backward propagation
d) Encrypt all inputs

**40.** A single "logical" inference in a reasoning model may require:
a) A single forward pass
b) Two backward passes
c) Thousands of individual inferences in a chain of thought
d) No inferences at all

**41.** What is the idea behind agents?
a) Combining chain of thought with the ability to interact with external sources
b) Replacing all model parameters with random values
c) Running training on CPUs only
d) Removing the verifier model

**42.** What kind of clusters are most new AI clusters being built as?
a) Training-only clusters
b) Inference clusters
c) Database clusters
d) CPU-only clusters

**43.** Why is memory efficiency challenging in inference?
a) We don't know in advance how much memory a model will need to allocate
b) GPUs have unlimited memory
c) Models always use exactly the same memory
d) Memory is not relevant for inference

**44.** Which is a strategy to improve inference memory efficiency?
a) Reducing memory wastage within a model
b) Sharing memory across models (e.g., KV cache)
c) Both a and b
d) Neither a nor b

**45.** In an inference serving system, what does the system do automatically?
a) Distributes models across GPUs and packs them while maintaining latency
b) Re-trains models from scratch
c) Encrypts all queries
d) Deletes unused embeddings

---

## Section 7: RAG Motivation and Concepts

**46.** What is the main motivation behind RAG?
a) To reduce GPU usage
b) To augment the LLM context with information the model wasn't trained on
c) To compress model parameters
d) To remove the need for embeddings

**47.** RAG databases are different from traditional databases because:
a) They store vectors representing the data instead of raw data
b) They use SQL queries
c) They run only on GPUs
d) They are always read-only

**48.** Instead of computing SQL queries, RAG systems typically:
a) Find the K nearest neighbors
b) Train a new model
c) Run gradient descent
d) Use a parameter server

**49.** In the lecture's analogy, RAG is to disk as context is to:
a) Training data
b) Memory (GPU memory)
c) The optimizer
d) A backward pass

**50.** Which of the following is a use case for RAG?
a) LLM-backed chatbot
b) Context-aware code generation/completion
c) Both a and b
d) Neither a nor b

---

## Section 8: Embedding Vectors and Indexing

**51.** What does an embedding model do?
a) Maps a text chunk into an embedding vector
b) Trains the model from scratch
c) Performs backward propagation
d) Caches GPU memory

**52.** What property do relevant texts have in embedding space?
a) They have completely different vectors
b) They have similar embedding vectors
c) They are stored in different clusters
d) They are encrypted

**53.** Which is given as an example of an embedding model?
a) voyage-code-3
b) GPT-3
c) BERT-Large
d) DeepSeek R1

**54.** What is the typical pipeline for vector databases?
a) Chunk documents → translate to embedding vectors → index vectors
b) Encrypt → compress → store
c) Train → predict → discard
d) Cluster → train → infer

**55.** The vector index problem is defined as: given a query vector, find the:
a) Exact nearest neighbor
b) Top-k approximate nearest neighbor vectors efficiently
c) Furthest vectors
d) Random sample of vectors

**56.** What are the two types of vector indices discussed?
a) Clustering and graph indices
b) Hash and tree indices
c) Linear and exponential indices
d) GPU and CPU indices

---

## Section 9: Clustering Vector Indices

**57.** How are clustering vector indices built?
a) By running a clustering algorithm (e.g., k-means) on the vectors
b) By training a deep neural network
c) By encrypting all vectors
d) By compressing each vector to one bit

**58.** At query time, clustering indices search:
a) All clusters equally
b) Only the clusters that are close to the query vector
c) Random clusters
d) Only one cluster regardless of query

**59.** Which are example clustering vector indices?
a) IVFF, SPANN
b) HNSW, NSW, DiskANN
c) BERT, GPT
d) Ring, Tree, Butterfly

**60.** What is a key challenge of clustering indices?
a) They cannot be built
b) Many vectors could be at the boundary of different clusters
c) They cannot run on GPUs
d) They require gradient descent

---

## Section 10: Graph Vector Indices

**61.** Which are example graph vector indices?
a) IVFF, SPANN
b) HNSW, NSW, DiskANN
c) k-means, DBSCAN
d) BERT, GPT

**62.** What distinguishes on-disk graph indices from in-memory ones?
a) They store the index on disk to handle large datasets
b) They never use disk
c) They are slower than in-memory in all cases
d) They do not support similarity search

**63.** Which of the following is an on-disk graph vector index?
a) DiskANN
b) IVFF
c) SPANN
d) k-means

**64.** Which of the following is an on-disk cluster vector index?
a) DiskANN
b) Starling
c) SPANN
d) HNSW

---

## Section 11: Scaling Vector Databases

**65.** For the SPACEV1B dataset (1.4 billion vectors, 100 dimensions, 1B per dimension), how large is the vector data?
a) 13 GB
b) 130 GB
c) 334 GB
d) 1.3 TB

**66.** For SPACEV1B with 64 neighbors per node and 4B per neighbor, how large is the neighbor list?
a) 130 GB
b) 334 GB
c) 1 TB
d) 50 GB

**67.** What is the main implication of these data sizes?
a) The entire vector index cannot fit in the memory of a single server
b) Vector indices are trivial to build
c) GPUs are unnecessary
d) Disk caching is irrelevant

**68.** Why is disk bandwidth a concern for vector databases?
a) Disks have limited bandwidth, which limits max throughput of the vector DB
b) Disks have unlimited bandwidth
c) Disks cannot store vectors
d) Disks are faster than memory

**69.** What is "bandwidth amplification" in vector databases?
a) Disks read at least one block (≥ 4KB), but each graph node may be much smaller
b) GPUs amplify their own bandwidth
c) The model amplifies bandwidth via training
d) Vectors are duplicated on disk

**70.** Approximately how large is one node in the example given (100B + 64 × 4B)?
a) 64 B
b) 356 B
c) 4 KB
d) 1 MB

**71.** How is the access distribution to vectors typically described?
a) Uniform — every vector is equally popular
b) Skewed — only a few vectors are popular
c) Random with no patterns
d) Always sequential

**72.** Why does access skew matter for vector databases?
a) It enables effective caching of hot/popular vectors
b) It makes caching useless
c) It removes the need for indexing
d) It eliminates bandwidth concerns

---

## Section 12: Open Challenges

**73.** Which of the following is NOT listed as an open challenge in vector databases?
a) Efficiently updating/deleting vectors
b) Efficiently caching vectors
c) Partitioning vector databases across multiple servers
d) Replacing gradient descent with random search

**74.** Most vector databases today are:
a) Read-only
b) Write-only
c) Distributed transactional
d) Encrypted with full ACID guarantees

**75.** Which storage cost example is given for a Samsung 990 EVO 5.0 NVMe SSD 2TB?
a) $130 at 5 GB/s
b) $270 at 10 GB/s
c) $50 at 1 GB/s
d) $500 at 20 GB/s

---

# Answer Key

| Q  | Ans | Q  | Ans | Q  | Ans | Q  | Ans | Q  | Ans |
|----|-----|----|-----|----|-----|----|-----|----|-----|
| 1  | d   | 16 | b   | 31 | a   | 46 | b   | 61 | b   |
| 2  | b   | 17 | a   | 32 | b   | 47 | a   | 62 | a   |
| 3  | c   | 18 | b   | 33 | c   | 48 | a   | 63 | a   |
| 4  | c   | 19 | a   | 34 | a   | 49 | b   | 64 | c   |
| 5  | b   | 20 | b   | 35 | d   | 50 | c   | 65 | b   |
| 6  | b   | 21 | a   | 36 | b   | 51 | a   | 66 | b   |
| 7  | b   | 22 | d   | 37 | a   | 52 | b   | 67 | a   |
| 8  | b   | 23 | a   | 38 | b   | 53 | a   | 68 | a   |
| 9  | b   | 24 | b   | 39 | b   | 54 | a   | 69 | a   |
| 10 | a   | 25 | b   | 40 | c   | 55 | b   | 70 | b   |
| 11 | d   | 26 | b   | 41 | a   | 56 | a   | 71 | b   |
| 12 | c   | 27 | b   | 42 | b   | 57 | a   | 72 | a   |
| 13 | a   | 28 | b   | 43 | a   | 58 | b   | 73 | d   |
| 14 | b   | 29 | a   | 44 | c   | 59 | a   | 74 | a   |
| 15 | a   | 30 | b   | 45 | a   | 60 | b   | 75 | a   |
