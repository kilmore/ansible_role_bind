# [dns](#dns)

Install and configure dns on your system.

Forked from [Robert De Bock ansible-role-dns](https://github.com/robertdebock/ansible-role-dns)

## [Example Playbook](#example-playbook)

Install's bind DNS server. Example zone defaults are used. 
```yaml
---
- name: InstallDNS
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: currax_
```

For verification `molecule/resources/verify.yml` runs after the role has been applied.
```yaml
---
- name: Verify
  hosts: all
  become: yes
  gather_facts: yes

  vars:
    _nslookup_package:
      Alpine: bind-tools
      Archlinux: dnsutils
      Debian: dnsutils
      RedHat: bind-utils

    nslookup_package: "{{ _nslookup_package[ansible_os_family] }}"

  tasks:
    - name: install nslookup
      package:
        name: "{{ nslookup_package }}"
        state: present

    - name: test resolving www.example.com
      command: nslookup www.example.com 127.0.0.1
```


## [Role Variables](#role-variables)
These variables are set in `defaults/main.yml`:
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

# An optional list of acls to allow recursion. ("any" and "none" are always available.)
dns_allow_recursion:
  - none

# An optional list of IPv4 on which the DNS server will listen. ("any" and "none" are always available.)
dns_options_listen_on:
  - any

# A optional list of IPv6 on which the DNS server will listen. ("any" and "none" are always available.)
dns_options_listen_on_v6:
  - any

# An optional list of IP which are allowed to query the server. ("any" and "none" are always available.)
# Default: "any"
# dns_options_allow_query:
#  - any
#  - 127.0.0.1

# An optional list of IP which are allowed to run a AXFR query. ("any" and "none" are always available.)
# Default: "none"
# dns_options_allow_transfer:
#   - none
#   - 172.16.0.1

# An optional setting to congifure the path where the pid file will be created.
# dns_pid_file: /var/run/named/named.pid

# An optional setting to forward traffic to other DNS servers.
# dns_options_forwarders:
#   - 1.1.1.1
#   - 8.8.8.8

# Another example thanks to @blaisep.
# dns_zones:
#   - name: lab.controlplane.info
#     ttl: 600
#     ns:
#       - name: ns.lab.controlplane.info.
#     mx:
#       - name: mail1.lab.controlplane.info.
#         priority: 10
#       - name: mail2.lab.controlplane.info.
#         priority: 20
#     records:
#       - name: ns
#         value: 192.168.254.27
#       - name: git
#         value: 192.168.254.19
#       - name: dl380
#         value: 192.168.254.27
#       - name: mail1
#         value: 192.168.123.123
#       - name: mail2
#         value: 192.168.123.123
#   - name: forwarded.lab.controlplane.info
#     ns:
#       - name: forwarded.lab.controlplane.info.
#     records:
#       - name: ns
#         value: 192.168.254.27
#       - name: "@"
#         value: 192.168.123.123
#     dns_zone_forwarders:
#       - 9.9.9.9
#       - 8.8.8.8
```

## [Requirements](#requirements)

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next release of Ansible.)


## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/robertdebock):

|container|tags|
|---------|----|
|Alpine  |all|
|Amazon Linux |all|
|Centos |7, 8|
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
