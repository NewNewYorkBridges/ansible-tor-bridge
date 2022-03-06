# ansible-tor-bridge

This Ansible role allows you to deploy and configure multiple Tor Bridge nodes.

## Supported Operating Systems

- Debian 11
- Ubuntu 20.4
- OpenBSD 6.9
- FreeBSD 12.3
- FreeBSD 13.0
- RHEL / CentOS / Rocky Linux 8
- RHEL / CentOS 7
- Fedora 35

## Installation

This role is available on Galaxy: https://galaxy.ansible.com/alphacorvus/ansible_tor_bridge

```
$ ansible-galaxy install alphacorvus.ansible_tor_bridge
```

## Usage

Prepare your Ansible Hosts file. You may use an email address that will be used for the contact info as per Tor Project's [documentation](https://community.torproject.org/relay/setup/bridge/).

If you want to associate bridges to a different user you are free to create multiple groups as below.

```
[bridgesA]
host1 tor_port=1234 obfs4_port=12345

[bridgesA:vars]
email_address=alice@example.com

[bridgesB]
host2 tor_port=1234 obfs4_port=12345

[bridgesB:vars]
email_address=bob@example.com

[bridges:children]
bridgesA
bridgesB
```

Of course you can have a single group:

```
[bridges]
host1 tor_port=1234 obfs4_port=12345
host2 tor_port=1234 obfs4_port=12345

[bridges:vars]
email_address=alice@example.com
```

Last but least, do not forget to change the variables `tor_port` and `obfs4_port`. Please avoid the ports 9001 because it's commonly associated with Tor and censors may be scanning the Internet for this port.

The following playbook can be used:

```
---
- name: Deploy Tor Bridges
  hosts: "{{ servers }}"
  become: yes
  roles:
    - alphacorvus.ansible_tor_bridge
```

Deploy the Tor Bridge nodes use the following command: 

```
$ ansible-playbook /etc/ansible/playbooks/deploy_bridge.yml -e "servers=bridges" -t install_all
```

**Optional**: you can print out the full bridge line of your nodes. You'll need to precise the public IP of your node with the variable `public_ip` (if your node has a public IP directly attached to an interface, you can do `public_ip=ansible_[ethX].ipv4.address`). The output is redirected in `/tmp/bridge.txt`:

```
$ ansible-playbook /etc/ansible/playbooks/deploy_bridge.yml -e "servers=bridges" -e "public_ip={{ ansible_ens3.ipv4.address }}" -t bridge_line
```

Output:
```
$ cat /tmp/bridge.txt
Bridge obfs4 14.XX.XX.XX:24XXX F17C53823026E4FD0DXF7D1448F23BAE084F255X cert=Xp7nXXXXX/egXo2YrHv6qOlRE9Ar8t6vvUVXXXXXXXXXXXXTLREiNXXXR8dvDyTgDz iat-mode=0
Bridge obfs4 145.XX.XX.XX:24XXX F17C62823426E4FD0DAF7D1498F3BA2084F544Z cert=Xp7nXXXXX/egXo2YrHv6qOlRE9Ar8t6vvUVXXXXXXXXXXXXTUEHiY6nZZdvDyTgDw iat-mode=0
Bridge obfs4 84.XX.XX.XX:2411X F17C64883026E4FD0DDF7D1418F81BA5084F525A cert=Xp7nXXXXX/egXo2YrHv6qOlRE9Ar8t6vvUVXXXXXXXXXXXXTLDViC6nUZZZyTgDy iat-mode=0
...
```

## About Vars

You will find default values in `default/main.yml` corresponding to parameters in the `torrc` config file. You can override the config in your host file, your playbook or in the `vars` folder. Modify the `vars` file corresponding to the operating system used.

## License

GPLv3

## Authors Information

This role was originally created in 2022 by [Lilian BAZILLE](https://github.com/lilianbaz) and [AlphaCorvus](https://github.com/AlphaCorvus).

Support for FreeBSD and OpenBSD added by [pjsier](https://github.com/pjsier).
