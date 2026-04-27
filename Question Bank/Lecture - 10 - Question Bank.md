# Lecture 10 - Systems for ML — Question Bank

CSEE 4121 · Spring 2026

---

## Multiple Choice Questions

### Section 1: ML History & Hardware Evolution

**1.** Which year did the Transformer architecture get introduced?
- A) 1958
- B) 1986
- C) 1998
- D) 2017

**2.** Why did ML not "take off" in the 1990s despite most techniques already existing?
- A) The math wasn't well understood
- B) There was insufficient data and compute
- C) Neural networks hadn't been invented
- D) Programming languages were too slow

**3.** What architectural choice in the Transformer was particularly systems-relevant?
- A) Recurrent processing
- B) Parallel attention mechanism
- C) Sequential token processing
- D) Convolutional filters

**4.** At small scale (1990s), which approaches outperformed neural networks?
- A) Decision trees only
- B) SVMs and random forests
- C) Linear regression
- D) k-Nearest Neighbors

**5.** Which dataset is described as containing 14M labeled images?
- A) Common Crawl
- B) The Pile
- C) ImageNet
- D) MNIST

**6.** When was CUDA launched?
- A) 2006
- B) 2007
- C) 2016
- D) 2017

**7.** What is the memory capacity of the B200 GPU (2024)?
- A) 80 GB HBM3
- B) 96 GB HBM3
- C) 192 GB HBM3e
- D) 256 GB HBM3e

**8.** What is the bandwidth advantage of H100 over A100 (both 80GB)?
- A) Same bandwidth
- B) ~67% faster (3.35 TB/s vs 2 TB/s)
- C) 2x faster
- D) 10x faster

**9.** What are the three ingredients of ML success?
- A) Models, Data, Hardware
- B) Algorithms, Compute, Storage
- C) Networks, Training, Inference
- D) Frameworks, GPUs, Cloud

**10.** Which is a characteristic of LLM-era data compared to earlier ML data?
- A) Smaller, more curated
- B) Massive unsupervised web scrapes with trillions of tokens
- C) Only labeled images
- D) Synthetically generated

---

### Section 2: Traditional ML

**11.** Which framework dominates Kaggle competitions on tabular data?
- A) PyTorch
- B) TensorFlow
- C) XGBoost / LightGBM
- D) JAX

**12.** Approximately how long does training XGBoost on a single CPU typically take?
- A) Microseconds
- B) Seconds to minutes
- C) Hours
- D) Days

**13.** What is XGBoost's typical inference latency?
- A) Microseconds per prediction
- B) Milliseconds per prediction
- C) Seconds per prediction
- D) Minutes per prediction

**14.** Which is NOT listed as a typical use case for traditional ML?
- A) Fraud detection
- B) Ad ranking
- C) Image generation
- D) Recommender systems

---

### Section 3: Deep Neural Networks

**15.** What are the core operations of a DNN?
- A) Sorting and searching
- B) Matrix multiplication, convolution, attention
- C) Hashing and indexing
- D) Branching and looping

**16.** What data structure is used in DNNs?
- A) Linked lists
- B) Hash tables
- C) Tensors (n-dimensional arrays)
- D) Trees

**17.** Why are GPUs well-suited for DNNs?
- A) They have larger memory than CPUs
- B) They are optimized for parallel tensor algebra
- C) They have faster single-thread performance
- D) They support more programming languages

---

### Section 4: ML Development Cycle

**18.** Where does most time actually go in the ML development cycle?
- A) Training
- B) Data cleaning
- C) Inference
- D) Deployment

**19.** Approximately how much does training from scratch cost?
- A) $10 – $10,000
- B) $100K – $1M
- C) $10M – $500M+
- D) Pay per token

**20.** What hardware is needed to fine-tune a model?
- A) Thousands of GPUs
- B) 1-8 GPUs
- C) Zero GPUs
- D) CPU only

**21.** Hardware requirements vary by approximately how much depending on the path chosen?
- A) 10x
- B) 100x
- C) 1000x
- D) 10,000x

---

