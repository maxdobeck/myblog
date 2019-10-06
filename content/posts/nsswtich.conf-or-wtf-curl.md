---
title: "nsswitch.conf aka WTF curl?"
date: 2019-10-06T13:57:46-07:00
draft: true
---

I started getting a weird curl response after setting up a simple cloudflare worker:

```
curl https://dev-signups.sih.workers.dev/
curl: (7) Failed to connect to dev-signups.sih.workers.dev port 443: Connection refused
```

Other devices on my network successfully resolved this but the terminal and browsers on my main laptop failed to resolve this subdomain.  For the record I could get to cloudflare.com itself and workers.dev.

Moving `dns` up in the hosts listing of the `etc/nsswitch.conf` file resolved the issue.  Moved to from this:

`hosts:          files mdns4_minimal [NOTFOUND=return] prax dns myhostname`

To this:

`hosts:          files dns mdns4_minimal [NOTFOUND=return] prax myhostname`