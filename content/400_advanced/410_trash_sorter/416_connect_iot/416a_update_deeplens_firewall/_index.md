---
title: "Update DeepLens Firewall Configuration"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 57
---
### Step 1

AWS DeepLens does not allow communication to port 8883 by default which is required for other local Greengrass devices (Raspberry Pi in this case) to communicate with the Greengrass core (DeepLens is the Greengrass Core in this situation).  

**1. SSH into the AWS DeepLens**

```bash

ssh aws_cam@<deeplens_ip_address>

```

**2. Check status of AWS DeepLens firewall and update to allow communication**

```bash
sudo ufw status verbose
sudo ufw allow 8883
```
![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416a_update_deeplens_firewall/416a_step1_ssh_ufw.png)
