# Lecture 8 — Regex, Stream Processing, Security & Compliance

## Topics Covered
- Regex
- Stream processing (Spark Streaming)
- Security and privacy (CIA + Authenticity)
- Cryptography basics (symmetric, asymmetric, digital signatures)
- Compliance (types, principles, vs. security)
- Real-world security tips

---

## 1. Regular Expressions (Regex)

- Scripting language for **matching/manipulating strings of text**.
- Built-in Python string functions can be cumbersome — regex is more powerful.
- Supported in **Python, PySpark, SQL, Spark**, etc.

### Common Operations
- Find substrings (e.g., `"helloworld"` → `"Hello World"`).
- Find/exclude patterns (e.g., match `"fox"` but not `"foxtrot"`).
- Strip special characters / punctuation.

### Matching Examples (text: `fox foxtrot ox box`)

| Pattern | Meaning |
|---|---|
| `fox` | matches `fox`, and `fox` inside `foxtrot` |
| `ox` | matches `ox` everywhere it appears |
| `fox\w` | `\w` = any word character (so requires a char after `fox`) |
| `fox\w*` | `*` = zero or more of preceding char |
| `fox\w+` | `+` = one or more of preceding char |
| `[^f]ox` | `ox` not preceded by `f` |
| `[^f\W]ox` | `ox` not preceded by `f` or non-word char |
| `[bf]ox` | `b` or `f` followed by `ox` |

- Debug tool: **regex101.com**.
- Hint: useful in HW combined with a UDF.

---

## 2. Stream Processing

### Motivation: Hybrid Between OLAP and OLTP
- Need **real-time views** of large data:
  - Twitter real-time search, Google Analytics, intrusion detection.
- Process large data with **low latency (seconds)** and **high throughput**.
- **Not OLTP** — updates aren't in-place on rows.
- **Not OLAP** — it's real-time with granular updates.
- A relatively new class of data system.

### Why MapReduce / Normal Spark Don't Work
- They are **batch processing** — must wait for full computation to complete.
- Not designed for long-running, real-time stream processing.

### Examples of Stream Processing Jobs
- **Uber** — surge price calculation.
- **LinkedIn** — aggregating updates into one email.
- **Netflix** — user behavior → personalization.
- **TripAdvisor** — daily earnings & fraud detection.

### Discretized Stream Processing
- Run streaming computation as a **series of very small, deterministic batch jobs**.
- Inherits MapReduce-style fault recovery:
  - Jobs split into deterministic tasks.
  - Failed/slow tasks rerun in parallel on other nodes.
- Same recovery techniques applied at lower time scales.
- Transformations not lost (or duplicated) if a worker dies.

### Spark Streaming
- State between batches kept **in memory** as a **fault-tolerant dataset** (RDD/DataFrame/Dataset).
- Batch sizes can be as low as **0.5s** → ~1s latency.
- Combines streaming + batch workloads.
- **Alternatives**: Apache Storm, Apache Flink, Amazon Kinesis, Google Dataflow.

### Fault Recovery
- State stored as RDD:
  - Deterministically re-computable.
  - Remembers **lineage** of operations.
- Input dataset is **replicated and fault-tolerant**.
- State RDD **not replicated** — recomputed via lineage.
- Fault/straggler recovery happens **in parallel** on other nodes → fast recovery without full data replication.

### Programming Model — DStream
- A **DStream (Discretized Stream)** = a series of RDDs representing a stream.
- API very similar to RDDs.
- Created from:
  - Live streaming data, OR
  - Transforming other DStreams.

### DStream Data Sources
- HDFS, Kafka, Flume, Twitter, etc.

### Transformations
- **Filter/aggregate**: `map`, `flatMap`, `filter`, `count`, `reduce`, `groupByKey`, `reduceByKey`, `sortByKey`, `join`.
- **Window/stateful**: `window`, `countByWindow`, `reduceByWindow`, `countByValueAndWindow`, `reduceByKeyAndWindow`, `updateStateByKey`.

