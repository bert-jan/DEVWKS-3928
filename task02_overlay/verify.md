# Verify Interface Configuration
In your browser, open another instance of the browser or another tab. Copy the URL of Nexus Dashboard:

* ND URL: **https://10.15.0.59**

## Step 1 - Login to Nexus Dashboard
At the Nexus Dashboard login screen, login using your username and password:

* Username: **admin**
* Password: **cisco.123**

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task01/ndfc_login.png)

## Step 2 - Nagivate to NDFC Application
After logging in to NDFC, Click Services on the left menu and select Fabric Controller to bring up the NDFC application:

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task01/ndfc_app.png)

## Step 3 - Nagivate to fabric fabric-stage
Once the NDFC Application is opened, open fabric-stage

1. Click *LAN*
2. Click *Fabrics*
3. *Double-click* fabric-stage or Click the fabric-stage then click icon

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task01/fabric1.png)

## Step 4 - Verify VRF and Networks created on fabric-stage
Once the fabric page is opened, verify that VRF and Networks are created:

1. Click *VRFs* and verify **vrf_devnet** is created

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task01/vrf.png)

2. Click VRF attachments and verify **vrf_devnet** is attached to both leaf switches.

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task01/vrf_attach.png)

3. Click Networks and verify **network_devnet1** and **network_devnet2** are created

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task01/network.png)

4. Click *Network attachments* and verify **network_devnet1** and **network_devnet2** are attached to both leaf switches.

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task01/network_attach.png)
