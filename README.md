# 🔍 Passive Subdomain Discovery & Port Scanner

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-macOS%20%7C%20Linux-lightgrey)](#-requirements)
[![Shell](https://img.shields.io/badge/shell-bash-green)](https://www.gnu.org/software/bash/)

A single-file bash tool for **passive** reconnaissance. It gathers subdomains from public certificate-transparency logs and OSINT sources, resolves them, and looks up open ports and known CVEs via Shodan's free InternetDB. **No packets are sent to the target** and no API keys are needed. Keys are optional and unlock extra sources.

Useful when you can't run port scans or DNS brute-forcing.

## 🚀 Features

- **8 free subdomain sources**, queried in parallel: crt.sh, crt.name, AnubisDB, HackerTarget, AlienVault OTX, BufferOver, URLScan.io, Wayback Machine CDX
- **Optional key-enhanced sources**: Shodan, SecurityTrails, VirusTotal, Censys, BinaryEdge
- **DNS resolution** with `dig`, `drill`, `host` or `nslookup` (whichever is installed)
- **Port, service and CVE data** from Shodan InternetDB (free, no key)
- **Output formats**: TXT, CSV, Markdown and JSON
- **Result caching** (24h) to spare rate limits, plus proxy support
- **Clean terminal UI**: numbered steps, a summary block, `NO_COLOR` and `--no-color` support

## 📋 Requirements

`bash` 3.2+, `curl`, `jq`, and one of `dig` / `drill` / `host` / `nslookup`. The script checks for these and prints install hints.

| OS | Install |
|----|---------|
| macOS | `brew install curl jq bind` |
| Debian / Ubuntu / Kali | `sudo apt-get install curl jq dnsutils` |
| RHEL / CentOS / Fedora | `sudo dnf install curl jq bind-utils` |

## 🛠️ Installation

```bash
git clone https://github.com/whatsdd/Subdomain-port-scanner-passive.git
cd Subdomain-port-scanner-passive
chmod +x subdomain_scanner.sh
./subdomain_scanner.sh example.com
```

## 💻 Usage

```
subdomain_scanner.sh [OPTIONS] <domain>

  -o, --output DIR       Output directory (default: recon_DOMAIN_TIMESTAMP)
  -n, --threads N        Parallel worker threads (default: 5)
  -T, --timeout SECS     curl max-time per request (default: 30)
  -d, --delay SECS       Delay between port API requests (default: 0.5)
  -f, --format FORMAT    all | json | csv | md (default: all)
  -v, --verbose          Debug output
  -q, --quiet            Errors only
  -p, --proxy URL        HTTP/HTTPS proxy for all requests
  -C, --no-cache         Disable result caching
  -k, --keys FILE        API keys config file
  -c, --no-color         Disable colors (also honours NO_COLOR)
  -V, --version          Show version
  -h, --help             Show help
```

Examples:

```bash
./subdomain_scanner.sh example.com
./subdomain_scanner.sh -v -n 10 -f json example.com
./subdomain_scanner.sh -q -o /tmp/scan -f csv example.com
./subdomain_scanner.sh -p http://127.0.0.1:8080 example.com
```

Any domain works: for crt.name the apex (eTLD+1) is derived automatically (handles `co.uk`-style suffixes) and results are filtered to the requested domain.

### Optional API keys

On first run a template is created at `~/.config/subdomain_scanner/keys.conf`. Uncomment the keys you have:

```
SHODAN_API_KEY=...
SECURITYTRAILS_API_KEY=...
VIRUSTOTAL_API_KEY=...
CENSYS_API_ID=...
CENSYS_API_SECRET=...
BINARYEDGE_API_KEY=...
```

## 📊 Output

```
recon_example.com_20260929_143022/
├── subdomains.txt              # Unique subdomains
├── subdomains_with_ips.csv     # Subdomain → IP mapping
├── ports_and_services.csv      # Ports, tags and CVEs per IP
├── results.json                # Full structured results
├── summary.md                  # Human-readable report
└── scan.log                    # Run log
```

Cache lives in `~/.cache/subdomain_scanner/` (24h TTL, disable with `-C`).

## 🔧 How It Works

1. **Discover**: queries all sources in parallel and merges the results.
2. **Resolve**: resolves each subdomain to IPs using the threaded worker pool.
3. **Enrich**: looks up each IP in Shodan InternetDB for ports, hostnames, tags and CVEs.
4. **Report**: writes the requested output formats and prints a summary.

## ⚠️ Rate limits

Free sources apply their own limits (crt.name allows 100 requests per IP per day; HackerTarget's free tier is also small). The cache and `--delay` help. A source that fails or is rate-limited is skipped, and the rest still run.

## 🤝 Contributing

Fork, branch, commit, open a PR. Ideas: DNS brute-force mode, Docker image, CI with ShellCheck, more sources.

## ⚠️ Legal Disclaimer

For authorized security testing and education only. Get explicit permission before assessing domains you don't own. The authors accept no liability for misuse.

## 📄 License

MIT. See [LICENSE](LICENSE).

## 🙏 Acknowledgments

[crt.sh](https://crt.sh/), [crt.name](https://crt.name/), [AnubisDB](https://anubisdb.com/), [HackerTarget](https://hackertarget.com/), [AlienVault OTX](https://otx.alienvault.com/), [URLScan.io](https://urlscan.io/), [Wayback Machine](https://web.archive.org/), [Shodan InternetDB](https://internetdb.shodan.io/).

## 📞 Support

[Issues](https://github.com/whatsdd/Subdomain-port-scanner-passive/issues) · [Homepage](https://ahmad.science/)
