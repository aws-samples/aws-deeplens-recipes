---
title: "Add Subscriptions to Greengrass"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 60
---
### Step 4 

In this step, you enable the DeepLens to send MQTT messages to the RaspberryPi_SenseHAT device.

**1. On the group configuration page, choose Subscriptions, and then choose Add Subscription.**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416d_add_gg_subscriptions/416d_step1_choose_subscription.png)

**2. Configure the subscription:**

* Under Select a source, choose Lambdas, and then choose deeplens_trash_classification.
* Under Select a target, choose Devices, and then choose RaspberryPi_SenseHAT.
* Choose Next.

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416d_add_gg_subscriptions/416d_step2_add_subscription.png)


**3. Under Topic filter, enter *deeplens/trash/infer*, and then choose Next.**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416d_add_gg_subscriptions/416d_step3_add_subscription_topic.png)

**4. Choose Finish, then verify Subscriptions**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416d_add_gg_subscriptions/416d_step4_add_subscription_finish.png)

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416d_add_gg_subscriptions/416d_step4b_add_subscription_verify.png)


**5. Choose Actions and then Choose Deploy**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416d_add_gg_subscriptions/416d_step5_add_subscription_deploy.png)

**You will see the status update like below while the deployment is In Progress**
![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416d_add_gg_subscriptions/416d_step5_add_subscription_deploy_progress.png)


**You will see the status change to the below when the deployment is Successfully Completed**
![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416d_add_gg_subscriptions/416d_step5_add_subscription_deploy_success.png)



**This will update the AWS DeepLens Greengrass Core configuration to send the MQTT topic to the RaspberryPi_SenseHAT device that was added.**


**6. Before we move to the next step, we need to get a couple pieces of information from the IoT Console:**

* We need to get the IP Address of the AWS DeepLens:
    
    * Choose Cores, then Select the deeplens
    ![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416d_add_gg_subscriptions/416d_step6_get_dl_ip.png)
    
    * Choose Connectivity
    ![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416d_add_gg_subscriptions/416d_step6_get_dl_ip_conn.png)

    * Take a note of the IP Address at the top of the list, this should be an IPv4 address from your local network. Then Choose Back Arrow in upper left corner.  This will get you back to the Greengrass Group Console.
    ![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416d_add_gg_subscriptions/416d_step6_get_dl_ip_view.png)


* Determine your AWS IoT endpoint:
    
    * We need to Click the Back Arrow one more time in the upper left corner to take us back to the IoT Console.
    ![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416d_add_gg_subscriptions/416d_step6_get_iot_endpoint_back.png)

    * In the AWS IoT console, in the navigation pane, choose Settings.
    ![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416d_add_gg_subscriptions/416d_step6_get_iot_endpoint_settings.png)
   

    * Under Settings, make a note of the value of Endpoint. You will use this value in as we configure the Raspberry Pi to communicate with the DeepLens.
    ![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416d_add_gg_subscriptions/416d_step6_get_iot_endpoint.png)
