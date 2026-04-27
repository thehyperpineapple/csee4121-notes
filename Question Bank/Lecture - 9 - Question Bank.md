# Lecture 9 — Security and Privacy: Question Bank

## Section A — Cryptographic Primitives & Goals

**1.** Which cryptographic primitive provides confidentiality using the SAME key for encryption and decryption?
A) RSA
B) SHA-256
C) AES (symmetric encryption)
D) Diffie-Hellman

**2.** Which primitive maps an arbitrary input to a fixed-size digest and is one-way (not reversible)?
A) Symmetric encryption
B) Hashing
C) Asymmetric encryption
D) Digital signature

**3.** A digital signature provides which two security properties?
A) Confidentiality + Availability
B) Integrity + Authenticity
C) Confidentiality + Integrity
D) Availability + Authenticity

**4.** Diffie-Hellman is primarily used for:
A) Hashing passwords
B) Signing JWTs
C) Two parties deriving a shared secret over an insecure channel
D) Encrypting bulk data

**5.** Which keys are used to verify a digital signature?
A) Sender's private key
B) Recipient's public key
C) Sender's public key
D) Recipient's private key

**6.** Symmetric encryption is generally preferred over asymmetric encryption for bulk data because:
A) It is more secure
B) It is faster
C) It does not require keys
D) It produces shorter ciphertext

**7.** Which is NOT one of the four security goals discussed?
A) Confidentiality
B) Integrity
C) Anonymity
D) Authenticity

**8.** GDPR fines can reach up to what percentage of global annual revenue?
A) 1%
B) 2%
C) 4%
D) 10%

---

## Section B — Attacks on Data Systems

**9.** Which attack tricks a system into executing attacker-controlled code by mixing untrusted input with code?
A) Brute force
B) Injection attack
C) Volumetric DoS
D) Phishing

**10.** What is the primary fix for SQL injection?
A) Removing semicolons from input
B) Using parameterized queries / prepared statements
C) Storing passwords in plaintext
D) Disabling SELECT statements

**11.** When an attacker enters `' OR 1=1 --` as a username, why does the login bypass succeed?
A) The hash collides
B) `OR 1=1` is always true and `--` comments out the password check
C) The DB rejects the query and defaults to allowing access
D) The user table is publicly readable

**12.** Which payload represents the "Bobby Tables" classic?
A) `' UNION SELECT credit_card FROM customers --`
B) `'; DROP TABLE users --`
C) `' OR 1=1 --`
D) `<script>alert(1)</script>`

**13.** In MongoDB, an attacker sending `{"username": {"$gt": ""}}` is performing:
A) Buffer overflow
B) NoSQL injection
C) XSS
D) DDoS

**14.** Stored XSS differs from Reflected XSS in that:
A) Stored XSS is server-side only
B) Stored XSS persists in the application (e.g., in a comment) while Reflected XSS is sent via URL
C) Reflected XSS only affects mobile devices
D) Stored XSS cannot steal cookies

**15.** Credential stuffing exploits which user behavior?
A) Choosing strong passwords
B) Reusing passwords across services
C) Using MFA
D) Changing passwords frequently

**16.** What mitigation is most effective against brute force password attempts?
A) Disabling logging
B) Rate limiting, account lockouts, CAPTCHAs
C) Allowing unlimited password attempts
D) Storing passwords in plaintext

**17.** A spear phishing attack is best described as:
A) A volumetric DoS attack
B) Phishing targeted at a specific individual using personal details
C) A worm that spreads via email
D) An exploit of TCP SYN handshake

**18.** A SYN flood attack exploits:
A) The TCP handshake by sending SYN packets but never completing
B) DNS amplification
C) HTTP form submissions
D) Encrypted traffic inspection

**19.** Application-layer DoS attacks are harder to defend against because:
A) They only target network bandwidth
B) They look like legitimate traffic
C) They require physical access
D) They cannot be rate-limited

**20.** The Mirai botnet (2016) primarily compromised what kind of devices?
A) Mainframes
B) Cloud VMs
C) IoT devices (cameras, routers)
D) Smartphones only

