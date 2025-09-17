Encryption secures data on the Internet (e.g., payments, emails, personal data) by transforming it into unreadable form for unauthorized users. This is achieved using **cryptographic algorithms** and **digital keys**.

The strength of encryption depends on the method and key length: modern algorithms with long keys are considered highly secure.

There are two main types:

- **Symmetric encryption**: same key for encryption and decryption. 
- **Asymmetric encryption**: key pair (public/private); widely used in digital communication today.

# Public-Key Encryption

Asymmetric encryption is secure because it relies on **hard mathematical problems**, making it resistant to simple attacks. It also solves the **key exchange problem** found in symmetric systems. Since the **public key** can be shared openly, no secret exchange is required. Additionally, asymmetric methods enable **authentication** through digital signatures.

# Data Encryption Standard (DES)

- **Type**: Symmetric-key block cipher
- **Key length**: 64 bits (56 effective bits, 8 checksum)
- **Operation**: Encrypts 64-bit plaintext blocks into 64-bit ciphertext blocks using substitution and permutation.
- **Weakness**: 56-bit effective key is insecure by modern standards.
### Triple DES (3DES)

- Extension of DES using **three keys** (Encrypt → Decrypt → Encrypt).
- Provides stronger security but still limited by 56-bit blocks.
- Superseded by **AES**.

# Advanced Encryption Standard (AES)

- **Type**: Symmetric-key block cipher
- **Key lengths**: 128, 192, or 256 bits
- **Advantages**: Faster and more efficient than DES/3DES, can process multiple blocks at once.
- **Applications**:  
   WLAN IEEE 802.11i, IPsec, SSH, VoIP, PGP, OpenSSL

# Cipher Modes

Block ciphers encrypt **fixed-size blocks** (64 or 128 bits). **Cipher modes** define how these blocks are combined for longer messages.

|            Mode             |                                         Description                                          |
| :-------------------------: | :------------------------------------------------------------------------------------------: |
| ECB (Electronic Code Book)  |      Not recommended. Fails to hide data patterns; vulnerable to statistical analysis.       |
| CBC (Cipher Block Chaining) |      Default AES mode. Used in disk encryption, emails, TLS/SSL, TrueCrypt, VeraCrypt.       |
|    CFB (Cipher Feedback)    |        Suited for **real-time data streams** (e.g., network comms, PKCS, BitLocker).         |
|    OFB (Output Feedback)    |         Also for real-time streams; better key stream generation. Used in PKCS, SSH.         |
|        CTR (Counter)        |                  Encrypts real-time data streams; used in IPsec, BitLocker.                  |
|  GCM (Galois/Counter Mode)  | Provides **confidentiality + integrity**. Used in VPNs, wireless security, modern protocols. |

