
**Summary – From LFI to Full Data Exfiltration**

**1. Recon & Enumeration**
- Port scan uncovered a Python/Werkzeug web app on a non‑standard port.
- Directory brute‑forcing revealed typical endpoints (`/login`, `/register`, `/dashboard`, etc.), mapping authentication flow and potential attack surfaces.

**2. Understanding the Application**
- After registering, HTTP interception showed 302 redirects, session cookies, and authenticated dashboard access.
- DevTools exposed an API parameter (e.g., `layout`) that the server uses to load layout/theme files dynamically.

**3. Discovering the LFI**
- By manipulating the `layout` parameter with traversal strings like `../../../../etc/passwd`, the server returned arbitrary local file contents.
- This confirmed a lack of input validation and an unrestricted file‑read vulnerability.

**4. Escalating the LFI**
- **Process enumeration:** Accessing `/proc/self/cmdline` revealed the absolute path of the running source code.
- **Source‑code disclosure:** Using the discovered path, the attacker retrieved backend code, uncovering hard‑coded credentials and sensitive tokens.

**5. Data Exfiltration**
- The stolen credentials allowed authentication to a privileged API.
- An export function was abused to download the entire database, achieving full application data compromise and administrative‑level access.

**6. Core Security Concepts**
- **Local File Inclusion (LFI):** User‑controlled file paths without proper sanitization enable arbitrary file reads.
- **Directory Traversal:** `../` sequences navigate up the filesystem to sensitive files.
- **Post‑exploitation via LFI:** Leads to source‑code leak, credential harvesting, config/token exposure, and database access.

**7. Defensive Measures**
- Enforce strict whitelisting of allowed files.
- Normalize and sanitize all file‑path inputs.
- Map file requests to absolute paths internally and block traversal patterns.
- Remove user control over filesystem paths and restrict access to system files.
- Avoid hard‑coded credentials in code and use HttpOnly/secure cookies and CSP where applicable.

**8. Takeaway**
- An LFI that appears to only allow file reads can be chained with enumeration, source‑code exposure, and credential theft to achieve full data exfiltration and privilege escalation. Successful exploitation depends on thorough enumeration, understanding of backend logic, and systematic chaining of small flaws into a complete compromise.
