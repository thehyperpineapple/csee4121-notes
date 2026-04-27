
## 1. Recap

### Cryptographic Primitives

| Operation | What It Does | Keys Used | Provides | Example |
|---|---|---|---|---|
| Symmetric Encrypt/Decrypt | Transform plaintext ↔ ciphertext, reversible with same key | Shared secret key | Confidentiality | AES |
| Asymmetric Encrypt | Encrypt with recipient's public key; only their private key decrypts | Recipient's public key | Confidentiality | RSA |
| Asymmetric Decrypt | Recover plaintext using private key | Recipient's private key | Confidentiality | RSA |
| Hash | Map arbitrary input to fixed-size digest; one-way | None (keyless) | Integrity | SHA |
| Digital Sign | Hash message, encrypt hash with sender's private key | Sender's private key | Integrity + Authenticity | RSA / ECDSA |
| Verify Signature | Decrypt signature with sender's public key; compare to hash | Sender's public key | Integrity + Authenticity | RSA / ECDSA |
| Key Exchange | Derive shared secret over insecure channel | Each party's ephemeral keys | Confidentiality (enables symmetric) | Diffie-Hellman |

- **Symmetric** = fast, used for bulk data
- **Asymmetric** = slower, used for key exchange & signatures
- **Hashing** = one-way, used for integrity checks

### Previously Covered
- **Four security goals**: Confidentiality, Integrity, Availability, Authenticity (CIA + A)
- Symmetric (AES), Asymmetric (RSA), Digital signatures, Diffie-Hellman, Certificates/PKI
- Compliance: HIPAA, GDPR, PCI, FERPA

### Why Data Scientists Care
- Build/query systems storing PII
- Asked to "anonymize and share" datasets
- Use APIs requiring authentication tokens
- Average breach cost: ~$4.5M (IBM)
- GDPR fines: up to 4% of global annual revenue
- **Ignorance is not a defense**

---

## 2. Common Attacks on Data Systems

### Categories
- **Injection attacks** — trick system into executing attacker-controlled code
- **Credential-based attacks** — compromise user identities
- **Denial of service (DoS)** — destroy availability
- **Supply chain attacks** — compromise a dependency
- **Social engineering** — manipulate humans (phishing)
- **Software vulnerabilities** — Heartbleed, Meltdown, Spectre, buffer overflow
- **Intentional backdoors** — crypto algorithms with builtin vulnerabilities

### SQL Injection
Concatenating user input into SQL queries enables attackers to inject syntax.

```python
# Vulnerable
query = "SELECT * FROM users WHERE username = '" + user_input + "' AND password = '" + pass_input + "'"

# Attacker enters: username = ' OR 1=1 --
# Resulting: SELECT * FROM users WHERE username = '' OR 1=1 --' AND password = ''
# OR 1=1 → always true; -- → comments out password check
```

**Beyond login bypass**, attackers can:
- Exfiltrate: `' UNION SELECT credit_card, ssn FROM customers --`
- Modify: `'; UPDATE accounts SET balance = 1000000 WHERE user = 'attacker' --`
- Delete: `'; DROP TABLE users --` (Bobby Tables)
- Execute system commands

**The Fix — Parameterized Queries:**
```python
query = "SELECT * FROM users WHERE username = %s AND password = %s"
db.execute(query, (user_input, pass_input))
```
- DB treats parameters as **data**, never as code
- Additional defenses: input validation, least privilege DB accounts, WAFs

### Other Injection Types
- **NoSQL injection** — e.g., MongoDB `{"username": {"$gt": ""}}` bypass
- **Command injection** — `os.system("ping " + user_input)` → `google.com; cat /etc/passwd`
- **Log injection** — fake log entries to cover tracks
- **Cross-Site Scripting (XSS)**:
  - **Stored XSS**: `<script>steal_cookies()</script>` in a comment
  - **Reflected XSS**: malicious script in URL query parameter
  - Impact: steal session cookies, hijack accounts, redirect to phishing

**Common thread**: untrusted input mixed with code → injection possible.

### Credential-Based Attacks
- **Credential stuffing** — leaked username/password pairs reused elsewhere
- **Brute force** — try every possible password (mitigate: rate limiting, lockouts, CAPTCHAs)
- **Dictionary attacks** — try common passwords (mitigate: complexity requirements)

### Phishing
- Trick user into giving credentials voluntarily
- Fake login page captures credentials
- **Spear phishing** — targeted at specific individual using personal details
- Most major breaches start with phishing
- Defense: **MFA**, awareness training, email filtering

### Denial of Service (DoS)
Targets the **A** in CIA.
- **Volumetric** — flood network bandwidth (UDP flood, DNS amplification)
- **Protocol** — exploit protocol weakness (SYN flood exhausts TCP connection table)
- **Application-layer** — expensive requests (complex SQL, large uploads)
- **DNS KeyTrap**

