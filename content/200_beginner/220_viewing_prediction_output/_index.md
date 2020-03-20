---
title: "View results"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 22
tags:
  - beginner
---

## Viewing results from AWS DeepLens

There are two ways that we can observe the predictions from our model:

1. View text predictions that AWS DeepLens sends back to the cloud
2. View the video stream with the predictions overlaid onto image (the project stream)



#### Option 1. View text predictions in AWS IoT

We can see the inference results inside the AWS IoT console. Inside your device page, click **copy** to copy the IoT topic.

![lab1-view-iot-1](/images/210_deploy_a_sample_project/lab1-view-iot-1.png)

Then click **AWS IoT console** to view the output.

Paste in the IoT topic and click **Subscribe**.

![lab1-view-iot-2](/images/210_deploy_a_sample_project/lab1-view-iot-2.png)

You should see results like below:

![lab1-view-iot-3](/images/210_deploy_a_sample_project/lab1-view-iot-3.png)

Because this is all captured and recorded in the cloud, you can make a function that acts on the inference results. 

#### Option 2. View the video stream

DeepLens outputs 2 video streams: one from the raw camera and one with the predictions overlaid on top of the camera feed (project stream).

Here are the steps to view the video stream from your computer:

##### Step 1: Install the streaming certificate

From your AWS DeepLens console, go to **Devices** and click on your device name. You should see a page like the one below.

![lab0-setup-stream-1](/images/200_device_setup/lab0-setup-stream-1.png)

Now connect your device via USB cable and select **Device Settings** under *Video Streaming*.

Device Settings

![lab0-setup-device-settings](/images/200_device_setup/lab0-setup-device-settings.png)

Under *video streaming*, select your browser and OS. Follow the *instructions* provide to install the streaming certificate. 

![lab0-setup-stream-2](/images/200_device_setup/lab0-setup-stream-2.png)

*Hint*: for newer macOS and Windows, you can double click on the downloaded certificate to install it. You will need administrative access to your computer to install the certificate.

##### Step 2: View the video stream

Go back to your device page and click **View video stream**.

You will be taken to a page like this. Do not be alarmed. 

Click on **Advanced** and **Proceed to x.x.x.x (unsafe)**. Trust us, it's safe.

![lab0-setup-stream-3](/images/200_device_setup/lab0-setup-stream-3.png)

For macOS you'll need to select the streaming certificate you installed earlier.

![lab0-setup-stream-4](/images/200_device_setup/lab0-setup-stream-4.png)

It will take a couple of seconds for the stream to load, please be patient.

