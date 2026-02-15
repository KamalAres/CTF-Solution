
## 🚀 Gobuster – CTF Quick Summary  

### 📌 What Is Gobuster?  
- Fast Go‑written tool for **directory, file, DNS, and virtual‑host brute‑forcing**.  
- Sends HTTP/DNS requests using a wordlist and evaluates responses.  
- Core enumeration tool in CTF web challenges.

### 🛠️ Modes & Purposes  

| Mode   | Purpose                              |
|--------|--------------------------------------|
| `dir`  | Brute‑force directories & files      |
| `dns`  | Brute‑force subdomains                |
| `vhost`| Discover virtual hosts                |
| `fuzz` | General fuzzing (newer versions)     |

### 📂 1️⃣ Directory Enumeration (Most Common)  
**Typical command**  
```bash
gobuster dir -u http://target.com -w wordlist.txt -x php,txt,html -t 50
```  
**Key flags**  

- `-u` Target URL  
- `-w` Wordlist  
- `-x` File extensions (e.g., php,txt,html)  
- `-t` Threads (parallelism)  
- `-o` Output file  
- `-s` Show specific status codes  
- `-b` Blacklist status codes  
- `-k` Skip SSL verification  
- `--add-slash` Append “/” to directories  

### 🌐 2️⃣ Subdomain Enumeration  
```bash
gobuster dns -d target.com -w subdomains.txt
```  
Useful flags: `-d` Domain, `-w` Wordlist, `-i` Show IP, `-t` Threads.

### 🖥️ 3️⃣ Virtual‑Host Enumeration  
```bash
gobuster vhost -u http://10.10.10.10 -w wordlist.txt
```
Ideal for targets hosting multiple sites on a single IP.

### 📁 Recommended Wordlists (Kali)  

| Category | Path & Example | Notes |
|----------|----------------|-------|
| **Dirb common** | `/usr/share/wordlists/dirb/common.txt` | Small, fast – great for beginners |
| **SecLists (medium/small)** | `/usr/share/seclists/Discovery/Web-Content/`<br>`directory-list-2.3-medium.txt`<br>`directory-list-2.3-small.txt`<br>`common.txt` | Industry‑standard, works well in HTB/TryHackMe |
| **Big wordlist** | `directory-list-2.3-big.txt` | Slower, noisy – reserve for advanced runs |
| **Subdomains** | `/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt` | Good balance of coverage and speed |

### 🎯 Recommended CTF Workflow  

1. **Start small** – run `gobuster dir` with a tiny list (e.g., `common.txt`).  
2. **Add extensions** – `-x php,txt,html,js,bak,old,zip`.  
3. **Scale up** – switch to a medium/large list if nothing appears.  
4. **Check virtual hosts** – use `gobuster vhost` on IP‑based targets.  
5. **Subdomains** – run `gobuster dns` with a DNS wordlist.  

### ⚡ Pro Tips  

- Scan `/robots.txt` first.  
- Use multiple extensions; many CTF flags hide behind uncommon suffixes.  
- If many 403 responses appear, try adding a trailing slash or different extensions.  
- Correlate Gobuster output with Burp Suite or manual browsing.  
- Pay attention to response size variations to spot hidden resources.

### 🧠 Why Gobuster Matters in CTFs  

- Uncovers hidden admin panels, backup files, upload directories, flag files, and API endpoints.  
- Provides the essential reconnaissance step; without enumeration, exploitation often fails.  

### 🏁 Final Takeaway  

Gobuster is **fast**, **simple**, and **highly effective** for web enumeration in CTFs. Mastering its modes, flags, and wordlist strategy dramatically improves your chances of finding flags and succeeding in challenges.
