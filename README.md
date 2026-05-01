# 🌐 HTTP – Complete Guide for Web Security & Pentesting

This guide covers everything you need to know about HTTP for web application security testing.

---

## 📌 Table of Contents

1. What is HTTP?
2. HTTP vs HTTPS
3. HTTP Methods
4. HTTP Status Codes
5. HTTP Headers
6. Cookies & Sessions
7. Common HTTP Vulnerabilities
8. HTTP Testing Tools & Commands
9. Hands-on Labs
10. Quick Cheatsheet

---

## 1. What is HTTP?

HTTP (HyperText Transfer Protocol) is a request-response protocol used to transfer data between a client (browser) and a web server.

- **Port:** 80
- **Stateless:** Each request is independent
- **Plain text:** No encryption by default

How it works:

Client (Browser) --- Request --> Server
Client (Browser) <-- Response --- Server

---

## 2. HTTP vs HTTPS

| Feature | HTTP | HTTPS |
| :--- | :--- | :--- |
| Port | 80 | 443 |
| Encryption | No | Yes (SSL/TLS) |
| Security | Low | High |
| Certificate | Not required | Required |
| Browser indication | "Not Secure" | Padlock icon |

Always use HTTPS in production.

---

## 3. HTTP Methods

| Method | Purpose | Example |
| :--- | :--- | :--- |
| GET | Retrieve data | GET /index.html |
| POST | Submit data | POST /login.php |
| PUT | Upload a file | PUT /upload |
| DELETE | Delete a resource | DELETE /user/1 |
| PATCH | Partial update | PATCH /profile |
| HEAD | Get headers only | HEAD /secret |
| OPTIONS | Check allowed methods | OPTIONS /api |

### Testing Methods with cURL

curl -X GET https://example.com
curl -X POST -d "name=test" https://example.com
curl -X PUT -T file.txt https://example.com/upload
curl -X DELETE https://example.com/user/1
curl -I https://example.com

---

## 4. HTTP Status Codes

### 2xx – Success

| Code | Meaning |
| :--- | :--- |
| 200 | OK |
| 201 | Created |

### 3xx – Redirection

| Code | Meaning | Security Risk |
| :--- | :--- | :--- |
| 301 | Moved Permanently | Open redirect |
| 302 | Found | Open redirect |

### 4xx – Client Errors

| Code | Meaning | What it tells a tester |
| :--- | :--- | :--- |
| 401 | Unauthorized | Authentication required |
| 403 | Forbidden | Access denied |
| 404 | Not Found | Directory enumeration |
| 405 | Method Not Allowed | Reveals allowed methods |

### 5xx – Server Errors

| Code | Meaning |
| :--- | :--- |
| 500 | Internal Server Error |
| 502 | Bad Gateway |
| 503 | Service Unavailable |

---

## 5. HTTP Headers

### Request Headers (Client -> Server)

| Header | Example | Purpose |
| :--- | :--- | :--- |
| Host | Host: example.com | Target domain |
| User-Agent | User-Agent: Mozilla/5.0 | Browser identity |
| Cookie | Cookie: session=abc | Session data |
| Referer | Referer: google.com | Previous page |
| Authorization | Authorization: Basic xxx | Credentials |
| Content-Type | Content-Type: application/json | Request body format |

### Response Headers (Server -> Client)

| Header | Example | Purpose |
| :--- | :--- | :--- |
| Set-Cookie | Set-Cookie: session=abc | Set session cookie |
| Location | Location: /dashboard | Redirect destination |
| Server | Server: Apache/2.4 | Server information |
| Content-Type | Content-Type: text/html | Response body format |

---

## 6. Cookies & Sessions

Cookies are used to maintain session state (login, preferences).

### Security Flags

Set-Cookie: sessionId=abc123; Secure; HttpOnly; SameSite=Strict; Max-Age=3600

| Flag | Purpose |
| :--- | :--- |
| Secure | Cookie sent only over HTTPS |
| HttpOnly | JavaScript cannot access (prevents XSS theft) |
| SameSite | Prevents CSRF attacks |
| Max-Age | Session expiration time |

### Session Hijacking Risks

