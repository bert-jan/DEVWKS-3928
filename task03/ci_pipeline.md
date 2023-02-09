# Trigger CI Pipeline
>## Check Branch
>Before starting this lab, make sure you are on the stage branch

## Step 1 - Commit code to local stage branch
Make sure you are in the root directory of the project folder

Visual Studio Code Terminal

**`cd /home/cisco/CiscoLive/DEVWKS-3928`**

First issue the **`git status .`** command to see the list of files that have been modified in the previous lab tasks.

Output:
```
➜  DEVWKS-3928 git:(stage) ✗ git status .
       On branch stage
       Changes not staged for commit:
         (use "git add ..." to update what will be committed)
         (use "git restore ..." to discard changes in working directory)
               modified:   build.yml
               modified:   group_vars/all/overlay.yml
               modified:   group_vars/prod/fabric.yml
               modified:   group_vars/prod/interface.yml
               modified:   group_vars/prod/overlay.yml
               modified:   group_vars/stage/fabric.yml
               modified:   group_vars/stage/interface.yml
               modified:   group_vars/stage/overlay.yml
               modified:   hosts.prod.yml
               modified:   hosts.stage.yml

    no changes added to commit (use "git add" and/or "git commit -a")
    ➜  DEVWKS-3928 git:(stage) ✗
```

>## Check Closely!
> Make sure your modified list of files matches the list of files above in the lab guide. If it does not then that means you missed a step or did not save one of the files you modified.

Now commit the code to your github repo.

Visual Studio Code Terminal

`git commit -a -m "initial setup"

Output
```
➜  DEVWKS-3928 git:(stage) ✗ git commit -a -m "initial setup"
[stage 304f3a9] initial setup
 15 files changed, 516 insertions(+), 4 deletions(-)
 rewrite hosts.prod.yml (100%)
 rewrite hosts.stage.yml (100%)
➜  DEVWKS-3928 git:(stage)
```

## Step 2 - Push local branch stage to remote

Visual Studio Code Terminal

`git push --set-upstream origin stage`

Output
```
➜  DEVWKS-3928 git:(stage) git push --set-upstream origin stage
Enumerating objects: 57, done.
Counting objects: 100% (44/44), done.
Delta compression using up to 8 threads
Compressing objects: 100% (28/28), done.
Writing objects: 100% (31/31), 4.88 KiB | 2.44 MiB/s, done.
Total 31 (delta 6), reused 0 (delta 0), pack-reused 0
remote:
remote: To create a merge request for stage, visit:
remote:   http://10.0.208.215/CL-POD16/DEVWKS-3928/-/merge_requests/new?merge_request[source_branch]=stage
remote:
To 10.0.208.215:CL-POD16/DEVWKS-3928.git
 * [new branch]      stage -> stage
branch 'stage' set up to track 'origin/stage'.
➜  DEVWKS-3928 git:(stage)
```

## Step 3 - Check the code on gitlab
Open gitlab on a new tab:

* Gitlab URL: http://10.0.208.215/CL-POD17/DEVWKS-3928

At the Gitlab login screen, login using your username and password:

* Username: user17
* Password: cisco.123

Select **stage** branch:

1. Click main
2. Click stage branch

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/gitlab_branch.png)

## Step 4 - Check to make sure the Lint Stage was run.
As soon as the code was committed to the stage branch, Ansible Lint validation was triggered, open pipelines:

1. Click CI/CD on the side menu
2. Then click Pipelines

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/pipeline.png)

Only the lint stage is triggered when committing the code to the stage branch, circle check icon  indicates pipeline succeeds
 
![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/validate.png)

To see details of the job Click passed under the Status column

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/passed_job.png)

This shows the **ansible_lint** job ran successfully for the initial setup commit.

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/ansible_lint.png)

If you want to see detailed logs of the job run you can click the ** ansible lint** job.

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/ansible_lint_closeup.png)

## Step 5 - Create a Merge Request (Also known as a pull request)
Now we need to create merge request to verify/test the configuration against the NDFC staging environment fabric.

1. Click `Merge requests` on the side menu
2. Then click `New merge request`

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/new_pr.png)

3. Select stage as Source branch
4. Select main as Target branch
5. Then click Compare branches and continue

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/select_branch.png)

Leave all of the fields with the default settings, then click `Create merge request`

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/createpr.png)

Click the circle icon in screen below to navigate to pipeline page

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/ci_pipeline.png)

8. Wait until the pipeline finishes, if any pipeline fails, you can click the step to check the error message

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/ci_pipeline_success.png)

## Step 6 - Verfiy on NDFC
In your browser, open another instance of the browser or another tab. Copy the url of Nexus Dashboard:

* ND URL: https://10.15.0.59/appcenter/cisco/ndfc/ui/lan-fabric/fabrics

* Username: **admin**
* Password: **cisco.123**
Open fabric page and open **fabric-stage**, and verify VRF and Networks are provisioned:

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/stage_vrf.png)

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/stage_network.png)

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task03/workflow_cicd_pipeline.png)
