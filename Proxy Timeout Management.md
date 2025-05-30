# Nginx Proxy Timeout Management

A quick reference for configuring proxy timeouts and related settings in Nginx to ensure that backend requests don’t hang indefinitely and that your server resources are used efficiently.

---

## Table of Contents

1. [Introduction](#introduction)  
2. [Key Directives](#key-directives)  
   - [proxy_connect_timeout](#proxy_connect_timeout)  
   - [proxy_send_timeout](#proxy_send_timeout)  
   - [proxy_read_timeout](#proxy_read_timeout)  
   - [send_timeout](#send_timeout)  
   - [proxy_buffering](#proxy_buffering)  
   - [proxy_max_temp_file_size](#proxy_max_temp_file_size) 
3. [Choosing Appropriate Timeout Values](#choosing-appropriate-timeout-values)  
4. [Impact of Proxy Timeout Management](#impact-of-proxy-timeout-management)  
5. [Testing and Adjusting Timeouts](#testing-and-adjusting-timeouts)  
6. [License & Contributing](#license--contributing)  

---

## Introduction

When proxying requests to an upstream server in Nginx, it’s crucial to configure appropriate timeout directives so that Nginx doesn’t wait indefinitely for the backend or for slow clients. Proper timeouts lead to better resource utilization, improved security, and a more predictable user experience.

---

## Key Directives

### 1. proxy_connect_timeout

> Maximum time to wait for establishing a TCP connection with upstream.  
```nginx
location /api/ {
   proxy_pass http://backend;
   proxy_connect_timeout 10s;  # Abort if connection not established within 10s
}
```

### 2. proxy_read_timeout
> Max time to wait for a response from upstream after the request has been sent.
```nginx
location /api/ {
    proxy_pass http://backend;
    proxy_read_timeout 20s;  # Abort if no response in 20s
}
```

### 3. send_timeout
> Max time between two successive writes to the client.
```nginx
location /api/ {
    proxy_pass http://backend;
    send_timeout 30s;  # Abort if client doesn’t receive data within 30s
}
```
### 4. proxy_buffering
> Enable or disable buffering of responses from upstream.

> Use Case: Turning off buffering is useful for low-latency streaming.
```nginx
location /api/ {
    proxy_pass http://backend;
    proxy_buffering off;  # Stream responses directly to client
}
```
### 5. proxy_max_temp_file_size
> Max size of temporary files for buffering large upstream responses.
```nginx
location /api/ {
    proxy_pass http://backend;
    proxy_max_temp_file_size 50m;  # Limit temp files to 50 MB
}
```
---
### Choosing Appropriate Timeout Values
**proxy_connect_timeout:**
Set to 5–10 s for quick failure on unreachable backends.

**proxy_send_timeout:**
Adjust based on upload sizes; 20–30 s often works for larger payloads.

**proxy_read_timeout:**
Increase for long-running requests; 60 s is a sensible default for most apps.

**send_timeout:**
Raise for streaming large files (e.g., video), e.g. 60–120 s

---
### Impact of Proxy Timeout Management
**Efficiency:**
Fails fast on unresponsive backends, freeing worker processes.

**Security:**
Protects against slow‐loris or similar DoS attacks by closing stale connections.

**User Experience:**
Provides quicker error feedback instead of hanging the client.

---
### Testing and Adjusting Timeouts
> Load Testing Tools:

- Apache Benchmark (ab)

- wrk

> Simulate Slow Backends/Clients:

- Introduce artificial delays.

- Measure response and error rates.

> Iterate:

- Tighten or loosen timeouts based on real-world traffic patterns and performance goals.
