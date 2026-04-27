# Lecture 10 - Systems for ML

CSEE 4121 · Spring 2026

---

## 1. The Rise of Machine Learning

### Why ML Took Off
- Most ML techniques (Perceptron 1958, Backprop 1986, SVM 1992, ConvNet 1998, GBM 1999) existed by the 1990s
- The **Transformer (2017)** was the breakthrough — its attention mechanism is **parallel**, enabling scaling that RNNs could not achieve
- Three ingredients had to converge:
  1. **Models** (ResNet, Transformers, MoE)
  2. **Data** (ImageNet, Common Crawl)
  3. **Hardware** (GPUs, TPUs, HBM)

### Neural Networks vs Traditional ML
- At small scale (1990s), SVMs/random forests outperformed neural nets
- Neural networks have a **higher accuracy ceiling** — but only with enough data and compute
- Modern hardware + internet-scale data pushed past the crossover point

### Big Data Evolution
- **Earlier**: curated, labeled datasets (ImageNet = 14M labeled images)
- **LLM era**: massive unsupervised web scrapes — trillions of tokens (Common Crawl, The Pile)
- Shift: from "labeled examples for one task" → "all text on the internet"

### AI Hardware Timeline
| Year | Milestone |
|------|-----------|
| 2006 | Public Cloud |
| 2007 | CUDA / Nvidia GPU |
| 2016 | TPU v1 |
| 2017 | Tensor Cores |
| 2020 | A100 (80GB HBM2e) |
| 2022 | H100 (80GB HBM3) |
| 2024 | B200 (192GB HBM3e) |

- Each generation: ~2-3x more compute, more memory, higher bandwidth
- H100 vs A100: same 80GB but **3.35 TB/s vs 2 TB/s** (67% faster memory)
- B200: first single GPU that can hold a 70B model in FP16

---

## 2. Traditional ML Still Matters

- Regressions, random forests, SVM, XGBoost, LightGBM are still widely used
- **Cheap**: train on a single CPU in seconds to minutes
- **Inference**: microseconds per prediction
- XGBoost/LightGBM dominate Kaggle on tabular data
- Used in: fraud detection, monitoring, networking, ad ranking, recommender systems
- Frameworks: scikit-learn, XGBoost, LightGBM

> **Always ask:** Does my task actually require a deep neural network?

---

## 3. Deep Neural Networks

- Collection of trainable mathematical units organized in layers
- **Core operations**: matrix multiplication, convolution, attention
- **Data structures**: tensors (n-dimensional arrays)
- A DNN is essentially a pipeline of tensor algebra operations
- GPUs help because they're optimized for the same parallel math

### ML Development Cycle
1. Data collection
2. Data cleaning *(where most time goes)*
3. Training / pretraining
4. Post-training (RLHF, DPO, distillation, constitutional methods)
5. Model tuning
6. Integration with RAG
7. Inference *(cost grows with users — often dominates total spend)*

### Pretrained vs Train From Scratch

| Path | Hardware | Time | Cost |
|------|----------|------|------|
| Train from scratch | Thousands of GPUs | Months | $10M – $500M+ |
| Fine-tune | 1-8 GPUs | Hours to days | $10 – $10,000 |
| Use an API | Zero GPUs | Seconds | Pay per token |

> Hardware needs vary by **1000x** depending on path.

---

## 4. Hardware for ML

### CPU
- General-purpose, originally serial execution
- **64-192 cores**, 100s GB - TBs DDR5 DRAM
- Bandwidth: **~200-600 GB/s**
- Strengths: versatility, huge memory, branching/control flow
- Companies: Intel, AMD, ARM

### GPU
- Originally for graphics, repurposed since CUDA (2007)
- **Thousands of small cores** organized into Streaming Multiprocessors (SMs)
- Cores grouped into **warps** (32 threads, same instruction = SIMT)
- Memory: 24-192 GB **HBM** (High-Bandwidth Memory)
- Bandwidth: **2-8 TB/s** (10-30x higher than CPU)
- H100 specs: 132 SMs, 80 GB HBM, 3.35 TB/s
- Nvidia: >90% market share for ML training

### HBM (High-Bandwidth Memory)
- Memory chips **stacked** on top of each other
- **Through-Silicon-Vias (TSV)** provide high bandwidth
- Interposer connects DRAM stack with GPU
- Smart memory controller (mini processor) manages it
- **100x bandwidth** of regular DRAM, but **10x lower capacity**, much more expensive
- This is why GPU memory is precious — every GB matters

