# Configure interfaces
## Step 1 - Examine the top level build.yml playbook
We will now examine the top level playbook called build.yml that will be used to call the role level playbook we just created in the previous Design Interface Playbook section.

Visual Studio Code Terminal

`code -r /home/cisco/CiscoLive/DEVWKS-3928/build.yml`

`build.yml`

```
---
# This is the top level build playbook that runs the various
# Ansible roles that will be used to build out the fabric
- name: Build Out Fabric on NDFC
  hosts: ndfc
  gather_facts: false
  roles:
    - task01_interfaces
    # - task02_overlay
    # - task04_template_policy
```
NOTICE task01_interfaces role is uncommented in the file above (line 8)

## Step 2 - Create Ansible Inventory File for Staging Topology
We are almost ready to run the **build.yml** playbook to configure the vPC and Loopback interfaces on NDFC but first, we need to configure the Ansible inventory file to tell Ansible how to reach the NDFC controller for the Staging topology

Visual Studio Code Terminal

`code -r /home/cisco/CiscoLive/DEVWKS-3928/hosts.stage.yml`

Copy the content below to the file and press Ctrl+s to save the file.

`hosts.stage.yml`

```
---
# Connection Information For Staging Fabric
#
# This file defines how Ansible will connect to the NDFC controller
ndfc:
  children:
    stage:
      hosts:
        10.15.0.59:
          ansible_connection: ansible.netcommon.httpapi
          ansible_httpapi_use_ssl: true
          ansible_httpapi_validate_certs: false
          ansible_python_interpreter: auto_silent
          ansible_network_os: cisco.dcnm.dcnm
          ansible_user: admin
          ansible_password: "{{ ndfc_password }}"
          device_spine: 10.15.17.11
          device_leaf1: 10.15.17.12
          device_leaf2: 10.15.17.13
```

The variables above provide important information that tells Ansible how to connect and authenticate with the NDFC controller. It also includes connection information for the devices in the fabric that it manages. Notice that the ndfc_password is passed in as a variable. In our lab, we are using Ansible vault to encyrpt the password.

## Step 3 - Make sure you are in the root directory of the project folder

Visual Studio Code Terminal

`cd /home/cisco/CiscoLive/DEVWKS-3928`

>** Note
>Use the following password when prompted for the Ansible vault password in the next step

Password: **cisco.123**

## Step 4 - Run the build.yml Ansible playbook

Visual Studio Code Terminal

`ansible-playbook -i hosts.stage.yml build.yml --ask-vault-password`

```
➜  DEVWKS-3928 git:(stage) ✗ ansible-playbook -i hosts.stage.yml build.yml --ask-vault-password
Vault password:

PLAY [Build Out Fabric on NDFC] *********************************************************************************************************************************************************************************************************************************************

TASK [task01_interfaces : Create vPC interfaces between leaf1 and leaf2] ****************************************************************************************************************************************************************************************************
changed: [10.15.0.59]

TASK [task01_interfaces : Create a Loopback interface on leaf1 and leaf2] ***************************************************************************************************************************************************************************************************
changed: [10.15.0.59]

PLAY RECAP ******************************************************************************************************************************************************************************************************************************************************************
10.15.0.59                 : ok=2    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

➜  DEVWKS-3928 git:(stage) ✗
```
