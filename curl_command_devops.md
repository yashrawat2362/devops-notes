# `curl` Command — Complete DevOps Guide

`curl` (Client URL) is a command-line tool used to **send and receive data over the network** — mainly used to talk to APIs, test websites, download files, and check if servers are alive. It's one of the **most-used tools in DevOps**, especially for testing deployments and debugging APIs.

---

## Why DevOps Engineers Use `curl`

- Testing if an API/server is up and responding
- Checking response codes (200, 404, 500, etc.) after a deployment
- Downloading files/scripts onto a server
- Debugging webhooks, load balancers, and microservices
- Automating health checks inside scripts and CI/CD pipelines

---

## 1. Basic Syntax

```bash
curl [options] URL
```

**Example:**
```bash
curl https://example.com
```
**Explanation:** This sends a request to `example.com` and prints the raw HTML response on your screen — a quick way to check if a website is reachable.

---

## 2. Most-Used `curl` Flags (Cheat Sheet)

| Flag | Meaning |
|---|---|
| `-X` | HTTP method (GET, POST, PUT, DELETE) |
| `-H` | Add a header |
| `-d` | Send data (for POST/PUT) |
| `-i` | Show response headers |
| `-I` | Show ONLY headers (no body) |
| `-o` | Save output to a file |
| `-O` | Save with the original filename |
| `-s` | Silent mode (no progress bar) |
| `-v` | Verbose mode (show full request/response details) |
| `-L` | Follow redirects |
| `-k` | Ignore SSL certificate errors |
| `-w` | Print custom info (like status code, time taken) |

---

## 3. Checking if a Server/API is Alive

### Basic health check
```bash
curl https://api.example.com/health
```
**Explanation:** Hits the health-check endpoint of a service. If it responds, the app is alive.

### Check only the HTTP status code (very common in DevOps scripts)
```bash
curl -o /dev/null -s -w "%{http_code}\n" https://api.example.com/health
```
**Explanation:**
- `-o /dev/null` → throws away the actual response body (we don't need to see it)
- `-s` → hides the progress bar
- `-w "%{http_code}\n"` → prints only the status code, like `200` or `500`

**This is exactly how automated health checks work in deployment scripts.**

### Show only response headers (no body)
```bash
curl -I https://example.com
```
**Explanation:** Useful to quickly check things like content type, server type, or if the page redirects — without downloading the full page.

---

## 4. Making API Requests (GET, POST, PUT, DELETE)

### GET request (fetch data) — with an Auth token
```bash
curl -X GET https://api.example.com/users \
  -H "Authorization: Bearer $TOKEN"
```
**Explanation:** `-X GET` explicitly sets the method (GET is actually the default, but it's good practice to write it). `-H` adds an authorization header — required by almost every real-world API.

### POST request (create/send data)
```bash
curl -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -d '{"name": "John", "role": "devops"}'
```
**Explanation:** `-X POST` tells the server we're creating something new. `-H "Content-Type: application/json"` tells the server the data format. `-d` sends the actual JSON data (like submitting a form).

### PUT request (update existing data)
```bash
curl -X PUT https://api.example.com/users/101 \
  -H "Content-Type: application/json" \
  -d '{"role": "senior-devops"}'
```
**Explanation:** Updates user `101`'s role. `PUT` is used when replacing/updating a resource that already exists.

### DELETE request (remove data)
```bash
curl -X DELETE https://api.example.com/users/101 \
  -H "Authorization: Bearer $TOKEN"
```
**Explanation:** Deletes user `101` from the system. Common in cleanup scripts or CI/CD pipeline teardown steps.

---

## 5. Downloading Files

### Save with a custom filename
```bash
curl -o terraform.zip https://releases.hashicorp.com/terraform/1.7.0/terraform_1.7.0_linux_amd64.zip
```
**Explanation:** Downloads the file and saves it as `terraform.zip`.

### Save with the original filename automatically
```bash
curl -O https://releases.hashicorp.com/terraform/1.7.0/terraform_1.7.0_linux_amd64.zip
```
**Explanation:** `-O` (capital O) keeps the original filename from the URL — no need to type it yourself. Very common when installing CLI tools (Terraform, kubectl, AWS CLI) on a fresh server.

---

## 6. Debugging with `curl` (Very Useful for DevOps)

### Verbose mode — see the full request & response
```bash
curl -v https://api.example.com/health
```
**Explanation:** Shows everything: DNS lookup, SSL handshake, request headers sent, response headers received. **This is the #1 command to debug "why is my API not responding correctly."**

### Follow redirects
```bash
curl -L https://short.url/abc123
```
**Explanation:** Some URLs redirect (301/302) to another URL. Without `-L`, curl stops at the redirect and shows nothing useful. `-L` tells curl to follow it automatically.

### Ignore SSL certificate errors (use carefully — mainly for internal/staging servers)
```bash
curl -k https://internal-staging.example.com
```
**Explanation:** Skips SSL certificate validation — useful when testing internal servers with self-signed certificates, but **never use this in production** for real security checks.

### Measure response time (useful for performance checks)
```bash
curl -o /dev/null -s -w "Time taken: %{time_total}s\n" https://api.example.com
```
**Explanation:** Prints exactly how long the request took — helpful for spotting slow APIs or a struggling server.

---

## 7. Real DevOps Use Case: CI/CD Health Check Script

```bash
#!/bin/bash
# health_check.sh - Used after deployment to confirm the app is live

URL="https://api.example.com/health"
STATUS=$(curl -o /dev/null -s -w "%{http_code}" $URL)

if [ "$STATUS" -eq 200 ]; then
  echo "✅ Deployment successful. App is healthy (HTTP $STATUS)"
else
  echo "❌ Deployment failed! App returned HTTP $STATUS"
  exit 1
fi
```
**Explanation:** This is a real pattern used in CI/CD pipelines (Jenkins, GitHub Actions, GitLab CI). After deploying, the pipeline calls this script — if the app doesn't return `200`, the pipeline fails the build and alerts the team.

---

## 8. Testing Webhooks/Load Balancers

```bash
curl -X POST https://webhook.site/your-unique-id \
  -H "Content-Type: application/json" \
  -d '{"event": "deployment_success", "service": "user-api"}'
```
**Explanation:** Simulates sending a webhook notification — used to test if your CI/CD pipeline or Slack integration correctly receives deployment events.

---

## 9. Quick Reference / Cheat Sheet

```bash
curl URL                                    # basic GET request
curl -I URL                                 # headers only
curl -i URL                                 # headers + body
curl -X POST URL -d 'data'                  # POST with data
curl -X POST URL -H "Content-Type: application/json" -d '{}'   # POST JSON
curl -H "Authorization: Bearer TOKEN" URL   # authenticated request
curl -O URL                                 # download file (keep original name)
curl -o filename URL                        # download file (custom name)
curl -L URL                                 # follow redirects
curl -k URL                                 # skip SSL verification
curl -v URL                                 # verbose/debug mode
curl -s -o /dev/null -w "%{http_code}" URL  # get only status code
curl -w "%{time_total}\n" URL               # measure response time
```

---

## Key Takeaway

`curl` is your go-to tool to **talk to any server or API from the terminal** — whether you're checking if a deployment succeeded, testing an API endpoint, downloading a tool, or debugging why a service isn't responding. Almost every CI/CD pipeline and monitoring script uses `curl` somewhere behind the scenes.
