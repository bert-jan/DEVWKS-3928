# Design Policy Role Playbook
**NOTE:** This task is not required to complete the lab but if you have time, this bonus task introduces you to a few more of the powerful NDFC Ansible modules in the collection.

>## Check Branch
>Before starting this lab, make sure you are on the stage branch

Visual Studio Code Terminal

`git branch --show-current`

>## Quick Tip
>Before starting this section, it might be helpful to close out your file tabs at the top of your VSCode editor from the previous lab task. This is certainly not mandatory but might keep the flow more organized and uncluttered.

Step 1 - Create policy playbook
In this task you will create the playbook that uses the cisco.dcnm.dcnm_template and cisco.dcnm.dcnm_policy Ansible modules. These modules are used to define templates and then apply custom policy configuration based on the templates. Open file roles/task04_template_policy/tasks/main.yml

Visual Studio Code Terminal

`code -r /home/cisco/CiscoLive/DEVWKS-3928/roles/task04_template_policy/tasks/main.yml`

Copy the below content to the main.yml playbook file and press Ctrl+s to save the file.

**`roles/task04_template_policy/tasks/main.yml`**
```
---
# This main.yml file includes two task files that will be used to
# define and apply templates and policies
- { include: template_telemetry.yml, tags: ['telemetry'] }
- { include: template_variables.yml, tags: ['ntp_vars'] }
```
This role is structured a little differently then the previous roles. The main.yml file for the role will include various task files to configure the templates and policies.

## Step 2 - Populate the template_telemetry.yml task file.
Open file `roles/task04_template_policy/tasks/template_telemetry.yml`. This Ansible task file will contain a template defining telemetry configuration to be applied to the leaf devices in the fabric and a policy to use the template and actually apply it.

Visual Studio Code Terminal

`code -r /home/cisco/CiscoLive/DEVWKS-3928/roles/task04_template_policy/tasks/template_telemetry.yml`

Copy the content below to the file and press Ctrl+s to save the file.

**`roles/task04_template_policy/tasks/template_telemetry.yml`**
```
---

# Create Templates on NDFC
#
# -----------------------------------------------------------------------------
- name: Create Template To Enable Telemetry Feature
  cisco.dcnm.dcnm_template:
    state: merged
    config:
      - name: template_telemetry_feature
        description: "Template to enable Telemetry Feature"
        tags: "telemetry feature"
        content: |
          feature telemetry

- name: Create Template For Telemetry Configuration
  cisco.dcnm.dcnm_template:
    state: merged
    config:
      - name: template_telemetry
        description: "Template for configuring Telemetry"
        tags: "telemetry config"
        content: |
          telemetry
            certificate /bootflash/telegraf.crt telegraf
            destination-profile
              use-vrf management
            destination-group 101
              ip address 192.168.55.55 port 57101 protocol gRPC encoding GPB
            sensor-group 101
              data-source DME
              path sys/ch depth unbounded
            subscription 101
              dst-grp 101
              snsr-grp 101 sample-interval 10101

# Create Policies and Apply them to the leaf1 device
#
# -----------------------------------------------------------------------------
- name: Create and Apply Policy for Telemetry Configuration
  cisco.dcnm.dcnm_policy:
    fabric: "{{ fabric.name }}"
    state: merged
    deploy: true
    config:
      - name: template_telemetry_feature  # This must be a valid template name
        create_additional_policy: false  # Do not create a policy if it already exists
        priority: 1

      - name: template_telemetry  # This must be a valid template name
        create_additional_policy: false  # Do not create a policy if it already exists
        priority: 2

      - switch:
        - ip: "{{ device_leaf1 }}"

# Query To Make Sure Policies are Applied on the leaf1 device
#
# -----------------------------------------------------------------------------
- name: Query Policies
  cisco.dcnm.dcnm_policy:
    fabric: "{{ fabric.name }}"
    state: query
    deploy: true
    config:
      - name: template_telemetry_feature
      - name: template_telemetry
      - switch:
        - ip: "{{ device_leaf1 }}"
  register: result

- name: Display Query Result
  debug: msg="{{ result['response'] }}"
```

Examine the template_telemetry.yml task file above. This file will create two templates. One template to enable the telemetry feature and another template to define the telemetry configuration. Finally, policies will be created based on these two templates and applied to the leaf device. A query task is also included to display the policy after it gets applied to the leaf device.

You may have noticed that we are using the Fully Qualified Collection Name (FQCN) for the **`dcnm_template`** and **`dcnm_policy`** modules in the file tasks above. This is perfectly fine but the reason we did not need to do this when we defined the interface and overlay roles in task01 and task02 is because we specified the namespace under the meta/main.yml file for the role.

For Example:

**`roles/task02_overlay/meta/main.yml`**
```
---
collections:
  - cisco.dcnm
```
This is a nice way of providing a shortcut so you don't have to type the FQCN each time you add a task using the NDFC modules.

## Step 3 - Populate the template_variables.yml task file.
Open file **`roles/task04_template_policy/tasks/template_telemetry.yml`**. This Ansible task file contains a template that will allow you to substitute variables making it more flexible then the previous template.

Visual Studio Code Terminal

`code -r /home/cisco/CiscoLive/DEVWKS-3928/roles/task04_template_policy/tasks/template_variables.yml`

Copy the content below to the file and press Ctrl+s to save the file.

**`roles/task04_template_policy/tasks/template_variables.yml`**
```
---

- name: Create and Apply NTP Server Config using the ntp_server NDFC template
  cisco.dcnm.dcnm_policy:
    fabric: "{{ fabric.name }}"
    state: merged
    config:
      - switch:
        - ip: "{{ device_leaf1 }}"
          policies:
            - name: ntp_server
              create_additional_policy: false
              priority: 4
              policy_vars:
                NTP_SERVER: 10.55.0.1
                NTP_SERVER_VRF: management

        - ip: "{{ device_leaf2 }}"
          policies:
            - name: ntp_server
              create_additional_policy: false
              priority: 4
              policy_vars:
                NTP_SERVER: 10.66.0.2
                NTP_SERVER_VRF: management

        - ip: "{{ device_spine }}"
          policies:
            - name: ntp_server
              create_additional_policy: false
              priority: 4
              policy_vars:
                NTP_SERVER: 10.188.0.55
                NTP_SERVER_VRF: management
  vars:
      ansible_command_timeout: 60
      ansible_connect_timeout: 60
```

The **template_variable.yml** task file above is a bit more flexible then the telemetry template we created. This task uses a pre-existing template on the NFDC controller called ntp_server that allows passing of variable data. This allows custom per device parameters to be passed to the template/policy. In this particular case, the policy is created using the variables **NTP_SERVER** and **NTP_SERVER_VRF** (See lines 33 and 34).

We are using the same ntp_server template but using the variables to configure different NTP_SERVER addresses on the spine and leaf devices. In pracice you would probaby want to use the same NTP_SERVER on devices in the same Fabric but this example is just to demonstrate the flexibility of templates / policies with variable data.

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task04_template_policy/workflow_task04.png)
