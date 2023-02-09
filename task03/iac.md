# What is IaC?
Infrastructure as Code(IaC) allows automation, provisioning and management of the technology stack. Manual tasks are translated into reusable, robust, distributable code. IaC relies on practices that have been successfully used for years in software development such as:

1. Version Control
2. Automated Testing
3. Release Tagging
4. Continuous Delivery

## So... Why is it needed?

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/why_iac.png)

### Scalability & Reliability:
You can replicate the process to scale as quickly as the code executes and resources are provisioned. A core component of IaC is to add tests/validation after each essential step in the workflow.

### Automation & Agility:
Reduce human error by embracing automation. Stop building things by hand. Put everything into repeatable code configuration (e.g., YAML for Ansible). Agility comes from the ability to have multiple teams committing code, submitting changes, testing, etc. in multiple places in the pipeline; all of which result in faster delivery and app deployment to meet business requirements.

### Higher ROI & Lower TCO:
Invest in automation today to gain time tomorrow. To lower your Total Cost of Ownership(TCO), IaC reduces your downtime (e.g., migrations, testing cycles, maintenance windows, outages, etc.), and consolidates your application development technologies into the minimum number of IaC orchestration tools as possible. This provides a quicker turnaround for catching mistakes using various checks and balances.

## How to get started:
A Version Control System(VCS) like Github/GitLab is a vital part of IaC. It provides a way to track changes and easily merge or revert changes. Most VCSs have CI/CD pipeline systems built-in which can be used to test your code and configuration before deploying it on the production network. There is a wide range of tools available which can be used to serve this purpose. in this lab, we will be using GitLab.

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/common_tools.png)

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/how_iac.png)

Whenever there is a code or config change it's a good idea to follow this general set of steps:

1. Changes should always be made in a `staging` branch before they go into the `control branch`. (The control branch is often called main)
2. In the `staging` branch, make changes and commit the code/config and then open a merge request against the `control branch`
3. The *CI pipeline* is triggered to test/validate the code/config changes on your `staging` environment
4. Assuming the CI pipeline succeeds, the changes can be merged back to the control or main branch
5. Merging to the control branch triggers the *CD pipeline* to deploy the changes to your `production` environment
**NOTE:** When using NX-OS, the staging environment can be simulated using Nexus 9000v virtual switches before depoloying to your production environment.

Now that you have a high level understanding of CI/CD let's build it out!
