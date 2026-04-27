# Lecture 11 - Question Bank: Open Systems Challenges in ML

## Section 1: Overview of Open ML Challenges

**Q1.** Which of the following is NOT one of the four major open systems/security challenges in ML discussed in the lecture?
A) Testing and debugging
B) Version control and rolling back models
C) Managing models on heterogeneous hardware
D) Optimizing hyperparameter tuning

**Q2.** According to the lecture, the open challenges in ML systems are best described as:
A) Solved problems with standardized tooling
B) Open problems with no standard solutions/tools
C) Problems already addressed by major cloud providers
D) Issues only relevant to academic research

**Q3.** Which is one of the four major challenges in ML systems?
A) Models leaking private information
B) Reducing GPU costs
C) Increasing dataset size
D) Improving training speed

**Q4.** The lecture frames these ML challenges as:
A) Strictly security challenges
B) Strictly performance challenges
C) Both systems and security challenges
D) Only data engineering challenges

---

## Section 2: Testing and Debugging — Why It Matters

**Q5.** Why is correctness of deep learning models particularly important?
A) DL training is expensive
B) DL is used in safety-critical systems
C) DL models are computationally efficient
D) DL models are easy to debug

**Q6.** Which of these is given as an example of a safety-critical DL application?
A) Recommendation systems
B) Self-driving cars
C) Music generation
D) Search ranking

**Q7.** Which is NOT listed as a safety-critical DL application in the lecture?
A) Self-driving cars
B) Medical diagnosis
C) Malware detection
D) Video game AI

**Q8.** What real-world failure was cited as a model reliability example?
A) Tesla autopilot failing to recognize a white truck against a bright sky
B) Google Translate misinterpreting an emergency message
C) A facial recognition system locking out a CEO
D) A self-driving Uber crashing into a pedestrian

**Q9.** The Tesla autopilot incident demonstrated:
A) That DL models can fail catastrophically in corner cases
B) That DL training is too slow
C) That hardware is unreliable
D) That sensors are not needed

---

## Section 3: Why DL Testing is Hard

**Q10.** What is the common practice for testing DL models?
A) Formal verification
B) Measuring accuracy on a randomly chosen test input set
C) Symbolic execution
D) Manual code review

**Q11.** What is "Problem 1" with random test sets for DL models?
A) Computation time is too high
B) Coverage of the test set is unclear
C) Models are too large to test
D) Test sets are too small to fit in memory

**Q12.** Why is good test coverage hard to achieve in DL?
A) DL decision logic is incredibly complex
B) Hardware is too slow
C) Datasets are too small
D) Models cannot be loaded in memory

**Q13.** What is "Problem 2" with traditional DL testing?
A) Test data must be manually labeled
B) Models train too slowly
C) GPUs are unreliable
D) Frameworks are incompatible

**Q14.** To enlarge a DL test set, what is required?
A) Manual labeling of more data
B) More GPUs
C) Smaller models
D) Larger batch sizes

**Q15.** Which of these is a problem with traditional DL testing approaches?
A) Unclear test set coverage AND expensive labeling
B) Slow training AND limited memory
C) Poor GPU support AND lack of frameworks
D) Limited dataset sizes AND expensive hardware

---

## Section 4: Adversarial Testing (Approach 1)

**Q16.** Adversarial testing was introduced by:
A) Pei et al.
B) Szegedy et al.
C) Dwork et al.
D) Tao et al.

**Q17.** What does adversarial testing aim to find?
A) Inputs that maximize accuracy
B) Corner-case inputs imperceptible to humans but that induce errors
C) Inputs that minimize training loss
D) Inputs that activate every neuron

**Q18.** In the school bus example for adversarial testing, the perturbed image was misclassified as a(n):
A) Airplane
B) Truck
C) Ostrich
D) Dog

**Q19.** What does the school bus → ostrich example demonstrate?
A) That DL models are robust to noise
B) That carefully crafted noise can fool DL models even when imperceptible to humans
C) That DL models cannot classify buses
D) That training data was insufficient

