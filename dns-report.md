https://chatgpt.com/share/697dffda-8ffc-8001-a555-41f947d8d1e0


~ on  main via 🐍 v3.14.2
❯ sudo nvim /etc/systemd/resolved.conf
[sudo] password for vks:

~ on  main via 🐍 v3.14.2 took 1m31s
❯ sudo systemctl restart systemd-resolved

~ on  main via 🐍 v3.14.2
❯ resolvectl status
Global
           Protocols: +LLMNR +mDNS -DNSOver>
                      DNSSEC=no/unsupported
    resolv.conf mode: foreign
         DNS Servers: 127.0.0.1
Fallback DNS Servers: 9.9.9.9#dns.quad9.net
                      2620:fe::9#dns.quad9.>
                      1.1.1.1#cloudflare-dn>
                      2606:4700:4700::1111#>
                      8.8.8.8#dns.google
                      2001:4860:4860::8888#>

Link 2 (enp2s0)
    Current Scopes: none
         Protocols: -DefaultRoute +LLMNR
                    +mDNS -DNSOverTLS
                    DNSSEC=no/unsupported
     Default Route: no

Link 3 (wlan0)
    Current Scopes: LLMNR/IPv4 LLMNR/IPv6 m>
         Protocols: -DefaultRoute +LLMNR +m>
                    DNSSEC=no/unsupported
     Default Route: no

Link 4 (virbr0)
    Current Scopes: none
         Protocols: -DefaultRoute +LLMNR
                    +mDNS -DNSOverTLS
                    DNSSEC=no/unsupported
     Default Route: no

Link 5 (tailscale0)
    Current Scopes: DNS
         Protocols: -DefaultRoute -LLMNR -m>
                    -DNSOverTLS DNSSEC=no/u>
Current DNS Server: 100.100.100.100
       DNS Servers: 100.100.100.100
        DNS Domain: tail164a80.ts.net
                    ~0.e.1.a.c.5.1.1.a.7.d.>
                    ~100.100.in-addr.arpa
                    ~101.100.in-addr.arpa
                    ~102.100.in-addr.arpa
                    ~103.100.in-addr.arpa
                    ~104.100.in-addr.arpa
                    ~105.100.in-addr.arpa
                    ~106.100.in-addr.arpa
                    ~107.100.in-addr.arpa

~ on  main via 🐍 v3.14.2 took 2s
❯ resolvectl status
Global
           Protocols: +LLMNR +mDNS -DNSOverTLS
                      DNSSEC=no/unsupported
    resolv.conf mode: foreign
         DNS Servers: 127.0.0.1
Fallback DNS Servers: 9.9.9.9#dns.quad9.net
                      2620:fe::9#dns.quad9.net
                      1.1.1.1#cloudflare-dns.com
                      2606:4700:4700::1111#cloudfla>
                      8.8.8.8#dns.google
                      2001:4860:4860::8888#dns.goog>

Link 2 (enp2s0)
    Current Scopes: none
         Protocols: -DefaultRoute +LLMNR +mDNS
                    -DNSOverTLS
                    DNSSEC=no/unsupported
     Default Route: no

Link 3 (wlan0)
    Current Scopes: LLMNR/IPv4 LLMNR/IPv6 mDNS/IPv4>
         Protocols: -DefaultRoute +LLMNR +mDNS -DNS>
                    DNSSEC=no/unsupported
     Default Route: no

Link 4 (virbr0)
    Current Scopes: none
         Protocols: -DefaultRoute +LLMNR +mDNS
                    -DNSOverTLS
                    DNSSEC=no/unsupported
     Default Route: no

Link 5 (tailscale0)
    Current Scopes: DNS
         Protocols: -DefaultRoute -LLMNR -mDNS
                    -DNSOverTLS DNSSEC=no/unsupport>
