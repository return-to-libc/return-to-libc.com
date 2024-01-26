+++
title = "DNS updates to Wireguard Endpoints"
date = "2024-01-26T23:00:00+01:00"
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["wireguard", "vpn", "dns"]
keywords = ["wireguard", "wg", "vpn", "dns", "endpoint"]
description = "WireGuard itself only resolves endpoint domain names when it starts up, so if you change the IP address of an existing WireGuard server, like when you replace an old server, or if you use DNS failover to provide redundancy among two or more WireGuard servers, and one server fails, clients of the old server will continue to try to connect to the old IP address without ever checking DNS for a new IP address."
showFullContent = false
+++

WireGuard itself only resolves endpoint domain names when it starts up, so if you change the IP address of an existing WireGuard server, like when you replace an old server, or if you use DNS failover to provide redundancy among two or more WireGuard servers, and one server fails, clients of the old server will continue to try to connect to the old IP address without ever checking DNS for a new IP address.

To solve this, we can use [reresolve-dns.sh](https://git.zx2c4.com/wireguard-tools/tree/contrib/reresolve-dns/reresolve-dns.sh) script with a cron each 1 minute.

All credit goes to Jason A. Donenfeld <Jason@zx2c4.com>

~~~
#!/bin/bash
# SPDX-License-Identifier: GPL-2.0
#
# Copyright (C) 2015-2020 Jason A. Donenfeld <Jason@zx2c4.com>. All Rights Reserved.

set -e
shopt -s nocasematch
shopt -s extglob
export LC_ALL=C

CONFIG_FILE="$1"
[[ $CONFIG_FILE =~ ^[a-zA-Z0-9_=+.-]{1,15}$ ]] && CONFIG_FILE="/etc/wireguard/$CONFIG_FILE.conf"
[[ $CONFIG_FILE =~ /?([a-zA-Z0-9_=+.-]{1,15})\.conf$ ]]
INTERFACE="${BASH_REMATCH[1]}"

process_peer() {
	[[ $PEER_SECTION -ne 1 || -z $PUBLIC_KEY || -z $ENDPOINT ]] && return 0
	[[ $(wg show "$INTERFACE" latest-handshakes) =~ ${PUBLIC_KEY//+/\\+}\	([0-9]+) ]] || return 0
	(( ($EPOCHSECONDS - ${BASH_REMATCH[1]}) > 135 )) || return 0
	wg set "$INTERFACE" peer "$PUBLIC_KEY" endpoint "$ENDPOINT"
	reset_peer_section
}

reset_peer_section() {
	PEER_SECTION=0
	PUBLIC_KEY=""
	ENDPOINT=""
}

reset_peer_section
while read -r line || [[ -n $line ]]; do
	stripped="${line%%\#*}"
	key="${stripped%%=*}"; key="${key##*([[:space:]])}"; key="${key%%*([[:space:]])}"
	value="${stripped#*=}"; value="${value##*([[:space:]])}"; value="${value%%*([[:space:]])}"
	[[ $key == "["* ]] && { process_peer; reset_peer_section; }
	[[ $key == "[Peer]" ]] && PEER_SECTION=1
	if [[ $PEER_SECTION -eq 1 ]]; then
		case "$key" in
		PublicKey) PUBLIC_KEY="$value"; continue ;;
		Endpoint) ENDPOINT="$value"; continue ;;
		esac
	fi
done < "$CONFIG_FILE"
process_peer
~~~
