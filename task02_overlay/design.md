# Design Overlay Role Playbook

## Step 1 - Examine overlay playbook
In this task you will examine the playbook that calls the `cisco.dcnm.dcnm_vrf` and `cisco.dcnm.dcnm_network` Ansible modules. 
This playbook will be used to create the VRF and Networks that are part of the overlay. Open file `roles/task02_overlay/tasks/main.yml`

`roles/task02_overlay/tasks/main.yml`

```
---
# -------------------
# CREATE VRF SECTION
# -------------------

- name: update attachments of vrfs
  set_fact:
    update_list: "{{ update_list + update }}"
  loop: "{{ vrfs }}"
  loop_control:
    label: "{{ item.vrf_name }}"
    index_var: i
  vars:
    update_list: []
    update:
      - path: vrfs[{{ i }}].attach
        value: "{{ vrf_attach_group[item.attach_group] }}"
  tags:
    - vrf
    - overlay

- name: update VRF config
  ansible.utils.update_fact:
    updates: "{{ update_list }}"
  register: updated_vrfs
  tags:
    - vrf
    - overlay

- name: current vrf config
  debug:
    msg: "{{ item }}"
  loop: "{{ updated_vrfs.vrfs }}"
  loop_control:
    label: "{{ item.vrf_name }}"
  tags:
    - vrf
    - overlay

- name: create vrfs
  dcnm_vrf:
    fabric: "{{ fabric.name }}"
    config: "{{ updated_vrfs.vrfs }}"
  tags:
    - vrf
    - overlay

# -----------------------
# CREATE NETWORK SECTION
# -----------------------

- name: update attachments of network
  set_fact:
    update_list: "{{ update_list + update }}"
  loop: "{{ networks }}"
  loop_control:
    label: "{{ item.net_name }}"
    index_var: i
  vars:
    update_list: []
    update:
      - path: networks[{{ i }}].attach
        value: "{{ attach_group[item.attach_group] }}"
  tags:
    - overlay
    - network

- name: update network config
  ansible.utils.update_fact:
    updates: "{{ update_list }}"
  register: updated_network
  tags:
    - overlay
    - network

- name: current netowrk config
  debug:
    msg: "{{ item }}"
  loop: "{{ updated_network.networks }}"
  loop_control:
    label: "{{ item.net_name }}"
  tags:
    - overlay
    - network

- name: create networks
  dcnm_network:
    fabric: "{{ fabric.name }}"
    config: "{{ updated_network.networks }}"
  tags:
    - overlay
    - network
```

## Step 2 - Define switch specific variables for the overlay
Open file `group_vars/stage/overlay.yml`. These variables define the switch specific settings for the overlay.

`group_vars/stage/overlay.yml`

```
---
vrf_attach_group:
  all_leaf:
    - ip_address: 10.15.17.12
    - ip_address: 10.15.17.13
attach_group:
  esxi:
    - ip_address: 10.15.17.12
      ports:
        - Port-channel10
    - ip_address: 10.15.17.13
      ports:
        - Port-channel10
```

## Step 3 - Examine global variables for the overlay
`overlay.yml` under the group_vars/all folder defines variables for all VRFs and Network objects that will be configured in the overlay. 
Open file `group_vars/all/overlay.yml`

```
---
vrfs:
  - vrf_name: &refvrf_devnet vrf_devnet
    vrf_id: 150001
    vlan_id: 2000
    attach: []
    attach_group: all_leaf
networks:
  - net_name: network_devnet1
    vrf_name: *refvrf_devnet
    net_id: 130001
    vlan_id: 2301
    vlan_name: network_devnet1_vlan2301
    gw_ip_subnet: "10.10.10.1/24"
    attach: []
    attach_group: esxi
  - net_name: network_devnet2
    vrf_name: *refvrf_devnet
    net_id: 130002
    vlan_id: 2302
    vlan_name: network_devnet2_vlan2302
    gw_ip_subnet: "10.10.11.1/24"
    attach_group: esxi
```