### CPU vs GPU Decision

| Use GPU when... | Use CPU when... |
|------|------|
| Large matrix multiplications | Small models (trees, regressions) |
| Same operation on lots of data | Irregular data structures |
| Millions+ parameters | Lots of branching/control flow |
| Deep neural networks | Data transfer overhead > computation |

> **Rule of thumb**: If your model fits in a spreadsheet, use a CPU. GPU instances cost 5-10x more per hour.

### Other Accelerators
- **Google TPU**: matrix ops, JAX integration
- **AMD MI300X**: 192GB HBM3, gaining traction for training
- **AWS Trainium, Meta MTIA**: custom silicon
- **Intel Gaudi**: limited adoption
- Reality: Nvidia ~70-80% of training, TPUs ~20-25% (mostly Google internal)
- **Why Nvidia dominates**: CUDA ecosystem + PyTorch integration = massive software moat

### Edge Hardware
- GPUs/NPUs on phones for ~10 years
- Optimized for **low power**, **inference only**
- Examples: Apple Neural Engine, Qualcomm Hexagon, Google Edge TPU
- Use cases: voice recognition, keyboard prediction, camera features (privacy + latency)

### When NOT to Use a GPU
- **Example**: Fraud detection at a bank (XGBoost, 50 features, 1,000 trees)
  - Training: 30 sec on CPU
  - Inference: microseconds per row
- GPU would be slower because:
  - Data transfer overhead (CPU → GPU) exceeds computation
  - Model too small to use thousands of cores
  - Trees don't map to parallel matrix math

---

## 5. Programming ML Hardware

### CUDA
- Nvidia's GPU programming language (C/C++ like)
- Very low-level: programmer manages threads, blocks, memory
- Launched in 2007 — major reason for Nvidia's dominance
- Most ML developers don't write CUDA directly, but it powers PyTorch

### CUDA Matrix Addition Takeaways
- CPU and GPU code clearly separated
- Programmer specifies thread count and block size
- Each thread knows its position via `blockIdx + threadIdx`
- Thread blocks execute in any order → scalable

### The Software Stack
```
Python Code         (what you write)
    ↓
PyTorch / JAX       (framework layer)
    ↓
CUDA Kernels        (cuDNN, cuBLAS - optimized GPU code)
    ↓
GPU Hardware        (Nvidia H100, A100, ...)
```
- **PyTorch** (Meta): de facto standard, "Pythonic"
- **JAX** (Google): functional, used by Google DeepMind
- **TensorFlow** (Google): historically important, now legacy

### Computation as a Graph
- Both PyTorch and TensorFlow model computation as a **directed graph**
- **Nodes** = operations (MatMul, Add, ReLU, Softmax)
- **Edges** = tensors flowing between operations
- Similar to Spark (nodes = transformations, edges = RDDs/DataFrames)
- Enables: **operator fusion, memory planning, automatic differentiation**

### PyTorch Features
- **Eager execution**: code runs line by line (easy to debug)
- **torch.autograd**: automatic differentiation
- **.to('cuda')**: one line to move computation to GPU
- **torch.compile()**: newer graph-mode optimization
- Ecosystem: Hugging Face, torchvision, torchaudio

### Usability vs Performance
| Tool | Tradeoff |
|------|----------|
| CUDA | Fastest, hardest |
| Cutlass | C++ templates, matrix ops |
| Triton | Python-like GPU kernels (now mainstream via torch.compile) |
| PyTorch | Easiest, slowest raw perf |

---

## 6. Memory Math for ML

### Does My Model Fit in Memory?
**Model memory = # parameters × bytes per parameter**

| Precision | Bytes/Param | Llama 3 8B | Llama 3 70B |
|-----------|-------------|------------|-------------|
| FP32 | 4 | 32 GB | 280 GB |
| FP16 / BF16 | 2 | 16 GB | 140 GB |
| INT8 | 1 | 8 GB | 70 GB |
| INT4 | 0.5 | 4 GB | 35 GB |

> **Training**: multiply by ~4x (weights + gradients + optimizer states; Adam stores 2 extra copies)
> Example: Llama 8B in FP16 ≈ 16 GB × 4 = ~64 GB → barely fits on 80GB A100

