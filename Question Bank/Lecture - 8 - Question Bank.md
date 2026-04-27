# Lecture 8 — Question Bank

Topics: Regex • Stream Processing (Spark Streaming) • Security Goals • Cryptography • Compliance • Security Tips

---

## Section A — Regex

**1.** What is regex primarily used for?
A. Compiling code
B. Matching and/or manipulating strings of text
C. Compressing files
D. Querying databases via SQL only

**2.** Which of the following supports regex (per the lecture)?
A. Only Python
B. Only Spark
C. Python, PySpark, SQL, Spark
D. Only SQL

**3.** In regex, what does `\w` match?
A. Whitespace only
B. Any digit
C. Any word character
D. Any non-word character

**4.** What does the `*` quantifier mean in regex?
A. Exactly one of the preceding character
B. One or more of the preceding character
C. Zero or more of the preceding character
D. Zero or one of the preceding character

**5.** What does the `+` quantifier mean in regex?
A. Zero or more of the preceding character
B. One or more of the preceding character
C. Exactly one of the preceding character
D. Match any character

**6.** Given text `fox foxtrot ox box`, what does the pattern `[^f]ox` match?
A. Only `fox`
B. `ox` preceded by an `f`
C. `ox` not preceded by `f`
D. Nothing

**7.** What does `[bf]ox` match?
A. Either `box` or `fox`
B. Only `box`
C. Only `fox`
D. Neither `box` nor `fox`

**8.** What does `[^f\W]ox` match?
A. `ox` preceded by `f` or whitespace
B. `ox` preceded by anything except `f` or a non-word character
C. Only digits before `ox`
D. Nothing in normal text

**9.** Which free online tool was recommended for debugging regex?
A. regexr.io
B. regexbuddy.com
C. regex101.com
D. regexlab.com

**10.** Why might regex be preferred over built-in Python string functions?
A. It is always faster
B. Manipulating text with built-in Python string functions can be cumbersome
C. It uses less memory
D. It is the only option in Spark

---

## Section B — Stream Processing Motivation

**11.** Stream processing is described as a hybrid between which two paradigms?
A. OLAP and OLTP
B. OLAP and ETL
C. OLTP and ETL
D. Batch and ETL

**12.** Which is NOT given as a real-time data motivation?
A. Twitter real-time search
B. Google Analytics website statistics
C. Intrusion detection systems
D. Monthly financial reports

**13.** Why is stream processing not exactly OLTP?
A. Because OLTP cannot run in real time
B. Because updates don't happen in place (e.g., on rows in a table)
C. Because OLTP only handles small data
D. Because OLTP requires Spark

**14.** Why is stream processing not exactly OLAP?
A. OLAP doesn't support SQL
B. It is real-time with granular updates
C. OLAP is too fast
D. OLAP cannot be parallelized

**15.** Why don't MapReduce or normal Spark work well for streaming?
A. They are too slow at parallelization
B. They are batch processing — must wait for entire computation to complete
C. They cannot scale
D. They are not fault tolerant

**16.** Which of these is an example of a stream processing job?
A. Uber surge price calculation
B. LinkedIn aggregating updates into one email
C. Netflix personalization
D. All of the above

**17.** What does TripAdvisor use stream processing for in the lecture's examples?
A. Hotel booking
B. Calculating earnings per day & fraud detection
C. User registration
D. Image hosting

---

## Section C — Discretized Stream Processing & Spark Streaming

**18.** What is the core idea of Discretized Stream Processing?
A. Run streaming as one giant batch job
B. Run streaming as a series of very small, deterministic batch jobs
C. Use only OLTP transactions
D. Avoid all batch processing

**19.** How are failed/slow tasks handled in discretized stream processing?
A. Restart the entire job
B. Rerun failed/slow tasks in parallel on other nodes
C. Drop them silently
D. Wait for the worker to come back

**20.** In Spark Streaming, where is state between batches kept?
A. On disk only
B. In memory in a fault-tolerant dataset (RDD/DataFrame/Dataset)
C. In a relational database
D. In Kafka

**21.** Spark Streaming batch sizes can be reduced to as low as:
A. 1 minute
B. 10 seconds
C. 1/2 second
D. 5 milliseconds

