# Applications - DNS, WEB, Video

## DNS - Domain Name Service

The internet has one **global system** for 
- _Addressing_ hosts by design : `IP`
- _naming_ hosts by accident, an afterthought : `Domain Names`

A great analogy : Let phone numbers be IP addresses then the contact you saved it under are the domain names.

### IP and Domain Names

Using Internet Service can be divided into four logical steps :
1. A person has namae of entity aka she wants to access : `www.ethz.ch`
2. She invokes an application to perform the task : `Chrome`
3. The applicaiton invokes DNS to resolve the name into an IP address : `129.132.19.216`
4. The application invokes transport protocol to establish an app-to-app connection 

The DNS system is a **distributed database** which enables resolving a name to an IP address. In practice :
-  names can be mapped to more than one IP. e.g. for a service that is widely used, you can't possibly have only one ip. **Load-Balancing**
-  IP's can be mapped by more than one domain name. e.g. **reusing your machine** if your servive is small and want to reuse it

The question that arises is : *How does one resolve a name into an IP?*

Initially, some decades ago we had a file called `hosts.txt` that provided all name to address mappings. But there are several problems as one might already think : 
- **scalabaility** in term of **query load & speed**
- **management**
- **consistency** - just imagine trying to have millions of devices be up-to-date with hosts.txt
- **availbility**

There are some rules of thumbs to follow : 
1. When you need **more flexiblity** you add **a layer of indirection** `gethostbyname()`
2. When you need **more scalibility**, you add **a hierachical structure**

### DNS Hierarchies
To scale, DNS uses **three** intertwined hierarchies :
1. `naming structure` : addresses are hierarchical
2. `management` : hierarchy of authority over names
3. `infrastructure` : hierarchy of DNS servers

---

***Naming Structure***
Top Level Domain (**TLD**) sit at the top e.g. all the under the root ones in the following figure. We can look at Domains as subtrees : 

<p align = "center">
    <img src="./Images/Image_11.PNG" width = "700px">
</p>

A name. e.g. `inf.ethz.ch` represents a leaf-to-root patah in the hierarchy : 

<p align = "center" >
    <img src="./Images/Image_12.PNG" width = "700px">
</p>

---

***Management*** : The DNS system is hierarchically administered
- `root` is managed by IANA
- `ch.` is managed by *The Swiss Education & Research Network*
- `ethz.` is managed by *ETH Zürich Informatikdienste ICT-Networks*

There are a total of 13 root servers that serve as root and are managed professionally. `TLDs` server are also managed professionally by mostly private or non-profit-organisations

<p align = "center" >
    <img src="./Images/Image_13.PNG" width = "400px">
</p>

The bottom (and bulk) of the hierarchy is managed by Interenet Service Providers or locally e.g. `ehtz`.

---

Each root server knows the address of all TLD servers. In the following example `a.root-servers.ne ch.` knows all the addresses of all children.

```powershell
$ dig @a.root-servers.net ch.
(server) (name)
ch.         172800 IN    NS  a.nic.ch.
ch.         172800 IN    NS  b.nic.ch.
ch.         172800 IN    NS  c.nic.ch.
ch.         172800 IN    NS  d.nic.ch.
ch.         172800 IN    NS  e.nic.ch.
ch.         172800 IN    NS  f.nic.ch.
ch.         172800 IN    NS  h.nic.ch.
```

Any `.ch` DNS server knows the addresses of all sub-domains. e.g. `ch` knows the addresses of `ethz` or `nzz`

---

**Glue Records**
Glue records are DNS records created at the domain’s registrar. The record provides a complete answer when the TLD nameserver returns a reference for an authoritative nameserver for a domain. For example, the domain name “example.net” has nameservers “ns1.example.net” “ns2.example.net”. To resolve the domain name, the DNS would query in order: root, TLD nameserver, and authoritative nameserver. However, by having the authoritative nameservers inside the domain itself, these nameservers cannot be found without outside assistance. This is called a ‘circular reference’. Creating a glue record, an A record served by the TLD nameserver, avoids circular references and allows for both DNS name resolution and listing the nameservers inside the domain itself

<p align = "center" >
    <img src="./Images/Image_14.PNG" width = "700px">
</p>

More detail will be discussed in further lectures.

---

To ensure **availability**, each domain must have at least a primary and secondary DNS server. (In case one server is down). It needs to have the following properties :
- Ensure name service availability as long as one of the servers is up i.e. accessible under the same Domain name
- DNS queries can be load-balanced across the replicas i.e. split their work
- On Timeout, clients use alternate servers : exponential backoff when trying the same server i.e. when one server isn't working 

--- 

### DNS Queries and Components
Let's look at a specific example :  You've founded `next-startup.ch` and want to host it yourself, how do you insert it into the DNS?

1. You register `next-startup.ch` at a **registrar** $X$ e.g. Swisscom or GoDaddy 
2. Provide $X$ with the name IP of your DNS servers e.g. `[ns1.next-startup.ch, 129.132.19.253]` 
3. You set-up a DNS server `@129.132.19.352` define A records for *www*, MX records for *next-startup.ch*

Here is an overview of what Record hosts what value : 
<p align = "center" >
    <img src="./Images/Image_15.PNG" width = "400px">
</p>

--- 

Using DNS relies on two components : 
<p align = "center" >
    <img src="./Images/Image_16.PNG" width = "700px">
</p>

- DNS queries and replies using UDP (port 53). It's reliabaility is implemented by repeating requests
- A DNS server stores *Resource Records* composed of `(name, value, type, TTL)`.
- DNS resolution can either be recursive or iterative

---

Let's further look at those two different types of queries.

**Recursive Query**
When performing a *recusive* query, the client offloads the task of resolving to the server. Basically pushing task to the server and waiting for an answer.
<p align = "center" >
    <img src="./Images/Image_17.PNG" width = "700px">
</p>

**Iterative Query**
When performing a *iterative* query, the server only returns the address of the next server to query. 
<p align = "center" >
    <img src="./Images/Image_18.PNG" width = "700px">
</p>

We know that we can reduce loading times in a Computer when we cache some data and access it again. The same can be said for the resolution times. DNS also relies on caching.
- DNS servers caache responses to former queries and your cliend and the applications
- Authoritative servers associate a lifetime to each record : **Time-To-Live (TTL)**
- DNS records can only be cached for TTL seconds after which they must be cleared

As top-level servers rarely change & popular websites are visited often, caching is very **effective**. 
- Top 10% of names account for 70% of lookups
- 9% of lookups are unique - i.e. **Limit chace hit ratio to 91%** 
- Practical cache hit rates **~75%**

---

Some more facts about DNS :
- is often used for blocking/censoring
- is often used for monitroing/tracking
- is often behind security breachees 

Additional readings for DNS : 
- 1.4 Delay, Loss and throughput in packet switched networks
- 1.7 DNS - The internet's directory service

## WEB 