### Mixed Precision Training
| Format | Bits | Notes |
|--------|------|-------|
| **FP32** | 32 | Full precision, traditional default |
| **FP16** | 16 | Half memory, 2x throughput on tensor cores; can have numerical instability |
| **BF16** | 16 | Same range as FP32, less precision — **preferred** |
| **FP8** | 8 | Quarter memory, 4x throughput; cutting edge (DeepSeek V3) |

> `torch.cuda.amp` handles this automatically. Almost always a free lunch.

### Batch Size Tradeoffs
- **Larger batch** → more memory, better GPU utilization
- **Too large** → OOM crash
- **Too small** → idle GPU cores
- **Practical**: start small, double until OOM, back off one step

### Data Loading as a Bottleneck
**Pipeline**: Disk/Network → CPU RAM → Preprocess → GPU HBM

If GPU finishes a batch before next is ready → **data starvation**
- **Causes**: slow disk, complex CPU preprocessing, single thread
- **Solutions**:
  - Multiple DataLoader workers (`num_workers`)
  - Prefetching
  - Pinned memory
  - Fast formats: WebDataset, pre-tokenized data

### GPU Utilization (`nvidia-smi`)
- **0-10%**: data loading bottleneck or model too small
- **30-50%**: batch too small or CPU preprocessing bottleneck
- **80-100%**: good
- Other tools: PyTorch Profiler, Nsight Systems, Weights & Biases

---

## 7. Choosing Cloud Hardware

| GPU | Memory | ~Cost/hr | Good for |
|-----|--------|----------|----------|
| T4 | 16 GB | $0.50 | Inference, small fine-tuning |
| A10G | 24 GB | $1.50 | Medium workloads |
| A100 | 40/80 GB | $10-15 | Serious training |
| H100 | 80 GB | $25-35 | Large-scale training, fastest |

### Decision Framework
1. Do I need a GPU at all? (XGBoost/sklearn → probably not)
2. Does my model fit on one GPU?
3. How long will training take?
4. What's my budget? (hours × cost/hr)

> **Spot/preemptible** instances: 60-70% cheaper but interruptible — use **checkpointing**!

### Running LLMs on Laptops
| Model | FP16 Size | INT4 Size | Runs on... |
|-------|-----------|-----------|------------|
| Llama 3 8B | 16 GB | 4 GB | Any modern laptop |
| Llama 3 70B | 140 GB | 35 GB | 64GB Mac or server GPU |
| DeepSeek V3 671B | 1.3 TB | ~170 GB | ~$10K server (MoE: only 37B active) |

- Tool: **ollama** — one-line install, runs quantized models locally
- Quantization makes the impossible possible

---

## 8. Training & Inference

### One Training Step
1. **Forward Pass**: input → layers → store intermediate activations
2. **Loss Computation**: compare output to ground truth
3. **Backward Pass**: compute gradients using stored activations
4. **Optimizer Update**: adjust weights (Adam: running mean + variance)

> Memory peaks during backward pass — need activations + gradients + optimizer states simultaneously.

### Activations: Hidden Memory Cost
**Activation memory ≈ batch_size × seq_len × hidden_dim × num_layers × bytes**

Example: Llama 8B, batch=8, seq_len=2048, hidden=4096, 32 layers, BF16
- Per layer: 8 × 2048 × 4096 × 2 ≈ 128 MB
- All 32 layers: ~4 GB just for activations

**Activation checkpointing (gradient checkpointing)**:
- Don't store all activations — recompute during backward pass
- Saves memory, costs ~33% more compute
- PyTorch: `torch.utils.checkpoint`

### Training Time Estimation
**Training time ≈ total tokens / throughput (tokens/sec)**

Example: Fine-tuning Llama 8B on 1M examples
- Throughput on A100: ~3,000 tokens/sec
- 1M × 512 tokens = 512M tokens
- 512M / 3,000 ≈ 47 hours ≈ 2 days
- Cost: 47 hrs × $15/hr ≈ $700

> Always do a short test run (1% of data) first, then extrapolate.

### Checkpointing & Fault Tolerance
- Hardware fails: GPUs overheat, machines crash, instances preempted
- Without checkpoints: lose all progress = waste thousands
- Checkpoint = model + optimizer + training state to disk
- 8B model FP32 ≈ ~96 GB per checkpoint
- **Frequency tradeoff**: too often = slow I/O; too rarely = lost work
- Typical: every 30 min – few hours

