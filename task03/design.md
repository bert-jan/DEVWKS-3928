# Define Ansible Production Environment Files

## Check Branch
Before starting this lab, make sure you are on the stage branch

Visual Studio Code Terminal

`git branch --show-current`

>## Quick Tip
>Before starting this section, it might be helpful to close out your file tabs at the top of your VSCode editor from the previous lab task. This is certainly not mandatory but might keep the flow more organized and uncluttered.

## Step 1 - Populate Ansible NDFC Inventory File for Production Environment
All of our previous tasks have targeted the staging environment. In this section we need to define how Ansible will reach the NDFC controller to manage the **production environment**. 
For the staging environment we defined a file called `hosts.stage.yml`. Now we need to define a file called `hosts.prod.yml`. Open the file.

Copy the content below to the file and press **Ctrl+s** to save the file.

`hosts.prod.yml`
```
---
# Connection Information For Production Fabric
#
# This file defines how Ansible will connect to the NDFC controller
ndfc:
  children:
    prod:
      hosts:
        10.15.0.59:
          ansible_connection: ansible.netcommon.httpapi
          ansible_httpapi_use_ssl: true
          ansible_httpapi_validate_certs: false
          ansible_python_interpreter: auto_silent
          ansible_network_os: cisco.dcnm.dcnm
          ansible_user: admin
          ansible_password: "{{ ndfc_password }}"
          device_spine: 10.15.17.14
          device_leaf1: 10.15.17.15
          device_leaf2: 10.15.17.16
```

## Step 2 - Define Production Environment group_vars
There are 3 files we need to populate under the **group_vars/prod** directory with production environment specific values. They are `group_vars/prod/fabric.yml`, `group_vars/prod/interface.yml` and, `group_vars/prod/ovleray.yml`

Open file `group_vars/prod/fabric.yml`:

Visual Studio Code Terminal

`code -r /home/cisco/CiscoLive/DEVWKS-3928/group_vars/prod/fabric.yml`

Copy below the content to the file and press **Ctrl+s** to save it.


`group_vars/prod/fabric.yml`
```
---
fabric:
  name: fabric-prod
  asn: 65088
  inventory:
    - seed_ip: 10.15.17.14
      auth_proto: MD5
      user_name: admin
      password: ""
      max_hops: 0
      role: spine
      preserve_config: false
    - seed_ip: 10.15.17.15
      user_name: admin
      password: ""
      max_hops: 0
      role: leaf
      preserve_config: false
    - seed_ip: 10.15.17.16
      user_name: admin
      password: ""
      max_hops: 0
      role: leaf
      preserve_config: false
```

Open file `group_vars/prod/interface.yml`:

```
---
interface:
  vpc:
    - name: vpc10
      type: vpc
      switch:
        - 10.15.17.15
        - 10.15.17.16
      profile:
        admin_state: true
        mode: trunk
        peer1_members:
          - e1/1
        peer2_members:
          - e1/1
        pc_mode: 'active'
        bpdu_guard: true
        port_type_fast: true
        mtu: jumbo
    - name: vpc20
      type: vpc
      switch:
        - 10.15.17.15
        - 10.15.17.16
      profile:
          admin_state: true
          mode: trunk
          peer1_members:
            - e1/2
          peer2_members:
            - e1/2
          pc_mode: 'active'
          bpdu_guard: true
          port_type_fast: true
          mtu: jumbo
  loopback:
    - name: lo10
      type: lo
      switch:
        - 10.15.17.15
      profile:
        mode: lo
        int_vrf: "vrf_red"
        ipv4_addr: "10.10.10.101"
        route_tag: "12345"
    - name: lo10
      type: lo
      switch:
        - 10.15.17.16
      profile:
        mode: lo
        int_vrf: "vrf_red"
        ipv4_addr: "10.10.10.102"
        route_tag: "12345"
```

Open file `group_vars/prod/overlay.yml`:

```
---
vrf_attach_group:
  all_leaf:
    - ip_address: 10.15.17.15
    - ip_address: 10.15.17.16
attach_group:
  esxi:
    - ip_address: 10.15.17.15
      ports:
        - Port-channel10
    - ip_address: 10.15.17.16
      ports:
        - Port-channel10
```
