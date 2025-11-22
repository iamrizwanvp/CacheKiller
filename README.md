🧠 CacheKiller
Advanced Automated Web Cache Poisoning & CDN Behavior Scanner

CacheKiller is a research-grade framework for discovering and exploiting web-cache poisoning vectors, unkeyed header behavior, CDN normalization bugs, CP-DoS (Cache Poisoning DoS), and advanced response-differential anomalies.

🚀 Key Features
Phase 1 — Discovery

Automatically detects cacheable endpoints by checking:

/

/robots.txt

/sitemap.xml

/favicon.ico

/manifest.json

/service-worker.js

/*.map

/assets/*, /static/*

Each endpoint is verified with:

4 repeated requests

content hashing

length delta

header normalization

cache HIT/MISS detection (Age, X-Cache, CF-Cache-Status)

Results saved to:

output/cache_possible.json

Phase 2 — Advanced Poisoning Engine

Runs a full suite of cache poisoning probes on cache-enabled targets.

🔥 Includes high-value modules:
Module	Purpose
header_probe	brute-force headers (built-in + file list) w/ canary or special poisoning values
xhttp_override	test X-HTTP-Method-Override: HEAD/POST poisoning
accept_version	Fastify CVE-2020-7764 DoS vector
fat_get	GET + request body poisoning
cloudflare_403	cacheable 403 detection
ats_fragment	ATS fragment %23 poisoning
fastly_host	Fastly host-key bypass
host_port	Host: domain.com:12345 CP-DoS
referer_pair	dual-referer poisoning
illegal_headers	raw-socket CRLF/NULL cache poisoning (dangerous)

Everything is logged to one file:

output/poisoning_candidates.json


No extra logs.
No noise.
Only actionable findings.

📦 Repository Structure
cachekiller/
├─ cli.py               # Main runner (Phase1 + Phase2)
├─ core_http.py         # Async HTTP engine + scheduler + rate limiter
├─ discovery.py         # Cacheability detection
├─ phase2_advanced.py   # Full poisoning engine
├─ analyzer.py          # diff / reflection / anomaly scoring
├─ storage.py           # JSON helpers
├─ utils_canary.py      # unique canary generators
├─ subdomains.txt       # input list
├─ headers.txt          # custom header list
└─ output/
     ├─ cache_possible.json
     └─ poisoning_candidates.json

🛠 Installation
1️⃣ Clone the repository
git clone https://github.com/yourusername/CacheKiller.git
cd CacheKiller

2️⃣ Install dependencies
pip install -r requirements.txt

📥 Input Format (Very Important)
✔ Accepted:
assets.example.com
cdn.example.com
api.target.com

✔ ALSO supported now (auto-handled):
https://assets.example.com
http://cdn.example.com/
https://api.example.com/path   <-- path is ignored


CacheKiller automatically strips:

http://

https://

trailing slashes

URL paths

Your input is normalized to:

assets.example.com
cdn.example.com
api.example.com

▶️ Running CacheKiller
⭐ Recommended full safe mode
python3 cli.py --subdomains subdomains.txt --run-phase2 --header-file headers.txt --verbose

🔥 Full advanced scan (includes dangerous raw-socket tests)
python3 cli.py --subdomains subdomains.txt --run-phase2 --header-file headers.txt --allow-dangerous --verbose

🎯 Run only specific plugins
python3 cli.py \
  --subdomains subdomains.txt \
  --run-phase2 \
  --plugins header_probe,fastly_host,ats_fragment \
  --verbose

📤 Output Files
output/cache_possible.json

Contains endpoints that are confirmed cacheable:

{
  "target": "assets.example.com",
  "path": "/robots.txt",
  "status": 200,
  "cache_headers": {
      "X-Cache": "Hit from cloudfront"
  }
}

output/poisoning_candidates.json

All anomalies found during Phase2:

{
  "ts": 1763717577,
  "target": "technical-delivery.float.com",
  "path": "/",
  "probe_kind": "header",
  "header": "Content-Length",
  "value": "CK-df3a1...",
  "base": { "status": 200, "cache_hit": true },
  "test": { "status": 400, "cache_hit": false },
  "anomalies": [
      "STATUS_CHANGE",
      "CACHE_BEHAVIOR_CHANGE",
      "CONTENT_MISMATCH"
  ],
  "notes": "200 → 400 | HIT → MISS | len 3330 → 273"
}

⚠️ Legal Disclaimer

CacheKiller can trigger:

CDN misbehavior

cached 400/403

redirect loops

CP-DoS

illegal-header undefined behavior

poisoning of public cache layers

Use only where legally permitted.
You are fully responsible for misuse.

🤝 Contributing

Suggestions and PRs welcome:

More poisoning modules

CDN-specific patterns (Akamai, CloudFront, Fastly, StackPath)

Better diff-scoring

Dashboard / reporting

UI wrapper
