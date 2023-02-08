# Step 1 - Examine interface playbook

In this task you will examine the playbook that calls the `cisco.dcnm.dcnm_interface` Ansible module. This playbook will be used to create two vPC interfaces and a Loopback interface. Open file `roles/task01_interfaces/tasks/main.yml`

Visual Studio Code Terminal

`code -r /home/cisco/CiscoLive/DEVWKS-3928/roles/task01_interfaces/tasks/main.yml`

roles/task01_interfaces/tasks/main.yml
```
---
# Create/Configure vPC Interfaces
- name: Create vPC interfaces between leaf1 and leaf2
  dcnm_interface:
    fabric: "{{ fabric.name }}"
    config: "{{ interface.vpc }}"
  vars:
    ansible_command_timeout: 90
    ansible_connect_timeout: 90
  tags:
    - interface
    - vpc

# Create/Configure Loopback Interface
- name: Create a Loopback interface on leaf1 and leaf2
  dcnm_interface:
    fabric: "{{ fabric.name }}"
    config: "{{ interface.loopback }}"
  tags:
    - interface
    - loopback
```

# Step 2 - Create playbook variables for interfaces
If you examine the playbook above you will notice that there are several variable names inside of double curly braces.

For Example: `{{ interface.vpc }}`

File `interface.yml` under group_vars defines the playbook interface variables which will be replaced when the playbook gets run.

Open file `group_vars/stage/interface.yml`

Visual Studio Code Terminal

`code -r /home/cisco/CiscoLive/DEVWKS-3928/group_vars/stage/interface.yml`

Copy the content below to the file and press Ctrl+s to save the file.

>## Quick Tip
>You can hover over the Visual Studio Code Terminal example below to make a Copy button appear on the right hand side. Go ahead and try it now. Then just click the Copy button to copy the command and paste it into the terminal to bring up the file in your VSCode editor to make changes to the file. This functionality will be available in many of the tasks throughout this lab.

`group_vars/stage/interface.yml`

```
---
interface:
  vpc:
    - name: vpc10
      type: vpc
      switch:
        - 10.15.17.12
        - 10.15.17.13
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
        - 10.15.17.12
        - 10.15.17.13
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
        - 10.15.17.12
      profile:
        mode: lo
        int_vrf: "vrf_red"
        ipv4_addr: "10.10.10.101"
        route_tag: "12345"
    - name: lo10
      type: lo
      switch:
        - 10.15.17.13
      profile:
        mode: lo
        int_vrf: "vrf_red"
        ipv4_addr: "10.10.10.102"
        route_tag: "12345"
```

# Step 3 - Create playbook variables for fabric name
File `fabric.yml` under group_vars defines the `fabric.name` variable in the task. Open file `group_vars/stage/fabric.yml`

Visual Studio Code Terminal

`code -r /home/cisco/CiscoLive/DEVWKS-3928/group_vars/stage/fabric.yml`

Copy the content below to the file and press Ctrl+s to save the file.

`group_vars/stage/fabric.yml`

```
---
fabric:
  name: fabric-stage
  asn: 65088
  inventory:
    - seed_ip: 10.15.17.11
      auth_proto: MD5
      user_name: admin
      password: ""
      max_hops: 0
      role: spine
      preserve_config: false
    - seed_ip: 10.15.17.12
      user_name: admin
      password: ""
      max_hops: 0
      role: leaf
      preserve_config: false
    - seed_ip: 10.15.17.13
      user_name: admin
      password: ""
      max_hops: 0
      role: leaf
      preserve_config: false
```

We will be using the `fabric.name` variable from the file above in this lab. The other variables were used previously to create the initial state for this lab and are included here for your reference.

Notice that the name is `fabric-stage`. This will become important later in this lab.
