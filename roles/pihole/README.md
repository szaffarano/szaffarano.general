Pihole role
=========

This Ansible role installs and configures a [pihole](https://pi-hole.net/) setup.  It assumes that the host has docker
and docker-compose installed.  You can use the role along with [geerlingguy. docker](https://github.com/geerlingguy/ansible-role-docker) role.

It also includes a [nginx](https://github.com/nginx-proxy/nginx-proxy) proxy setup to server the UI instead of the
builtin web
server.

Requirements
------------

N/A

Role Variables
--------------

- **pihole_host**: The hostname of the pihole server.
- **pihole_app_dir**: The root directory where the pihole application is installed (docker-compose files).
- **pihole_etc_dir**: Directory to store the pihole etc configuration files.
- **pihole_certs_dir**: Directory to store the certificates to host the pihole app.  In case you want to provide your
  own certificates, they should be named `{pihole_host}.crt` and `{pihole_host}.key`.
- **pihole_dnsmasq_dir**: Directory to store the dnsmasq configuration files.
- **pihole_logs_dir**: Directory to store the pihole logs.
- **pihole_log**: Name of the pihole main log file.
- **pihole_environment**: Map with environment variables to be set in the pihole container.  More info
  [here](https://github.com/pi-hole/docker-pi-hole#environment-variables=).
- **pihole_dns_cnames**:  Common names to configure in the dnsmasq configuration file.  Map key is the cname register
  and the map value is the A register, eg:
  ```
  pihole_dns_cnames:
    mycnamedomain.home.arpa: mydomain.home.arpa
  ```
- **pihole_local_dns**: Extra hosts to add to pihole configuration.  All the inventory hosts will be added by default


Dependencies
------------

N/A

Example Playbook
----------------

```yaml
---
- name: Configure pihole
  hosts: my-pihole-host

  vars:
    pihole_host: my-pihole-host
    pihole_environment:
      FTLCONF_REPLY_ADDR4: 1.2.3.4
      PROXY_LOCATION: pihole
      VIRTUAL_HOST: my-pihole-host.home.arpa
      VIRTUAL_PORT: 80
      TZ: Europe/Stockholm
      PIHOLE_DNS_: 208.67.222.222;208.67.220.220
      REV_SERVER: true
      REV_SERVER_TARGET: 1.2.3.4
      REV_SERVER_CIDR: 1.2.3.4/24
      WEBPASSWORD: >-
        {{
            lookup('community.general.passwordstore', 'pihole/key/path missing=create')
        }}
      DHCP_ACTIVE: false

    pihole_dns_cnames:
      cnamed-host-one: host-one

    pihole_local_dns:
      1.2.3.4: some-host.home.arpa

    # docker role specific config, needed to install it in a raspberry pi
    docker_apt_arch: armhf
    docker_compose_arch: armv7

  roles:
    - geerlingguy.docker
    - szaffarano.general.pihole
```

License
-------

MIT
