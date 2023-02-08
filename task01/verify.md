# Verify Interface Configuration
In your browser, open another instance of the browser or another tab. Copy the url of Nexus Dashboard:

> ND URL: https://10.15.0.59

## Step 1 - Login to Nexus Dashboard
At the Nexus Dashboard login screen, login using your username and password:

* Username: **admin**
* Password: **cisco.123**

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task01/ndfc_login.png)

## Step 2 - Nagivate to NDFC Application
After logging in to NDFC, Click **Services** on the left menu and select **Fabric Controller** to bring up the NDFC application:

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task01/ndfc_app.png)

## Step 3 - Nagivate to fabric fabric-stage
Once the NDFC Application is opened, open **fabric-stage**:

1. Click LAN
2. Click Fabrics
3. Double-click fabric-stage or Click the fabric-stage then click icon

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task01/fabric1.png)

## Step 4 - Verify interfaces created on fabric-stage
On fabric page, verify interface configuration:

1. Click `Interfaces`
![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task01/interface.png)

2. In filter, select `Interface, contains`, then type `vpc`
![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task01/interface.png)

3. Verify two vPCs are created on vPC peer staging-leaf1/staging-leaf2
![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task01/interface.png)

4. Remove previous filter, select `Interface,==` then type `loopback10`
![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task01/select_interface.png)

5.Verify two loopbacks are created on leaf `staging-leaf1` and `staging-leaf2`, the Oper-Status is down because the VRF has not been created yet
![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task01/loopback.png)
