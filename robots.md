
## 🤖 Importance of Checking `robots.txt` in CTFs  

### 📌 What is `robots.txt`?  
- A publicly accessible file (e.g., `https://target.com/robots.txt`).  
- Instructs search‑engine crawlers which paths **not** to index.

### 🛡️ Why It Matters for CTFs  

| Reason | Details |
|--------|---------|
| **Discovery of hidden directories** | Disallowed entries like `/admin/`, `/backup/`, `/secret-panel/` often point to admin panels, backup files, dev environments, or flag locations. |
| **Information disclosure** | Reveals sensitive endpoints, internal structure, debug/staging routes, and forgotten directories—valuable reconnaissance data. |
| **Bypassing security‑by‑design assumptions** | `robots.txt` is *not* a security control; it merely advises crawlers. In CTFs it demonstrates that security through obscurity is weak and that public files can leak secrets. |
| **Low effort, high reward** | No authentication needed, takes seconds to fetch, and frequently leads directly to flags, especially in beginner/intermediate challenges. |

### 🔎 Typical CTF Workflow  

1. Open the target website.  
2. Access `/robots.txt`.  
3. List the `Disallow` paths.  
4. Manually browse those directories.  
5. Look for exposed files or flags.

### 🎯 Key Takeaway  

Checking `robots.txt` is a quick, high‑impact reconnaissance step. Ignoring it can cause you to miss easy flags and valuable clues about the target's attack surface. Include it in every initial enumeration checklist.
