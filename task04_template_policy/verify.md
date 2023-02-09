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

## Step 3 - Nagivate to NDFC Template Settings
Next Click **Operations** on the left menu and select **Templates**:

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task04_template_policy/template.png)

## Step 4 - Verify template telemetry
On template page, follow below path to verify template configuration:

1. In filter, select `Name, ==`, then type `template_telemetry`

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task04_template_policy/select_equal.png)![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task04_template_policy/select_telemetry.png)

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task04_template_policy/template_config.png)

## Step 5 - Verify Policy Config
1. Click LAN on the left menu, then select Fabric

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task04_template_policy/fabric.png)

2. `Double-click` fabric-stage or Click the fabric-stage then click icon  to open fabric setting
3. Click `Polices` tab, use `Template == template_telemetry` as filter

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task04_template_policy/telemetry_policy.png)

4. clear filter then use `Template == ntp_server` as filter

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task04_template_policy/ntp_policy.png)

**NOTE:** The IP Address may not match the specific IP's for your POD. The 3rd octet '9' should match your pod number.

![](https://github.com/bert-jan/DEVWKS-3928/blob/main/task04_template_policy/workflow_task04.png)
