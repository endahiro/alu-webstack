# HTTPS SSL – Project Notes

This project configures HTTPS, SSL termination, and HTTP to HTTPS redirection using HAProxy.

> **Note:** I was unable to claim the free domain for this project (per ALU instructions I did not purchase my own domain), so all verification was done using the load balancer IP address instead of a hostname. The Intranet checker still shows failing checks, but the configurations below are valid HAProxy configs and can be tested manually on a real server.

---

## Files

- `0-world_wide_web`  
  Bash script that uses `dig` and `awk` to inspect DNS records for the subdomains:
  - `www`
  - `lb-01`
  - `web-01`
  - `web-02`  
  **This task assumes a real domain and DNS A records.** Since I do not have a domain, this script cannot be fully validated in my environment.

- `1-haproxy_ssl_termination`  
  Example HAProxy configuration file (`/etc/haproxy/haproxy.cfg`) that:
  - Listens on **port 80** (HTTP) and **port 443** (HTTPS)
  - Terminates SSL on **port 443** using a certificate file
  - Proxies traffic to the two web servers:
    - `54.82.254.54:80` (web-01)
    - `44.202.63.206:80` (web-02)

- `2-redirect_http_to_https`  
  HAProxy configuration that:
  - Listens on **port 80** (HTTP) and **443** (HTTPS)
  - Redirects all HTTP traffic on port 80 to HTTPS with a **301** status code
  - Terminates SSL on port 443 and proxies to the same web servers as above

---

## How to test Task 1 (HAProxy SSL termination)

These steps assume:

- You have a load balancer server (Ubuntu, with `haproxy` installed)
- You know its public IP address (referred to as `<LB_IP>` below)
- Your web servers are running on:
  - `54.82.254.54:80`
  - `44.202.63.206:80`
  and return a page that contains **“Holberton School”** at `/`.

### 1. Copy the config to the server

On your local machine:

```bash
scp https_ssl/1-haproxy_ssl_termination ubuntu@<LB_IP>:/tmp/haproxy.cfg

