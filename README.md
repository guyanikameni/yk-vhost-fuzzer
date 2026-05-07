# YK Vhost Fuzzer

A small Bash wrapper around `ffuf` for discovering **virtual hosts** during authorized web security testing, CTFs, and lab work.

> Author: **Youmbi Kameni Guyani (YK)**  
> Focus: Web reconnaissance, infrastructure pentesting, and practical tooling

---

## Overview

Modern web servers often host several applications behind the same IP address or domain. Some of these virtual hosts may not be visible through normal browsing or basic DNS enumeration.

Examples:

```text
www.example.com
admin.example.com
dev.example.com
staging.example.com
internal.example.com
```

`yk-vhost-fuzzer.sh` helps you quickly test for hidden vhosts by building the right `Host` header and running `ffuf` with a clean syntax.

Instead of typing the full command every time:

```bash
ffuf -H "Host: FUZZ.example.com" -w wordlist.txt -u http://example.com -fs 0
```

You can run:

```bash
./yk-vhost-fuzzer.sh example.com wordlist.txt 0
```

---

## Why this matters in pentesting

Vhost discovery can expose attack surfaces that are not visible from the main website, such as:

- admin panels
- staging environments
- development applications
- forgotten internal tools
- older versions of the same application

In real engagements, these hidden services are often weaker than the main production site because they may have poor authentication, outdated code, debug features, or test credentials.

---

## Features

- Simple `ffuf` wrapper for vhost fuzzing
- Automatically injects the `Host: FUZZ.<domain>` header
- Optional `-fs` response-size filtering
- Basic input validation
- Clear terminal banner and usage output
- Designed for Kali/Linux pentest workflows

---

## Requirements

- Linux or Kali Linux
- Bash
- [`ffuf`](https://github.com/ffuf/ffuf)

Install `ffuf` on Kali:

```bash
sudo apt update
sudo apt install ffuf -y
```

---

## Installation

```bash
git clone https://github.com/guyanikameni/yk-vhost-fuzzer.git
cd yk-vhost-fuzzer
chmod +x yk-vhost-fuzzer.sh
```

---

## Usage

```bash
./yk-vhost-fuzzer.sh <DOMAIN> <WORDLIST> [FS_FILTER]
```

Arguments:

| Argument | Description | Example |
|---|---|---|
| `DOMAIN` | Target domain | `example.com` |
| `WORDLIST` | Path to subdomain/vhost wordlist | `/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt` |
| `FS_FILTER` | Optional `ffuf -fs` value to filter common false positives | `0` |

---

## Example

```bash
./yk-vhost-fuzzer.sh example.com /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt 0
```

The script runs a command similar to:

```bash
ffuf -H "Host: FUZZ.example.com" -H "User-Agent: YK-PENTEST" -c -w /path/to/wordlist -u http://example.com -fs 0
```

---

## Typical workflow

1. Add the target IP and domain to `/etc/hosts` if needed.
2. Run the vhost fuzzer.
3. Watch for responses with different status codes, sizes, or word counts.
4. Re-test interesting vhosts manually in the browser and Burp Suite.
5. Add discovered vhosts to scope notes and continue enumeration.

Example `/etc/hosts` entry:

```text
10.10.10.10 example.com
```

---

## Pentester notes

When analyzing results, do not trust only status codes. A `200`, `301`, `302`, `401`, `403`, or even unusual response size can indicate an interesting vhost.

Good things to investigate:

- different response length
- different title
- authentication prompt
- redirect to admin or SSO
- debug/error page
- default framework page

---

## Legal notice

Use this tool only on systems where you have explicit authorization. This project is intended for legal penetration testing, security labs, and CTF environments.