**Q20.** Which of these is a limitation of adversarial testing (Problem 3)?
A) It assumes a very powerful adversary; not realistic
B) It requires too much computation
C) It only works on small models
D) It requires retraining the model

**Q21.** Adversarial testing's "powerful adversary" assumption is supported by which citation?
A) Sharif et al. CCS'16
B) Dwork et al.
C) Tao et al.
D) Szegedy et al.

**Q22.** Adversarial testing addresses which of the following problems?
A) Coverage of the test set
B) Expensive labeling effort
C) Both A and B
D) Neither — adversarial testing has the same problems plus realism issues

---

## Section 5: DeepXplore (Approach 2)

**Q23.** DeepXplore was developed by:
A) Pei et al. at Columbia
B) Szegedy et al.
C) Dwork et al.
D) Google Research

**Q24.** What is DeepXplore's main approach?
A) Manually labeling more test data
B) Treating testing as an optimization problem
C) Using larger models
D) Reducing model size

**Q25.** What is "neuron coverage" in DeepXplore analogous to?
A) Code coverage in software testing
B) Memory coverage in OS testing
C) Network coverage in distributed systems
D) Disk coverage in storage systems

**Q26.** Why does DeepXplore optimize for neuron coverage?
A) To reduce model size
B) To exercise all neurons so that a "rogue" neuron doesn't cause unexpected results
C) To speed up training
D) To reduce memory usage

**Q27.** How does DeepXplore find divergent behaviors across models?
A) By training a single model multiple times
B) By using multiple models and optimizing input to find when outputs diverge
C) By comparing weights across layers
D) By analyzing gradient norms

**Q28.** DeepXplore mutates inputs using:
A) Random sampling
B) Gradient descent
C) Genetic algorithms
D) Simulated annealing

**Q29.** Why can DeepXplore use gradient descent on inputs?
A) Because DNNs are differentiable
B) Because models are linear
C) Because inputs are sparse
D) Because GPUs accelerate it

**Q30.** What constraints does DeepXplore apply to mutated inputs?
A) Realistic constraints (e.g., lighting)
B) Random constraints
C) No constraints
D) Adversarial constraints only

**Q31.** What are DeepXplore's two main optimization objectives?
A) Maximize accuracy and minimize loss
B) Maximize differences between model outputs and maximize neuron coverage
C) Minimize input perturbation and maximize speed
D) Maximize batch size and minimize memory

**Q32.** Does DeepXplore require labeled seed inputs?
A) Yes, all seeds must be labeled
B) No, seed inputs without labels can be used
C) Only positive examples
D) Only negative examples

---

## Section 6: Version Control — Phishing Detection Example

**Q33.** What kind of model is used as the version control example in the lecture?
A) Image classifier
B) Phishing email detector
C) Recommender system
D) Speech recognition

**Q34.** Which of the following is given as a feature for phishing detection?
A) Email font size
B) Whether the sender's email differs from their normal address
C) Time taken to type the email
D) Recipient's password strength

**Q35.** Which is NOT listed as a phishing detection feature in the lecture?
A) Sender's email differs from normal address
B) Reply-to email differs from sender email
C) Email sent at non-normal hour
D) Number of attachments

**Q36.** Why does feature engineering matter so much for these models?
A) Small tweaks to features can substantially change accuracy
B) Features take up too much memory
C) Features slow down training
D) Features don't matter much

**Q37.** Why must one be careful about specific data points (not just features)?
A) Including a specific example might significantly change model accuracy
B) Specific data points consume too much memory
C) Data points slow down inference
D) Data points cannot be versioned

**Q38.** What kind of data point is given as particularly important?
A) A rare but important example (e.g., a phishing email)
B) Common spam emails
C) Marketing emails
D) Personal emails

---

## Section 7: Version Control Difficulties

**Q39.** What happens as new versions of a model evolve?
A) It becomes hard to track/predict the importance of features and datapoints
B) Models become more accurate automatically
C) Older versions are automatically archived
D) Performance always improves

**Q40.** Which of these is a challenge with old model versions?
A) No record of old model performance
B) Cannot identify which data was used to train them
C) Difficult to reproduce results
D) All of the above

