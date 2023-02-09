# Setup CI/CD Pipeline
Now we need to setup the pipline stages in the **`.gitlab-ci.yml file`**. This file acts as the control file for defining all CI/CD jobs.

We also need to define the test cases that will be used to validate changes to the **staging** and **production** environments.

## Test cases
When managing your network with IaC, adding good test cases is critical. Your test code should be equal to or better than the production code. 
The main objective for IaC is to validate the code and configuration on a **staging environment** before deploying it to the **production environment**. This validation can reduce the chance of an outage dramatically.

In this lab, we will use an Ansible validation playbook for the following:

Verify that all VRFs are deployed and in the correct state
Verify that all Networks are deployed and in the correct state
**NOTE: The validation we do as part of this lab is just a simple example. The validation needed in a real staging and production environment should include as many tests as required in order to ensure that what you are deploying to the production environment will not break your system.**

## Step 1 - Examine the Ansible validation playbook
Open **`roles/verify/tasks/main.yml`**:

```
---
- name: verify | check if all VRFs are deployed
  block:
    - name: verify | query all VRFs from {{ fabric.name }}
      dcnm_rest:
        path: "/appcenter/cisco/ndfc/api/v1/lan-fabric/rest/top-down/fabrics/{{ fabric.name }}/vrfs"
        method: GET
      register: result
    - name: verify | check if status is DEPLOYED
      assert:
        that:
          - item.vrfStatus != "OUT-OF-SYNC"
        quiet: true
      loop: "{{ result.response.DATA }}"
  tags:
    - verify

- name: verify | check if all Networks are deployed
  block:
    - name: verify | query all Networks from {{ fabric.name }}
      dcnm_rest:
        path: "/appcenter/cisco/ndfc/api/v1/lan-fabric/rest/top-down/fabrics/{{ fabric.name }}/networks"
        method: GET
      register: result
    - name: verify | check if status is DEPLOYED
      assert:
        that:
          - item.networkStatus != "OUT-OF-SYNC"
        quiet: true
      loop: "{{ result.response.DATA }}"
  tags:
    - verify
```

Examine the playbook above. We are using the **`dcnm_rest`** module from the collection to query the VRF's and Networks on NDFC and make sure they are deployed properly. The dcnm_rest module can be used to call any REST API exposed by NDFC but we recommend using other higher level function modules in the collection first if they are available for your particuler automation workflow.

## Step 2 - Add a new VRF and Network
We now want to add a new VRF and two more Networks that will automatically get deployed and verified in our staging environment when we trigger the CI pipeline in the next section.

Open **`group_vars/all/overlay.yml`**:

Visual Studio Code Terminal

`code -r /home/cisco/CiscoLive/DEVWKS-3928/group_vars/all/overlay.yml`

>## Quick Tip
>It's probably easiest to just copy and replace the entire contents of the file with the contents below.

```
---
vrfs:
  - vrf_name: &refvrf_devnet vrf_devnet
    vrf_id: 150001
    vlan_id: 2000
    attach: []
    attach_group: all_leaf
  # -----------------------------------------------
  # Insert the new VRF vrf_app1 below into the file
  # -----------------------------------------------
  - vrf_name: &refvrf_app1 vrf_app1
    vrf_id: 50001
    vlan_id: 2010
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
  # ------------------------------------------------------------------------
  # Insert the new Networks network_web and network_app below into the file
  # ------------------------------------------------------------------------
  - net_name: network_web
    vrf_name: *refvrf_app1
    net_id: 30001
    vlan_id: 2311
    vlan_name: network_web_vlan2311
    gw_ip_subnet: "10.1.1.1/24"
    attach: []
    attach_group: esxi
  - net_name: network_app
    vrf_name: *refvrf_app1
    net_id: 30002
    vlan_id: 2312
    vlan_name: network_web_vlan2312
    gw_ip_subnet: "10.1.2.1/24"
    attach: []
    attach_group: esxi
```

## Step 3 - Examine Top Level verify.yml Playbook
Similar to the top level **`build.yml`** playbook, we need a top level **`verify.yml`** playbook to drive the valiation tasks.

Open **`verify.yml`**:
```
---
- name: verify | verify the fabric is in the correct state
  hosts: ndfc
  gather_facts: false
  roles:
    - verify
```

## Step 4 - Define the CI/CD pipeline stages
### CI/CD pipeline
The pipeline defines the stages of the CI/CD workflow. Some stages should be triggered when a PR is created, and some should be triggered when the code is merged. Different VCSs have different methods to define the pipeline. Since we are using Gitlab, the pipeline stages are defined in file **`.gitlab-ci.yml`** in the project root folder.
Open the **`.gitlab-ci.yml`** file.

Visual Studio Code Terminal

`code -r /home/cisco/CiscoLive/DEVWKS-3928/.gitlab-ci.yml`

Copy the content below to the file and press Ctrl+s to save the file.

