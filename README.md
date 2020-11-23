# [dns](#dns)

Install and configure a master DNS server. Does not yet support primary and secondary. 



## Configuraiton Options:
Below are options for configuruing DNS. 


### **Zone Records**
These are the heart of the seutp. Each record creates a zone file. 

```yaml
dns_zones:
  - name: localhost
    soa: localhost
    serial: 1
    refresh: 604800
    retry: 86400
    expire: 2419200
    ttl: 604800
    records:
      - name: "@"
        type: NS
        value: localhost.
      - name: "@"
        value: 127.0.0.1
      - name: "@"
        type: AAAA
        value: ::1
```

### **Recursion**
An optional list of acls to allow recursion. ("any" and "none" are always available.)
```yaml
dns_allow_recursion:
  - none
```

### **Listen On v4**
An optional list of IPv4 on which the DNS server will listen. ("any" and "none" are always available.)
```yaml
dns_options_listen_on:
  - any
```

### **Listen On v6**
A optional list of IPv6 on which the DNS server will listen. ("any" and "none" are always available.)
dns_options_listen_on_v6:
  - any

### **Listen on IP**
 An optional list of IP which are allowed to query the server. ("any" and "none" are always available.) Defaults to any.

```yaml
dns_options_allow_query:
  - any
  - 127.0.0.1
```

### **Allow Transfers **
An optional list of IP which are allowed to run a AXFR query. ("any" and "none" are always available.) Defaults to None

```yaml
dns_options_allow_transfer:
  - none
  - 172.16.0.1
```

**### PID Path**

An optional setting to congifure the path where the pid file will be created.
```yaml
dns_pid_file: /var/run/named/named.pid
```

**### Global Forwaders**

An optional setting to forward traffic to other DNS servers.
```yaml
dns_options_forwarders:
  - 1.1.1.1
  - 8.8.8.8
```

## Examples

Examples below demonstrate use

```yaml
---
# defaults file for dns

# Should the DNS server be a caching DNS server?
dns_caching_dns: yes

# A list of zones and properties per zone.
dns_zones:
  - name: localhost
    soa: localhost
    serial: 1
    refresh: 604800
    retry: 86400
    expire: 2419200
    ttl: 604800
    records:
      - name: "@"
        type: NS
        value: localhost.
      - name: "@"
        value: 127.0.0.1
      - name: "@"
        type: AAAA
        value: ::1

  - name: 127.in-addr.arpa
    ttl: 604800
    records:
      - name: "@"
        type: NS
        value: localhost.
      - name: 1.0.0
        type: PTR
        value: localhost.

  - name: 0.in-addr.arpa
    records:
      - name: "@"
        type: NS
        value: localhost.

  - name: 255.in-addr.arpa
    records:
      - name: "@"
        type: NS
        value: localhost.

  - name: example.com
    ttl: 604800
    ns:
      - name: dns1.example.com.
      - name: dns2.example.com.
    mx:
      - name: mail1.example.com.
        priority: 10
      - name: mail2.example.com.
        priority: 20
    records:
      - name: dns1
        value: 127.0.0.1
      - name: dns2
        value: 127.0.0.1
      - name: www
        value: 127.0.0.1
      - name: dns1
        value: 127.0.0.1
      - name: dns2
        value: 127.0.0.1
      - name: mail1
        value: 127.0.0.1
      - name: mail2
        value: 127.0.0.1

  - name: forwarded.example.com
    type: forward
    dns_zone_forwarders:
      - 1.1.1.1
      - 8.8.8.8
```


## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/robertdebock):

|container|tags|
|---------|----|
|Alpine  |all|
|Amazon Linux |all|
|Centos  |7, 8|
|debian |buster, bullseye|
|fedora  |all|
|ubuntu  |focal, bionic, xenial|

The minimum version of Ansible required is 2.9, tests have been done to:

- The previous version.
- The current version.
- The development version.

## [Exceptions](#exceptions)

Some variarations of the build matrix do not work. These are the variations and reasons why the build won't work:

| variation                 | reason                 |
|---------------------------|------------------------|
| EL | RedHat does not supply `bind` without a subscription. |
| openSuse | Some tasks will not get idempotent. |


## [License](#license)

Apache-2.0

## Credits
-- Shamelessly Forked from [Robert De Bock ansible-role-dns](https://github.com/robertdebock/ansible-role-dns). 
-- Modified to meet my needs. 