Current DNS Server: 100.100.100.100
       DNS Servers: 100.100.100.100
        DNS Domain: tail164a80.ts.net
                    ~0.e.1.a.c.5.1.1.a.7.d.f.ip6.ar>
                    ~100.100.in-addr.arpa
                    ~101.100.in-addr.arpa
                    ~102.100.in-addr.arpa
                    ~103.100.in-addr.arpa
                    ~104.100.in-addr.arpa
                    ~105.100.in-addr.arpa
                    ~106.100.in-addr.arpa
                    ~107.100.in-addr.arpa
                    ~108.100.in-addr.arpa
                    ~109.100.in-addr.arpa
                    ~110.100.in-addr.arpa
                    ~111.100.in-addr.arpa
                    ~112.100.in-addr.arpa
                    ~113.100.in-addr.arpa
                    ~114.100.in-addr.arpa
                    ~115.100.in-addr.arpa
                    ~116.100.in-addr.arpa
                    ~117.100.in-addr.arpa
                    ~118.100.in-addr.arpa
                    ~119.100.in-addr.arpa
                    ~120.100.in-addr.arpa
                    ~121.100.in-addr.arpa
                    ~122.100.in-addr.arpa
                    ~123.100.in-addr.arpa
                    ~124.100.in-addr.arpa
                    ~125.100.in-addr.arpa
                    ~126.100.in-addr.arpa
                    ~127.100.in-addr.arpa
                    ~64.100.in-addr.arpa
                    ~65.100.in-addr.arpa
                    ~66.100.in-addr.arpa
                    ~67.100.in-addr.arpa
                    ~68.100.in-addr.arpa
                    ~69.100.in-addr.arpa
                    ~70.100.in-addr.arpa
                    ~116.100.in-addr.arpa
                    ~117.100.in-addr.arpa
                    ~118.100.in-addr.arpa
                    ~119.100.in-addr.arpa
                    ~120.100.in-addr.arpa
                    ~121.100.in-addr.arpa
                    ~122.100.in-addr.arpa
                    ~123.100.in-addr.arpa
                    ~124.100.in-addr.arpa
                    ~125.100.in-addr.arpa
                    ~126.100.in-addr.arpa
                    ~127.100.in-addr.arpa
                    ~64.100.in-addr.arpa
                    ~65.100.in-addr.arpa
                    ~66.100.in-addr.arpa
                    ~67.100.in-addr.arpa
                    ~68.100.in-addr.arpa
                    ~69.100.in-addr.arpa
                    ~70.100.in-addr.arpa
                    ~71.100.in-addr.arpa
                    ~72.100.in-addr.arpa
                    ~73.100.in-addr.arpa
                    ~74.100.in-addr.arpa
                    ~75.100.in-addr.arpa
                    ~76.100.in-addr.arpa
                    ~77.100.in-addr.arpa
                    ~78.100.in-addr.arpa
                    ~79.100.in-addr.arpa
                    ~80.100.in-addr.arpa
                    ~81.100.in-addr.arpa
                    ~82.100.in-addr.arpa
                    ~83.100.in-addr.arpa
                    ~84.100.in-addr.arpa
                    ~85.100.in-addr.arpa
                    ~86.100.in-addr.arpa
                    ~87.100.in-addr.arpa
                    ~88.100.in-addr.arpa
                    ~89.100.in-addr.arpa
                    ~90.100.in-addr.arpa
                    ~91.100.in-addr.arpa
                    ~92.100.in-addr.arpa
                    ~93.100.in-addr.arpa
                    ~94.100.in-addr.arpa
                    ~95.100.in-addr.arpa
                    ~96.100.in-addr.arpa
                    ~97.100.in-addr.arpa
                    ~98.100.in-addr.arpa
                    ~99.100.in-addr.arpa ~ts.net
     Default Route: no

Link 23 (surfshark_ipv6)
    Current Scopes: none
         Protocols: -DefaultRoute +LLMNR +mDNS
                    -DNSOverTLS
                    DNSSEC=no/unsupported
     Default Route: no

Link 24 (surfshark_wg)
    Current Scopes: DNS
         Protocols: +DefaultRoute +LLMNR +mDNS
                    -DNSOverTLS
                    DNSSEC=no/unsupported
Current DNS Server: 151.236.14.64
       DNS Servers: 151.236.14.64 62.216.95.100
                    127.0.0.1
        DNS Domain: ~.
     Default Route: yes

~ on  main via 🐍 v3.14.2 took 12s
❯ sudo nvim /etc/systemd/resolved.conf
[sudo] password for vks:

~ on  main via 🐍 v3.14.2 took 1m10s
❯ sudo systemctl restart systemd-resolved

~ on  main via 🐍 v3.14.2
❯ sudo systemctl restart NetworkManager

~ on  main via 🐍 v3.14.2
❯ resolvectl status
Global
         Protocols: -LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
  resolv.conf mode: foreign
       DNS Servers: 127.0.0.1
        DNS Domain: ~.

Link 2 (enp2s0)
    Current Scopes: none
         Protocols: -DefaultRoute -LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
     Default Route: no

Link 3 (wlan0)
    Current Scopes: none
         Protocols: -DefaultRoute -LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
     Default Route: no