### Output Operations
- `saveAsHadoopFiles` — write to disk.
- `print` — prints on the driver's screen.
- `foreach` — arbitrary op on every RDD.

### Example: Top 20 Hashtags in Last 10 Minutes (updated every 1 sec)

```python
tweets = ssc.twitterStream(username, password)
hashtags = tweets.flatMap(lambda tweet: tweet.getTags())

# Approach 1: window then countByValue
tagCounts = hashtags.window(Minutes(10), Seconds(1)).countByValue()

# Approach 2: incremental — countByValueAndWindow
tagCounts = hashtags.countByValueAndWindow(Minutes(10), Seconds(1))
```

- **Window length** = how far back to aggregate (e.g., 10 min).
- **Sliding interval** = how often to update (e.g., 1 sec).
- `countByValueAndWindow` is **incremental**:
  - Adds counts from the **new batch** entering the window.
  - Subtracts counts from the **old batch** leaving the window.

---

## 3. Security Goals — CIA + Authenticity

### The Four Goals
1. **Confidentiality** — secret data not leaked/stolen.
2. **Integrity** — data/system not tampered with.
3. **Availability** — data/system available when needed (e.g., resists DoS).
4. **Authenticity** — belief in the source of the data (sender is who they claim).

### How They're Addressed
- **Cryptography** → handles Confidentiality, Integrity, Authenticity.
- **Replication / consensus** → handles Availability.

### Confidentiality: Define Your Adversary
- Hackers, partner/family, competitors, anyone online, cloud provider, ISP, your government, foreign government, etc.
- **You must explicitly define the adversary** to design defenses.

### End-to-End Security
- Confidentiality applies at every level of the stack:
  - **Data** (DB / cloud FS encryption).
  - **Network** (HTTPS).
  - **Device** (full-disk encryption on laptop/phone).
  - **User identity** (passwords, MFA).
- Securing one level ≠ secure system. Examples of breakdowns:
  - Uploading password to a phishing site over HTTPS.
  - Thief with phone + PIN gets all disk-encrypted data.

---

## 4. Cryptography

### Crypto Core
- **Secret key establishment** between parties (Alice ↔ Bob).
- **Secure communication** with confidentiality + integrity.
- Crypto can also do: **digital signatures**, **anonymous communication**, **cryptocurrencies/blockchain**.

### Symmetric Encryption
- `E(x, k) = y` — output looks random (`x` plaintext, `k` key).
- `D(y, k) = x` — decryption is usually harder to compute than encryption.
- **Key is secret** — possession = ability to decrypt.
- **Hash function** `h(x)`:
  - Like one-way encryption (no decryption).
  - Same output possible for different inputs if output space is small (Bloom filter false positives) — in crypto we avoid this.

### Symmetric in the Real World
- The **workhorse** of encryption — bulk data (storage, network packets).
- Most common: **AES**.
  - Single secret key for encrypt/decrypt.
  - Good performance; modern CPUs accelerate AES.
- Most modern algorithms are **heuristic-based** (no formal proof AES is hard, but it has held up).

### Asymmetric Encryption
- Different keys for encryption and decryption.
- **Public key** — published, used to encrypt.
- **Private key** — secret, used to decrypt.
- Useful for sharing a secret (e.g., a symmetric key) over an untrusted network.
- Typical pattern:
  1. Bob encrypts a symmetric key with Alice's public key.
  2. Alice decrypts with her private key.
  3. Both then use the symmetric key for bulk data.

### Asymmetric in the Real World
- Used mostly to **encrypt symmetric keys / small shared secrets** — slower than symmetric.
- Most common: **RSA**.
- More **theoretically sound** than AES — based on **multiplication being easy, factorization being hard**.
  - Private key = two large primes.
  - Public key = their product.
