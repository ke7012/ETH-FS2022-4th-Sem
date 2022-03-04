# 2.4 DNS — The Internet’s Directory Service 

One identifier for a Internet host is its **hostname**. Hostnames—such as www.facebook.com, www.google.com , gaia.cs.umass.edu —are mnemonic and are therefore appreciated by humans. Hosts are also identified by so-called **IP addresses**.

An IP address consists of four bytes and has a rigid hierarchical structure. An IP address looks like `121.7.106.83`, where each period separates one of the bytes expressed in decimal notation from 0 to 255. 

## 2.4.1 Services Provided by DNS
The main task of the Internet's **domain name system (DNS)** is to translate hostnames to IP addresses. The DNS is (1) a **distributed database** implemented in a hierarchy of DNS servers, and (2) an application-layer protocol that allows hosts to **query the distributed database**.

In order for the user’s host to be able to send an HTTP request message to the Web server, the user’s host must first obtain the IP address of `www.someschool.edu` 

1. The same user machine runs the client side of the DNS application.
2. The browser extracts the hostname, `www.someschool.edu` , from the URL and passes the hostname to the client side of the DNS application
3. The DNS client sends a query containing the hostname to a DNS server
4. The DNS client eventually receives a reply, which includes the IP address for the hostname
5. Once the browser receives the IP address from DNS, it can initiate a TCP connection to the HTTP server process located at port 80 at that IP address

The DNS adds an additional delay—sometimes substantial—to the Internet applications that use it. Other services provided by the DNS are :
- **Host Aliasing** : sometimes complicated hostnames (aka **canonical hostname**) have aliases. The DNS can obtain the canonical hostname for alias hostnames.
- **Mail Server aliasing** : DNS can be invoked by a mail application to obtain the canonical hostname for a supplied alias hostname as well as the IP address of the host
- **Load Distribution** : Busy sites are replicated over multiple servers with each having different IP address, the DNS database contains the set of IP addresses associated to the canonical hostname.


## 2.4.2 Overview of How DNS Works
1. Application in a user's host needs to translate hostname to IP address
2. Application will invoke the client side of DNS
3. DNS in user's host sends a query message into the network
4. All query and reply messages are sent within UDP datagrams to port 53
5. DNS in user's host recevies a reply message with desired mapping
6. mapping passed to the application

A simple design for DNS would have one DNS server that contains all the mappings. In this centralized design, clients simply direct all queries to the single DNS server, and the DNS server responds directly to the querying clients. Some problems of centralized design include 

- **A single point of failure**. If the DNS server crsahes, so does the internet
- **Traffic Volume** Needs to handle all DNS queries
- **Distant centralaized database** potentially long travel route
- **Maintenance** huge, frequently update urgent database

A centralized database in a single DNS server simply doesn’t **scale**. Consequently, the DNS is **distributed by design.**

### A Distributed, Hierarchical Database

The DNS uses a large number of servers, organized in a hierarchical fashion and distributed around the world. The mappings are distributed across the DNS servers. There are three classes of DNS servers :

1. **root** DNS servers - managed by 13 different organisations - provide IP addresses of TLD servers
2. **Top-level domain** TLD DNS Servers - e.g. `com`, `org`, `edu` .. country codes `jp`, `uk`
3. **authoritative** DNS servers - houses DNS records - most companies/universities have their own authoritative DNS server.

To determine the IP address for example `www.amazon.com` :
1. Client contactts one of the root servers -> returns IP addresses for TLD servers for top-level domain `com.`
2. Client contacts one of these TLD servers - returns IP address of an authoritative server for `amazon.com`
3. Client contacts authoritative server for `amazon.com` - returns IP address for hostname `www.amazon.com`

**Local DNS server** is typically "close to" the host.  When a host makes a DNS query, the query is sent to the local DNS server, which acts a proxy, forwarding the query into the DNS server hierarchy

Let's take a closer look on how to obtain an IP address. Suppose the host `cse.nyu.edu` desires the IP address of `gaia.cs.umass.edu`

