# 🕵️‍♂️ YK Vhost Fuzzer

Small Bash wrapper around [`ffuf`](https://github.com/ffuf/ffuf) to quickly fuzz **virtual hosts (vhosts)** during web pentests, CTFs and labs.

> Author: **Youmbi Kameni Guyani (YK)** – Pentester & Red Teamer  

---

## ✨ Overview

One IP or domain can host many virtual websites (vhosts):

- `www.example.com`
- `admin.example.com`
- `dev.example.com`
- `staging.example.com`

These extra vhosts are often **not visible in the UI** and may not appear in DNS results, but they still exist on the web server.

`yk-vhost-fuzzer.sh` is a simple helper that:

- Uses `ffuf` to brute-force possible vhosts.
- Automatically builds a `Host` header like:  
  `Host: FUZZ.example.com`
- Lets you focus on results instead of remembering the full `ffuf` syntax.

---

## 🛠 Requirements

- Linux (tested on Kali)
- `bash`
- [`ffuf`](https://github.com/ffuf/ffuf)

On Kali, you can install ffuf with:

```bash
sudo apt update
sudo apt install ffuf