**22.** Which of these is NOT listed as a Spark Streaming alternative?
A. Apache Storm
B. Apache Flink
C. Amazon Kinesis
D. PostgreSQL

**23.** State RDDs in Spark Streaming achieve fault recovery via:
A. Full data replication
B. Remembering lineage of operations and re-computing
C. Snapshots taken every minute
D. Manual checkpointing only

**24.** Is the **state RDD** replicated for fault tolerance?
A. Yes, fully replicated
B. No — it is not replicated; it is recomputed from lineage
C. Yes, replicated to disk only
D. Yes, replicated to a remote backup

**25.** Is the **input dataset** in Spark Streaming replicated and fault-tolerant?
A. Yes — replicated and fault-tolerant
B. No, never replicated
C. Only on disk
D. Replicated only on the driver

**26.** Fault/straggler recovery in Spark Streaming happens:
A. Sequentially on a single node
B. In parallel on other nodes
C. Manually by the operator
D. Only via checkpoint files

---

## Section D — DStream Programming Model

**27.** A DStream is:
A. A SQL view
B. A series of RDDs representing a stream of data
C. A graph database
D. A persistent disk store

**28.** A DStream's API is:
A. Identical to SQL
B. Very similar to RDDs
C. Identical to Pandas
D. Specific to Hadoop only

**29.** How can DStreams be created?
A. From live streaming data only
B. By transforming other DStreams only
C. Either from live streaming data or by transforming other DStreams
D. Only from HDFS

**30.** Which is NOT listed as a DStream data source?
A. HDFS
B. Kafka
C. Twitter
D. SAP HANA

**31.** Which is a transformation listed for DStreams?
A. `map`
B. `flatMap`
C. `reduceByKey`
D. All of the above

**32.** Which of these is a window/stateful operation?
A. `filter`
B. `countByValueAndWindow`
C. `flatMap`
D. `join`

**33.** Which output operation prints to the driver's screen?
A. `saveAsHadoopFiles`
B. `print`
C. `foreach`
D. `display`

**34.** What does `foreach` do?
A. Runs an arbitrary operation on every RDD
B. Saves to disk
C. Prints to driver screen
D. Filters elements

---

## Section E — Streaming Example (Hashtag Counting)

**35.** In the lecture example, the goal is to find the top 20 hashtags in:
A. The last 1 minute, updated every 10 seconds
B. The last 10 minutes, updated every 1 second
C. The last hour, updated every minute
D. The last day, updated every hour

**36.** In `hashtags = tweets.flatMap(lambda tweet: tweet.getTags())`, what is `flatMap` doing?
A. Filtering tweets by tag
B. Extracting tags from each tweet and flattening them into one stream
C. Grouping tweets by author
D. Sorting tags alphabetically

**37.** What is the **window length** in `window(Minutes(10), Seconds(1))`?
A. 1 second
B. 10 minutes
C. 10 seconds
D. 1 minute

**38.** What is the **sliding interval** in `window(Minutes(10), Seconds(1))`?
A. 10 minutes
B. 1 second
C. 10 seconds
D. 1 minute

**39.** How does `countByValueAndWindow` differ from a naive `window().countByValue()`?
A. It is slower
B. It is incremental — adds new batch counts and subtracts old batch counts as the window slides
C. It only counts distinct values
D. It does not slide

**40.** What is the relationship between `countByValueAndWindow` and the window?
A. Adds counts from new batch entering window, subtracts counts from old batch leaving window
B. Only adds counts
C. Only subtracts counts
D. Replaces the window entirely

---

## Section F — Security Goals

**41.** What are the four security goals listed?
A. Confidentiality, Integrity, Availability, Authenticity
B. Confidentiality, Integrity, Anonymity, Authenticity
C. Confidentiality, Identity, Availability, Authentication
D. Cryptography, Integrity, Availability, Authority

**42.** Which security goal addresses "data was not tampered with"?
A. Confidentiality
B. Integrity
C. Availability
D. Authenticity

**43.** Which goal is best addressed by replication / consensus mechanisms?
A. Confidentiality
B. Integrity
C. Availability
D. Authenticity

**44.** Which goals can cryptography help with?
A. Confidentiality, Integrity, Authenticity
B. Availability only
C. Confidentiality only
D. All four equally

