# Bootstrap

This role is meant to bootstrap new nodes to be managed by Ansible.

## Requirements

None

## Role Variables

Please refer to the [defaults](./defaults/main.yml) file for the list of variables:

- **bootstrap_setup_username**: The username to use for the bootstrap setup.
- **bootstrap_ansible_username**: The username to create as the ansible user.
- **bootstrap_ansible_passwordless**: Whether to use passwordless sudo.
- **bootstrap_delete_setup_user**: Whether to delete the bootstrap setup user once the setup is complete.
- **bootstrap_ansible_exclusive_key**: Whether remove any other ssh keys from the ansible user.

## Dependencies

None

## Example Playbook

```yml
---
- name: Bootstrap a new node to be managed by ansible
  hosts: "{{ bootstrap_hosts }}"
  gather_facts: false
  become: true

  vars:
    bootstrap_setup_username: <some existent username>
    bootstrap_ansible_pubkey: https://github.com/szaffarano.keys
    bootstrap_ansible_username: ansible
    bootstrap_ansible_passwordless: true
    bootstrap_ansible_password: >-
      {{
        lookup('community.general.passwordstore',
                'ansible/nodes/{{ inventory_hostname }}/users/{{ bootstrap_ansible_username }} missing=create')
      }}
    bootstrap_root_password: >-
      {{
        lookup('community.general.passwordstore',
                'ansible/nodes/{{ inventory_hostname }}/users/root missing=create')
      }}
  roles:
    - bootstrap
```

And finally execute the playbook:

```sh
$ ansible-playbook bootstrap.yaml \
  -e bootstrap_hosts=<some existent node in ansible configuration> \
  --ask-pass
```

This sentence will ask for the password of the provided `bootstrap_setup_username` and start the bootstrap process:
1. Create an `ansible` user.
2. Add the `bootstrap_ansible_pubkey` to the `ansible` user.
3. Configure `ansible` user to use passwordless sudo.
4. Set the provided password to the `ansible` user.
5. Set the provided password to the `root` user.

Note the use of `community.general.passwordstore` module to retrieve (or eventually create a new one and store it) the
password from the [password store](https://www.passwordstore.org/) or alternatively [gopass](https://www.gopass.pw/)
which implements the same interface.

## License

MIT