```
---
variables:
  environ: "staging"
stages:
  - lint
  - deploy
  - verify

ansible lint:
  stage: lint
  image:
    name: "cytopia/ansible-lint:5"
    entrypoint: [""]
  rules:
    - if: '$CI_PIPELINE_SOURCE == "push" && $CI_COMMIT_BRANCH == "stage"'
  before_script:
    - echo $ANSIBLE_VAULT_PASSWORD > .vault_pass
    - export ANSIBLE_PERSISTENT_COMMAND_TIMEOUT=1000
    - export ANSIBLE_PERSISTENT_CONNECT_TIMEOUT=1000
    - export no_proxy="localhost,127.0.0.1,172.25.74.47"
    - python3 -m pip install requests
    - echo $ANSIBLE_VAULT_PASSWORD > .vault_pass
  script:
    - ansible-galaxy -vvv collection install -r collections/requirements.yml
    - ansible-lint -p build.yml
    - ansible-lint -p group_vars/all/overlay.yml

deploy_on_stage:
  stage: deploy
  image:
    name: "cytopia/ansible"
    entrypoint: [""]
  rules:
    - if: $CI_PIPELINE_SOURCE == 'merge_request_event' && $CI_MERGE_REQUEST_TARGET_BRANCH_NAME == "main"
  before_script:
    - echo $ANSIBLE_VAULT_PASSWORD > .vault_pass
    - export ANSIBLE_PERSISTENT_COMMAND_TIMEOUT=1000
    - export ANSIBLE_PERSISTENT_CONNECT_TIMEOUT=1000
    - python3 -m pip install requests
  script:
    - ansible-playbook --version
    - ansible-galaxy -vvv collection install -r collections/requirements.yml
    - ansible-playbook -i hosts.stage.yml build.yml --vault-password-file .vault_pass

verify_on_stage:
  stage: verify
  image:
    name: "cytopia/ansible"
    entrypoint: [""]
  rules:
    - if: $CI_PIPELINE_SOURCE == 'merge_request_event' && $CI_MERGE_REQUEST_TARGET_BRANCH_NAME == "main"
  before_script:
    - echo $ANSIBLE_VAULT_PASSWORD > .vault_pass
    - python3 -m pip install requests
  script:
    - sleep 10
    - ansible-playbook --version
    - ansible-galaxy -vvv collection install -r collections/requirements.yml
    - ansible-playbook -i hosts.stage.yml verify.yml  --vault-password-file .vault_pass

deploy_on_prod:
  stage: deploy
  image:
    name: "cytopia/ansible"
    entrypoint: [""]
  rules:
    - if: '$CI_PIPELINE_SOURCE == "push" && $CI_COMMIT_BRANCH == "main"'
  before_script:
    - echo $ANSIBLE_VAULT_PASSWORD > .vault_pass
    - export ANSIBLE_PERSISTENT_COMMAND_TIMEOUT=1000
    - export ANSIBLE_PERSISTENT_CONNECT_TIMEOUT=1000
    - python3 -m pip install requests
  script:
    - ansible-playbook --version
    - ansible-galaxy -vvv collection install -r collections/requirements.yml
    - ansible-playbook -i hosts.prod.yml build.yml --vault-password-file .vault_pass

verify_on_prod:
  stage: verify
  image:
    name: "cytopia/ansible"
    entrypoint: [""]
  rules:
    - if: '$CI_PIPELINE_SOURCE == "push" && $CI_COMMIT_BRANCH == "main"'
  before_script:
    - echo $ANSIBLE_VAULT_PASSWORD > .vault_pass
    - python3 -m pip install requests
  script:
    - sleep 10
    - ansible-playbook --version
    - ansible-galaxy -vvv collection install -r collections/requirements.yml
    - ansible-playbook -i hosts.prod.yml verify.yml  --vault-password-file .vault_pass
```

The pipeline file above defines 3 stages.

* lint stage
* deploy stage
* verify stage

## Lint Stage
The **lint** stage above will only run when you commit or push code to the **stage** branch. The **ansible-lint** tool runs against the **build.yml** and **group_vars/all/overlay.yml** files in our repository. Ansible Lint is a command-line tool for linting playbooks, roles and collections aimed towards any Ansible users. Its main goal is to promote proven practices, patterns and behaviors while avoiding common pitfalls that can easily lead to bugs or make code harder to maintain. This CI stage can be extended to lint any additional files as required.

## Deploy Stage
The **deploy** stage above will only run when a pull request or merge request is opened against the **main** branch or when code is committed or pushed to the **main** branch.
When a merge request is opened from the **stage** branch against the **main** branch, the **build.yml** playbook will run against the **staging environment**.
When the code from the merge request is pushed from the **stage** branch into the **main** branch, the **build.yml** playbook will run against the **production environment**.

## Verify Stage
The **verify** stage above will only run when a pull request or merge request is opened against the **main** branch or when code is committed or pushed to the **main** branch.
When a merge request is opened from the **stage** branch against the **main** branch, the **verify.yml** playbook will run against the **staging environment**.
When the code from the merge request is pushed from the stage branch into the main branch, the verify.yml playbook will run against the production environment.

All of these stages run in a docker container that are built with the ansible-lint, ansible-playbook and ansible-galaxy binaries.

We will tigger the various pipeline stages in the next section.

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/workflow_cicd_pipeline.png)