**45.** "Belief in the source of the data" describes which goal?
A. Confidentiality
B. Integrity
C. Availability
D. Authenticity

**46.** A denial-of-service attack threatens which goal most directly?
A. Confidentiality
B. Integrity
C. Availability
D. Authenticity

**47.** When designing for confidentiality, what must you do first?
A. Buy encryption hardware
B. Explicitly define your adversary
C. Hash all data
D. Disable HTTPS

**48.** Which is NOT listed as a possible adversary?
A. Hackers
B. Cloud provider
C. Foreign government
D. The compiler

**49.** Why is applying security at only one level of the stack insufficient?
A. Because hardware can fail
B. Because attackers can bypass the unprotected levels (e.g., phishing site over HTTPS)
C. Because encryption is too slow
D. Because compliance forbids it

**50.** Which is an example given of end-to-end security failure?
A. Uploading password to a phishing site over HTTPS
B. Disk encryption alone protecting against a thief who has both phone and PIN
C. Both A and B
D. None of the above

---

## Section G — Cryptography Basics

**51.** In symmetric encryption, the function `E(x, k) = y` produces output that:
A. Is the same as `x`
B. Appears to be totally random
C. Is the hash of `x`
D. Is `x` reversed

**52.** What is the role of the key in symmetric encryption?
A. It is public
B. It is secret — possession enables decryption
C. It is the plaintext
D. It is generated by the receiver only

**53.** How is hashing similar to encryption?
A. It is two-way
B. Like a one-way encryption (cannot be decrypted)
C. It uses asymmetric keys
D. It is only used for compression

**54.** Why do we try to avoid hash output collisions in cryptography?
A. They make hashes faster
B. Same outputs for different inputs are dangerous (in cryptography we try to avoid that)
C. They reduce storage
D. They make decryption easier

**55.** What is the most common symmetric algorithm?
A. RSA
B. AES
C. DSA
D. SHA-256

**56.** What do most modern symmetric algorithms rely on?
A. Formal proofs of hardness
B. Heuristics — no formal proof, but withstood many attacks
C. Quantum-safe theorems
D. Public/private key pairs

**57.** What is symmetric encryption typically used for?
A. Encrypting bulk data (storage, network packets)
B. Digital signatures only
C. Hashing only
D. Anonymous communication only

**58.** In asymmetric encryption, which key is public?
A. The encryption key (e.g., used by sender to encrypt)
B. The decryption key
C. Both
D. Neither

**59.** Which key in asymmetric encryption is the secret?
A. Public key
B. Private key
C. Symmetric key only
D. Hash key

**60.** Why use asymmetric encryption?
A. It is faster than symmetric
B. To share secrets (e.g., a symmetric key) over an untrusted network
C. Because it requires no keys
D. Because it doesn't need a CPU

**61.** What is the most common asymmetric algorithm mentioned?
A. AES
B. RSA
C. SHA-256
D. DES

**62.** Why is asymmetric encryption typically used to encrypt symmetric keys / small secrets?
A. Asymmetric is more theoretically sound
B. Asymmetric usually has lower performance than symmetric
C. Symmetric is illegal to use directly
D. Asymmetric is required by HIPAA

**63.** RSA's basic security idea is based on:
A. Factorization is much harder than multiplication
B. Hashing is reversible
C. Symmetric keys
D. Quantum computing

**64.** The RSA private key is based on:
A. Two very large prime numbers
B. The product of two primes
C. A symmetric key
D. A hash digest

**65.** The RSA public key is:
A. Two very large primes
B. The product of two primes
C. A symmetric key
D. A digital signature

**66.** Has the factorization problem been formally proven hard?
A. Yes, since the 1970s
B. No — it has not been proven as "hard"
C. Yes, by RSA Labs
D. Only for small primes

---

## Section H — Digital Signatures

**67.** What goals do digital signatures address?
A. Confidentiality only
B. Integrity and authenticity (not confidentiality)
C. Availability only
D. All four goals

**68.** What is the first step in creating a digital signature?
A. Encrypt the data with the public key
B. Hash the data (also called a digest)
C. Sign with the public key
D. Compress the message

**69.** Which key signs the message in a digital signature?
A. Public key
B. Private key
C. Receiver's public key
D. A symmetric key

