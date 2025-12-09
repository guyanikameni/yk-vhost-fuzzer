#!/bin/bash
#
# yk-vhost-fuzzer.sh
# Simple vhost fuzzer wrapper around ffuf
# Author: Youmbi Kameni (YK) - Pentester & Red Teamer
#

# ---------- COLORS ----------
RED="\e[31m"
GREEN="\e[32m"
YELLOW="\e[33m"
BLUE="\e[34m"
MAGENTA="\e[35m"
CYAN="\e[36m"
BOLD="\e[1m"
RESET="\e[0m"

# ---------- BANNER ----------
banner() {
    echo -e "${MAGENTA}${BOLD}"
    cat << "EOF"
 __   __ _  __      _           _   ______                _            
 \ \ / // |/ _\    | | __ _ ___| |_|  ___|__  _   _ _ __ | |_ ___ _ __ 
  \ V / | |\ \_____| |/ _` / __| __| |_ / _ \| | | | '_ \| __/ _ \ '__|
   | || | |_\ \_____| | (_| \__ \ |_|  _| (_) | |_| | | | | ||  __/ |   
   |_| |_|\__\__/    |_|\__,_|___/\__|_|  \___/ \__,_|_| |_|\__\___|_|  
EOF
    echo -e "${RESET}"
    echo -e "${CYAN}           YK Vhost Fuzzer  -  ffuf wrapper${RESET}"
    echo -e "${CYAN}           Author: ${BOLD}Youmbi Kameni Guyani (YK)${RESET}"
    echo
}

# ---------- HELP ----------
show_help() {
    banner
    echo -e "${BOLD}Usage:${RESET} $0 <DOMAIN> <WORDLIST> [FS Filter]"
    echo
    echo -e "${BOLD}Arguments:${RESET}"
    echo -e "  ${YELLOW}DOMAIN   ${RESET}- Target domain (ex: ${CYAN}SilverPlatter.thm${RESET})"
    echo -e "  ${YELLOW}WORDLIST ${RESET}- Path to wordlist (ex: ${CYAN}/usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt${RESET})"
    echo -e "  ${YELLOW}FS Filter${RESET}- (optional) ffuf ${CYAN}-fs${RESET} value to filter by size (default: ${CYAN}0${RESET})"
    echo
    echo -e "${BOLD}Description:${RESET}"
    echo -e "  This script fuzzes ${YELLOW}virtual hosts (vhosts)${RESET} using ${CYAN}ffuf${RESET}."
    echo -e "  It builds Host headers like: ${GREEN}FUZZ.DOMAIN${RESET} (ex: ${GREEN}admin.SilverPlatter.thm${RESET})."
    echo -e "  URL is automatically set to: ${CYAN}http://DOMAIN${RESET}"
    echo
    echo -e "${BOLD}Example:${RESET}"
    echo -e "  $0 ${CYAN}SilverPlatter.thm${RESET} ${CYAN}/usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt${RESET} ${CYAN}0${RESET}"
    echo
}

# ---------- HELP OPTION ----------
if [[ "$1" == "-h" || "$1" == "--help" ]]; then
    show_help
    exit 0
fi

# ---------- CHECK FFUF ----------
if ! command -v ffuf >/dev/null 2>&1; then
    banner
    echo -e "${RED}[!] ffuf is not installed or not in PATH.${RESET}"
    echo -e "    Install ffuf and try again."
    exit 1
fi

# ---------- ARG CHECK ----------
if [ "$#" -lt 2 ] || [ "$#" -gt 3 ]; then
    banner
    echo -e "${RED}[!] Error: Missing or incorrect arguments.${RESET}"
    echo
    show_help
    exit 1
fi

# ---------- VARS ----------
DOMAIN="$1"
WORDLIST="$2"
FS="${3:-0}"                # default FS filter = 0
URL="http://$DOMAIN"        # use domain, IP is resolved via /etc/hosts

# ---------- BASIC CHECKS ----------
if [ ! -f "$WORDLIST" ]; then
    banner
    echo -e "${RED}[!] Error: Wordlist file not found:${RESET} $WORDLIST"
    exit 1
fi

# ---------- RUN ----------
banner
echo -e "${BOLD}${BLUE}[*] Starting vhost fuzzing with ffuf${RESET}"
echo -e "${GREEN}    Domain   :${RESET} $DOMAIN"
echo -e "${GREEN}    Wordlist :${RESET} $WORDLIST"
echo -e "${GREEN}    URL      :${RESET} $URL"
echo -e "${GREEN}    FS Filter:${RESET} $FS"
echo

ffuf -H "Host: FUZZ.$DOMAIN" -H "User-Agent: YK-PENTEST" -c -w "$WORDLIST" -u "$URL" -fs "$FS"