**21.** Which is NOT a typical DDoS mitigation?
A) CDNs absorbing traffic at the edge
B) Rate limiting per IP/region
C) Disabling all logs
D) Traffic scrubbing services

**22.** Which is a "supply chain attack"?
A) Phishing an HR employee
B) Compromising a third-party dependency to infiltrate upstream systems
C) Flooding a server with traffic
D) Cracking a password hash

---

## Section C — Authentication & Authorization

**23.** Authentication answers which question?
A) "What can you do?"
B) "Who are you?"
C) "Where are you?"
D) "Why are you here?"

**24.** Authorization answers which question?
A) "What are you allowed to do?"
B) "Who are you?"
C) "When did you log in?"
D) "How fast is your connection?"

**25.** A public S3 bucket that anyone can read is an example of:
A) Authentication without authorization
B) Authorization without authentication
C) MFA bypass
D) Encryption at rest

**26.** Which is the WEAKEST authentication factor category?
A) Something you know
B) Something you have
C) Something you are
D) All are equally strong

**27.** According to Microsoft, MFA blocks approximately what percentage of automated credential attacks?
A) 50%
B) 75%
C) 90%
D) 99%

**28.** Why is salting passwords important?
A) To make them shorter
B) So identical passwords don't produce identical hashes (defeats rainbow tables)
C) To make hashes reversible
D) To allow passwords to be stored in plaintext

**29.** Which password hashing algorithm is NOT recommended for password storage?
A) bcrypt
B) scrypt
C) argon2
D) MD5

**30.** bcrypt at cost 12 takes about ~250ms per hash. Why is this intentional?
A) To slow legitimate logins
B) To make brute force attacks computationally expensive
C) To reduce memory usage
D) To improve UX

**31.** Compared to session cookies, JWTs are:
A) Always encrypted
B) Stateless and self-contained — no server-side session store needed
C) Easier to revoke before expiration
D) Stored only on the server

**32.** A JWT is composed of which three parts?
A) header.body.footer
B) header.payload.signature
C) cipher.key.iv
D) salt.hash.password

**33.** By default, JWTs are:
A) Encrypted, not signed
B) Signed, not encrypted
C) Both encrypted and signed
D) Neither — they are plaintext

**34.** Which is a best practice for API keys?
A) Commit them to Git so the team can find them
B) Use them in URLs as query parameters
C) Store in environment variables or secrets managers, rotate periodically
D) Share one key across all environments

**35.** Which protocol is the modern, OAuth 2.0-based SSO standard?
A) SAML
B) Kerberos
C) OIDC
D) LDAP

**36.** In OAuth 2.0, the user's password:
A) Is sent to the third-party application
B) Never touches the third-party application
C) Is hashed and stored by the app
D) Is replaced by a static API key

**37.** In the OAuth 2.0 flow, what does the application exchange for an access token?
A) The user's password
B) The user's email
C) An authorization code
D) A session cookie

**38.** Which MFA factor is MOST resistant to phishing?
A) SMS codes
B) Authenticator apps (TOTP)
C) Hardware security keys (YubiKey)
D) Security questions

**39.** Why are SMS-based MFA codes considered the weakest form of MFA?
A) They are slow
B) They are vulnerable to SIM-swapping attacks
C) They use weak hashing
D) They require internet

**40.** Passkeys are based on which technology?
A) AES symmetric encryption
B) FIDO2 / WebAuthn (public-key crypto on device)
C) SAML XML tokens
D) Plain TOTP

**41.** Which is an advantage of cloud IAM roles for service-to-service authentication?
A) Hardcoded keys are stored in repositories
B) No API keys to manage or leak — compute resources inherit permissions
C) Roles bypass authentication
D) Roles are infinitely scoped

**42.** Mutual TLS (mTLS) means:
A) Only the server presents a certificate
B) Only the client presents a certificate
C) Both client and server present certificates
D) No certificates are used

---

