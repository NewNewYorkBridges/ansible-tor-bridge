# ansible-tor-bridge

This Ansible role allows you to deploy and configure multiple Tor Bridge nodes.

Currently this role is only supported on Debian. In future improvements we will support Red Hat systems.

## Prerequisites

Prepare your Ansible Hosts file. You may use an email address that will be used for the contact info as per Tor Project's [documentation](https://community.torproject.org/relay/setup/bridge/).

If you want to associate bridges to a different user you are free to create multiple groups as below.

```
[bridgesA]
host1 ip_priv=192.168.1.100 tor_port=1234 obfs4_port=12345

[bridgesA:vars]
email_address=alice@example.com

[bridgesB]
host2 ip_priv=192.168.1.101 tor_port=1234 obfs4_port=12345

[bridgesB:vars]
email_address=bob@example.com

[bridges:children]
bridgesA
bridgesB
```

Of course you can have a single group:

```
[bridges]
host1 ip_priv=192.168.1.100 tor_port=1234 obfs4_port=12345
host2 ip_priv=192.168.1.101 tor_port=1234 obfs4_port=12345

[bridges:vars]
email_address=alice@example.com
```

Last but least, do not forget to change the variables `tor_port` and `obfs4_port`. Please avoid the ports 9001 because it's commonly associated with Tor and censors may be scanning the Internet for this port.

## Usage

First, clone this repository. Use the following playbook:

```
---
- name: Deploy Tor Bridges
  hosts: "{{ servers }}"
  become: yes
  roles:
    - alphacorvus.ansible_tor_bridge
```

Deploy the Tor Bridge nodes use the following command: 

`ansible-playbook /etc/ansible/playbooks/deploy_bridge.yml -e "servers=bridges" -t install_all`

Optional: you can print out the full bridge line of your nodes using the following command (the output is redirected in `/tmp/bridge.txt`):

`ansible-playbook /etc/ansible/playbooks/deploy_bridge.yml -e "servers=bridges" -t bridge_line`

## License

GPLv3

## Authors Information

This role was created in 2022 by [Lilian BAZILLE](https://github.com/lilianbaz) and [AlphaCorvus](https://github.com/AlphaCorvus).