---
layout: post
title:  "vRA 8 + NSX-T Blog Series Part 3: vRA 8 Blueprint with Existing Security Group (network profile)"
description: "Learn how to create a vRA 8 blueprint to deploy machines with existing NSX-T security groups using a network profile."
author: Phoebe Kim 
date:   2020-03-18 20:53:00 -0500
categories: [vRA]
tags: [vRA, vRA8, NSX-T, existing security group, network profile, automation]
---

You can create a vRA 8 blueprint to deploy machines and place them in existing NSX-T security group(s) using a network profile. 

Note that with this method, however, any and all security groups that you select in the network profile will be applied to the machines. If you have multiple machines in a blueprint and want to specify which security group each machine should use then use vRA tag or segment port tag (refer to series part 4 & 5 for those methods).


### Prerequisites
vRA 8:
* NSX-T account connected
* Basic infrastructure configured (Projects, Cloud Zones, Flavor Mappings, Image Mappings)

NSX-T:
* security group(s) configured


### Process Overview
1. Create or edit a network profile.
2. Select which existing security groups you'd like to use.
3. Create a blueprint with Cloud Agnostic Machine and NSX Network objects.

optional steps:
* Create inputs in the blueprint to customize the machine name.


### Demo / Example

<h4><u>Configure Network Profile</u></h4>
1. Go to "Infrastructure" > "Network Profiles" (under Configure) and click "+ NEW NETWORK PROFILE". (or you can choose to edit an existing network profile).
2. Choose an account/region and give the profile a name.
3. Configure existing networks or on-demand networks. 
4. Go to "Security Groups" tab and select "+ ADD SECURITY GROUP". 
[Step4](/assets/images/vra8-nsxt-blog-series-part3/step4.png)
5. Select security group(s) that you'd like to apply. 
[Step5](/assets/images/vra8-nsxt-blog-series-part3/step5.png)
6. Save the network profile. 

<h4><u>Create and Configure Blueprint</u></h4>
7. Go to "Blueprints" and Click "+ NEW" to create a new blueprint.
8. Give a name to the blueprint and choose a project.
9. Drag on a Cloud Agnostic Machine and a NSX Network onto the canvas. <b>Note that you do not need to add the Security Group object to the blueprint.</b>
10. Connect the Cloud Agnostic Machine to the NSX Network on the canvas. 
11. On the right side in the YAML file, choose an image and size for the machine. 
12. Under `- network: `, add the line `assignment: static` to give a static IP address to the machine from the IP range we've created.
13. For the NSX network, change the `networkType` under `properties` accordingly depending on whether you have configured existing or on-demand networks in the network profile. In this demo, I'll be using an on-demand network. 
[Step13](/assets/images/vra8-nsxt-blog-series-part3/step13.png)
14. Click "TEST".
15. Click "DEPLOY" to create a new deployment.
16. Give it a deployment name, choose "Current Draft", the cick "DEPLOY".

<h4><u>Verify Deployment</u></h4>
17. Once deployed, go to "Deployments" tab in vRA and note the IP address of the deployment.
[Step17](/assets/images/vra8-nsxt-blog-series-part3/step17.png)
18. Now log into NSX-T UI and go to "Inventory" > "Groups".
[Step18](/assets/images/vra8-nsxt-blog-series-part3/step18.png)
19. Click "View Members" of the security group you have selected in the network profile.
20. Click "IP Addresses" and you'll see the IP address of the deployment. 
[Step20](/assets/images/vra8-nsxt-blog-series-part3/step20.png)