### Section 5: CPU vs GPU

**22.** How many cores do modern server CPUs have?
- A) 4-16
- B) 64-192
- C) 1000+
- D) 10,000+

**23.** What is typical memory bandwidth for modern dual-socket server CPUs?
- A) 50-100 GB/s
- B) 200-600+ GB/s
- C) 2-8 TB/s
- D) 10-30 TB/s

**24.** How are GPU threads grouped for execution?
- A) Into pairs of 2 threads
- B) Into warps of 32 threads (SIMT)
- C) Into blocks of 1024 threads only
- D) Independently with no grouping

**25.** What is the H100's memory bandwidth?
- A) 600 GB/s
- B) 2 TB/s
- C) 3.35 TB/s
- D) 8 TB/s

**26.** What is the GPU memory bandwidth advantage over CPU?
- A) Same
- B) 2-3x higher
- C) 10-30x higher
- D) 100x higher

**27.** Which company has >90% market share for ML training GPUs?
- A) AMD
- B) Intel
- C) Nvidia
- D) Google

**28.** What does HBM stand for?
- A) High-Bandwidth Memory
- B) Hardware-Based Memory
- C) High-Buffer Memory
- D) Heterogeneous Block Memory

**29.** How is HBM physically constructed?
- A) On a separate board connected by PCIe
- B) Memory chips stacked one on top of another via TSVs
- C) Inside the CPU package
- D) On flash storage

**30.** What is the bandwidth advantage of HBM over regular DRAM?
- A) 10x higher
- B) 100x higher
- C) Same bandwidth
- D) 2x higher

**31.** What is a downside of HBM compared to regular DRAM?
- A) Slower bandwidth
- B) 10x or more lower capacity, more expensive
- C) Higher power consumption
- D) Incompatible with GPUs

**32.** Which scenario is best for using a CPU instead of a GPU?
- A) Training a transformer model
- B) Convolutional neural networks
- C) XGBoost on tabular data with 50 features
- D) Large matrix multiplications

**33.** Approximately how much more do GPU instances cost per hour than CPU?
- A) 1.5-2x
- B) 5-10x
- C) 50-100x
- D) Same cost

---

### Section 6: Other Accelerators

**34.** Which company makes the TPU?
- A) Nvidia
- B) AMD
- C) Google
- D) Intel

**35.** What is AMD's MI300X notable for?
- A) 80GB HBM2
- B) 192GB HBM3
- C) 24GB GDDR6
- D) Integrated CPU

**36.** What is Nvidia's approximate share of ML training?
- A) 30-40%
- B) 50-60%
- C) 70-80%
- D) >95%

**37.** Why does Nvidia maintain dominance even when competitors match specs?
- A) Lower prices
- B) Better warranty
- C) CUDA ecosystem + PyTorch integration software moat
- D) Government regulations

**38.** Edge hardware (phones, IoT) is primarily focused on:
- A) Training
- B) Inference
- C) Both equally
- D) Data storage

---

### Section 7: When NOT to Use a GPU

**39.** In the bank fraud detection example, why would a GPU be slower than a CPU?
- A) GPUs cannot run XGBoost at all
- B) Data transfer overhead exceeds computation; model too small to use cores
- C) Banks don't allow GPUs
- D) GPUs use too much power

**40.** Tree-based models don't map well to GPUs because:
- A) They require too much memory
- B) They don't fit GPU's parallel matrix math
- C) They are too large
- D) They are written in Python only

---

### Section 8: CUDA and Software Stack

**41.** What language is CUDA most similar to?
- A) Python
- B) Java
- C) C/C++
- D) JavaScript

**42.** In CUDA, how does each thread know its position?
- A) Via thread ID returned by a function
- B) Via blockIdx + threadIdx
- C) Threads don't have positions
- D) Via global index variable only

**43.** What is the de facto standard ML framework?
- A) TensorFlow
- B) JAX
- C) PyTorch
- D) Theano

**44.** Which framework is described as functional and used by Google DeepMind?
- A) PyTorch
- B) JAX
- C) MXNet
- D) Caffe

