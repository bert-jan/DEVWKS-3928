# Trigger CD Pipeline
## Step 1 - Navigate to the Merge Request
To open the merge request page:

1. Click Merge requests on side menu
2. Then click PR we just created: initial setup

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/cd_pr.png)

## Step 2 - Merge the Request
On Merge Request details page:

1. Click Merge

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/merge.png)

Once code is merged, a new CD pipeline is triggered, click circle  in blow screen

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/cd_pipeline.png)

## Step 3 - Wait till pipeline is finished

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/cd_pipeline_success.png)

## Step 4 - Verify on NDFC
In your browser, open another instance of the browser or another tab. Copy the url of Nexus Dashboard:

* ND URL: https://10.15.0.59/appcenter/cisco/ndfc/ui/lan-fabric/fabrics
* Username: admin
* Password: cisco.123
>## Check fabric-prod
>This time open the fabric page and check fabric-prod, NOT fabric-stage. Remember the CD pipeline pushes changes to the production fabric.

Open fabric page and open **fabric-prod**, and verify VRF and Networks are provisioned:

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/prod_vrf.png)

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/prod_network.png)

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/workflow_cicd_pipeline.png)
