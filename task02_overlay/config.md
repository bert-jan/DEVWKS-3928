# Configure VRFs and Networks

## Step 1 - Modify the top level build.yml playbook to uncomment the task02_overlay role
We will now modify the top level `build.yml` playbook to include the task02_overlay role.

Uncomment the role on line 9 called **task02_overlay** in the file and press Ctrl+s to save it.

```
---
# This is the top level build playbook that runs the various
# Ansible roles that will be used to build out the fabric
- name: Build Out Fabric on NDFC
  hosts: ndfc
  gather_facts: false
  roles:
    - task01_interfaces
    - task02_overlay
    # - task04_template_policy
```

>## Be Careful!
>Indentation in Ansible Playbooks matters. Make sure the newly uncommented task is indented at the same level as the first task.

## Step 2 - Make sure you are in the root directory of the project folder

Visual Studio Code Terminal

`cd /home/cisco/CiscoLive/DEVWKS-3928`

>## Note
>Use the following password when prompted for the Ansible vault password in the next step

* Password: **cisco.123**

## Step 3 - Run the build.yml Ansible playbook

Visual Studio Code Terminal

`ansible-playbook -i hosts.stage.yml build.yml --ask-vault-password`

Output

```
➜  DEVWKS-3928 git:(stage) ✗ ansible-playbook -i hosts.stage.yml build.yml --ask-vault-password
Vault password:

PLAY [Build fabric on NDFC] **************************************************************************************************************************************

TASK [task01_interfaces : current vpc config] ********************************************************************************************************************
ok: [10.15.0.59] => (item=vpc10) => {
    "msg": "vpc10 of switch ['10.15.17.12', '10.15.17.13']"
}

TASK [task01_interfaces : Create a vPC interface between leaf1 and leaf2] ****************************************************************************************
ok: [10.15.0.59]

TASK [task01_interfaces : current loopback config] ***************************************************************************************************************
ok: [10.15.0.59] => (item=lo10) => {
    "msg": "lo10 of switch ['10.15.17.12']"
}
ok: [10.15.0.59] => (item=lo10) => {
    "msg": "lo10 of switch ['10.15.17.13']"
}

TASK [task01_interfaces : Create a Loopback interface on leaf1 and leaf2] ****************************************************************************************
ok: [10.15.0.59]

TASK [task02_overlay : update attachments of vrfs] ***************************************************************************************************************
ok: [10.15.0.59] => (item=vrf_devnet)

TASK [task02_overlay : update VRF config] ************************************************************************************************************************
changed: [10.15.0.59]

TASK [task02_overlay : current vrf config] ***********************************************************************************************************************
ok: [10.15.0.59] => (item=vrf_devnet) => {
    "msg": {
        "attach": [
            {
                "ip_address": "10.15.17.12"
            },
            {
                "ip_address": "10.15.17.13"
            }
        ],
        "attach_group": "all_leaf",
        "vlan_id": 2000,
        "vrf_id": 150001,
        "vrf_name": "vrf_devnet"
    }
}

TASK [task02_overlay : create vrfs] ******************************************************************************************************************************
changed: [10.15.0.59]

TASK [task02_overlay : update attachments of network] ************************************************************************************************************
ok: [10.15.0.59] => (item=network_devnet1)
ok: [10.15.0.59] => (item=network_devnet2)

TASK [task02_overlay : update network config] ********************************************************************************************************************
changed: [10.15.0.59]

TASK [task02_overlay : current netowrk config] *******************************************************************************************************************
ok: [10.15.0.59] => (item=network_devnet1) => {
    "msg": {
        "attach": [
            {
                "ip_address": "10.15.17.12",
                "ports": [
                    "Port-channel10"
                ]
            },
            {
                "ip_address": "10.15.17.13",
                "ports": [
                    "Port-channel10"
                ]
            }
        ],
        "attach_group": "esxi",
        "gw_ip_subnet": "10.10.10.1/24",
        "net_id": 130001,
        "net_name": "network_devnet1",
        "vlan_id": 2301,
        "vlan_name": "network_devnet1_vlan2301",
        "vrf_name": "vrf_devnet"
    }
}
ok: [10.15.0.59] => (item=network_devnet2) => {
    "msg": {
        "attach": [
            {
                "ip_address": "10.15.17.12",
                "ports": [
                    "Port-channel10"
                ]
            },
            {
                "ip_address": "10.15.17.13",
                "ports": [
                    "Port-channel10"
                ]
            }
        ],
        "attach_group": "esxi",
        "gw_ip_subnet": "10.10.11.1/24",
        "net_id": 130002,
        "net_name": "network_devnet2",
        "vlan_id": 2302,
        "vlan_name": "network_devnet2_vlan2302",
        "vrf_name": "vrf_devnet"
    }
}

TASK [task02_overlay : create networks] **************************************************************************************************************************
changed: [10.15.0.59]

PLAY RECAP *******************************************************************************************************************************************************
10.15.0.59                 : ok=10   changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

➜  DEVWKS-3928 git:(stage) ✗
```

Notice that the tasks from the task01_interface role ran but there was no change for those tasks because the NDFC controller is already configured for those tasks previously and in the correct state. 
The only change was for adding the VRFs and Networks defined in the task02_overlay role. 
This is known as idempotence. 
You can run the same tasks over and over again but they will not change the target system if the target system is already in the correct state.
