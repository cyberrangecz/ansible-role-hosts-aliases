# Ansible role - hosts-aliases
This role is adding records of hosts into the `/etc/hosts` file. It supports Linux systems.

[[_TOC_]]

## Requirements
* This role requires root access, so you either need to specify `become` directive as a global or while invoking the role.

    ```yml
    become: yes
    ```

## Parameters
Mandatory.

- `hosts_aliases_nodes_ip` - dictionary of nodes with corresponding IP address, which is added to `/etc/hosts`

## Example
This is the simplest example.

```yml
roles:
    - role: hosts-aliases
      vars:
          hosts_aliases_nodes_ip:
              node_1: 10.10.10.1
              node_2: 10.10.20.1
```

This example is suitable for KYPO-CRP.

```yml
- name: Set up hosts file for Linux nodes
  hosts: user_accessible_nodes:&ssh_nodes
  gather_facts: no

  pre_tasks:
      - set_fact:
            user_accessible_nodes_without_hidden: "{{ groups.user_accessible_nodes | difference(groups.hidden_hosts) }}"
      - set_fact:
            user_accessible_hostvars: "{{ hostvars | dict2items | selectattr('key', 'in', user_accessible_nodes_without_hidden) | map(attribute='value') }}"

  roles:
      - role: hosts-aliases
        become: yes
        vars:
            hosts_aliases_nodes_ip: "{{ user_accessible_hostvars | items2dict(key_name='inventory_hostname', value_name='user_network_ip') }}"
```
