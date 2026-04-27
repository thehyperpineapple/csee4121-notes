# Lecture 11 - Open Systems Challenges in ML

## Overview

Machine Learning systems face many open systems and security challenges. These are largely **open problems** with no standard solutions or tools yet. The four major challenge areas are:

1. Testing and debugging
2. Version control and rolling back models
3. Managing models on heterogeneous hardware
4. Models leaking private information

---

## 1. Testing and Debugging

### Why It Matters

Deep learning is increasingly used in **safety-critical systems** where correctness and security are crucial:

- Self-driving cars
- Medical diagnosis
- Malware detection

**Real-world failure**: Tesla autopilot failed to recognize a white truck against a bright sky, leading to a fatal crash.

### Why Testing DL Models is Hard

**Common practice**: Measure accuracy on a test input set of randomly chosen data.

**Problem 1 — Coverage**:
- DL decision logic is incredibly complex
- No guarantee that all corner cases are covered

**Problem 2 — Labeling cost**:
- Test set data must be manually labeled
- Enlarging the test set requires more manual labeling effort

### Approach 1: Adversarial Testing of DL Models

**Adversarial testing (Szegedy et al.)**: Find corner-case inputs that are imperceptible to humans but induce errors.

Example: A school bus + carefully crafted noise → classified as an ostrich.

**Limitations**:
- Problem 1: Coverage of test set still unclear
- Problem 2: Still requires expensive labeling
- Problem 3: Not realistic — assumes a very powerful adversary (Sharif et al. CCS'16)

### Approach 2: Testing as an Optimization (DeepXplore)

**DeepXplore (Pei et al., Columbia research project)**: Automatically optimize the testing inputs.

Key ideas:
- **Optimize input for "neuron coverage"** (similar to code coverage) — exercise all neurons so a "rogue" neuron doesn't cause unexpected results
- **Use multiple models** — optimize input to find when their outputs diverge
- **Use realistic inputs** under realistic constraints (e.g., lighting)

**How it works**:
1. Start with seed inputs (no labels needed)
2. Feed through multiple DNNs
3. Mutate inputs using **gradient descent** (DNNs are differentiable)
4. Objective: maximize differences between models AND maximize neuron coverage
5. New input activates different neurons, exposing divergent behaviors

---

## 2. Version Control and Rolling Back Models

### Real-World Example: Phishing Email Detector

Models depend heavily on **feature engineering** and specific data points. Examples of features for phishing detection:

- Is the sender's email different from their normal address?
- Is the reply-to email different from the sender email?
- Is the email sent at a non-"normal" hour?

**Key insight**: Small tweaks to features can substantially change accuracy.

### Why Version Control is Hard

You must be careful not just about features, but also about **data points** included in training:

- Including a specific email example might significantly change accuracy
- Rare but important examples (e.g., a specific phishing pattern) matter

### Models Keep Evolving

New versions add new features and new data. Over time it becomes difficult to:

- Track/predict the importance of different features and datapoints
- Keep records of old model performance
- Remember which data was used to train old models
- Reproduce past results
- Collaborate (new team members don't know why a feature is in the model)

### Same Problem with Deep Neural Networks

- Companies often **fine-tune existing open source models** (e.g., Llama, DeepSeek)
- This creates many versions

**Critical questions**:
- What if there's a bug in the source model? How to update all derived versions efficiently?
- What if there's a bug in one fine-tuned model? How to find it and update only the relevant "branch"?

---

## 3. Managing Models on Heterogeneous Hardware

### Edge Deployment

Many time-sensitive ML tasks must run on edge devices rather than the cloud:

- Google Translate
- Self-driving cars
- Face recognition for security cameras
- Object recognition for drones

### Inference Variability Across Devices

**Inference accuracy varies widely**:
- Different devices apply different **quantization**
- Different devices apply different compression on photos/videos

**Inference performance varies widely**:
- Inference can vary **10x or more** between high-end cloud GPU/TPU and edge GPU/TPU
- Power consumption must also be considered

### Challenges in Managing Edge Models

- Does my model have lower accuracy on the edge?
- Is my model too slow on the edge?
- How do I do version control for many models?
- How do I train different models for different devices?

---

## 4. Models Leaking Private Information

### The Privacy Misconception

Models are **thought to be privacy-preserving** because they aggregate over a lot of data, but many examples show models can leak information about individuals.

### Examples of Privacy Leaks

**Generative text models**:
- Auto-completing "My social security number is..."

**Recommender models**:
- With auxiliary information about a user's past behavior, you can reconstruct what they bought/watched

**Classification models**:
- Reconstruct whether a user was a member of a group (e.g., patients discharged from a specific hospital)

### Differential Privacy

**Differential privacy (Dwork et al.)**: A technique that guarantees a certain level of privacy.

- **Guarantee**: An observer seeing the output of a differentially private model cannot tell if an individual's information was used in the computation.

**Basic intuition**: Add some noise to the data or the training process so that no individual data point can be de-anonymized.

- **Downside**: Reduces accuracy of the algorithm.
- Originally used for aggregating queries from a database.
- Now being applied to ML.

**Examples of systems**:
- **PrivateKube** (Tao et al., Columbia researchers) — enables differential privacy on continuously trained ML models
- **TensorFlow Privacy** — also enables differential privacy in ML

---

## Key Takeaways

- ML systems face four interrelated challenges: testing, versioning, hardware heterogeneity, and privacy.
- These are **open research problems** without standard solutions.
- **Testing** must move beyond random labeled test sets — neuron coverage and multi-model differential testing (DeepXplore) are promising directions.
- **Versioning** is hard because small data/feature changes can drastically alter model behavior, and fine-tuning creates branching dependencies.
- **Edge deployment** introduces 10x+ performance variability and accuracy differences due to quantization and compression.
- **Privacy leaks** happen even in aggregated models; **differential privacy** offers formal guarantees at the cost of accuracy.
