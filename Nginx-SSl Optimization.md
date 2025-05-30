# Nginx SSL Optimization for security, perfomance and latency reduction.

A concise guide to improving the performance and security of SSL/TLS on your Nginx-powered site. These best practices cover protocol selection, cipher configuration, handshake tuning, and more—so you can serve encrypted traffic with minimal latency and maximum safety.

---

## Table of Contents

- [Introduction](#introduction)    
- [Key Optimization Techniques](#key-optimization-techniques)  
  1. [Use Modern TLS Protocols](#1-use-modern-tls-protocols)  
  2. [Optimized Cipher Suites](#2-optimized-cipher-suites)  
  3. [HTTP/2](#3-http2)  
  4. [Session Resumption](#4-session-resumption)  
  5. [OCSP Stapling](#5-ocsp-stapling)  
  6. [High-Performance SSL Library](#6-high-performance-ssl-library)  
  7. [CDN SSL Termination](#7-cdn-ssl-termination)  
  8. [Elliptic-Curve Key Exchange](#8-elliptic-curve-key-exchange)  
  9. [HTTP Strict Transport Security (HSTS)](#9-http-strict-transport-security-hsts)  
  10. [Certificate Best Practices](#10-certificate-best-practices)  
  11. [Handshake Compression](#11-handshake-compression)  
  12. [Monitoring & Testing](#12-monitoring--testing)  
- [Complete Example Configuration](#complete-example-configuration)  
- [Benefits](#benefits)  

---

## Introduction

SSL/TLS encryption is essential for protecting data in transit—but it can also add CPU overhead and handshake latency. This guide shows you how to tune Nginx so that encrypted connections are both fast and secure, even under heavy traffic load.


## Key Optimization Techniques

### 1. Use Modern TLS Protocols

> Disable obsolete SSL/TLS versions (SSL 3.0, TLS 1.0/1.1). 
 
> Use TLS 1.2+ which fixes known vulnerabilities;
 
> Use TLS 1.3 which reduces handshake round-trips.  
```nginx
ssl_protocols TLSv1.2 TLSv1.3;
```
---
### 2. Optimized Cipher Suites
>Select strong, authenticated, forward-secret ciphers which balances security with performance, prioritizing modern algorithms.
```nginx
ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:
             ECDHE-RSA-AES128-GCM-SHA256:
             ECDHE-ECDSA-AES256-GCM-SHA384:
             ECDHE-RSA-AES256-GCM-SHA384';
ssl_prefer_server_ciphers on;

```
---
### 3. Use HTTP/2
> Enable the HTTP/2 protocol over TLS. This is because multiplexing and header compression reduce latency for HTTPS sites.
```nginx
listen 443 ssl http2;
```
---
### 4. Session Resumption
> Cache and reuse negotiated sessions to skip full handshakes. This dramatically cuts CPU and round-trip costs for repeat visitors.
```nginx
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
```
---
### 5. OCSP Stapling
> Using server-stapled OCSP responses  to prove certificate validity to clients. This eliminates client-side OCSP lookups, speeding up handshakes.
```nginx
ssl_stapling on;
ssl_stapling_verify on;
resolver 8.8.8.8 1.1.1.1 valid=300s;
resolver_timeout 5s;
ssl_trusted_certificate /etc/letsencrypt/live/example.com/chain.pem;
```
---
### 6. Use High-Performance SSL Library
> Link Nginx against the latest OpenSSL or an optimized fork (e.g., BoringSSL). These newer libraries include algorithmic improvements and TLS 1.3 support.

### 7. CDN SSL Termination
> Offload TLS to edge servers via CDN (Cloudflare, AWS CloudFront, Fastly). This terminates encryption closer to users, reducing network latency.

### 8. Use Elliptic-Curve Key Exchange
> Use a modern ECDH curve for Diffie-Hellman key exchange. This leads to smaller keys and faster computations than RSA.
```nginx
ssl_ecdh_curve secp384r1;
```
---
### 9. Use HTTP Strict Transport Security (HSTS)
> Instruct browsers to use HTTPS only in order to prevent downgrade attacks and accidental HTTP visits.
```nginx
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
```
---
### 10. Common Certificate Best Practices
> ECDSA over RSA: Smaller certificate size, faster handshakes.

> SAN/Wildcard: Cover multiple domains with a single cert.

> Automate Renewal: Use Certbot or similar to avoid expiry.

### 11. Handshake Compression
> Compress TLS handshake payloads (e.g., Brotli, gzip) to reduce data transfer overhead during handshake phase.

### 12. Monitoring & Testing
> SSL Labs: Comprehensive configuration grading.

> Wireshark / tcpdump: Inspect handshake timings.

> Nginx Amplify: Continuous performance monitoring.