### DDoS (Distributed)
- Traffic from thousands/millions of sources via **botnets** (often IoT)
- **Mirai botnet (2016)**: ~600K IoT devices, took down DNS provider Dyn → Twitter, Netflix, Reddit, GitHub outages
- Mitigations: CDNs (Cloudflare, Akamai), rate limiting, traffic scrubbing, auto-scaling

---

## 3. Authentication & Authorization

### AuthN vs AuthZ
| Authentication (AuthN) | Authorization (AuthZ) |
|---|---|
| "Who are you?" | "What are you allowed to do?" |
| Verifies identity | Verifies permissions |
| Login, fingerprint, SSH key | Can user read this table? |
| Happens **first** | Happens **after** authentication |

- **Independent**: can be authenticated but unauthorized (intern in prod)
- Can be authorized without authentication (public S3 bucket)
- Many breaches exploit this gap

### Factors of Authentication
- **Something you know** — password, PIN (weakest)
- **Something you have** — phone (SMS/TOTP), YubiKey
- **Something you are** — fingerprint, face, iris (can't be changed if compromised)
- **MFA** combines two+; blocks ~99% of automated credential attacks (Microsoft)

### Passwords
- Most common AND weakest auth mechanism
- Problems: weak choices, reuse, phishing, keylogging

### Storing Passwords
**NEVER store plaintext.** Use **bcrypt, scrypt, or argon2** (intentionally slow).

```python
import bcrypt
hashed = bcrypt.hashpw(password.encode(), bcrypt.gensalt(rounds=12))
```
- **Salt** prevents rainbow table attacks (each user gets unique salt)
- bcrypt cost 12 ≈ 250ms per hash
- Legitimate user: negligible delay; attacker: centuries for billions of tries

### Session Management

**Session Cookies**
- Server creates session ID, stores in session store
- State on server; easy to revoke
- Requires server-side storage

**Token-Based (JWTs)**
- Server generates **signed** token with user info
- Client sends in `Authorization` header
- **Stateless** — great for distributed systems
- Harder to revoke (valid until expiration)

### JWT Structure
Three parts (`header.payload.signature`), base64-encoded, dot-separated:
```
Header:    {"alg": "HS256", "typ": "JWT"}
Payload:   {"sub": "alice", "role": "analyst", "exp": 1714000000}
Signature: HMAC(base64(header) + "." + base64(payload), secret_key)
```
- Payload is **readable** (just base64), but **not modifiable** without secret key
- JWTs are **signed, not encrypted** by default

### API Keys
- Long random string: `sk-abc123xyz789...`
- Sent in headers: `Authorization: Bearer sk-...`
- Best practices:
  - **Never commit to Git**
  - Use env vars / secrets managers
  - Rotate periodically
  - Different keys for dev/prod
  - Least-privilege scoping
- Bots continuously scan GitHub for exposed keys

### Single Sign-On (SSO)
- One auth event grants access to multiple apps
- Central **Identity Provider (IdP)** — apps trust IdP
- Protocols: **SAML** (XML, enterprise), **OIDC** (built on OAuth 2.0, modern)
- Benefits: centralized control, easy revocation, better UX, MFA enforcement

### OAuth 2.0 — Delegated Authorization
**Problem**: third-party app needs Google Drive access without your password.

**Flow:**
1. User clicks "Sign in with Google"
2. App redirects user to Google (Authorization Server)
3. User authenticates and consents
4. Google redirects back with **authorization code**
5. App exchanges code for **access token** (server-to-server)
6. App calls Google API on user's behalf

- User's password never touches the app
- Token is **scoped, time-limited, revocable**

### MFA in Practice
- **SMS codes** — weakest, vulnerable to SIM swapping
- **Authenticator apps (TOTP)** — shared seed generates 6-digit code every 30s
- **Hardware keys (YubiKey)** — strongest, phishing-resistant; Google: 0 successful phishing attacks on 85K+ employees
- **Passkeys** (FIDO2/WebAuthn) — public-key crypto on device, replaces passwords

### Service-to-Service Auth
- **Service accounts** — dedicated non-human accounts (GCP JSON key file)
- **IAM roles** (cloud) — permissions tied to compute resources (EC2, Lambda) — no keys to leak
- **Mutual TLS (mTLS)** — both client and server present certs (zero-trust, Istio, Linkerd)
- Credential leaks common in scripts, repos, plaintext configs

---

## 4. Equifax Breach (2017) Case Study

### Overview
- ~147 million Americans affected
- Stolen: SSNs, DOBs, addresses, driver's license numbers, credit card numbers

### Timeline
- **March 7, 2017**: Apache Struts vulnerability + patch released
- **March 9**: Equifax security team notified
- **March 15**: Scan run, fails to identify vulnerable system
- **May 13**: Attackers exploit unpatched system → initial access
- **May 13 – July 29 (76 days)**: Lateral movement, data exfiltration
- **July 29**: Suspicious traffic noticed
- **September 7**: Public disclosure

### What Went Wrong
1. **Unpatched software** — patch available 2+ months
2. **Failed vulnerability scan** — didn't cover all internet-facing systems
3. **Expired SSL certificate on inspection tool** — for 19 months → encrypted exfil undetected for 76 days
4. **Flat network** — no segmentation, free lateral movement
5. **Unencrypted PII** at rest
6. **Plaintext credentials** in config files

### Lessons
- Patch promptly (especially internet-facing)
- Encrypt at rest
- Network segmentation
- Defense in depth
- Monitor and alert (expired certs, anomalous traffic)
- Secrets managers (Vault, AWS Secrets Manager)

---

## 5. Access Control Models in Databases

### Principle of Least Privilege
- Every user/process gets **minimum permissions** needed
- Limits breach blast radius
- Limits accidental damage
- Required by HIPAA, GDPR
- **Hard in practice**: convenience pressure, permission creep

### Role-Based Access Control (RBAC)
Most common model. Three concepts:
- **Users** — individual accounts (alice, spark_etl_job)
- **Roles** — named permission sets (analyst, engineer, admin)
- **Permissions** — actions on resources (SELECT on table X)

Users → assigned to Roles → granted Permissions.

```sql
CREATE ROLE analyst;
CREATE ROLE engineer;
CREATE ROLE admin;

GRANT SELECT ON customers, orders, products TO analyst;
GRANT SELECT, INSERT, UPDATE ON customers, orders TO engineer;
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO admin;

GRANT analyst TO alice;
GRANT engineer TO bob;
REVOKE engineer FROM bob;
```

### Attribute-Based Access Control (ABAC)
Permissions based on attributes of user, resource, environment:
- **User**: department, title, clearance, location
- **Resource**: classification (public/confidential), owner
- **Environment**: time of day, IP, device

Example: "Finance can access confidential financial data, only from corporate network during business hours."

| RBAC | ABAC |
|---|---|
| Simpler, easier to audit | More expressive |
| Covers most use cases | For complex, context-dependent policies |

Many systems use hybrid.

### Views as Access Control
Expose only safe columns:
```sql
CREATE VIEW customer_analytics AS
SELECT customer_id, age_group, region, purchase_count, avg_order_value
FROM customers;
-- name, email, SSN, address EXCLUDED

GRANT SELECT ON customer_analytics TO analyst;
REVOKE SELECT ON customers FROM analyst;
```

### Row-Level Security (RLS)
Different users see different **rows** of same table.
```sql
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;
CREATE POLICY regional_access ON orders
FOR SELECT
USING (region = current_setting('app.user_region'));
```
Use cases: multi-tenant SaaS, regional access, hierarchical org access.

### Column-Level Encryption
Encrypt sensitive columns; even users with full access see ciphertext.
- **Pros**: defense in depth
- **Cons**: hard to query/index, complex key management, performance overhead
- Used in AWS RDS, Azure SQL, PostgreSQL with `pgcrypto`

### Dynamic Data Masking
DB returns masked values to unauthorized users.
```sql
ALTER TABLE customers ALTER COLUMN email
  ADD MASKED WITH (FUNCTION = 'email()');           -- aXXX@XXXX.com
ALTER TABLE customers ALTER COLUMN ssn
  ADD MASKED WITH (FUNCTION = 'partial(0, "XXX-XX-", 4)'); -- XXX-XX-1234
GRANT UNMASK TO admin_role;
```

### Comparison

| Mechanism | Controls | Granularity | Complexity | Strength |
|---|---|---|---|---|
| Views | Column visibility | Column | Low | Moderate |
| Row-Level Security | Per-user row visibility | Row | Medium | Strong |
| Column Encryption | Reading sensitive values | Column | High (key mgmt) | Strong |
| Dynamic Masking | Displayed values | Column | Low | Weak-Moderate |

### Human Side of Access Control
Common failures:
- Overly permissive defaults
- Shared service accounts (no audit trail)
- Permission creep
- Shadow IT (export to personal laptops, Sheets)

Best practices:
- Quarterly access reviews
- Automated provisioning tied to HR
- Least privilege as cultural norm
- Audit logging for sensitive queries

> **Tradeoff**: Excessive security hampers velocity.

---

## 6. Data Anonymization & De-identification

### Why Anonymize?
- Research datasets, third-party sharing, internal access without raw PII
- GDPR data minimization
- Open data (government, public health)
- **Goal**: preserve analytical utility while removing identifiability — much harder than it sounds

### Direct vs Quasi-Identifiers

| Direct Identifiers | Quasi-Identifiers |
|---|---|
| Uniquely identify alone | Identify only in **combination** |
| Name, SSN, email, phone, license # | ZIP, DOB, gender, occupation |
| Obvious — must be removed | Less obvious — together form a fingerprint |

**This is why anonymization is hard.**

### Sweeney's Result (1997)
- **87% of Americans** uniquely identifiable by **{5-digit ZIP, DOB, gender}**
- Cross-referenced anonymized MA medical records with public voter rolls
- Identified MA Governor's medical records (sent them to his office)
- **Removing direct identifiers is not enough** — quasi-identifiers enable **linkage attacks**

### Anonymization Techniques
- **Suppression** — drop the field entirely
- **Generalization** — reduce precision (Age 29 → 25-30; ZIP 10027 → 100**)
- **Pseudonymization** — consistent fake IDs (Alice → User_7392)
  - Reversible with mapping table; GDPR still considers this personal data
- **Masking** — replace with realistic fakes (123-45-6789 → XXX-XX-6789)
- **Perturbation** — add random noise ($95,000 → $93,200; preserves aggregates)

### Netflix Prize (2006)
- $1M competition, dataset: 100M ratings from ~500K users
- "Anonymization": removed usernames, random IDs, slight perturbation of ratings/dates
- **Attack** by Narayanan & Shmatikov (UT Austin):
  - Cross-reference with public IMDb ratings
  - Match on movies + approximate dates
  - **6 movies** rated → uniquely identify 99% of users
  - **2 movies + dates** → identify 68% of users
- **Consequences**:
  - Lawsuit under Video Privacy Protection Act (closeted lesbian mother plaintiff)
  - Netflix Prize 2 canceled
  - FTC investigation
- **Lessons**:
  - "Anonymized" ≠ anonymous
  - **Sparsity kills anonymity** — in high-dimensional data, everyone is unique
  - Release is **irreversible**

> Anonymization techniques don't provide **formal privacy guarantees**. We need a framework that mathematically bounds what an attacker can learn.

---

## 7. Privacy-Preserving Data Analysis

### k-Anonymity (Sweeney, 2002)
**Definition**: every record is indistinguishable from at least **(k-1) other records** on quasi-identifier fields.
- If k=5, any combination of (ZIP, age, gender) appears ≥5 times
- Achieved via generalization and suppression

**Before** (k violation):
```
ZIP    Age  Gender  Diagnosis
10027  29   M       Heart Disease
10027  31   M       Flu
10028  45   F       Diabetes
10028  47   F       Heart Disease
10029  29   M       Cancer
```

**After generalization (k=3)**:
```
ZIP    Age     Gender  Diagnosis
100**  25-35   M       Heart Disease
100**  25-35   M       Flu
100**  25-35   M       Cancer
100**  40-50   F       Diabetes
100**  40-50   F       Heart Disease
```

#### Limitations
- **Homogeneity attack**: all k records have **same sensitive value** → attacker learns it
  - E.g., 5 people in (100**, 25-35, M) all have HIV → attacker knows
- **Background knowledge attack**: side info narrows the set
  - "My neighbor is 28, M, in 100**, doesn't have flu" → eliminates possibilities

> k-anonymity provides **no guarantee** about what attacker learns about sensitive attributes.

### l-Diversity
**Definition**: each equivalence class has ≥ **l distinct sensitive values**.
- Group must contain at least l different diagnoses
- Addresses homogeneity attack

**Limitation**: ignores distribution
- {Cancer, Cancer, Cancer, Cancer, Flu, Diabetes} → l=3 ✓ but 67% confidence in Cancer

### t-Closeness
**Definition**: distribution of sensitive values in each group must be within distance **t** of overall distribution.
- Prevents both homogeneity and skew attacks

### The Cat-and-Mouse Problem
- k-anonymity → broken by homogeneity
- l-diversity → broken by distribution skew
- t-closeness → stronger but heavy generalization, reduced utility

> All are **syntactic guarantees** (structural properties), not bounds on what attackers learn.
> We want a **semantic guarantee** — a mathematical bound regardless of auxiliary information.
> (Leads into **Differential Privacy** — next.)

---

## Key Takeaways
1. Untrusted input + code = injection — always parameterize.
2. AuthN ≠ AuthZ; both must be enforced and the gap is a common breach vector.
3. Passwords are the weakest link — use slow hashing + salt + MFA.
4. Equifax shows defense-in-depth matters: patching, encryption, segmentation, monitoring, credential hygiene.
5. RBAC is the workhorse; ABAC, RLS, views, masking, encryption layer on top.
6. Anonymization via removing identifiers fails — quasi-identifiers enable linkage attacks (Sweeney; Netflix).
7. k-anonymity / l-diversity / t-closeness are syntactic patches; we need semantic guarantees (differential privacy).