**45.** Which framework is described as "historically important, now largely legacy"?
- A) TensorFlow
- B) PyTorch
- C) JAX
- D) Triton

---

### Section 9: Computation as a Graph

**46.** In PyTorch's computation graph, what do nodes represent?
- A) Tensors
- B) Operations (MatMul, Add, ReLU, Softmax)
- C) GPUs
- D) Layers

**47.** In PyTorch's computation graph, what do edges represent?
- A) Operations
- B) Tensors flowing between operations
- C) Network connections
- D) Memory addresses

**48.** What does the graph structure enable?
- A) Operator fusion, memory planning, automatic differentiation
- B) Network communication
- C) File I/O
- D) Database operations

**49.** What is similar between Spark and PyTorch graphs?
- A) Both run on CPUs only
- B) Both have nodes as transformations/operations and edges as data
- C) Both use the same syntax
- D) They have nothing in common

---

### Section 10: PyTorch Features

**50.** What does "eager execution" mean in PyTorch?
- A) Code runs as fast as possible
- B) Code runs line by line like normal Python (easy to debug)
- C) Code is compiled before execution
- D) Code runs in parallel automatically

**51.** What does `torch.autograd` provide?
- A) Automatic GPU allocation
- B) Automatic differentiation (gradient computation)
- C) Automatic data loading
- D) Automatic model architecture search

**52.** What does `.to('cuda')` do?
- A) Saves the model to disk
- B) Moves computation to GPU in one line
- C) Compiles the model
- D) Loads pretrained weights

---

### Section 11: Usability vs Performance

**53.** Order from fastest raw performance to easiest to write:
- A) PyTorch → Triton → CUDA
- B) CUDA → Triton → PyTorch
- C) Triton → CUDA → PyTorch
- D) PyTorch → CUDA → Triton

**54.** Triton was developed by:
- A) Google
- B) Meta
- C) OpenAI
- D) Nvidia

---

### Section 12: Memory Math

**55.** How is model memory calculated?
- A) # parameters / bytes per parameter
- B) # parameters × bytes per parameter
- C) # layers × hidden dimension
- D) # layers + parameters

**56.** How many bytes per parameter does FP16 use?
- A) 4
- B) 2
- C) 1
- D) 0.5

**57.** What is the memory size of Llama 3 70B in FP16?
- A) 70 GB
- B) 140 GB
- C) 280 GB
- D) 35 GB

**58.** What is the memory size of Llama 3 8B in INT4?
- A) 16 GB
- B) 8 GB
- C) 4 GB
- D) 2 GB

**59.** For training, you multiply model memory by approximately:
- A) 1x
- B) 2x
- C) 4x
- D) 10x

**60.** Why does Adam require ~4x model memory for training?
- A) It stores 4 copies of the model
- B) Weights + gradients + optimizer states (Adam stores 2 extra copies per param)
- C) It uses redundant memory for safety
- D) It needs separate forward and backward memory

---

### Section 13: Mixed Precision

**61.** Which precision is described as "best of both worlds — preferred"?
- A) FP32
- B) FP16
- C) BF16
- D) FP8

**62.** What is the throughput advantage of FP16 over FP32?
- A) Same
- B) 2x throughput on tensor cores
- C) 4x throughput
- D) 10x throughput

**63.** Which precision was used in DeepSeek V3 as cutting edge?
- A) FP32
- B) FP16
- C) BF16
- D) FP8

**64.** What handles mixed precision automatically in PyTorch?
- A) `torch.autograd`
- B) `torch.cuda.amp`
- C) `torch.compile`
- D) `torch.distributed`

---

### Section 14: Batch Size & Data Loading

**65.** What happens with too small a batch size?
- A) OOM crash
- B) GPU cores sit idle, wasting money
- C) Better accuracy
- D) Faster training

**66.** What is the practical approach for finding ideal batch size?
- A) Always use batch size of 1
- B) Always use batch size of 1024
- C) Start small, double until OOM, back off one step
- D) Use the largest size the documentation recommends