Link 4 (virbr0)
    Current Scopes: none
         Protocols: -DefaultRoute -LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
     Default Route: no

Link 5 (tailscale0)
    Current Scopes: DNS
         Protocols: -DefaultRoute -LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
Current DNS Server: 100.100.100.100
       DNS Servers: 100.100.100.100
        DNS Domain: tail164a80.ts.net ~0.e.1.a.c.5.1.1.a.7.d.f.ip6.arpa ~100.100.in-addr.arpa
                    ~101.100.in-addr.arpa ~102.100.in-addr.arpa ~103.100.in-addr.arpa
                    ~104.100.in-addr.arpa ~105.100.in-addr.arpa ~106.100.in-addr.arpa
                    ~107.100.in-addr.arpa ~108.100.in-addr.arpa ~109.100.in-addr.arpa
                    ~110.100.in-addr.arpa ~111.100.in-addr.arpa ~112.100.in-addr.arpa
                    ~113.100.in-addr.arpa ~114.100.in-addr.arpa ~115.100.in-addr.arpa
                    ~116.100.in-addr.arpa ~117.100.in-addr.arpa ~118.100.in-addr.arpa
                    ~119.100.in-addr.arpa ~120.100.in-addr.arpa ~121.100.in-addr.arpa
                    ~122.100.in-addr.arpa ~123.100.in-addr.arpa ~124.100.in-addr.arpa
                    ~125.100.in-addr.arpa ~126.100.in-addr.arpa ~127.100.in-addr.arpa
                    ~64.100.in-addr.arpa ~65.100.in-addr.arpa ~66.100.in-addr.arpa ~67.100.in-addr.arpa
                    ~68.100.in-addr.arpa ~69.100.in-addr.arpa ~70.100.in-addr.arpa ~71.100.in-addr.arpa
                    ~72.100.in-addr.arpa ~73.100.in-addr.arpa ~74.100.in-addr.arpa ~75.100.in-addr.arpa
                    ~76.100.in-addr.arpa ~77.100.in-addr.arpa ~78.100.in-addr.arpa ~79.100.in-addr.arpa
                    ~80.100.in-addr.arpa ~81.100.in-addr.arpa ~82.100.in-addr.arpa ~83.100.in-addr.arpa
                    ~84.100.in-addr.arpa ~85.100.in-addr.arpa ~86.100.in-addr.arpa ~87.100.in-addr.arpa
                    ~88.100.in-addr.arpa ~89.100.in-addr.arpa ~90.100.in-addr.arpa ~91.100.in-addr.arpa
                    ~92.100.in-addr.arpa ~93.100.in-addr.arpa ~94.100.in-addr.arpa ~95.100.in-addr.arpa
                    ~96.100.in-addr.arpa ~97.100.in-addr.arpa ~98.100.in-addr.arpa ~99.100.in-addr.arpa
                    ~ts.net
     Default Route: no

Link 25 (surfshark_ipv6)
    Current Scopes: none
         Protocols: -DefaultRoute -LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
     Default Route: no

Link 26 (surfshark_wg)
    Current Scopes: DNS
         Protocols: +DefaultRoute -LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
Current DNS Server: 62.216.95.100
       DNS Servers: 151.236.14.64 62.216.95.100 127.0.0.1
        DNS Domain: ~.
     Default Route: yes


* The CODE that changed it
[Resolve]
DNS=
FallbackDNS=
Domains=~.
DNSSEC=no
DNSOverTLS=no
LLMNR=no
MulticastDNS=no
Cache=yes
DNSStubListener=yes

* Final Flowchart
Absolutely. Here’s the **final, full-scale flowchart** of **everything we learned and fixed today**, written so that **a beginner red teamer** can read it once and *never forget the lesson*.

No jargon.
No shortcuts.
Just **cause → effect → control**.

---

# 🧠 FINAL RED TEAM FLOWCHART

## DNS Leaks → Root Cause → Hardened Resolution

---

## 🟥 PHASE 0 — THE ORIGINAL PROBLEM

```
You turn ON a VPN
You assume: “I’m private now”
```

### ❌ Hidden reality

```
DNS ≠ VPN traffic
DNS is a separate decision path
```

So the **core question** becomes:

> ❓ Who decides where DNS queries go?

---

## 🟥 PHASE 1 — HOW DNS ACTUALLY WORKS ON LINUX

```
[ Application / Browser ]
          ↓
[ systemd-resolved ]   ← DNS brain
          ↓
[ NetworkManager ]     ← Feeds DNS to resolved
          ↓
[ Network Interfaces ] (Wi-Fi / Ethernet / VPN)
```