- Still: factorization not formally proven hard.

### Digital Signatures
- Used for **integrity + authenticity** (not confidentiality).
- Goal: anyone can verify sender holds the private key.
- Steps:
  1. **Hash** the data (digest).
  2. **Sign** the hash with private key.
  3. **Append** signature to message.
  4. Receiver **verifies** with sender's public key.
- Real-world uses: domain ownership (google.com), email auth (columbia.edu), credit card validity.
- Algorithms: **DSA, RSA signatures** — based on asymmetric encryption, generally slow.

### Encryption Rules of Thumb
- **Never invent your own encryption algorithm or protocol.**
- Always use existing libraries / protocols.
- Even existing libraries are tricky to use correctly.
- Even widely used open source has had flaws (e.g., **OpenSSL Heartbleed**).

---

## 5. Data Compliance and Privacy

### Security ≠ Compliance
- **Compliance** = ensuring data conforms to laws / standards / trade body rules.
- Compliance sometimes includes security, but not always.
- Compliance is primarily a **legal framework** for handling data — usually about **privacy**, not security.
- **Being compliant ≠ being secure.**

### Types of Data Compliance
- **HIPAA** — healthcare patient data privacy.
- **GDPR** — privacy of EU citizens.
- **PCI** — credit card data.
- **FERPA** — student data privacy (grades, personal info).

### When Compliance Applies
- Handling **PII** (names, addresses, emails, phones, SSNs, etc.).
- Healthcare, finance, education, defense.

### General Principles
- **Protect PII** — usually encryption (level varies).
- **Auditability** — permanent log of any operation on PII; cannot delete log.
- **Access control** — restrict who can access PII.
- **Restrictions on sharing PII** — secure transfer, possibly legal agreements.
- **Right to be forgotten** — user can demand deletion.
- **Storage location restrictions** — e.g., EU PII must stay in EU.

### How Big Data Systems Implement Compliance
- Designate **specific systems** for PII, separated from non-sensitive systems.
- **Access control** with revocation.
- **Audit logging**.
- **Encryption at rest** + **encryption in transit**.
- **Data regionalization** — EU data in EU datacenters.
- Major **cloud providers** offer built-in compliance controls.

---

## 6. Real-World Security & Compliance Tips

### Keeping Data Confidential
- **Always use 2FA** — hardware token best, text/call OK.
- **Use a password manager** — Chrome's built-in is decent.
- **Never reuse passwords** for important systems (Columbia, email, bank, server).
- Companies → use **single sign-on** (Auth0, Okta, OneLogin).
- Companies → hire **3rd-party auditing / pen testing** — they always find something.
- **Email/text is the #1 attack vector** — beware suspicious links / fake emails from "boss"/"professor"/"colleague".
- **Never design your own crypto protocol.**
- **Compliance ≠ security** — you can be HIPAA/GDPR/PCI compliant but still insecure.

### Staying Compliant
- **Never store user passwords or credit cards** — outsource to Stripe, Plaid, etc.
- Understand which regulation applies (PCI / HIPAA / GDPR most common).
- Maintain a **clear guidelines document** (where PII is stored, which apps can access it).
- **Consult a lawyer** — but lawyers don't understand security.
- Get **third-party auditing/certification** when possible.

---

## Key Takeaways
- **Regex** is a powerful, cross-language tool for string manipulation.
- **Stream processing** = many small batch jobs (DStream = sequence of RDDs); fault recovery via lineage and parallel re-computation.
- **Four security goals**: Confidentiality, Integrity, Availability, Authenticity — crypto handles 3, replication handles availability.
- **Symmetric (AES)** = bulk data; **Asymmetric (RSA)** = key exchange / signatures.
- **Digital signatures** = hash + private key signing → public key verification.
- **Compliance ≠ Security** — they overlap but solve different problems.
- **Never roll your own crypto.** **Always use 2FA.** **Never reuse passwords.**
