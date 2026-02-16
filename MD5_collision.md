
**MD5 Collision Exploitation Walkthrough – Detailed Summary**

---

### 📌 Objective
- Demonstrate how to defeat a web‑based challenge by using an **MD5 hash collision** to bypass the server’s validation and retrieve the flag.

---

### 🧩 Context
- The challenge’s authentication/validation logic relies on MD5 hashes.
- By presenting two different inputs that share the same MD5 digest, the application can be fooled into accepting a malicious payload.

---

### 🔍 Key Concepts  

| Concept | Details |
|---------|---------|
| **MD5 Hash** | A widely‑used cryptographic hash that is **no longer collision‑resistant**. |
| **Collision** | Two distinct inputs producing the **exact same MD5 value**. |
| **Impact** | When an app only checks the hash, a crafted colliding input can satisfy the check while containing arbitrary (e.g., malicious) data. |

---

### 🛠️ Attack Strategy  

1. **Analyse the validation logic**  
   - Identify that the server compares MD5 hashes rather than the raw inputs.  
   - Understand that any pair of colliding strings will be treated as equivalent.

2. **Generate an MD5 collision**  
   - Use a tool such as **fastcoll** (or pre‑computed collisions) to create two files with identical MD5 digests.

3. **Deploy the colliding payload**  
   - Submit the “malicious” colliding file to the web interface.  
   - The server sees the matching hash and accepts the request.

4. **Trigger the vulnerability**  
   - Upon successful validation, the challenge reveals the flag or confirms the bypass.

---

### 🧠 Learning Takeaways  

- **MD5 collisions are exploitable** when applications misuse raw hash values for security decisions.  
- **Collision attacks** are especially potent in CTF‑style web challenges and poorly designed file‑upload mechanisms.  
- **Understanding hash weaknesses** is essential for both attackers and defenders in web security assessments.

---

## 🧰 Tool Highlight – **`fastcoll`**

#### 📌 What It Is
`fastcoll` is a command‑line utility (Docker image available) that generates **MD5 collision pairs**—two different binary blobs that share the same MD5 digest. It implements the Marc Stevens collision algorithm.

#### 🔍 Why It Matters
- MD5 is cryptographically broken; `fastcoll` lets you **produce colliding files quickly**.  
- Enables **hash‑only validation bypasses**, file‑upload evasion, and crafting of benign/malicious pairs that appear identical to a hash check.

#### 🛠️ How `fastcoll` Works (Typical Docker Command)

```bash
docker run --rm -it -v $PWD:/work -w /work \
  -u $UID:$GID brimstone/fastcoll \
  --prefixfile input \
  -o msg1.bin msg2.bin
```

| Step | Explanation |
|------|-------------|
| **`--prefixfile input`** | Supplies a base file (`input`) that will be common to both outputs. |
| **Collision block generation** | `fastcoll` computes two 128‑byte blocks that follow the prefix yet still yield the same MD5 hash. |
| **`-o msg1.bin msg2.bin`** | Writes the two colliding files. Their contents differ, but `md5sum` reports identical digests. |

#### 📈 Typical Workflow in a Challenge  

1. **Create a prefix** – e.g., a known header, JSON skeleton, or any required initial content.  
2. **Run `fastcoll`** to obtain `msg1.bin` and `msg2.bin`.  
3. **Append the collision blocks** to the prefix (the tool already does this).  
4. **Verify**:

   ```bash
   md5sum msg1.bin msg2.bin
   ```

   Both hashes should match.  

5. **Submit** the appropriate file (usually the malicious one) to the web app.

#### 🚀 Exploitation Use‑Cases  

- **Hash‑only validation**: The server only checks the MD5 of an uploaded file.  
- **File‑upload bypass**: Provide a benign file that matches the expected hash, then replace it with the malicious colliding counterpart.  
- **CTF challenges**: Commonly used to gain unauthorized access or retrieve hidden flags.

#### 🧠 Important Notes  

- The method **fails against secure hashes** (e.g., SHA‑256, SHA‑3).  
- `fastcoll` creates **standard collisions**; it does **not** support arbitrary chosen‑prefix collisions. For those, more advanced tools (e.g., `hashclash`) are required.  

---

### 📋 Next Steps (Optional)

If you’d like a **step‑by‑step cheat sheet** that includes exact commands, environment setup, and sample payload construction, just let me know and I’ll generate it for you!
