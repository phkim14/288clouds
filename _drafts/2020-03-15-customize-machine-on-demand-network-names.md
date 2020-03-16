---
layout: post
title:  "Customize Machine and On-demand Network Names"
description: "Learn how to customize the machine and on-demand network names in a vRA 8 blueprint."
author: Phoebe Kim, Michael Patton
date:   2020-03-15 21:30:00 -0500
categories: [vRA]
tags: [vRA, vRA8, NSX-T, naming convention, names, custom properties, input, blueprint, automation]
---

vRA 8 blueprint gives random names and numbers to the VMs and on-demand NSX-T networks deployed if you do not specify a naming convention. This blog will cover how you can customize the names of your VMs and on-demand NSX-T networks when they are deployed from a vRA 8 blueprint. 

### Process Overview
1. Set up the existing networks with IP ranges you want to use for the machines.
2. Create a network profile.
3. Add existing networks you want to use in the network profile.
4. Create a blueprint. 
5. Drag a Cloud Agnostic Machine object onto the canvas.
6. Drag a NSX Network object onto the canvas.
7. Connect the machine to the network. 
8. Edit the yml file to specify the network. 

### Demo / Example

<h4><u>Configure Existing Network IP Range</u></h4>