1. The host `cse.nyu.edu` sends DNS query message to its local DNS server `dns.nyu.edu.` - message contains hostname to be translated 
2. Local DNS forwards the query to a root DNs server
3. Root DNS server takes note of `edu` suffix and returns to local DNS server a list of IP addresses for TLD servers responsible for `edu`
4. Local DNS server resends query to one those TLD servers
5. TLD server takes note of the `umass.edu` suffix and responds with IP address of authoritative DNS server for Uni. of Massachusetts - `dns.umass.edu`. 
6. Local DNS server resends query directly to `dns.umass.edu` - responds with IP address of `gaia.cs.umass.edu`

A total of 8 DNS messages were sent : four query and four reply messages. 

Note that obtaining an IP address of a hostname makes use of both **recursive queries** and **iterative queries**. The first query is recursive sine the query asks `dns.nyu.edu` to obtain the mapping on its behalf. The three subsequent queries are Iterative since all of the replies are directly returned to `dns.nyu.edu`. 

The query from the requesting host to the local DNS server is recursive, and the remaining queries are iterative.

### DNS Caching

DNS extensively exploits DNS caching in order to improve the delay performance and to reduce the number of DNS messages ricocheting around the Internet. 

In a query chain, when a DNS server receives a DNS reply (containing, for example, a mapping from a hostname to an IP address), it can cache the mapping in its local memory. If a hostname/IP address pair is cached in a DNS server and another query arrives to the DNS server for the same hostname, the DNS server can provide the desired IP address, even if it is not authoritative for the hostname. The cache needs to be discarded after a period of time.

## 2.4.3 DNS Records and Messages

DNS servers store `resource records (RRs)`, including RRs that provide hostname-to-IP address mappings. Each DNS reply message carries one or more RRs. 

### DNS Records

A resource record is a four-tuple that contains the following fields:
```
(Name, Value, Type, TTL)
``` 
**TTL** is the **time to live** of the resource record; it determines when a resource should be removed from a cache

- If `Type = A` then `Name` is a hostname and `Value` is the IP address for the hostname. Type A record provides **standard hostname**-to-ip address mapping. e.g. `(relay1.bar.foo.com, 145.37.93.126, A)`
- If `Type = NS` then `Name` is a domain and `Value` is the hostname of an authoritataive DNS server that knows how to obtain the IP addressses for hosts in the domaina. NS records are used to route DNS queries further along in the query chain. e.g. `(foo.com, dns.foo.com, NS)`
- If `Type=CNAME` , then Value is a canonical hostname for the alias hostname `Name`. This record can provide querying hosts the canonical name for a hostname. e.g. ` (foo.com, relay1.bar.foo.com, CNAME)`
- If `Type=MX` , then `Value` is the canonical name of a mail server that has an alias hostname `Name`. e.g. `(foo.com, mail.bar.foo.com, MX)`

### DNS Messages

DNS messages are composed of various fields.
- **Header Section** : The first field is a **16-bit number that identifies the query**. This **identifier** is copied into the reply message to a query, allowing the client to match received replies with sent queries.
- **Question Section** : contains information about query - includes name that is being queried - type field indicating type of question
- **Answer Section** : contains resource records for the name  
- **Authority Section** : contains recourd of other authoritative servers
- **Additional Section** : contains other helpful records - e.g. Type A record providing IP for canonical hostname of a mail server


### Inserting Records into the DNS Database

1. Register domain name `samplekekultra.com` at a **registrar**. A Registrar verifies the uniqueness of the domain name, enters it into the DNS Database for a small fee
2. Provide the registrar with names and IP addresses of primary and secondary authoritative DNS servers - Registrar makes sure that Type NS and Type A record are entered into the TLD com servers.
    ```
    (samplekekultra.com, dns1.samplekekultra.com, NS)
    (dns1.samplekekultra.com, 212.212.212.1, A)
    ```
3. Make sure Type A RRs for web server `www.samplekekultra.com` and Type MX RRs for mail server `mail.samplekekultra.com` aare entered into your authoritative DNS servers
