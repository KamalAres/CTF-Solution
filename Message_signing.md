
**📌 Detailed Summary – “Signed Messages” (LoveNote) Room**

---

### 🔎 Room Overview
- **Challenge Theme:** Valentine’s‑themed app **LoveNote** that lets users send digitally signed messages.  
- **Goal:** Forge a **valid RSA‑PSS signature** for the user **`admin`** and retrieve the flag.  

### 🔐 Core Vulnerability
- **Deterministic RSA key generation** based on a predictable seed:  

  ```python
  seed_str = f"{username}_lovenote_2026_valentine"
  p = nextprime(SHA256(seed_str))
  q = nextprime(SHA256(seed_str + "pki"))
  ```
- No randomness → **private key reproducible** for any known username.  
- Knowing the username (`admin`) lets an attacker rebuild the exact RSA private key on a local machine.

### 🧠 Attack Strategy
| Step | What to Do | Key Detail |
|------|------------|------------|
| **1** | Identify deterministic key generation | The seed is a simple string; primes are derived with `nextprime()` after hashing. |
| **2** | Re‑generate the admin key | Use `username = "admin"` → compute the seed, hash, convert to integer, apply `nextprime()` for *p* and *q*, then construct the RSA key (`n = p·q`, `e = 65537`). |
| **3** | Handle RSA‑PSS salt correctly | RSA‑PSS verification fails if the salt length isn’t calculated as:<br>`emLen = ceil((modBits‑1)/8)`<br>`maxSalt = emLen - hashLen - 2`<br>Use this `maxSalt` when creating the signer: `pss.new(key, salt_bytes=maxSalt)`. |
| **4** | Forge the signature | Sign the target message with the reconstructed private key, output the signature in hex, and submit it as the `admin` user. |
| **5** | Capture the flag | Successful verification on the web page returns the flag. |

### 📐 Technical Deep‑Dive
- **Why it’s broken:** RSA security hinges on *random, unpredictable* primes. Deriving primes from SHA‑256 of a known string eliminates secrecy, effectively leaking the private key.  
- **RSA‑PSS nuance:** The padding scheme isn’t just a “black box”; the maximum allowed salt size depends on the modulus size and hash length. Mis‑calculating this leads to verification errors even with a correct private key.  

### 🛠 Exploit Skeleton (Python‑style)

```python
import hashlib
from Crypto.Util.number import next_prime, bytes_to_long
from Crypto.PublicKey import RSA
from Crypto.Signature import pss
from Crypto.Hash import SHA256

def gen_key(username):
    seed = f"{username}_lovenote_2026_valentine".encode()
    p = next_prime(bytes_to_long(hashlib.sha256(seed).digest()))
    q = next_prime(bytes_to_long(hashlib.sha256(seed + b"pki").digest()))
    n = p * q
    e = 65537
    d = pow(e, -1, (p-1)*(q-1))
    return RSA.construct((n, e, d, p, q))

key = gen_key("admin")
msg = b"TARGET_MESSAGE"
h = SHA256.new(msg)

# RSA‑PSS max‑salt calculation
modBits = key.size_in_bits()
emLen   = (modBits - 1 + 7) // 8
maxSalt = emLen - h.digest_size - 2

signer = pss.new(key, salt_bytes=maxSalt)
sig = signer.sign(h)
print(sig.hex())
```

### 🎓 Key Learning Points
1. **Never use deterministic seed‑based RSA key generation.** Randomness is non‑negotiable.  
2. **Predictable seeds = private‑key leakage.** An attacker can fully reconstruct keys for any known identifier.  
3. **RSA‑PSS padding must be implemented exactly** (correct salt length, proper `emLen` calc).  
4. **Implementation mistakes outweigh strong mathematics.** Proper cryptographic libraries should be used instead of custom code.  

### 🧩 Real‑World Relevance
- Custom crypto in IoT/firmware, internal PKI scripts, or legacy systems often suffer from similar deterministic key generation.  
- Exploiting such flaws lets attackers forge signatures, decrypt traffic, or impersonate trusted entities.  

---

**If you’d like any of the following, just let me know:**  
- Thumbnail/SEO‑optimized YouTube description  
- LinkedIn post version  
- Blog‑style article (Markdown) for GitHub notes  
- Advanced cryptographic breakdown (OSCP/OSWE depth)  

🚀 Just tell me what you need next!
