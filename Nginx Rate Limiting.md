# Nginx Rate Limiting

Rate limiting is an essential feature to protect your server from abuse—whether it’s brute-force attacks, denial-of-service (DoS) attempts, or simply too many concurrent requests that degrade performance. Nginx offers built-in rate limiting via the `limit_req` module, giving you fine-grained control over request rates per client.

---

## Table of Contents

- [How It Works](#how-it-works)  
- [Basic Configuration](#basic-configuration)  
- [Key Parameters](#key-parameters)  
- [Customizing Responses](#customizing-responses)  
- [Advanced Examples](#advanced-examples)  
  - [Different Limits per Location](#different-limits-per-location)  
  - [Custom Keys](#custom-keys-for-rate-limiting)  
  - [Whitelisting Trusted IPs](#whitelisting-trusted-ips)  
- [Notes & Tips](#notes--tips)  
- [License](#license)  

---

## How It Works

1. **Shared Memory Zone**  
   Nginx allocates a shared-memory zone to track client requests.  
2. **Unique Key**  
   Each client is identified by a key (e.g. their IP: `$binary_remote_addr`).  
3. **Enforcement**  
   - **`limit_req_zone`** defines how many requests per second are allowed.  
   - **`limit_req`** applies the defined zone to specific `server` or `location` blocks.  

---

## Basic Configuration

```nginx
http {
    # Define a zone "one": 10 MB shared RAM, 5 requests/sec per IP
    limit_req_zone $binary_remote_addr zone=one:10m rate=5r/s;

    server {

        location / {
            # Enforce the above rate, allow bursts of 10
            limit_req zone=one burst=10;
            proxy_pass http://127.0.0.1:8000;
        }
    }
}
```
> zone=one:10m
Allocates 10 MB (≈160 000 IPs) for tracking.

>rate=5r/s
Limits each IP to 5 requests per second.

>burst=10
Allows up to 10 requests in excess of the rate before limiting kicks in.
## Customizing Responses
By default, excess requests are delayed or rejected with HTTP 503. You can override this:

```nginx
error_page 503 /rate_limit_exceeded.html;

location = /rate_limit_exceeded.html {
    root /var/www/html;
}
```
## Advanced Examples
Different Limits per Location
```nginx
http {
    limit_req_zone $binary_remote_addr zone=api:10m rate=10r/s;
    limit_req_zone $binary_remote_addr zone=web:10m rate=20r/s;

    server {
        listen 80;

        location /api/ {
            limit_req zone=api burst=5 nodelay;
            proxy_pass http://127.0.0.1:8000;
        }

        location / {
            limit_req zone=web burst=10;
            proxy_pass http://127.0.0.1:8000;
        }
    }
}
```
### Notes & Tips
> Monitor your shared memory usage (status_zone directive via the stub_status module).

> Adjust burst and nodelay to balance protection vs. user experience.

> Combine with caching (limit_req_zone + proxy_cache) for better performance under load.