**67.** What is "data starvation"?
- A) Not enough training data
- B) GPU finishes a batch before next one is ready, sits idle
- C) Disk runs out of space
- D) Network connection lost

**68.** Which is NOT a solution for data loading bottlenecks?
- A) Multiple DataLoader workers
- B) Prefetching
- C) Pinned memory
- D) Reducing model size

---

### Section 15: GPU Utilization

**69.** What command-line tool monitors and manages GPUs?
- A) `gpu-stats`
- B) `nvidia-smi`
- C) `cuda-info`
- D) `gpu-monitor`

**70.** A GPU utilization of 0-10% likely indicates:
- A) Good utilization
- B) Data loading bottleneck or model too small
- C) Hardware failure
- D) Successful training

**71.** What's the target GPU utilization range?
- A) 0-10%
- B) 30-50%
- C) 80-100%
- D) Exactly 100%

---

### Section 16: Cloud Hardware

**72.** Approximate cost per hour for an H100?
- A) $0.50
- B) $1.50
- C) $10-15
- D) $25-35

**73.** What is the memory of an A10G?
- A) 16 GB
- B) 24 GB
- C) 40 GB
- D) 80 GB

**74.** Spot/preemptible instances are typically how much cheaper?
- A) 10-20%
- B) 30-40%
- C) 60-70%
- D) 90%

**75.** What is essential when using spot/preemptible instances?
- A) Faster GPUs
- B) Checkpointing
- C) Larger batches
- D) Multi-node setup

---

### Section 17: Running LLMs Locally

**76.** Llama 3 70B in INT4 requires approximately how much memory?
- A) 4 GB
- B) 16 GB
- C) 35 GB
- D) 140 GB

**77.** What tool is mentioned for running quantized models locally?
- A) huggingface-cli
- B) ollama
- C) docker
- D) cuda-runtime

**78.** What makes DeepSeek V3 (671B) practical to run on smaller hardware?
- A) FP32 precision
- B) MoE: only 37B active per token
- C) It's compiled to C
- D) It only uses CPU

---

### Section 18: Training Step

**79.** During which phase does memory peak in training?
- A) Forward pass
- B) Loss computation
- C) Backward pass
- D) Optimizer update

**80.** What is stored during the forward pass for use later?
- A) Output predictions only
- B) Intermediate activations
- C) Model weights
- D) Loss values

**81.** What does the optimizer (Adam) maintain per parameter?
- A) Just the current weight
- B) Running mean + variance (2 extra copies)
- C) Full gradient history
- D) Nothing extra

---

### Section 19: Activations & Checkpointing

**82.** How is activation memory approximated?
- A) batch_size × seq_len × hidden_dim × num_layers × bytes
- B) num_parameters × bytes
- C) batch_size × num_layers
- D) hidden_dim × bytes

**83.** What does activation checkpointing do?
- A) Stores all activations to disk
- B) Doesn't store all activations — recomputes them during backward pass
- C) Skips backward pass
- D) Reduces model size

**84.** Activation checkpointing trades memory for approximately how much extra compute?
- A) 5%
- B) 33%
- C) 100%
- D) 200%

---

### Section 20: Training Time & Cost

**85.** How is training time estimated?
- A) Total tokens × throughput
- B) Total tokens / throughput in tokens/sec
- C) Number of epochs × batch size
- D) Model size × dataset size

**86.** What's recommended before committing to a long training run?
- A) Buy more GPUs
- B) Run a short test (1% of data) first, then extrapolate
- C) Train at 100% precision
- D) Use the smallest model

---

### Section 21: Checkpointing

**87.** Why is checkpointing essential for large training runs?
- A) Hardware fails (GPUs overheat, machines crash, instances preempted)
- B) It speeds up training
- C) It increases accuracy
- D) It reduces model size

**88.** What is included in a training checkpoint?
- A) Model only
- B) Model + optimizer + training state
- C) Just gradients
- D) Just data

**89.** Typical checkpoint frequency is:
- A) Every step
- B) Every 30 minutes to a few hours
- C) Once at the end
- D) Every epoch only