**Q41.** Why is collaboration difficult with evolving models?
A) New team members don't know why a certain feature is in the model
B) Models cannot be shared
C) Code is too long
D) Frameworks are incompatible

**Q42.** Which of these is NOT cited as a version control challenge?
A) Difficulty reproducing results
B) Lack of records of old model performance
C) Difficulty knowing which data was used historically
D) Inability to deploy models

---

## Section 8: Version Control with Deep Neural Networks

**Q43.** Why do companies often have many versions of deep neural networks?
A) Because they fine-tune existing open source models like Llama or DeepSeek
B) Because models are required to be regenerated daily
C) Because GPUs change frequently
D) Because users demand new versions

**Q44.** Which open source models are mentioned as often being fine-tuned?
A) GPT-2 and BERT
B) Llama and DeepSeek
C) ResNet and VGG
D) ImageNet and CIFAR

**Q45.** What is a critical version control question for fine-tuned DNNs?
A) If there's a bug in the source model, how to update all versions efficiently?
B) How to share GPUs among versions?
C) How to compress old models?
D) How to retrain from scratch?

**Q46.** When a bug is found in one fine-tuned model, the challenge is:
A) Finding it and updating only the relevant "branch"
B) Retraining all models from scratch
C) Discarding all models
D) Replacing the source model

---

## Section 9: Edge Deployment

**Q47.** What does "edge" deployment refer to?
A) Models running on mobile devices, cameras, or sensors rather than the cloud
B) Models running on edge servers in data centers
C) Models running at the edge of model architectures
D) Models running on the boundary of training data

**Q48.** Why are some ML tasks deployed at the edge?
A) They are time-sensitive
B) They use less RAM
C) They are easier to debug
D) They have more storage

**Q49.** Which of the following is NOT given as an edge ML application?
A) Google Translate
B) Self-driving cars
C) Face recognition for security cameras
D) Cloud-hosted recommender systems

**Q50.** Object recognition for which device is given as an edge example?
A) Drones
B) Servers
C) Mainframes
D) Game consoles

---

## Section 10: Inference Variability on Edge

**Q51.** Inference accuracy on the edge varies due to:
A) Different quantization across devices
B) Different compression on photos/videos
C) Both A and B
D) Neither

**Q52.** What is quantization?
A) Compressing model weights to lower precision
B) Counting tokens
C) Splitting data into batches
D) Encrypting model parameters

**Q53.** How much can inference performance vary between high-end cloud GPU/TPU and edge GPU/TPU?
A) 2x or more
B) 5x or more
C) 10x or more
D) 100x or more

**Q54.** Beyond accuracy and performance, what other factor must edge deployment account for?
A) Power consumption
B) Disk space
C) RAM size only
D) Network throughput only

**Q55.** Which of these is a challenge in managing edge models?
A) Does my model have lower accuracy on the edge?
B) Is my model too slow on the edge?
C) Version control for many models
D) All of the above

**Q56.** Why might one need to train different models for different devices?
A) Devices have different hardware capabilities and constraints
B) Different users prefer different models
C) Frameworks differ across devices
D) Operating systems differ

---

## Section 11: Models Leaking Private Information

**Q57.** Why are models often *thought* to be privacy-preserving?
A) Because they aggregate over a lot of data
B) Because they encrypt outputs
C) Because they are hosted on secure servers
D) Because they require authentication

**Q58.** What is the reality, despite the assumption of privacy?
A) Models can leak information about individuals
B) Models always leak training data
C) Models cannot be trained on private data
D) Models do not generalize

**Q59.** Generative text models can leak information by:
A) Auto-completing prompts like "My social security number is..."
B) Refusing to respond to prompts
C) Encrypting outputs
D) Detecting adversarial inputs

**Q60.** How can recommender models leak private information?
A) With auxiliary information about a user, you can reconstruct what they bought/watched
B) By exposing model weights
C) By revealing training data directly
D) By logging user IDs

**Q61.** Classification models can leak privacy by:
A) Reconstructing whether a user was a member of a group
B) Refusing to classify private inputs
C) Returning user emails
D) Logging input data