### Monitoring
- **Loss curve**: should decrease; plateaus/spikes/divergence = problem
- **GPU utilization**: should be high; drops = data loading or batch issues
- **Memory usage**: watch for gradual leaks
- **Gradient norms**: spikes = numerical instability (esp. FP16)
- Tools: Weights & Biases, TensorBoard, nvidia-smi, PyTorch Profiler

> Don't launch a multi-day run and walk away — monitor it, especially the first hours.

### DeepSeek V3 vs Llama 3
- DeepSeek V3: **2.7M GPU hours**
- Llama 3 405B: **30.8M GPU hours**
- ~12x fewer GPU hours for a larger model
- $5.5M reported cost (GPU rental only)

**How DeepSeek did it**:
- **FP8 mixed precision**: ~80% computation at FP8
- **Mixture of Experts**: 671B total but only 37B active per token
- **Custom pipeline parallelism (DualPipe)**: better GPU utilization

> The $5.5M only counts GPU rental at ~$2/hr — excludes R&D, failed experiments, salaries, 50K total GPUs (~$1.3B in hardware). "Training cost" is ambiguous.

### Multi-GPU Preview
- **Model doesn't fit on one GPU** → model parallelism
- **Training too slow** → data parallelism (same model, different data, sync gradients)
- **Both** → hybrid parallelism
- Largest models use 3-4 types of parallelism across thousands of GPUs

---

## 9. Inference: Different Bottlenecks

### Training vs Inference
| Training | Inference |
|----------|-----------|
| Large batches (1000s) | One token at a time per user |
| Lots of matrix multiplications | Weight × tiny vector each step |
| **Compute-bound** | **Memory-bound** |
| GPU cores are bottleneck | Bandwidth is bottleneck |

### Why Inference is Memory-Bound
- To generate one token: load **all** model weights from HBM, multiply by one small vector
- Example: 70B model in FP16
  - Load 140 GB from memory
  - At 3 TB/s (H100): ~47 ms just to read weights
  - Computation: tiny fraction of that
- **Arithmetic intensity = FLOPs / bytes loaded** = very low for single-token generation
- GPU compute cores mostly idle, waiting for data
- Training (batch of 1024) → 1024x more useful work per weight load

### The KV Cache
- Each new token attends to all previous tokens
- Naive: recompute everything each step → wasteful
- **KV cache**: store Key/Value matrices from previous tokens
- **Problem**: grows with `seq_length × num_layers × hidden_dim × 2`
- Example: Llama 70B, seq 4096, FP16
  - KV cache per request: ~10 GB
  - 8 users simultaneously: 80 GB just for KV caches (= model weight memory!)
- **Managing KV cache memory is THE central systems challenge of modern inference**

### Batching for Inference
- Fix for memory-boundedness: **serve multiple users simultaneously**
- Loading 140 GB weights anyway → multiply by 32 users' vectors instead of 1
- **Static batching**: wait for N requests, process together — fast requests wait for slow
- **Continuous batching**: as one finishes, slot in new one (like OS scheduler)
  - Used by vLLM, TensorRT-LLM, SGLang

### Quantization for Deployment
- Represent weights with fewer bits **after training**
- FP16 → INT8: half memory, ~same quality
- FP16 → INT4: quarter memory, small quality loss, huge speedup
- **Why it helps inference**: memory-bound → halving weights ≈ doubling speed
- Tools: GPTQ, AWQ, bitsandbytes, llama.cpp
- Almost always do it for deployment (unless max precision needed)
- Post-training quantization is simpler and usually sufficient

### GPU Costs in the Real World
**Scenario**: Serve Llama 70B for 10,000 requests/day
- 500 input + 200 output tokens
- INT8 batched on H100: ~100 tokens/sec → ~7 sec/request
- 10,000 × 7 sec = 19.4 GPU-hours/day
- ~580 GPU-hours/month × $30/hr = **~$17,500/month** (one GPU)
- With redundancy (2-3 GPUs): **$35-50K/month**

> Training is one-time. Serving is ongoing and scales with users. This is why inference optimization matters more for most companies — and why many use APIs (OpenAI, Anthropic).

---

## Key Takeaways

1. **GPUs are powerful but expensive** — only use when workload justifies
2. **Memory is the binding constraint** — know your model size math
3. **Mixed precision is (almost) always a free lunch**
4. **Training is compute-bound; inference is memory-bound** — different optimizations for each
5. **Quantization makes large models accessible** on smaller hardware
6. **Infrastructure costs are dominated by inference at scale**, not training
7. **Always estimate costs before committing** to a training or serving strategy
