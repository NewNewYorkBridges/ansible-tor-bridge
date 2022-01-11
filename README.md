# ansible-tor-bridge

This Ansible role allows you to deploy and configure multiple Tor Bridge nodes.

Currently this role is only supported on Debian. In future improvements we will support other distributions.

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

`ansible-playbook /etc/ansible/playbooks/deploy_bridge.yml -e "servers=bridges" -t install_all`

Optional: you can print out the full bridge line of your nodes. You'll need to precise the public IP of your node with the variable `public_ip` (if your node has a public IP directly attached to an interface, you can do `public_ip=ansible_[ethX].ipv4.address`). The output is redirected in `/tmp/bridge.txt`:

`ansible-playbook /etc/ansible/playbooks/deploy_bridge.yml -e "servers=bridges" -e "public_ip={{ ansible_ens3.ipv4.address }}" -t bridge_line`

## License

GPLv3

## Authors Information

This role was created in 2022 by [Lilian BAZILLE](https://github.com/lilianbaz) and [AlphaCorvus](https://github.com/AlphaCorvus).
