**Key exchange methods** are used to securely exchange cryptographic keys between two parties. This is a critical part of many cryptographic protocols since the secrecy of the keys ensures the security of the encryption.

These methods typically allow two parties to agree on a **shared secret key** over an insecure channel, using mathematical operations or cryptographic functions.

# Diffie-Hellman (DH)

- Allows two parties to agree on a shared secret without prior communication or shared private info
- Used in **TLS** to establish secure communication channels
- **Limitations** 
  - Vulnerable to MITM attacks    
  - Requires relatively high CPU power (less ideal for low-power devices or fast key generation needs)

# Rivest–Shamir–Adleman (RSA)

- Uses properties of large prime numbers for secure key exchange 
- Security relies on difficulty of factoring large numbers 
- Widely used in:  
   - Encrypting/signing messages (confidentiality & authentication)
   - Protecting data in **SSL/TLS**
   - Generating/verifying digital signatures
   - Authenticating users/devices (e.g., **Kerberos PKINIT**)
   - Protecting sensitive information (personal data, confidential docs)
   - **Limitation**: computationally intensive

## Elliptic Curve Diffie-Hellman (ECDH)

- Variant of Diffie-Hellman using elliptic curve cryptography
- More **efficient** and **secure** than traditional DH 
- Commonly used for:
  - Establishing secure channels (TLS)
  - Forward secrecy
  - Authentication in **IKE** for VPNs    
  
## Elliptic Curve Digital Signature Algorithm (ECDSA)

- Uses elliptic curve cryptography for **digital signatures**
- Provides **authentication** in key exchanges
- More secure and efficient than traditional RSA/DH signatures

# Comparison of Algorithms

|     Algorithm      | Acronym |                   Security Notes                   |
| :----------------: | :-----: | :------------------------------------------------: |
|   Diffie-Hellman   |   DH    |    Relatively secure, computationally efficient    |
|        RSA         |   RSA   | Widely used, secure, but computationally intensive |
| Elliptic Curve DH  |  ECDH   |          Enhanced security and efficiency          |
| Elliptic Curve DSA |  ECDSA  |   Strong digital signatures with high efficiency   |

---

# Internet Key Exchange (IKE)

**IKE** is a protocol for establishing and maintaining secure sessions (commonly in **VPNs**).

- Combines **Diffie-Hellman** and other cryptographic techniques
- Exchanges keys and negotiates security parameters
- Used alongside **RSA** (digital signatures) and **AES** (encryption)

# Modes of Operation

## **Main Mode**

- Default and more secure
- Three phases with incremental key/parameter exchange
- Provides stronger security but slower performance

## **Aggressive Mode**

- Two phases, fewer message exchanges
- Faster but weaker security (no identity protection)