**Q62.** Which type of model was NOT explicitly cited as leaking private information?
A) Generative text models
B) Recommender models
C) Classification models
D) Reinforcement learning models

---

## Section 12: Differential Privacy

**Q63.** Differential privacy is associated with which researcher(s)?
A) Pei et al.
B) Szegedy et al.
C) Dwork et al.
D) Sharif et al.

**Q64.** What does differential privacy guarantee?
A) An observer cannot tell if an individual's information was used in the computation
B) That all data is encrypted at rest
C) That training data is never shared
D) That models cannot be reverse-engineered

**Q65.** What is the basic intuition of differential privacy?
A) Add noise to the data or training process so no individual data point can be de-anonymized
B) Encrypt the model weights
C) Train on synthetic data only
D) Limit the size of the training set

**Q66.** What is the downside of differential privacy?
A) It reduces the accuracy of the algorithm
B) It increases training time exponentially
C) It requires custom hardware
D) It only works for image data

**Q67.** Differential privacy was originally used for:
A) Aggregating queries from a database
B) Image classification
C) Language modeling
D) Speech recognition

**Q68.** Which of the following is a system that enables differential privacy in ML?
A) PrivateKube
B) TensorFlow Privacy
C) Both A and B
D) Neither

**Q69.** PrivateKube was developed by researchers from:
A) Stanford
B) MIT
C) Columbia
D) Google

**Q70.** PrivateKube was developed by:
A) Tao et al.
B) Pei et al.
C) Dwork et al.
D) Sharif et al.

**Q71.** What is PrivateKube specifically designed for?
A) Continuously trained ML models with differential privacy
B) Adversarial testing of models
C) Versioning fine-tuned models
D) Edge deployment of models

---

## Section 13: Synthesis and Cross-Topic Questions

**Q72.** Which of these correctly pairs a problem with a proposed solution from the lecture?
A) Testing → DeepXplore (neuron coverage)
B) Privacy leaks → Differential privacy (PrivateKube, TF Privacy)
C) Both A and B
D) Neither

**Q73.** Both DeepXplore and adversarial testing aim to:
A) Find inputs that expose model failures
B) Reduce model size
C) Speed up inference
D) Compress training data

**Q74.** What is a key advantage of DeepXplore over adversarial testing?
A) DeepXplore uses realistic inputs and works without labeled seed inputs
B) DeepXplore is faster
C) DeepXplore uses smaller models
D) DeepXplore requires more labels

**Q75.** Which best summarizes the lecture's overall message?
A) ML systems face many open systems and security challenges with no standard solutions yet
B) ML systems are largely solved problems
C) ML systems are fully secure as long as data is aggregated
D) ML systems should always run in the cloud

---

## Answer Key

| Q | Ans | Q | Ans | Q | Ans | Q | Ans | Q | Ans |
|---|-----|---|-----|---|-----|---|-----|---|-----|
| 1 | D | 16 | B | 31 | B | 46 | A | 61 | A |
| 2 | B | 17 | B | 32 | B | 47 | A | 62 | D |
| 3 | A | 18 | C | 33 | B | 48 | A | 63 | C |
| 4 | C | 19 | B | 34 | B | 49 | D | 64 | A |
| 5 | B | 20 | A | 35 | D | 50 | A | 65 | A |
| 6 | B | 21 | A | 36 | A | 51 | C | 66 | A |
| 7 | D | 22 | D | 37 | A | 52 | A | 67 | A |
| 8 | A | 23 | A | 38 | A | 53 | C | 68 | C |
| 9 | A | 24 | B | 39 | A | 54 | A | 69 | C |
| 10 | B | 25 | A | 40 | D | 55 | D | 70 | A |
| 11 | B | 26 | B | 41 | A | 56 | A | 71 | A |
| 12 | A | 27 | B | 42 | D | 57 | A | 72 | C |
| 13 | A | 28 | B | 43 | A | 58 | A | 73 | A |
| 14 | A | 29 | A | 44 | B | 59 | A | 74 | A |
| 15 | A | 30 | A | 45 | A | 60 | A | 75 | A |
