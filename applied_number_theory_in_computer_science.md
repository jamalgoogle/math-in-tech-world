# Applied Number Theory in Computer Science

Number theory—often considered one of the purest branches of mathematics—provides the essential foundation for modern cybersecurity, data integrity, probabilistic computing, and digital communications. This document details four central domains where elementary and advanced number-theoretic principles are applied in computer systems.

---

## 1. Public-Key Cryptography (Asymmetric Encryption)

Unlike symmetric encryption, which relies on a shared secret key, asymmetric cryptography uses key pairs (public and private keys) derived from trapdoor one-way functions: mathematical operations that are easy to compute in one direction but computationally infeasible to invert without special information (the "trapdoor").

### 1.1 The RSA Cryptosystem
RSA security relies on the integer factorization problem—specifically, the difficulty of factoring the product of two large prime numbers.

* **Key Generation:**
  1. Select two distinct large prime numbers $p$ and $q$.
  2. Compute $n = p \cdot q$. The integer $n$ serves as the modulus for both keys.
  3. Calculate Euler's totient function:
     $$\phi(n) = (p - 1)(q - 1)$$
  4. Choose an integer $e$ such that $1 < e < \phi(n)$ and $\gcd(e, \phi(n)) = 1$. (Commonly, $e = 65537$).
  5. Compute the modular multiplicative inverse $d$ using the Extended Euclidean Algorithm:
     $$e \cdot d \equiv 1 \pmod{\phi(n)}$$
  6. **Public Key:** $(e, n)$; **Private Key:** $(d, n)$.

* **Encryption and Decryption:**
  * **Encryption:** Converts a plaintext message $m \in \{0, \dots, n-1\}$ to ciphertext $c$:
    $$c \equiv m^e \pmod n$$
  * **Decryption:** Recovers message $m$ using the private exponent $d$:
    $$m \equiv c^d \pmod n$$
  * **Correctness Proof:** By Euler's Generalization of Fermat's Little Theorem ($m^{\phi(n)} \equiv 1 \pmod n$ for $\gcd(m, n) = 1$), $c^d \equiv m^{ed} \equiv m^{1 + k\phi(n)} \equiv m \cdot (m^{\phi(n)})^k \equiv m \pmod n$.

* **Real-World Applications:** RSA is fundamental to key exchange protocols in HTTPS (TLS/SSL), SSH session setup, and secure online banking transactions.

### 1.2 Elliptic Curve Cryptography (ECC)
ECC offers equivalent security to RSA with significantly smaller key sizes (e.g., a 256-bit ECC key provides security comparable to a 3072-bit RSA key), reducing computational overhead and bandwidth.

* **Algebraic Structure:** An elliptic curve over a finite field $\mathbb{F}_p$ (where $p > 3$ is a prime) consists of points $(x, y) \in \mathbb{F}_p \times \mathbb{F}_p$ satisfying the Weierstrass equation:
  $$y^2 \equiv x^3 + ax + b \pmod p$$
  along with a designated point at infinity $\mathcal{O}$.

* **Elliptic Curve Discrete Logarithm Problem (ECDLP):**
  Given a base point $P$ on the curve and a scalar product point $Q = k \cdot P$ (computed via repeated geometric point addition), it is computationally intractable to determine the integer $k$ when $p$ is sufficiently large.

* **Key Protocols:**
  * **Elliptic Curve Diffie-Hellman (ECDH):** Enables two parties to establish a shared secret over an insecure channel.
  * **Elliptic Curve Digital Signature Algorithm (ECDSA):** Powers authentication protocols in mobile devices, Bitcoin, and Ethereum.

---

## 2. Hash Functions, Digital Signatures, and Blockchain

Number theory provides the guarantees behind data integrity, non-repudiation, and distributed consensus mechanisms.

### 2.1 Cryptographic Hash Functions & Modular Reduction
Cryptographic hash functions (such as SHA-256) map arbitrary-length bitstrings to fixed-length values ($n$ bits).

* **Properties Derived from Modular Arithmetic:**
  * **Pre-image Resistance:** Given $h$, it is computationally infeasible to find $m$ such that $\text{Hash}(m) = h$.
  * **Collision Resistance:** It is hard to find two distinct inputs $m_1 \neq m_2$ such that $\text{Hash}(m_1) = \text{Hash}(m_2)$.
* Compression functions within hash architectures (like Merkle-Damgård) utilize bitwise rotation, modular addition ($\pmod{2^{32}}$ or $\pmod{2^{64}}$), and nonlinear Boolean operations to mix bit positions uniformly.

### 2.2 Digital Signature Schemes
A digital signature provides authenticity, non-repudiation, and integrity by combining hash functions with asymmetric key operations.

* **Signing:** A message $M$ is hashed to $h = \text{Hash}(M)$, then signed with private key $d$:
  $$S \equiv h^d \pmod n \quad (\text{RSA Signature})$$
* **Verification:** The receiver verifies $S$ using public key $e$:
  $$h' \equiv S^e \pmod n$$
  If $h' = \text{Hash}(M)$, the signature is valid.

### 2.3 Blockchain and Cryptographic Proofs
* **Proof-of-Work (PoW):** Miners iterate over a non-negative integer parameter (nonce) to solve a modular inequality:
  $$\text{SHA-256}(\text{Block Header} \parallel \text{nonce}) < \text{Target}$$
