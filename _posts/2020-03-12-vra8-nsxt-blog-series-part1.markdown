---
layout: post
title:  "vRA 8 + NSX-T Blog Series Part 1: vRA 8 Blueprint with Existing NSX-T Networks"
date:   2020-03-13 07:48:00 -0500
categories: [vRA]
tags: [vRA, vRA8, NSX-T, automation]
---

You can create a vRA 8 blueprint to deploy machines on existing NSX-T networks. 

### Prerequisites
vRA 8:
* NSX-T account connected
* Basic infrastructure configured (Projects, Cloud Zones, Flavor Mappings, Image Mappings)

NSX-T:
* logical router (tier-0 or tier-1) configured
* logical segment(s) attached to a logical router


### Process Overview
1. Set up the existing networks with IP ranges you want to use for the machines.
2. Create a network profile.
3. Add existing networks you want to use in the network profile.
4. Create a blueprint. 
5. Drag a Cloud Agnostic Machine object onto the canvas.
6. Drag a NSX Network object onto the canvas.
7. Connect the machine to the network. 
8. Edit the yml file to specify the network. 

optional steps:
* Create tags on the existing networks with names of your choice.
* Create an input in the blueprint to customize the machine name.


### Demo / Example

<h4><u>Configure Existing Network IP Range</u></h4>
1. Log into vRA Cloud Assembly.
2. Go to "Infrastructure" > "Networks" (under Resources).
3. Click on the existing network you want to use and make sure the information is filled out. 
![Step3](/assets/images/vra8-nsxt-blog-series-part1/step3.png)
4. Click the checkbox next to the existing network you want to use and click "MANAGE IP RANGES".
![Step4](/assets/images/vra8-nsxt-blog-series-part1/step4.png)
5. Click "+ NEW IP RANGE".
6. Create an IP range that you'd like to use to give IP addresses to the machines deployed from this blueprint.
![Step6](/assets/images/vra8-nsxt-blog-series-part1/step6.png)
7. Double-check the start and end IP addresses on the range created to make sure it's correct.
![Step7](/assets/images/vra8-nsxt-blog-series-part1/step7.png)

<h4><u>Configure Network Profile</u></h4>
8. Go to "Infrastructure" > "Network Profiles" (under Configure) and click "+ NEW NETWORK PROFILE".
![Step8](/assets/images/vra8-nsxt-blog-series-part1/step8.png)
9. Choose an account/region and give the profile a name.
![Step9](/assets/images/vra8-nsxt-blog-series-part1/step9.png)
10. Go to "Networks" tab and click "+ ADD NETWORK".
![Step10](/assets/images/vra8-nsxt-blog-series-part1/step10.png)
11. Choose the existing network(s) you would like to use in the blueprint then save.

<h4><u>Create Blueprint</u></h4>
12. Go to "Blueprints" and Click "+ NEW" to create a new blueprint.
13. Give a name to the blueprint and choose a project.
![Step13](/assets/images/vra8-nsxt-blog-series-part1/step13.png)
14. Drag on a Cloud Agnostic Machine and a NSX Network onto the canvas. 
![Step14](/assets/images/vra8-nsxt-blog-series-part1/step14.png)
15. Connect the Cloud Agnostic Machine to the NSX Network on the canvas. 
![Step15](/assets/images/vra8-nsxt-blog-series-part1/step15.png)
16. On the right side in the YAML file, choose an image and size for the machine. When you click between the quotes, a dropdown menu of available options should show up.
![Step16](/assets/images/vra8-nsxt-blog-series-part1/step16.png)
17. Under `- network: `, add the line `assignment: static` to give a static IP address to the machine from the IP range we've created.
18. For the NSX network, make sure it says `networkType: existing` under `properties`.
19. Below `networkType` add the line `constraints:` then another line `- tag:` to choose the existing network you want to use.
![Step19](/assets/images/vra8-nsxt-blog-series-part1/step19.png)
20. Click "TEST".
![Step20](/assets/images/vra8-nsxt-blog-series-part1/step20.png)
21. Click "DEPLOY" to create a new deployment.
22. Give it a deployment name, choose "Current Draft", the cick "DEPLOY".
![Step22](/assets/images/vra8-nsxt-blog-series-part1/step22.png)
23. Monitor the progress. Once completed, you should be able to see the deployment under "Deployments" in vRA UI as well as the new machine created in the vSphere UI.
![Step23](/assets/images/vra8-nsxt-blog-series-part1/step23.png)
![Step23-1](/assets/images/vra8-nsxt-blog-series-part1/step23-1.png)
![Step23-2](/assets/images/vra8-nsxt-blog-series-part1/step23-2.png)

<h4><u>Demo / Example Blueprint YAML File</u></h4>
{% highlight ruby %}
formatVersion: 1
inputs: {}
resources:
  Cloud_Machine_1:
    type: Cloud.Machine
    properties:
      image: 'centos-temp'
      flavor: 'small'
      networks:
        - network: '${resource.Cloud_NSX_Network_1.id}'
          assignment: static
  Cloud_NSX_Network_1:
    type: Cloud.NSX.Network
    properties:
      networkType: existing
      constraints: 
        - tag: subnet-cidr:192.168.34.0/24
{% endhighlight %}