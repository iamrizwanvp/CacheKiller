🧠 CacheKiller
Advanced Automated Web Cache Poisoning Scanner

A modular, async, research-driven framework for discovering cache anomalies, unkeyed headers, CDN misconfigurations, CP-DoS vectors, and poisoning primitives.

📌 Overview

CacheKiller is an advanced, extensible framework built for security researchers and bug hunters.
It automates the detection of:

Web Cache Poisoning

CP-DoS (Cache Poisoning Denial of Service)

Unkeyed header vulnerabilities

CDN misconfigurations (Cloudflare, Fastly, Akamai, ATS, etc.)

Reflection-based poisoning

Cache key normalization issues

Header-based evasions and edge-case request behaviors

CacheKiller is built using async Python, supports rate-limiting, dangerous mode, raw-socket tests, and records everything into clean JSON outputs.

It is inspired by research from:
AlbinoWax, Iustin24, CPDoS.org, Practical Web Cache Poisoning, Web Cache Entanglement, and many others.

✨ Features
🔍 Phase 1 — Discovery

Automatically checks:

Base URL cacheability

Static assets:
/robots.txt, /favicon.ico, /sitemap.xml, /manifest.json,
/static/*, /assets/*, /*.map, /service-worker.js

4x repeated fingerprinting to confirm cache HIT/MISS behavior

Saves results to:
output/cache_possible.json

🔥 Phase 2 — Advanced Exploitation

Runs a full suite of poisoning techniques on cache-eligible endpoints:

✓ Header brute forcing (supports 2900+ headers)

Reflection detection

Status code deltas

Content mismatches (hash/length)

Cache HIT → MISS / MISS → HIT

Redirect/location changes

Cached 400/403 detection

✓ Special high-value poisoning plugins

X-Forwarded-* poisoning

Fastly-Host injection

Accept-Version (Fastify CVE-2020-7764)

X-HTTP-Method-Override

CP-DoS via invalid Authorization (Cloudflare/S3/Azure)

ATS encoded fragment (%23) cache-key bypass

Fat-GET (GET with body)

User-Agent cache poisoning

Capitalized Host header (case normalization)

✓ NEW: Advanced Plugins Added

Referer Pair Tester → dual-Referer poisoning

Host:InvalidPort Injection → host-based CP-DoS

Fastly host+port bypass

✓ Dangerous (raw-socket) tests

Enabled only with --allow-dangerous:

Raw duplicate header injection

Illegal header fuzzing (\, NULL-byte, CRLF-injection)

Raw #fragment injection

Low-level socket-based cache poisoning

Everything is written into:

output/poisoning_candidates.json

📁 Project Structure
cachekiller/
├─ cli.py
├─ core_http.py
├─ discovery.py
├─ phase2_advanced.py
├─ storage.py
├─ utils_canary.py
├─ analyzer.py
├─ subdomains.txt
├─ headers.txt
└─ output/
     ├─ cache_possible.json
     └─ poisoning_candidates.json

⚙️ Installation
1) Clone the repo
git clone https://github.com/yourusername/CacheKiller.git
cd CacheKiller

2) Install Python requirements
pip install -r requirements.txt


(Optional) Ensure you use Python ≥ 3.10 for asyncio features.

🚀 Usage
Step 1 — Prepare your inputs
subdomains.txt
assets.example.com
static.example.com
files.example.com


⚠️ Do NOT include https:// or http://
The tool handles protocol automatically.

headers.txt (optional)

One header per line:

x-forwarded-for
x-client-ip
foo
bar
testheader

▶️ Run Discovery + Phase2 (Full Scan)
Full safe scan
python3 cli.py --subdomains subdomains.txt \
               --run-phase2 \
               --header-file headers.txt \
               --verbose

Full scan with dangerous tests

⚠️ USE ONLY IF YOU HAVE PERMISSION! ⚠️

python3 cli.py --subdomains subdomains.txt \
               --run-phase2 \
               --header-file headers.txt \
               --allow-dangerous \
               --verbose

🧪 Example Output
cache_possible.json
{
  "target": "assets.example.com",
  "path": "/robots.txt",
  "status": 200,
  "cache_headers": { "X-Cache": "Hit from cloudfront", "Age": "24500" }
}

poisoning_candidates.json
{
  "ts": 1763717577,
  "target": "assets.example.com",
  "path": "/",
  "probe_kind": "header",
  "header": "Content-Length",
  "value": "CK-3b10e31b",
  "base": { "status": 200, "cache_hit": true },
  "test": { "status": 400, "cache_hit": false },
  "anomalies": ["STATUS_CHANGE", "CACHE_BEHAVIOR_CHANGE"],
  "notes": "200 -> 400 | cache_hit True -> False"
}

⚠️ Legal Notice (VERY IMPORTANT)

CacheKiller includes dangerous low-level tests capable of causing:

Cache poisoning

CDN-wide CP-DoS

Cache corruption

Temporary service disruption

Reflection-based stored XSS hijacks

Origin server overload

You are ONLY allowed to use this tool:

On your own systems

On Bug Bounty programs that explicitly allow high-impact testing

With written permission

You are 100% responsible for how you use this tool.

🤝 Contributing

Pull requests for:

new poisoning techniques

CDN-specific modules

test cases

code improvements

…are welcome!

If you publish research and want your technique added, open an issue.
