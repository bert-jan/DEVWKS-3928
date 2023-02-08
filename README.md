# DEVWKS-3928
## Ansible NDFC Collection

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/ndfc-overview.png)

## Introduction to Ansible Content Collections
While a complete discussion around Ansible Content Collections is outside the scope of this Lab, this section provides some basic background information to ensure that you understand basic collection terms and functions. If you have used Ansible Content Collections in previous learning labs or other sessions, feel free to skip this page and move on.

## Ansible Content Collections
Ansible Content Collections are a packaging format for bundling and distributing Ansible content such as plugins, roles and modules. They can be released independent of other Ansible collections or the ansible-base engine so features can be made available sooner to users. They are installed using the **ansible-galaxy collection install** command

`ansible-galaxy collection install [namespace.collection]`

The NDFC Ansible collection namespace for this lab is cisco and the collection name is dcnm.

This represents the **Fully Qualified Collection Name(FQCN)** for the NDFC Ansible collection.

`ansible-galaxy collection install cisco.dcnm`

An Ansible Playbook for NDFC might look as follows,

```
- name: Configure NDFC VRF Object and Deploy To Fabric Switches
  cisco.dcnm.dcnm_vrf:
    fabric: cisco-live
    state: merged
    config:
      - vrf_name: vrf-red
        vrf_id: 9008011
        vlan_id: 2000
        attach:
          - ip_address: 192.168.1.224
            deploy: true
```

>### Module Names
>The Ansible NDFC modules in the collection were originally developed for DCNM. NDFC support was added later using the same code base but for >backward compatibility, the same FQCN is used. The collection's connection plugin automatically detects if the controller is running as DCNM >or NDFC so no changes are needed to the playbooks when automating for DCNM or NDFC.

In this example, the Ansible task will use the `cisco.dcnm.dcnm_vrf` module from the collection to configure a VRF object on the NDFC fabric named `cisco-live` and then attach and deploy the VRF to the fabric switch with IP address `129.168.1.224`

# Ansible Documentation
All documentation for the NDFC Ansible collection can be found on Ansible Galaxy. Navigate to [Ansible Galaxy](https://galaxy.ansible.com/cisco/dcnm) to see the list of modules and review the documentation.

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/galaxy_docs.png)