**70.** Which key is used by the receiver to verify a digital signature?
A. The sender's private key
B. The sender's public key
C. The receiver's private key
D. A new symmetric key

**71.** Which is NOT a real-world use case for digital signatures listed?
A. This website actually belongs to google.com
B. This email was sent from columbia.edu
C. This credit card is real
D. This file is exactly 4 KB

**72.** Common digital signature algorithms include:
A. AES, DES
B. DSA, RSA signatures
C. SHA-256 only
D. MD5, SHA-1 only

**73.** A general rule about digital signature performance:
A. Always faster than symmetric encryption
B. Generally slow
C. Free in hardware
D. Faster than hashing

---

## Section I — Encryption in Practice

**74.** Which is a key real-world rule about cryptography?
A. Always invent your own algorithm
B. Never invent your own encryption algorithm or protocols
C. Always avoid existing libraries
D. Cryptography is unnecessary

**75.** Why is "even when using existing libraries" still risky?
A. Libraries are often broken
B. It is tricky not to use them incorrectly
C. They cost money
D. They are illegal

**76.** Which open-source vulnerability was given as an example?
A. Apache Log4Shell
B. OpenSSL Heartbleed
C. Shellshock
D. EternalBlue

---

## Section J — Compliance

**77.** What is data compliance?
A. The process of ensuring a dataset conforms to rules from national/international laws or trade body standards
B. Hashing all your data
C. Replicating data across regions
D. Encrypting all data with AES

**78.** Compliance is primarily concerned with:
A. Privacy (more than security)
B. Hardware reliability
C. Performance
D. Network speed

**79.** Which statement is true?
A. Being compliant means your data is fully secure
B. Being compliant does not mean your data is secure
C. Compliance and security are the same
D. Compliance replaces encryption

**80.** Which compliance regulation governs healthcare patient data?
A. HIPAA
B. GDPR
C. PCI
D. FERPA

**81.** Which regulation protects EU citizen privacy?
A. HIPAA
B. GDPR
C. PCI
D. FERPA

**82.** Which regulation protects credit card data?
A. HIPAA
B. GDPR
C. PCI
D. FERPA

**83.** Which regulation protects student information?
A. HIPAA
B. GDPR
C. PCI
D. FERPA

**84.** Which use case is NOT typically affected by compliance?
A. Personally identifiable information (PII)
B. Healthcare
C. Finance and education
D. Open-source library licensing

---

## Section K — Compliance Principles

**85.** Which is NOT a general compliance principle listed?
A. Protect PII (usually some kind of encryption)
B. Auditability
C. Maximize ad targeting
D. Right to be forgotten

**86.** The "right to be forgotten" means:
A. Data is automatically deleted after a year
B. If a user asks to delete their data, you must do so
C. You can never delete data
D. Only servers can be forgotten

**87.** Audit logs in a compliance setting:
A. Can be deleted at will
B. Cannot be deleted; must be a permanent log of operations on PII
C. Must be encrypted only
D. Are optional

**88.** What is "encryption at rest"?
A. Encrypting data while it is being transmitted
B. Encryption of sensitive data within the system (when stored)
C. Hashing data
D. Encryption of memory only

**89.** What is "encryption in transit"?
A. Encryption applied when data is being ingested or leaving the system
B. Encryption when data is at rest
C. Hashing in flight
D. Encryption of disks only

**90.** "EU PII can only be stored in EU" is an example of:
A. Right to be forgotten
B. Restrictions on where data can be stored (data regionalization)
C. Auditability
D. Access control

**91.** How do big data systems typically separate PII?
A. Mix PII with all other data
B. Store PII in dedicated systems separate from non-sensitive systems
C. Print PII to logs
D. Email PII between teams

**92.** Which group provides built-in compliance controls?
A. Major public cloud providers
B. Local hardware vendors
C. ISPs
D. Cell phone carriers

---

## Section L — Real-World Tips

**93.** Which is the recommended best 2FA mechanism?
A. SMS
B. Phone call
C. Hardware token
D. Email confirmation

**94.** Which is NOT a recommended principle for confidential data?
A. Never reuse a password for an important system
B. Use a password manager
C. Always send passwords over email
D. Always use 2FA