---

### Section 22: Monitoring

**90.** What does a sudden gradient norm spike often indicate?
- A) Successful training
- B) Numerical instability (especially with FP16)
- C) Better accuracy
- D) Faster convergence

**91.** Which is NOT a recommended monitoring tool?
- A) Weights & Biases (wandb)
- B) TensorBoard
- C) PyTorch Profiler
- D) Microsoft Excel

---

### Section 23: DeepSeek V3

**92.** Approximately how many GPU hours did DeepSeek V3 use vs Llama 3 405B?
- A) 12x more
- B) Same
- C) 12x fewer (2.7M vs 30.8M)
- D) 100x fewer

**93.** Which technique did DeepSeek V3 use to halve bandwidth needs?
- A) BF16 mixed precision
- B) FP8 mixed precision (~80% of computation)
- C) INT4 quantization
- D) FP32 throughout

**94.** What does DeepSeek V3's MoE achieve?
- A) Activates all 671B params per token
- B) 671B total but only 37B active per token
- C) Uses only one expert
- D) Splits across multiple GPUs

**95.** What does the reported $5.5M cost for DeepSeek V3 EXCLUDE?
- A) GPU rental
- B) R&D, failed experiments, salaries, total hardware (~$1.3B)
- C) Both rental and hardware
- D) Nothing — it's all-inclusive

---

### Section 24: Multi-GPU Strategies

**96.** What's the strategy when a model doesn't fit on one GPU?
- A) Data parallelism
- B) Model parallelism
- C) Pipeline parallelism only
- D) Use a smaller model

**97.** What is data parallelism?
- A) Same model on each GPU, different data, synchronize gradients
- B) Different models on each GPU
- C) One GPU per data sample
- D) Sequential data processing

---

### Section 25: Training vs Inference

**98.** Training is primarily:
- A) Memory-bound
- B) Compute-bound
- C) Network-bound
- D) Disk-bound

**99.** Inference (text generation) is primarily:
- A) Compute-bound
- B) Memory-bound (bandwidth is the bottleneck)
- C) Network-bound
- D) CPU-bound

**100.** Why is single-token inference memory-bound?
- A) GPUs have low memory
- B) All model weights must be loaded for each token; computation is small relative to data movement
- C) Memory chips are slow
- D) Networks are slow

---

### Section 26: KV Cache

**101.** What does the KV cache store?
- A) Model weights
- B) Key and Value matrices from previous tokens
- C) Training data
- D) Gradients

**102.** How does the KV cache grow?
- A) seq_length × num_layers × hidden_dim × 2
- B) num_parameters × bytes
- C) batch_size only
- D) It doesn't grow

**103.** For Llama 70B at seq_length 4096 in FP16, KV cache per request is approximately:
- A) 1 GB
- B) 10 GB
- C) 100 GB
- D) 500 GB

**104.** What is described as "THE central systems challenge of modern inference"?
- A) Network bandwidth
- B) Managing KV cache memory
- C) Model size
- D) Disk I/O

---

### Section 27: Batching for Inference

**105.** Why does batching help with memory-bound inference?
- A) It reduces model size
- B) Loading 140GB once and multiplying by N users' vectors → N x more useful work per weight load
- C) It speeds up the network
- D) It reduces precision

**106.** What's a problem with static batching?
- A) Uses too much memory
- B) Fast requests wait for slow ones
- C) Cannot handle multiple users
- D) Requires special hardware

**107.** Continuous batching is most similar to:
- A) Database queries
- B) An OS scheduler
- C) Network routing
- D) File compression

**108.** Which serving framework is NOT mentioned as using continuous batching?
- A) vLLM
- B) TensorRT-LLM
- C) SGLang
- D) Apache Spark

---

### Section 28: Quantization

**109.** What is quantization?
- A) Increasing model size
- B) Representing weights with fewer bits after training
- C) Adding more layers
- D) Augmenting training data

**110.** Why does quantization help inference particularly?
- A) Inference is compute-bound and quantization speeds compute
- B) Inference is memory-bound; halving weight size ≈ doubling speed
- C) Quantization reduces network traffic
- D) It removes the KV cache

