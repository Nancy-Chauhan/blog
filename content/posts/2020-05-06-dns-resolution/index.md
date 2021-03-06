---
title: DNS Resolution
author: Nancy Chauhan
date: 2020-05-06
hero: ./images/dns.png
excerpt: DNS acts as the phonebook of the internet 🌐. It converts a web address such as "example.com" to an IP address, which computers use to connect. As a result, we don't have to remember complicated IP addresses 🤩.

---

*Amazing Sketch/Comic by* [Julia Evans!](https://twitter.com/b0rk?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor)

Recently was working around DNS and thought to put it here!

Computers work with numbers. Computers talk to another computer using a numeric address called IP address. Though structured and thus great for computers, it is tough for humans to remember.

DNS acts as the phonebook of the internet 🌐. It converts a web address such as "example.com" to an IP address, which computers use to connect. As a result, we don't have to remember complicated IP addresses 🤩.

We are trying to open example.com on a browser. A Typical DNS lookup goes like this:

1. The browser first looks up "example.com" in its DNS cache. If it is present, the browser uses the cached IP address and connects to "example.com". If not, then the browser goes to the next step.
2. Browser issues a `gethostbyname (3)` and passes the responsibility of name resolution to the operating system (OS). The OS    now becomes the resolver.
3. OS looks for the domain name in the system DNS cache. If found then it
   returns the IP address to the browser else the OS goes to the next step.
4. The OS looks into `\etc\hosts`, known as the hosts file. The hosts file is a method of maintaining hostname to IP address mapping from the ARPANET days. If an entry exists, the OS returns the IP address else it goes to the next step.
5. The OS tries to connect to your configured DNS Servers and sends a DNS query for "example.com". You can manually set your      DNS Servers, or your connected networks can configure it for you. The DNS server now becomes the resolver and has to return a response to the OS of the machine that has sent the DNS query.
6. The DNS server (resolver) looks into its DNS cache for the hostname. If it finds an entry, it returns the same to the calling machine. Else it goes to the next step.
7. The DNS server tries to connect to root nameserver (.) You can do `dig .` to find root nameserver your DNS server is trying to connect. At present, there are 13 root nameservers named with the letters "a" to "m" &mdash; `a.root-servers.net.`
    ```
    ➜ dig -t NS .

    ; <<>> DiG 9.10.6 <<>> -t NS .
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 45206
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 13, AUTHORITY: 0, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 4096
    ;; QUESTION SECTION:
    ;.              IN  NS

    ;; ANSWER SECTION:
    .           48  IN  NS  a.root-servers.net.
    .           48  IN  NS  d.root-servers.net.
    .           48  IN  NS  k.root-servers.net.
    .           48  IN  NS  g.root-servers.net.
    .           48  IN  NS  j.root-servers.net.
    .           48  IN  NS  c.root-servers.net.
    .           48  IN  NS  b.root-servers.net.
    .           48  IN  NS  m.root-servers.net.
    .           48  IN  NS  f.root-servers.net.
    .           48  IN  NS  h.root-servers.net.
    .           48  IN  NS  l.root-servers.net.
    .           48  IN  NS  e.root-servers.net.
    .           48  IN  NS  i.root-servers.net.

    ;; Query time: 80 msec
    ;; SERVER: 10.254.254.210#53(10.254.254.210)
    ;; WHEN: Wed May 06 22:51:43 IST 2020
    ;; MSG SIZE  rcvd: 239

    ```
8. Now the DNS server requests on of the above root nameserver for the TLD
   level root nameserver for TLD for ".com".
    ```
    ➜ dig @d.root-servers.net. -t NS com.

    ; <<>> DiG 9.10.6 <<>> @d.root-servers.net. -t NS com.
    ; (1 server found)
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 106
    ;; flags: qr rd; QUERY: 1, ANSWER: 0, AUTHORITY: 13, ADDITIONAL: 27
    ;; WARNING: recursion requested but not available

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 1450
    ;; QUESTION SECTION:
    ;com.               IN  NS

    ;; AUTHORITY SECTION:
    com.            172800  IN  NS  a.gtld-servers.net.
    com.            172800  IN  NS  b.gtld-servers.net.
    com.            172800  IN  NS  c.gtld-servers.net.
    com.            172800  IN  NS  d.gtld-servers.net.
    com.            172800  IN  NS  e.gtld-servers.net.
    com.            172800  IN  NS  f.gtld-servers.net.
    com.            172800  IN  NS  g.gtld-servers.net.
    com.            172800  IN  NS  h.gtld-servers.net.
    com.            172800  IN  NS  i.gtld-servers.net.
    com.            172800  IN  NS  j.gtld-servers.net.
    com.            172800  IN  NS  k.gtld-servers.net.
    com.            172800  IN  NS  l.gtld-servers.net.
    com.            172800  IN  NS  m.gtld-servers.net.

    ;; ADDITIONAL SECTION:
    a.gtld-servers.net. 172800  IN  A   192.5.6.30
    b.gtld-servers.net. 172800  IN  A   192.33.14.30
    c.gtld-servers.net. 172800  IN  A   192.26.92.30
    d.gtld-servers.net. 172800  IN  A   192.31.80.30
    e.gtld-servers.net. 172800  IN  A   192.12.94.30
    f.gtld-servers.net. 172800  IN  A   192.35.51.30
    g.gtld-servers.net. 172800  IN  A   192.42.93.30
    h.gtld-servers.net. 172800  IN  A   192.54.112.30
    i.gtld-servers.net. 172800  IN  A   192.43.172.30
    j.gtld-servers.net. 172800  IN  A   192.48.79.30
    k.gtld-servers.net. 172800  IN  A   192.52.178.30
    l.gtld-servers.net. 172800  IN  A   192.41.162.30
    m.gtld-servers.net. 172800  IN  A   192.55.83.30
    a.gtld-servers.net. 172800  IN  AAAA    2001:503:a83e::2:30
    b.gtld-servers.net. 172800  IN  AAAA    2001:503:231d::2:30
    c.gtld-servers.net. 172800  IN  AAAA    2001:503:83eb::30
    d.gtld-servers.net. 172800  IN  AAAA    2001:500:856e::30
    e.gtld-servers.net. 172800  IN  AAAA    2001:502:1ca1::30
    f.gtld-servers.net. 172800  IN  AAAA    2001:503:d414::30
    g.gtld-servers.net. 172800  IN  AAAA    2001:503:eea3::30
    h.gtld-servers.net. 172800  IN  AAAA    2001:502:8cc::30
    i.gtld-servers.net. 172800  IN  AAAA    2001:503:39c1::30
    j.gtld-servers.net. 172800  IN  AAAA    2001:502:7094::30
    k.gtld-servers.net. 172800  IN  AAAA    2001:503:d2d::30
    l.gtld-servers.net. 172800  IN  AAAA    2001:500:d937::30
    m.gtld-servers.net. 172800  IN  AAAA    2001:501:b1f9::30

    ;; Query time: 259 msec
    ;; SERVER: 199.7.91.13#53(199.7.91.13)
    ;; WHEN: Wed May 06 22:54:16 IST 2020
    ;; MSG SIZE  rcvd: 828

    ```
9. DNS server then requests one of the above root nameservers for the authoritative nameserver for the domain `example.com`. This set of nameservers host the addresses of the domain as well as any subdomains it may have.
    ```
    ➜ dig @a.gtld-servers.net. -t NS example.com

    ; <<>> DiG 9.10.6 <<>> @a.gtld-servers.net. -t NS example.com
    ; (1 server found)
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 1127
    ;; flags: qr rd; QUERY: 1, ANSWER: 0, AUTHORITY: 2, ADDITIONAL: 1
    ;; WARNING: recursion requested but not available

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 4096
    ;; QUESTION SECTION:
    ;example.com.           IN  NS

    ;; AUTHORITY SECTION:
    example.com.        172800  IN  NS  a.iana-servers.net.
    example.com.        172800  IN  NS  b.iana-servers.net.

    ;; Query time: 66 msec
    ;; SERVER: 192.5.6.30#53(192.5.6.30)
    ;; WHEN: Wed May 06 22:55:10 IST 2020
    ;; MSG SIZE  rcvd: 88
    ```
10. The DNS server requests the authoritative nameservers for IP addresses of the domain and returns the result to the system that sent it the DNS query.
    ```
    ➜ dig @a.iana-servers.net. -t A example.com

    ; <<>> DiG 9.10.6 <<>> @a.iana-servers.net. -t A example.com
    ; (1 server found)
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 5682
    ;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
    ;; WARNING: recursion requested but not available

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 4096
    ;; QUESTION SECTION:
    ;example.com.           IN  A

    ;; ANSWER SECTION:
    example.com.        86400   IN  A   93.184.216.34

    ;; Query time: 281 msec
    ;; SERVER: 199.43.135.53#53(199.43.135.53)
    ;; WHEN: Wed May 06 22:58:40 IST 2020
    ;; MSG SIZE  rcvd: 56
    ```

Using the IP address `93.184.216.34`, the web browser connects to the host.

Every stage maintains a cache for some number of seconds based on the `TTL` that every query returns. In the following DNS query result, the TTL is `86400` seconds

```
example.com.        86400   IN  A   93.184.216.34
```

A resolver can thus cache the contents of the query for 86400 seconds. This caching helps to speed up the process and reduces the load on DNS servers.

Originally posted at https://todayilearnt.xyz/posts/nancy/dns_resolution/