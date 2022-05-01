Wireguard client
=========

This Ansible role configures a Wireguard client.

Requirements
------------

N/A

Role Variables
--------------

- wg_client_name: The name of the Wireguard client. Defaults to `wg0`.
- wg_client_enabled: Whether the Wireguard client is enabled. Defaults to `true`.
- wg_iface_cidr: The CIDR of the Wireguard interface.
- wg_client_priv: The private key of the Wireguard client.
- wg_peer_pub: The public key of the Wireguard peer.
- wg_preshared_key: The preshared key of the Wireguard client and peer.
- wg_peer_endpoint: The endpoint of the Wireguard peer.
- wg_peer_allowed_ips:  The allowed IPs of the Wireguard peer. Defaults to `0.0.0.0/0, ::/0`.
- wg_peer_keepalive:  The keepalive interval of the Wireguard peer. Defaults to `25`.

Dependencies
------------

N/A

Example Playbook
----------------

```yaml
---
- name: Configure wg client
  hosts: all

  vars:
    wg_client_name: wg-client-01
    wg_client_enabled: false
    wg_iface_cidr: 10.1.2.3/24
    wg_client_priv: priv
    wg_peer_pub: pub
    wg_preshared_key: sha
    wg_peer_endpoint: localhost:1234
    wg_peer_allowed_ips: 0.0.0.0/0
    wg_peer_keepalive: 10

  roles:
    - role: szaffarano.general.wireguard_client
```

License
-------

MIT
