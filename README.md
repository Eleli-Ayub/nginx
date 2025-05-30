# 🔐 IP Restriction in Nginx

Control access to specific locations or resources in your Nginx server based on the client's IP address using the `allow` and `deny` directives.

---

## 📖 Table of Contents

- [Overview](#overview)
- [Basic Syntax](#basic-syntax)
- [Examples of IP Restriction](#examples-of-ip-restriction)
  - [1. Allow a Specific IP Address](#1-allow-a-specific-ip-address)
  - [2. Allow Multiple IP Addresses](#2-allow-multiple-ip-addresses)
  - [3. Allow Access from a Specific Subnet](#3-allow-access-from-a-specific-subnet)
  - [4. Handling Exceptions](#5-handling-exceptions)
- [🧪 Testing & Reloading](#-testing--reloading)
- [📝 Summary](#-summary)
- [📬 Contact](#-contact)

---

## 📌 Overview

In **Nginx**, you can restrict or allow access to certain parts of your application based on the client’s IP address. This is accomplished using the `allow` and `deny` directives inside specific `location` blocks.

---

## 🧾 Basic Syntax

```nginx
location /protected/ {
    allow ip_address;  # Allow a specific IP
    deny ip_address;   # Deny a specific IP
    deny all;          # Deny all other IPs

}
```
allow <ip>: Grants access to the specified IP.

deny <ip>: Blocks access from the specified IP or range.

deny all: Blocks access from all IPs except those explicitly allowed.

---
### 💡 Examples of IP Restriction
#### 1. Allow a Specific IP Address
Only allow access from 192.168.1.100:

```nginx
location /path/ {
    allow 192.168.1.100;
    deny all;
}
```
---
#### 2. Allow Multiple IP Addresses
Allow two specific IPs:

```nginx
location /path/ {
    allow 192.168.1.100;
    allow 192.168.1.101;
    deny all;
}
```
#### 3. Allow Access from a Specific Subnet
Use CIDR notation to allow a whole subnet:
```nginx
location /admin/ {
    allow 192.168.1.0/24;
    deny all;
}
```
#### 4. Handling Exceptions
Order of directives matters. This example denies one IP while allowing others:

```nginx
location /path/ {
    allow 192.168.1.100;
    deny 192.168.1.101;
    allow all;
}
```
🧪 Testing & Reloading
Once you've updated the configuration, test and reload:

Test Nginx config:
```bash
sudo nginx -t
```
Reload Nginx:
```bash
sudo systemctl reload nginx
```

---
### Summary
> Use allow and deny to control access by IP.

> Restrict specific locations like /admin/.

> Use CIDR for subnet control.

> Use third-party tools for country-level filtering.

> Always test configuration before reloading.