- Without HttpOnly: XSS can steal cookies via document.cookie
- Without Secure: Cookie sent over HTTP (can be sniffed)

---

## 7. Common HTTP Vulnerabilities

| Vulnerability | Description | Fix |
| :--- | :--- | :--- |
| Missing HTTPS | Data sent in plaintext | Enforce HTTPS + HSTS |
| Open Redirect | User-controlled redirect destination | Validate redirect URLs |
| Information Disclosure | Server version exposed | Hide or minimize headers |
| Missing Security Headers | No CSP, X-Frame-Options | Add all security headers |
| Session Hijacking | Weak cookie flags | Set Secure, HttpOnly, SameSite |

### Essential Security Headers (to add on server)

Strict-Transport-Security: max-age=31536000; includeSubDomains
Content-Security-Policy: default-src 'self'
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
Referrer-Policy: strict-origin-when-cross-origin

---

## 8. HTTP Testing Tools & Commands

### cURL – HTTP Client

# GET request
curl https://example.com

# Show response headers only
curl -I https://example.com

# POST request with data
curl -X POST -d "user=admin&pass=123" https://example.com/login

# POST request with JSON
curl -X POST https://api.example.com/login -d '{"user":"admin"}' -H "Content-Type: application/json"

# Custom headers
curl -H "User-Agent: MyBot" -H "X-Custom: test" https://example.com

# Follow redirects
curl -L https://example.com

# Save output to file
curl -o output.html https://example.com

### Nmap HTTP Scripts

# Discover HTTP service
nmap -p 80,443 <target>

# Enumerate HTTP methods
nmap -p 80 --script http-methods <target>

# Get HTTP headers information
nmap -p 80 --script http-headers <target>

# Directory enumeration with Nmap
nmap -p 80 --script http-enum <target>

### Burp Suite (Web Proxy)

1. Set browser proxy to 127.0.0.1:8080
2. Turn Intercept ON
3. Browse to any website
4. Capture and modify HTTP requests
5. Forward to see responses

### Gobuster – Directory Fuzzing

gobuster dir -u https://example.com -w /usr/share/wordlists/dirb/common.txt
gobuster dir -u https://example.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

---

## 9. Hands-on Labs

### Lab 1: Intercept and Modify HTTP Request (Burp Suite)

1. Launch Burp Suite
2. Set browser proxy to 127.0.0.1:8080
3. Turn Intercept ON
4. Visit http://testphp.vulnweb.com
5. Modify the User-Agent header
6. Forward the request
7. Observe the response

### Lab 2: Detect HTTP Methods

curl -X OPTIONS https://example.com -i
nmap -p 80 --script http-methods <target>

### Lab 3: Check Security Headers

curl -I https://example.com

Look for:
- Strict-Transport-Security
- Content-Security-Policy
- X-Frame-Options
- X-Content-Type-Options

If missing -> server is not following security best practices.

### Lab 4: Directory Enumeration

gobuster dir -u https://example.com -w /usr/share/wordlists/dirb/common.txt

---

## 10. Quick Reference Cheatsheet

| Task | Command / Tool |
| :--- | :--- |
| Send GET request | curl https://example.com |
| Show response headers | curl -I https://example.com |
| Send POST request | curl -X POST -d "key=value" https://example.com |
| Follow redirects | curl -L https://example.com |
| Intercept HTTP traffic | Burp Suite / OWASP ZAP |
| Scan for HTTP services | nmap -p 80,443 <target> |
| Enumerate directories | gobuster dir -u https://example.com -w wordlist.txt |
| Test HTTP methods | nmap -p 80 --script http-methods <target> |
| Check security headers | curl -I https://example.com |

---

## 🔗 Related Guides

- [TCP/IP, Subnetting & Routing Guide](link)
- [Application Layer Protocols (FTP, SMTP, POP3, IMAP)](link)
- [OSI Model Technical Definitions](link)
- [CLI Command Comparison](link)

---

## 📞 Connect with me

- GitHub: vrushabhp7496-a11y
- LinkedIn: [Your LinkedIn URL]

---

Master HTTP to master web security.

⭐ Star this repository if it helped you.