**95.** What is the most common attack vector mentioned?
A. Direct hacking of servers
B. Email/text (phishing) — fake emails from bosses/professors/colleagues
C. Network intrusion
D. Physical theft

**96.** A company should consider:
A. Implementing single sign-on (Auth0, Okta, OneLogin)
B. Hiring 3rd-party auditing/penetration testing
C. Both A and B
D. Avoiding both

**97.** Which is a key compliance rule?
A. Always store user passwords yourself
B. Never store user passwords or credit cards on your system; outsource (e.g., Stripe, Plaid)
C. Store credit cards in plaintext for speed
D. Email passwords to users

**98.** What is the lecture's view on lawyers and security?
A. Lawyers fully understand security
B. Lawyers don't understand security (consult them, but be aware)
C. Lawyers replace security teams
D. Lawyers should write your encryption code

**99.** Which statement reflects the relationship between compliance and security?
A. Compliance == security
B. You can be HIPAA/GDPR/PCI compliant but completely insecure
C. Security is a subset of compliance
D. Compliance ensures full security

**100.** What's a recommended approach to cryptographic protocols?
A. Design your own for stronger security
B. Never design your own cryptographic protocol
C. Hash twice for safety
D. Skip crypto if compliant

---

## Section M — Conceptual / Mixed

**101.** What is the relationship between DStream and RDD?
A. DStream is unrelated to RDD
B. DStream is a series of RDDs representing a stream
C. DStream replaces RDD in batch jobs
D. RDD is built on top of DStream

**102.** Which best describes Spark Streaming's recovery model?
A. Slow recovery via full replication
B. Fast recovery from faults without full data replication
C. Manual recovery only
D. No recovery available

**103.** Which combination is most accurate?
A. Asymmetric for bulk data, symmetric for key exchange
B. Symmetric for bulk data, asymmetric for sharing symmetric keys
C. Both symmetric and asymmetric for bulk data equally
D. Neither used for key exchange

**104.** Which is true about end-to-end security?
A. One strong layer is enough
B. Confidentiality must be applied at multiple levels (data, network, device, identity)
C. Only encryption matters
D. Only passwords matter

**105.** Why might a digital signature fail to provide confidentiality?
A. It uses hashing only and signs with the private key — it doesn't encrypt the message contents
B. It uses symmetric encryption
C. It is too slow
D. It only signs metadata

---

# Answer Key

| # | Ans | # | Ans | # | Ans | # | Ans | # | Ans |
|---|-----|---|-----|---|-----|---|-----|---|-----|
| 1 | B | 22 | D | 43 | C | 64 | A | 85 | C |
| 2 | C | 23 | B | 44 | A | 65 | B | 86 | B |
| 3 | C | 24 | B | 45 | D | 66 | B | 87 | B |
| 4 | C | 25 | A | 46 | C | 67 | B | 88 | B |
| 5 | B | 26 | B | 47 | B | 68 | B | 89 | A |
| 6 | C | 27 | B | 48 | D | 69 | B | 90 | B |
| 7 | A | 28 | B | 49 | B | 70 | B | 91 | B |
| 8 | B | 29 | C | 50 | C | 71 | D | 92 | A |
| 9 | C | 30 | D | 51 | B | 72 | B | 93 | C |
| 10 | B | 31 | D | 52 | B | 73 | B | 94 | C |
| 11 | A | 32 | B | 53 | B | 74 | B | 95 | B |
| 12 | D | 33 | B | 54 | B | 75 | B | 96 | C |
| 13 | B | 34 | A | 55 | B | 76 | B | 97 | B |
| 14 | B | 35 | B | 56 | B | 77 | A | 98 | B |
| 15 | B | 36 | B | 57 | A | 78 | A | 99 | B |
| 16 | D | 37 | B | 58 | A | 79 | B | 100 | B |
| 17 | B | 38 | B | 59 | B | 80 | A | 101 | B |
| 18 | B | 39 | B | 60 | B | 81 | B | 102 | B |
| 19 | B | 40 | A | 61 | B | 82 | C | 103 | B |
| 20 | B | 41 | A | 62 | B | 83 | D | 104 | B |
| 21 | C | 42 | B | 63 | A | 84 | D | 105 | A |
