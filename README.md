# cryptography
1. What is Cryptography? — Goals & Basic Terms
Cryptography is the science and practice of designing techniques for secure communication in the
presence of adversaries. It provides tools and methods to achieve:
• 
• 
• 
• 
Confidentiality: Only authorized parties can read the message (encryption).
Integrity: Detect unauthorized modification of data (hashes, MACs, signatures).
Authentication: Verify identity of message originator (keys, certificates, signatures).
Non‑repudiation: Sender cannot deny having sent a message (digital signatures).
Key terms: plaintext, ciphertext, encryption, decryption, key, nonce, IV (initialization vector), block cipher,
stream cipher, hash, MAC, digital signature, certificate, PKI.
2. Quick History & Classical Ciphers (intuition)
Why history matters: many modern ideas grew from classical ciphers; they help build intuition.
Caesar Cipher (Shift cipher)
• 
• 
• 
Replace each letter by the one a fixed number of positions down the alphabet.
Example: key = 3, HELLO → KHOOR.
Weakness: only 25 possible keys (easy brute force).
Substitution and Transposition ciphers
• 
• 
Substitution: replace letters via a permutation (monoalphabetic) — vulnerable to frequency
analysis.
Transposition: rearrange letters (permutation) — keeps letter frequencies.
Vigenère Cipher (polyalphabetic)
• 
Uses a repeating keyword to shift letters — better than simple shift but still breakable with Kasiski
analysis.
One‑Time Pad (OTP)
• 
• 
Use random key as long as message, used only once. If truly random and secret, provides perfect
secrecy.
Impractical due to key distribution/storage, but conceptually important.
1
3. Modern Cryptography: Overview
Modern cryptography uses mathematical primitives and rigorous proofs. Major building blocks:
1. 
2. 
3. 
4. 
5. 
6. 
Symmetric cryptography (secret‑key): same key for encryption & decryption. Fast. Used for data
at-rest and bulk data encryption.
Asymmetric cryptography (public‑key): key pairs (public/private). Used for key exchange,
signatures, and cases where key distribution is hard.
Hash functions: one‑way functions producing fixed-size digests. Useful for integrity checks and as
building blocks.
Message Authentication Codes (MACs): symmetric integrity + authentication (e.g., HMAC).
Digital Signatures: asymmetric authentication and non‑repudiation.
Protocols & primitives: TLS, SSH, PKI — combine primitives into secure systems.
4. Symmetric Cryptography
Use cases: disk encryption, TLS symmetric session after key exchange, VPNs, encrypted backups.
Block ciphers vs Stream ciphers
• 
• 
Block cipher: encrypts fixed-size blocks (e.g., AES: 128-bit block). Operate with modes to handle
messages > block size.
Stream cipher: produce keystream XORed with plaintext (e.g., RC4 historically, or modern
ChaCha20).
Notable algorithms
• 
• 
DES (Data Encryption Standard): 56‑bit key (now insecure due to small key size). 3DES = triple
application of DES (slower).
AES (Advanced Encryption Standard, Rijndael): block cipher with 128‑bit block size and key sizes
128/192/256 bits. Widely used today.
Modes of Operation (how to use block ciphers safely)
• 
• 
• 
• 
ECB (Electronic Codebook): insecure — identical plaintext blocks → identical ciphertext blocks (do
not use).
CBC (Cipher Block Chaining): needs random IV; provides confidentiality but not integrity.
CTR (Counter): turns block cipher into stream cipher using counters; parallelizable.
GCM (Galois/Counter Mode): provides authenticated encryption (confidentiality + integrity) —
recommended when available.
IV/Nonce rules: IVs must be random or unique depending on mode. Reusing an IV with the same key can
break security.
2
Padding
• 
When message is not a multiple of blocksize, padding schemes like PKCS#7 are used. Padding
oracles (incorrect handling) can be exploited.
5. Asymmetric Cryptography (Public‑Key)
High level: Each user has a key pair — a public key (shared) and a private key (kept secret). Encryption with
recipient's public key → only recipient can decrypt with private key. Signatures created with private key →
anyone can verify with public key.
RSA (Rivest–Shamir–Adleman)
• 
• 
• 
Security based on difficulty of factoring large integers.
Key generation (simplified): choose large primes p,q → n=pq; choose e (public exponent), compute
d (private exponent) such that e·d ≡ 1 (mod φ(n)).
Practical notes: Use padding like OAEP for encryption and PSS for signatures. Small e or poor
padding choices lead to vulnerabilities.
ECC (Elliptic Curve Cryptography)
• 
• 
• 
Uses points on elliptic curves; provides similar security with smaller keys vs RSA.
Common curves: secp256r1, Curve25519 (X25519 for key exchange), Ed25519 (signatures).
Many modern protocols prefer ECC for efficiency and forward secrecy.
Asymmetric uses: key exchange (Diffie‑Hellman, ECDH), digital signatures (RSA, DSA, ECDSA, EdDSA),
certificate-based authentication.
6. Hash Functions and MACs
Cryptographic hash functions
Properties:
• 
• 
• 
Preimage resistance: hard to find input from hash.
Second preimage resistance: given x, hard to find y ≠ x with same hash.
Collision resistance: hard to find any two different inputs with same hash.
Common hash families: MD5 (broken), SHA‑1 (deprecated), SHA‑2 (SHA‑256, SHA‑512), SHA‑3.
Use cases: integrity checks, commitment schemes, building blocks for HMAC, KDFs, digital signatures
(hash-then-sign).
3
HMAC (Hash‑based Message Authentication Code)
• 
Provides integrity & authentication using a shared secret + hash function (e.g., HMAC‑SHA256).
7. Digital Signatures
Purpose: verify origin and integrity; non‑repudiation.
How it works (concept):
• 
Sender hashes the message, then signs the hash with their private key. Receiver verifies signature
using sender's public key and recomputed hash.
Algorithms: RSA signatures (with PSS), DSA, ECDSA, EdDSA (Ed25519).
Real‑world uses: code signing, document signing, TLS certificate signatures, blockchain transaction
signatures.
8. Public Key Infrastructure (PKI) & Certificates
X.509 Certificate: contains subject info, public key, validity period, issuer, signature from CA.
CA (Certificate Authority): trusted entity that signs certificates. Browser/OS maintain root CA store.
Certificate chain: leaf cert → intermediate CA(s) → root CA.
Revocation: CRL (Certificate Revocation List) or OCSP (Online Certificate Status Protocol). Revocation is
tricky in practice.
TLS handshake (high level):
• 
• 
Client hello → server hello + certificate → key exchange (e.g., ECDHE) → both derive symmetric
session keys → application data encrypted with symmetric cipher (e.g., AES‑GCM).
Modern TLS versions (1.2, 1.3) recommend ephemeral key exchange (forward secrecy).
9. Password Storage Best Practices
Never store plaintext or raw hash without salt.
4
Good approach:
• 
• 
• 
Generate a unique salt per password.
Use a slow, memory‑hard KDF / password hash: bcrypt, scrypt, Argon2 (Argon2id recommended).
Optionally use a server‑side pepper (secret global key stored separately) for extra protection.
Why slow KDFs? They slow down brute force attacks by increasing cost per trial.
10. Common Attacks & Defenses
Attacks
• 
• 
• 
• 
• 
• 
• 
Brute force / dictionary attacks (defend with slow hashes, rate limiting).
Cryptanalysis: mathematical attacks specific to algorithm (e.g., linear/differential cryptanalysis on
block ciphers).
Padding oracle attacks (CBC + bad padding error messages).
Man‑in‑the‑middle (MitM): intercept and modify traffic if authentication missing.
Replay attacks: reusing valid messages (defend with nonces / timestamps).
Side‑channel attacks: timing, power analysis — leak key information from implementation.
Downgrade attacks: force use of weak ciphers/versions.
Defenses
• 
• 
• 
• 
Use authenticated encryption (AEAD like AES‑GCM or ChaCha20‑Poly1305).
Validate inputs; do constant‑time comparisons for secrets.
Use TLS properly (latest versions + strong ciphersuites + forward secrecy).
Rotate keys, minimize key lifetime, and use HSMs / secure key vaults for private keys.