* **Merkle Trees:** Binary trees where leaf nodes store data hashes, and parent nodes store hashes of concatenated children. Efficient proof of membership ($O(\log N)$) utilizes cryptographic hash properties.
* **Zero-Knowledge Proofs (zk-SNARKs/zk-STARKs):** Allow one party to prove knowledge of a secret satisfying a polynomial relation over finite fields $\mathbb{F}_p$ without disclosing the secret itself.

---

## 3. Pseudorandom Number Generation (PRNG)

Computer hardware relies on algorithmic Pseudorandom Number Generators (PRNGs) for simulations, randomized algorithms, and cryptographic key generation.

### 3.1 Linear Congruential Generators (LCG)
LCGs generate a sequence of pseudorandom numbers using a linear recurrence relation modulo $m$:

$$X_{n+1} = (a \cdot X_n + c) \pmod m$$

where:
* $m > 0$ is the modulus,
* $a$ ($0 < a < m$) is the multiplier,
* $c$ ($0 \le c < m$) is the increment,
* $X_0$ ($0 \le X_0 < m$) is the initial seed.

* **Hull-Dobell Theorem:** An LCG has a maximum full period $m$ for all seed values if and only if:
  1. $\gcd(c, m) = 1$,
  2. $a - 1$ is divisible by all prime factors of $m$,
  3. $a - 1$ is divisible by $4$ if $m$ is divisible by $4$.

### 3.2 Cryptographically Secure PRNGs (CSPRNG)
Standard LCGs are linearly predictable and unsuitable for security. CSPRNGs use intractable number-theoretic problems to guarantee that future bits cannot be predicted from past output.

* **Blum Blum Shub (BBS):**
  $$x_{n+1} = x_n^2 \pmod M$$
  where $M = p \cdot q$ is the product of two large prime numbers both congruent to $3 \pmod 4$ (Blum integers). Determining whether a output bit is predictable is as hard as solving the Quadratic Residuosity Problem modulo $M$.

---

## 4. Error-Detecting and Error-Correcting Codes

Transmission over noisy channels or storage on physical media requires mathematical mechanisms to detect and correct bit corruptions.

### 4.1 Checksums and Cyclic Redundancy Checks (CRC)
CRCs apply modular polynomial division over the finite field $GF(2)$.

* **Modulo 2 Division:** Arithmetic operations are performed without carries (addition and subtraction are equivalent to bitwise XOR):
  $$D(x) \cdot x^r = Q(x) \cdot G(x) + R(x)$$
  where $D(x)$ is the data polynomial, $G(x)$ is a fixed generator polynomial of degree $r$, and $R(x)$ is the remainder transmitted alongside the data as the CRC checksum.
* **Luhn Algorithm:** Uses modular arithmetic ($\pmod{10}$) as an error-detection check digit algorithm for credit card numbers and identification codes to detect single-digit errors and adjacent transpositions.

### 4.2 Hamming Codes and Parity Checks
* **Parity Bit:** Modulo 2 sum of data bits $\sum_{i} b_i \pmod 2$. Detects odd numbers of single-bit errors.
* **Hamming(7,4) Code:** Encodes $4$ data bits into $7$ bits by introducing $3$ parity check bits. Uses linear parity-check matrices $H$ over $\mathbb{Z}_2$:
  $$\vec{s} = H \cdot \vec{r}^T \pmod 2$$
  where non-zero syndrome vector $\vec{s}$ directly identifies the bit position of a corrupted single bit.

### 4.3 QR Codes & Error Correction (Reed-Solomon Codes)
QR codes utilize non-binary Reed-Solomon error correction defined over $GF(2^8)$:
* Data is mapped to coefficients of polynomials in $GF(2^8)[x]$.
* Generator polynomials guarantee that up to $30\%$ of a QR code image can be damaged or obscured while still enabling complete, loss-free data restoration.

---

## Summary Matrix

| Domain / Concept | Primary Number-Theoretic Basis | Core Cryptographic / Computing Usage |
| :--- | :--- | :--- |
| **RSA Encryption** | Prime Factorization, Euler's Totient $\phi(n)$, Extended Euclidean Algorithm | Digital Certificates, TLS/HTTPS, Key Exchange |
| **Elliptic Curve Cryptography** | Discrete Logarithms over $E(\mathbb{F}_p)$ | ECDSA, Bitcoin/Ethereum keys, Mobile Device Security |
| **Cryptographic Hash Functions** | Modular Arithmetic ($\pmod{2^k}$), Modular Exponentiation | Data Integrity, Nonce Mining in Proof-of-Work, Digital Signatures |
| **Blum Blum Shub (CSPRNG)** | Quadratic Residuosity Problem modulo Blum Integer $M=pq$ | Secure Key Generation, Cryptographic Nonces |
| **Cyclic Redundancy Check (CRC)** | Polynomial Division over $GF(2)$ | Network Packet Corruption Checks (Ethernet, ZIP archives) |
| **Reed-Solomon Codes** | Field Arithmetic in $GF(2^8)$, Modular Polynomial Roots | Storage Media (CD/DVD), QR Code Recovery, Satellite Comms |