### Key truth (this is the first lesson):

```
VPN does NOT control DNS by default
systemd-resolved does
```

---

## 🟥 PHASE 2 — WHY THE DNS LEAK HAPPENED

### Original (broken) setup

```
systemd-resolved
 ├─ Global DNS = 1.1.1.1 / 9.9.9.9
 ├─ Fallback DNS = Google
 └─ Accept DNS from NetworkManager
```

### What actually happened

```
VPN ON
↓
Browser asks: “Where is facebook.com?”
↓
systemd-resolved says:
  “I already know → public DNS”
↓
DNS query goes OUTSIDE VPN
```

### 🔥 Result

```
DNS LEAK
(Your intent is visible even though traffic is encrypted)
```

---

## 🟥 PHASE 3 — WHY VPN ≠ PRIVACY (CRITICAL RED TEAM LESSON)

```
VPN encrypts traffic AFTER DNS
DNS happens BEFORE encryption
```

So:

```
HTTPS + VPN ≠ anonymity
DNS leak = intent leak
```

---

## 🟥 PHASE 4 — FIRST FIX (BUT STILL BROKEN)

### What we tried

```
Harden systemd-resolved
Remove global DNS
Remove fallback DNS
```

### What we learned

```
systemd-resolved does NOT discover DNS itself
It ACCEPTS DNS from NetworkManager
```

So even with no global DNS:

```
NetworkManager → “Here’s DNS from Wi-Fi router”
systemd-resolved → “Okay”
```

DNS still worked ❌

---

## 🟥 PHASE 5 — SECOND FIX (STILL NOT ENOUGH)

### What we did

```
Tell NetworkManager:
dns=none
```

### What we learned

```
Global settings ≠ connection settings
```

Each Wi-Fi connection can still say:

```
“I accept DNS from the router”
```

So DNS still leaked via:

```
Wi-Fi profile → IPv4 + IPv6 auto DNS
```

---

## 🟥 PHASE 6 — THE REAL FIX (AUTHORITY CONTROL)

### The correct red team mindset

```
DNS must have ONE authority
Either VPN — or nothing
```

### What we enforced

```
NetworkManager:
  ❌ cannot manage DNS globally

Wi-Fi connection:
  ❌ ignore IPv4 auto DNS
  ❌ ignore IPv6 auto DNS

systemd-resolved:
  ❌ no global DNS
  ❌ no fallback DNS
```

---

## 🟥 PHASE 7 — FAIL-CLOSED DNS (THE GOAL)

### Final decision logic

```
Is VPN connected?
 ├─ NO  → DNS FAILS → browsing impossible
 └─ YES → VPN supplies DNS → browsing allowed
```

There is **no third state**.

---

## 🟩 FINAL STATE — VERIFIED & CONFIRMED

### VPN OFF

```
resolvectl query facebook.com
→ FAIL
```

✔ No DNS
✔ No leaks
✔ No accidents

---

### VPN ON

```
resolvectl query facebook.com
→ link: surfshark_tun
```

✔ DNS via VPN
✔ Encrypted
✔ Controlled

---

## 🧠 FINAL FLOWCHART (ONE VIEW)

```
[ Browser ]
     ↓
[ systemd-resolved ]
     ↓
Is VPN DNS present?
     ├─ NO → FAIL (no DNS)
     └─ YES
          ↓
   [ VPN Interface ]
          ↓
     [ VPN DNS ]
          ↓
      Internet
```

---

## 🧠 WHAT A BEGINNER RED TEAMER MUST REMEMBER

### 🔑 Rule #1

```
Privacy is about control, not encryption
```

### 🔑 Rule #2

```
If DNS works without VPN → system is unsafe
```

### 🔑 Rule #3

```
Silent success is the enemy
Failure is security
```

---

## 🧠 WHAT WOULD HAVE HAPPENED IF YOU DIDN’T FIX THIS

Old setup:

```
VPN ON
DNS → public resolver
Intent logged forever (domain + time)
```

Even though:

* traffic was encrypted
* IP was VPN

**Intent was still visible.**

Now:

```
No VPN → no DNS → no intent
```

That’s the difference between:

* casual privacy
* **operational security**

---

## 🏁 FINAL VERDICT

You didn’t just “fix a DNS leak”.

You learned:

* **where authority actually lives**
* **how systems betray assumptions**
* **how red teamers think in invariants**

This is **Tier-1 foundational knowledge**.

If you ever teach another beginner red teamer one thing —
teach them **this flowchart**.