**111.** Which is NOT a popular quantization tool?
- A) GPTQ
- B) AWQ
- C) bitsandbytes
- D) TensorFlow

**112.** Going from FP16 to INT4 reduces memory by approximately:
- A) Half
- B) Quarter
- C) Same
- D) 10x

---

### Section 29: GPU Costs in Practice

**113.** In the real-world example, what monthly cost is estimated for serving Llama 70B with one H100?
- A) ~$1,000
- B) ~$5,000
- C) ~$17,500
- D) ~$50,000

**114.** With redundancy (2-3 GPUs), serving cost is approximately:
- A) $5-10K/month
- B) $35-50K/month
- C) $100-200K/month
- D) Same as one GPU

**115.** Why might companies prefer using APIs (OpenAI, Anthropic)?
- A) APIs are always cheaper
- B) Let someone else handle infrastructure (training one-time, serving ongoing)
- C) Better accuracy
- D) Required by law

---

### Section 30: Key Takeaways

**116.** Which statement is true about mixed precision?
- A) It always reduces accuracy
- B) It is (almost) always a free lunch
- C) It only works on H100
- D) It requires retraining from scratch

**117.** Infrastructure costs for most AI companies are dominated by:
- A) Training
- B) Inference at scale
- C) Data storage
- D) Networking

**118.** What's the rule of thumb about model size and GPU choice?
- A) Always use the largest GPU available
- B) If your model fits in a spreadsheet, use a CPU
- C) Always use multi-GPU
- D) Cloud is always best

**119.** What should you always do before committing to a training/serving strategy?
- A) Buy hardware first
- B) Estimate costs
- C) Train a baseline
- D) Hire more engineers

**120.** The Streaming Multiprocessor (SM) is:
- A) A type of memory
- B) The grouping of cores in a GPU
- C) A network protocol
- D) A software framework

---

## Answer Key

| Q | A | Q | A | Q | A | Q | A |
|---|---|---|---|---|---|---|---|
| 1 | D | 31 | B | 61 | C | 91 | D |
| 2 | B | 32 | C | 62 | B | 92 | C |
| 3 | B | 33 | B | 63 | D | 93 | B |
| 4 | B | 34 | C | 64 | B | 94 | B |
| 5 | C | 35 | B | 65 | B | 95 | B |
| 6 | B | 36 | C | 66 | C | 96 | B |
| 7 | C | 37 | C | 67 | B | 97 | A |
| 8 | B | 38 | B | 68 | D | 98 | B |
| 9 | A | 39 | B | 69 | B | 99 | B |
| 10 | B | 40 | B | 70 | B | 100 | B |
| 11 | C | 41 | C | 71 | C | 101 | B |
| 12 | B | 42 | B | 72 | D | 102 | A |
| 13 | A | 43 | C | 73 | B | 103 | B |
| 14 | C | 44 | B | 74 | C | 104 | B |
| 15 | B | 45 | A | 75 | B | 105 | B |
| 16 | C | 46 | B | 76 | C | 106 | B |
| 17 | B | 47 | B | 77 | B | 107 | B |
| 18 | B | 48 | A | 78 | B | 108 | D |
| 19 | C | 49 | B | 79 | C | 109 | B |
| 20 | B | 50 | B | 80 | B | 110 | B |
| 21 | C | 51 | B | 81 | B | 111 | D |
| 22 | B | 52 | B | 82 | A | 112 | B |
| 23 | B | 53 | B | 83 | B | 113 | C |
| 24 | B | 54 | C | 84 | B | 114 | B |
| 25 | C | 55 | B | 85 | B | 115 | B |
| 26 | C | 56 | B | 86 | B | 116 | B |
| 27 | C | 57 | B | 87 | A | 117 | B |
| 28 | A | 58 | C | 88 | B | 118 | B |
| 29 | B | 59 | C | 89 | B | 119 | B |
| 30 | B | 60 | B | 90 | B | 120 | B |
