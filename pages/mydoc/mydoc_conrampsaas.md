---
title: Configuring Cloud OnRamp for SaaS
keywords: archives, tagging
last_updated: June 3, 2020
tags: [navigation]
summary: "Implementing Cloud OnRamp for SaaS in Cisco SD-WAN"
sidebar: mydoc_sidebar
permalink: mydoc_conrampsaas.html
folder: mydoc
---

<br>

{% include callout.html content="**Task List**
<br/><br/>
- Overview
<br/>
- Prerequisite configuration for Cloud OnRamp
<br/>
- Configuring Cloud OnRamp for SaaS
<br/>
- Verification and Testing
<br/>
" type="primary" %}

## Overview

With the changing network landscape, the way in which applications are consumed has also undergone a massive overhaul. Applications being hosted in the cloud (Public/Private) are a common occurrence, rather than the exception.

Cloud OnRamp for SaaS monitors widely used Cloud Applications and arrives at a vQoE score (Viptela Quality of Experience). Loss and latency are used to calculate the vQoE score and based on this, the solution routes traffic to the Cloud Application via the optimal path. The vQoE value is calculated periodically to ensure persistent optimal application performance.

<br/>

{% include callout.html content="**Task List**
<br/><br/>
- [~~Overview~~](#overview)
<br/>
- [Prerequisite configuration for Cloud OnRamp](#prerequisite-configuration-for-cloud-onramp)
<br/>
- [Configuring Cloud OnRamp for SaaS](#configuring-cloud-onramp-for-saas)
<br/>
- [Verification and Testing](#verification-and-testing)
<br/>
" type="primary" %}

## Prerequisite configuration for Cloud OnRamp

1. On the vManage GUI, navigate to **Configuration => Templates => Feature Tab**. Locate the *vEdge30_INET* template and click on the three dots next to it. Choose to **Edit** the template

    ![](/images/Cloud_OnRamp/06_edit.PNG)

2. Scroll down to the NAT section and set NAT to a global value of *On*. Click on **Update**

    ![](/images/Cloud_OnRamp/07_nat.PNG)

3. Click on **Next** and **Configure Device**. There are no changes to be made here since we are simply enabling NAT on the interface.

4. On the vManage GUI, go to **Configuration => Templates => Feature Tab**. Locate the *DC-vEdge_INET* template and click on the three dots next to it. Choose to **Edit** the template

    {% include note.html content="This step is not required if you have gone through the WAAS Integration. Please skip to the next section if WAAS integration has been done." %}

    ![](/images/Cloud_OnRamp/08_dcnat.PNG)

5. Scroll down to the NAT section and set **NAT** to a Global value of *On*. Click on **Update**. Click **Next/Configure Devices** to finish the update to the devices. Confirm the change on two devices and click **OK**

    {% include note.html content="This step is not required if you have gone through the WAAS Integration. Please skip to the next section if WAAS integration has been done." %}

    ![](/images/Cloud_OnRamp/09_enup.PNG)

    ![](/images/Cloud_OnRamp/10_conf.PNG)

We have enabled NAT on all the interfaces that will be communicating directly with the SaaS applications. There are other prerequisites that need to be taken into consideration while deploying this in production (a few examples are devices should be in vManage mode, DNS server details populated in VPN 0 etc.) but these have been fulfilled in our SD-WAN Network.

<br/>

{% include callout.html content="**Task List**
<br/><br/>
- [~~Overview~~](#overview)
<br/>
- [~~Prerequisite configuration for Cloud OnRamp~~](#prerequisite-configuration-for-cloud-onramp)
<br/>
- [Configuring Cloud OnRamp for SaaS](#configuring-cloud-onramp-for-saas)
<br/>
- [Verification and Testing](#verification-and-testing)
<br/>
" type="primary" %}

## Configuring Cloud OnRamp for SaaS

Go through the following steps in order to configure Cloud OnRamp for SaaS in our SD-WAN network.

1. On the vManage GUI, navigate to **Administration => Settings**

    ![](/images/Cloud_OnRamp/11_set.PNG)

2. Locate the **Cloud onRamp for SaaS** section and click on **Edit**. Set the radio button to **Enabled** and click on **Save**. Cloud OnRamp for SaaS needs to be enabled system wide before it can be used

    ![](/images/Cloud_OnRamp/12_en.PNG)

3. Once enabled, click on the **Cloud** icon in the top right-hand of the screen and click on **Cloud onRamp for SaaS**

    ![](/images/Cloud_OnRamp/13_cloud.PNG)

4. Click on **Dismiss**

    ![](/images/Cloud_OnRamp/14_dism.PNG)

5. Click on **Manage Cloud onRamp for SaaS** (top right-hand corner) and click on **Applications**

    ![](/images/Cloud_OnRamp/15_app.PNG)

6. Specify a random application (example shows Amazon AWS, but you can choose something else like Oracle or Google Apps) and populate a **VPN** of *10*

    ![](/images/Cloud_OnRamp/16_aws.PNG)

7. Make sure the chosen Application shows up and click on **Save Changes**

    ![](/images/Cloud_OnRamp/17_save.PNG)

8. Click on **Cloud onRamp for SaaS** (top right-hand corner) again and click on **Direct Internet Access (DIA) Sites**

    ![](/images/Cloud_OnRamp/18_dia.PNG)

9. Click on **Attach DIA Sites** and move Site 30 to the **Selected Sites** section. Click on **Attach**

    ![](/images/Cloud_OnRamp/19_attdia.PNG)

10. Wait for the task to go through successfully. Once it is done, click on the **Cloud** icon in the top right corner and click **Cloud onRamp for SaaS**

    ![](/images/Cloud_OnRamp/20_succcloud.PNG)

11. Click on **Manage Cloud onRamp for SaaS** and choose Gateways

    ![](/images/Cloud_OnRamp/21_gw.PNG)

12. Click on **Attach Gateways** and move Site *1* to the Selected Sites. Click on **Attach**

    ![](/images/Cloud_OnRamp/22_attgw.PNG)

13. If you go to **Configuration => Cloud OnRamp for SaaS** (or click the Cloud icon and go to Cloud onRamp for SaaS), you should see the selected Application with 3 Devices attached to it. Click on the Application and the three Devices should be tagged with a vQoE Status of Bad. Their vQoE score is 0.0, indicating that information hasn't been collected to arrive at a score. We will need to wait for some time (another tea/coffee?)

    ![](/images/Cloud_OnRamp/23_waitforcalc.PNG)

14. If you refresh the screen, you should notice devices gradually showing up with their vQoE score. Notice that vEdge30 is selecting a local path to the selected Application

    ![](/images/Cloud_OnRamp/24_ve30loccalc.PNG)

    ![](/images/Cloud_OnRamp/25_allgood.PNG)

    Through the DIA configuration, we have provided vEdge30 with a local breakout to the Application and by adding Site 1 as the Gateway, traffic can be punted over the MPLS link to the DC site and sent out the Internet breakout there, in the event of the local Site30 Internet breakout facing issues.

<br/>

{% include callout.html content="**Task List**
<br/><br/>
- [~~Overview~~](#overview)
<br/>
- [~~Prerequisite configuration for Cloud OnRamp~~](#prerequisite-configuration-for-cloud-onramp)
<br/>
- [~~Configuring Cloud OnRamp for SaaS~~](#configuring-cloud-onramp-for-saas)
<br/>
- [Verification and Testing](#verification-and-testing)
<br/>
" type="primary" %}

## Verification and Testing

1. Navigate to **Configuration => Template => Feature Tab** and locate the *vEdge30_INET* template. Click on the three dots next to it and choose to **Edit**

    ![](/images/Cloud_OnRamp/27_ve30inet.PNG)

2. Scroll down to the **ACL/QOS** section and specify a **Shaping Rate (Kbps)** of *1*. This will inject delay on our INET link connected to vEdge30. Click on **Update**

    ![](/images/Cloud_OnRamp/28_shaper.PNG)

3. Click on **Next/Configure Devices**. You can check the side-by-side configuration to see that the shaping rate is applied to interface ge0/0

    ![](/images/Cloud_OnRamp/29_sbs.PNG)

4. Wait for some time and traffic to the chosen Application from vEdge30 (check via Cloud icon => Cloud onRamp for SaaS => click on the Application) should have a DIA status of **gateway**, indicating that the DC Gateway is being used to contact Amazon AWS (in this example). The local/remote color is *mpls* with the system-ip of the gateway being used

    ![](/images/Cloud_OnRamp/30_redierect.PNG)

    The vQoE score might vary, as shown in the image below (it usually takes approximately 15 to 20 minutes for the expected results to show up)

    ![](/images/Cloud_OnRamp/123_vqoemightvary.PNG)

5. Go back to the *vEdge30-INET* Feature template (refer to Steps 1 and 2 of this section) and set the **Shaping Rate (Kbps)** to the Default value. Click on **Update**. Click on **Next/Configure Devices**

    ![](/images/Cloud_OnRamp/31_def.PNG)

6. Navigate to **Monitor => Network** and click on **Tunnel**. Make sure all the public-internet Tunnel Endpoints are selected. You should see the latency on the link drop

    ![](/images/Cloud_OnRamp/32_afterremoving.PNG)

7. Cloud OnRamp for SaaS takes a few minutes to converge, so monitor the **Cloud icon => Cloud onRamp for SaaS => Application** page - in time, you should see vEdge30 sending data via the local internet breakout

    ![](/images/Cloud_OnRamp/33_backtoloc.PNG)

    ![](/images/Cloud_OnRamp/34_allgood.PNG)

This completes the Cloud OnRamp for SaaS lab.

<br/>

{% include callout.html content="**Task List**
<br/><br/>
- [~~Overview~~](#overview)
<br/>
- [~~Prerequisite configuration for Cloud OnRamp~~](#prerequisite-configuration-for-cloud-onramp)
<br/>
- [~~Configuring Cloud OnRamp for SaaS~~](#configuring-cloud-onramp-for-saas)
<br/>
- [~~Verification and Testing~~](#verification-and-testing)
<br/>
" type="primary" %}
