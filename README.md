🧠 CacheKiller
Advanced Automated Web Cache Poisoning Scanner

A modular, async, research-driven framework for discovering cache anomalies, unkeyed headers, CDN misconfigurations, CP-DoS vectors, and poisoning primitives.

✨ Features
🔍 Phase 1 — Discovery

Automatically checks cacheability of:

/ (base)

/robots.txt

/sitemap.xml

/favicon.ico

/manifest.json

/service-worker.js

/*.map

/static/*, /assets/*

Performs 4× fingerprinted requests to confirm:

Cache HIT / MISS

Content hash stability

Header deltas

CDN behaviors

Outputs to:

output/cache_possible.json

🔥 Phase 2 — Advanced Exploitation

Runs full exploitation modules on cache-eligible targets.

🧪 Header Probe Engine

Bruteforces headers from:

Built-in poisoning headers

User-supplied header-wordlist

Special poisoning values

Canary values (unique long random tokens)

Detects:

Status code deltas

Cache behavior (HIT → MISS, MISS → HIT)

Content hash mismatch

Length mismatch

Reflection

Location/redirect poisoning

Cached 400/403 (CP-DoS)

Content-Type changes

⚙️ High-Value Poisoning Modules
Module	Purpose
xhttp_override	HEAD/POST override poisoning
accept_version	Fastify CVE-2020-7764 (DoS)
fat_get	GET + body mismatch
cloudflare_403	Cacheable 403 via invalid Authorization
ats_fragment	%23 fragment poisoning (ATS)
fastly_host	Host-key bypass poisoning
referer_pair	Dual-Referer poisoning + reflection
host_port	Host: domain:12345 CP-DoS
illegal_headers	Raw-socket CRLF/NULL poisoning (dangerous)

Everything is written to one file:

output/poisoning_candidates.json

📂 Repository Structure
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

⚡ Installation
1️⃣ Clone the repo
git clone https://github.com/yourusername/CacheKiller.git
cd CacheKiller

2️⃣ Install requirements
pip install -r requirements.txt

🚀 Usage Guide
✔️ Step 1 — Prepare Your Inputs
subdomains.txt
assets.example.com
static.example.com
api.example.com


⚠️ No https:// or http:// prefixes.
CacheKiller handles protocols automatically.

headers.txt (optional)
x-client-ip
x-forwarded-for
cache-control
pragma
foo
bar

▶️ Running CacheKiller
⭐ Full Safe Scan (recommended first)
python3 cli.py \
  --subdomains subdomains.txt \
  --run-phase2 \
  --header-file headers.txt \
  --verbose

🔥 Full Scan + Dangerous Raw Tests

⚠️ USE ONLY IF YOU HAVE PERMISSION!
Raw tests can poison caches + cause CP-DoS.

python3 cli.py \
  --subdomains subdomains.txt \
  --run-phase2 \
  --header-file headers.txt \
  --allow-dangerous \
  --verbose

🎛 Run Only Specific Plugins

Example: Header brute-force + host_port poisoning:

python3 cli.py \
  --subdomains subdomains.txt \
  --run-phase2 \
  --plugins header_probe,host_port \
  --verbose

📦 Outputs
output/cache_possible.json

Compact list of cache-eligible URLs:

{
  "target": "assets.example.com",
  "path": "/robots.txt",
  "status": 200,
  "cache_headers": { "X-Cache": "Hit from cloudfront" }
}

output/poisoning_candidates.json

Each finding or anomaly:

{
  "ts": 1763717577,
  "target": "example.com",
  "path": "/",
  "probe_kind": "header",
  "header": "Content-Length",
  "value": "CK-3b10e31b",
  "base": { "status": 200, "cache_hit": true },
  "test": { "status": 400, "cache_hit": false },
  "anomalies": [
    "STATUS_CHANGE",
    "CACHE_BEHAVIOR_CHANGE"
  ],
  "notes": "200 → 400 | HIT → MISS"
}

❗ Legal Disclaimer

CacheKiller performs:

cache corruption

raw-socket injection

poisoning of CDN layers

CP-DoS amplification

header clobbering

reject/collapse-based DoS

You must only use this tool on:

✔ your own systems
✔ bug bounty programs that explicitly allow high-impact testing
✔ environments you have permission to test

You are fully responsible for misuse.

🤝 Contributing

Pull requests welcome for:

new plugins

new poisoning techniques from research papers

CDN-specific modules (Akamai, Cloudfront, Fastly, ATS, Varnish)

optimisation / concurrency upgrades

dashboard/reporting modules
