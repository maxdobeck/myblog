---
title: "nsswitch.conf aka WTF curl?"
date: 2019-10-06T13:57:46-07:00
draft: false
---

I started getting a weird curl response after setting up a simple cloudflare worker:

```
curl https://dev-test.sih.workers.dev/
curl: (7) Failed to connect to dev-test.sih.workers.dev port 443: Connection refused
```

And if I went to the site I'd get a ERR_CONNECTION_REFUSED response on Chrome/Firefox. Other devices on my network successfully resolved this domain and I got the "Hello World" message but the terminal and browsers on my main laptop failed to resolve.  For the record I could get to cloudflare.com itself and workers.dev, so I had some ability to resolve the cloudflare.com domain.


### The solution
Moving `dns` up in the hosts listing of the `etc/nsswitch.conf` file resolved the issue.  Moved to from this:

`hosts:          files mdns4_minimal [NOTFOUND=return] prax dns myhostname`

To this:

`hosts:          files dns mdns4_minimal [NOTFOUND=return] prax myhostname`

I have not had this problem before nor have I had to edit this file before.  http://man7.org/linux/man-pages/man5/nsswitch.conf.5.html here's an overview of the man page for the nsswitch.conf file.  Again, I have not encountered this behavior prior to this.  I ended up doing this because changing the Firefox settings to "No proxy" resolved the issue in the browser.  After some time on stackoverflow/stackexhange someone said this helped as the DNS lookup is placed further up in the attempts to resolve a hostname.

### What I tried
- removing any proxy settings
- changing DNS nameservers to cloudflares 1.1.1.1 or Googles 8.8.8.8 (for ipv4 & ipv6)
- rebooting the laptop
- curling, telnet, ping, dig, nslookup of the workers.dev subdomain.  All came back the same, unable to resolve.