## Section D — Equifax Case Study

**43.** What software vulnerability did the Equifax 2017 attackers exploit?
A) OpenSSL Heartbleed
B) Apache Struts
C) Log4Shell
D) Spectre

**44.** Approximately how many Americans had data stolen in the Equifax breach?
A) 1 million
B) 47 million
C) 147 million
D) 800 million

**45.** Why did Equifax fail to detect data exfiltration for 76 days?
A) Attackers used quantum encryption
B) An expired SSL certificate on the inspection tool prevented decryption of monitored traffic for 19 months
C) The data was too small to notice
D) They had no logs at all

**46.** Which Equifax failure relates to "no segmentation — once inside, attackers moved freely"?
A) Unencrypted PII
B) Plaintext credentials
C) Flat network architecture
D) Failed vulnerability scan

**47.** Which is NOT a lesson from the Equifax breach?
A) Patch promptly, especially internet-facing systems
B) Use secrets managers
C) Disable network monitoring
D) Encrypt data at rest

---

## Section E — Access Control Models

**48.** The Principle of Least Privilege states:
A) Give all users admin access to avoid friction
B) Every user/process should have the minimum permissions needed
C) Share service accounts to simplify auditing
D) Disable access reviews

**49.** In RBAC, permissions are granted to:
A) Individual users only
B) Roles, which users are assigned to
C) IP addresses
D) Departments only

**50.** Which SQL command revokes a role from a user?
A) `DROP ROLE bob`
B) `REVOKE engineer FROM bob`
C) `DELETE FROM roles WHERE user='bob'`
D) `GRANT NONE TO bob`

**51.** ABAC differs from RBAC primarily because:
A) ABAC ignores user identity
B) ABAC bases permissions on attributes of user, resource, and environment
C) ABAC cannot be combined with RBAC
D) ABAC is simpler than RBAC

**52.** Using a SQL VIEW that excludes PII columns and granting access to the view (not the table) is an example of:
A) Row-Level Security
B) Column-level access control via views
C) Dynamic data masking
D) Column encryption

**53.** Row-Level Security (RLS) means:
A) Different users see different rows of the same table
B) All rows are encrypted
C) Only admins can read rows
D) Rows are stored in different databases

**54.** Which is a challenge of column-level encryption?
A) It is easier to query and index encrypted columns
B) Key management is complex; encrypted columns are hard to query/index
C) It requires no performance overhead
D) It only works on integer columns

**55.** Dynamic data masking means:
A) Data is permanently replaced by masked values
B) The DB returns masked values to unauthorized users and real values to authorized users
C) The DB encrypts all columns automatically
D) Masking is performed on the client side only

**56.** Which mechanism has the HIGHEST complexity due to key management?
A) Views
B) Row-Level Security
C) Column Encryption
D) Dynamic Masking

**57.** Which is the WEAKEST in terms of security strength?
A) Views
B) RLS
C) Column Encryption
D) Dynamic Masking

**58.** "Permission creep" refers to:
A) Permissions being denied incorrectly
B) Users accumulating permissions over time without revocation
C) Permissions expiring automatically
D) Roles being deleted

**59.** Which is NOT a best practice for access control?
A) Quarterly access reviews
B) Audit logging on sensitive queries
C) Sharing service accounts across teams
D) Automated provisioning tied to HR systems

---

## Section F — Anonymization & De-identification

**60.** Direct identifiers (e.g., SSN, name, email):
A) Cannot be combined to identify someone
B) Uniquely identify a person on their own
C) Are always safe to publish
D) Are the same as quasi-identifiers

**61.** Quasi-identifiers (e.g., ZIP, DOB, gender):
A) Cannot be used to identify anyone
B) Can identify individuals when combined
C) Are encrypted by default
D) Are the same as direct identifiers

**62.** Latanya Sweeney showed that approximately what percentage of Americans can be uniquely identified by {ZIP, DOB, gender}?
A) 25%
B) 50%
C) 87%
D) 100%

