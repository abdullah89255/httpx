# httpx
Using **httpx** (from ProjectDiscovery) is a powerful way to speed up reconnaissance in bug bounty hunting. It’s mainly used to probe URLs, detect live hosts, and gather useful metadata at scale.

Here’s a practical, step-by-step guide focused on bug bounty workflows:

---

# 🔧 1. Install httpx

If you don’t already have it:

```bash
go install -v github.com/projectdiscovery/httpx/cmd/httpx@latest
```

Or via package managers (like apt, brew, etc.).

---

# 📥 2. Basic Usage – Check Live Hosts

Start with a list of domains/subdomains:

```bash
cat domains.txt | httpx
```

✔️ This filters out only **alive HTTP/HTTPS servers**

---

# ⚡ 3. Add Useful Recon Flags

This is where httpx shines:

```bash
cat domains.txt | httpx -status-code -title -tech-detect -ip -cdn
```

This gives:

* Status codes (200, 403, etc.)
* Page titles
* Technologies (CMS, frameworks)
* IP address
* CDN detection

👉 Helps prioritize targets quickly

---

# 🎯 4. Find Interesting Endpoints

```bash
cat urls.txt | httpx -path /admin,/login,/dashboard -status-code
```

✔️ Checks common sensitive paths across all hosts

---

# 📸 5. Take Screenshots (Great for Recon)

```bash
cat domains.txt | httpx -screenshot
```

✔️ Generates visual previews
✔️ Helps spot:

* Admin panels
* Internal tools
* Forgotten apps

---

# 🧠 6. Filter for Valuable Targets

Example: only live + successful responses

```bash
cat domains.txt | httpx -mc 200,302
```

* `-mc` = match status codes

---

# 🔎 7. Probe for HTTPS & Redirects

```bash
cat domains.txt | httpx -follow-redirects -location
```

✔️ Tracks where apps redirect (useful for takeover or misconfigurations)

---

# 🚀 8. Combine with Subdomain Tools

Typical bug bounty pipeline:

```bash
subfinder -d example.com | httpx -silent | tee live.txt
```

Tools:

* subfinder
* amass

---

# 🧪 9. Advanced Probing

```bash
httpx -l domains.txt -ports 80,443,8080,8443 -title -tech-detect -threads 100
```

✔️ Scans multiple ports
✔️ Faster with threading

---

# 🔐 10. Detect Potential Vulnerabilities

Not a scanner, but helps you **spot attack surface**:

```bash
httpx -l urls.txt -server -web-server -x-powered-by
```

Look for:

* Outdated servers
* Exposed technologies
* Misconfigurations

---

# 🧰 Pro Tips for Bug Bounty

### 🔹 Focus on:

* Status `200`, `302`, `401`, `403`
* Login/admin panels
* APIs (`/api`, `/v1`, `/graphql`)
* Staging/dev environments

### 🔹 Combine with:

* `nuclei` (for automated vuln scanning)
* `ffuf` (for fuzzing)
* `gau` / `waybackurls` (for URL collection)

---

# 🧭 Example Full Workflow

```bash
subfinder -d target.com | httpx -silent \
| tee live.txt

cat live.txt | httpx -title -tech-detect -status-code \
| tee enriched.txt
```

---

# ⚠️ Important

* Only scan targets you are authorized to test (bug bounty scope)
* Respect rate limits (`-rate-limit` flag)

---