**63.** Which technique replaces a value with a consistent fake ID (e.g., Alice → User_7392)?
A) Suppression
B) Generalization
C) Pseudonymization
D) Perturbation

**64.** Reducing precision (e.g., Age 29 → 25-30, ZIP 10027 → 100**) is called:
A) Suppression
B) Generalization
C) Pseudonymization
D) Encryption

**65.** Adding random noise to numerical values (e.g., $95,000 → $93,200) is called:
A) Suppression
B) Generalization
C) Perturbation
D) Masking

**66.** Under GDPR, pseudonymized data is:
A) Not personal data
B) Still considered personal data
C) Exempt from regulation
D) Required to be deleted

**67.** In the Netflix Prize attack, researchers de-anonymized users by cross-referencing Netflix ratings with what?
A) US census data
B) Public IMDb ratings
C) Voter rolls
D) Twitter accounts

**68.** According to the Netflix Prize attack, how many movie ratings were enough to uniquely identify 99% of users?
A) 2
B) 6
C) 50
D) 100

**69.** A key lesson from the Netflix Prize is:
A) Anonymization is always sufficient
B) Sparsity kills anonymity — in high-dimensional data, everyone is unique
C) Removing usernames is enough
D) Perturbation always defeats linkage attacks

---

## Section G — Privacy-Preserving Data Analysis

**70.** k-anonymity guarantees:
A) Each record is indistinguishable from at least (k-1) others on quasi-identifiers
B) Each sensitive value appears at most k times
C) The dataset has at most k columns
D) Each row is encrypted with k keys

**71.** A homogeneity attack on k-anonymity occurs when:
A) The dataset has only one quasi-identifier
B) All k records in a group share the SAME sensitive value, so the attacker learns it
C) Every group has different sensitive values
D) k is set to 1

**72.** l-diversity addresses which weakness of k-anonymity?
A) Background knowledge attacks
B) Homogeneity attacks (lack of diverse sensitive values)
C) Encryption overhead
D) Dataset sparsity

**73.** A weakness of l-diversity is:
A) It requires encryption
B) It does not consider the DISTRIBUTION of sensitive values (skewed distributions still leak information)
C) It cannot be applied to medical data
D) It always preserves utility perfectly

**74.** t-closeness requires that:
A) The distribution of sensitive values within each group is within distance t of the overall distribution
B) Every group has exactly t records
C) Every record is encrypted with t keys
D) t different identifiers are removed

**75.** k-anonymity, l-diversity, and t-closeness are all described as:
A) Semantic privacy guarantees
B) Syntactic guarantees — structural properties, not bounds on what an attacker learns
C) Cryptographic primitives
D) Cloud IAM models

---

## Answers

| # | Ans | # | Ans | # | Ans | # | Ans | # | Ans |
|---|-----|---|-----|---|-----|---|-----|---|-----|
| 1 | C | 16 | B | 31 | B | 46 | C | 61 | B |
| 2 | B | 17 | B | 32 | B | 47 | C | 62 | C |
| 3 | B | 18 | A | 33 | B | 48 | B | 63 | C |
| 4 | C | 19 | B | 34 | C | 49 | B | 64 | B |
| 5 | C | 20 | C | 35 | C | 50 | B | 65 | C |
| 6 | B | 21 | C | 36 | B | 51 | B | 66 | B |
| 7 | C | 22 | B | 37 | C | 52 | B | 67 | B |
| 8 | C | 23 | B | 38 | C | 53 | A | 68 | B |
| 9 | B | 24 | A | 39 | B | 54 | B | 69 | B |
| 10 | B | 25 | B | 40 | B | 55 | B | 70 | A |
| 11 | B | 26 | A | 41 | B | 56 | C | 71 | B |
| 12 | B | 27 | D | 42 | C | 57 | D | 72 | B |
| 13 | B | 28 | B | 43 | B | 58 | B | 73 | B |
| 14 | B | 29 | D | 44 | C | 59 | C | 74 | A |
| 15 | B | 30 | B | 45 | B | 60 | B | 75 | B |
