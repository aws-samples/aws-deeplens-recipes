---
title: "Deploying the model to AWS DeepLens"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 333
tags:
  - intermediate
---


In this section, you set up your AWS DeepLens device, import a pre-trained model, and deploy the model to AWS DeepLens.

### Setting up your AWS DeepLens device

You first need to [register your AWS DeepLens device](https://docs.aws.amazon.com/deeplens/latest/dg/deeplens-getting-started-register.html), if you haven’t already.

After you register your device, you need to install the latest OpenCV (version 4.x) packages and Pillow libraries to enable the preprocessing algorithm in the DeepLens inference Lambda function. To do so, you need the IP address of AWS DeepLens on the local network, which is listed in the **Device details** section. You also need to ensure that Secure Shell (SSH) is enabled for your device. For more information about enabling SSH on your device, see [View or Update Your AWS DeepLens 2019 Edition Device Settings](https://docs.aws.amazon.com/deeplens/latest/dg/deeplens-v11-device-view-or-edit-settings.html).

![](/images/300_intermediate/330_guess_drawing/dl-ip-1.jpg)



Open a terminal application on your computer. SSH into your DeepLens by entering the following code into your terminal application:

```bash
ssh aws_cam@<YOUR_DEEPLENS_IP>
```

Then enter the following commands in the SSH terminal:

```bash
sudo su
pip install --upgrade pip
pip install opencv-python
pip install pillow
```

### Importing the model to AWS DeepLens

For this post, you use a pre-trained model. We trained the model for 36 objects on [The Quick Draw Dataset](https://github.com/googlecreativelab/quickdraw-dataset) made available by Google, Inc., under the [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/legalcode) license. For each object, we took 1,600 images for training and 400 images for testing the model from the dataset. Holding back 400 images for testing allows us to measure the accuracy of our model against images that it has never seen.

For instructions on training a model using [Amazon SageMaker](https://aws.amazon.com/sagemaker/) as the development environment, see [AWS DeepLens Recipes](https://www.awsdeeplens.recipes/) and [Amazon SageMaker: Build an Object Detection Model Using Images Labeled with Ground Truth](https://www.aws.training/Details/Video?id=37702).

To import your model, complete the following steps:

Download the model [aws-deeplens-pictionary-game.tar.gz](https://deeplens-public.s3.amazonaws.com/samples/doodle-game/aws-deeplens-doodle-game.tar.gz).

Create an [Amazon Simple Storage Service](http://aws.amazon.com/s3) (Amazon S3) bucket to store this model. For instructions, see [How do I create an S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html). The S3 bucket name must contain the term `deeplens`. The AWS DeepLens default role has permission only to access the bucket with the name containing `deeplens`.

After the bucket is created, upload `aws-deeplens-pictionary-game.tar.gz` to the bucket and copy the model artifact path.

On the [AWS DeepLens console](https://console.aws.amazon.com/deeplens), under **Resources**, choose **Models**.

Choose **Import model**.

On the **Import model to AWS DeepLens** page, choose **Externally trained model**.

For **Model artifact path**, enter the Amazon S3 location for the model you uploaded earlier.

For **Model name**, enter a name.

For **Model framework**, choose **MXNet**.

Choose **Import model**.

![](/images/300_intermediate/330_guess_drawing/dl-import-model-2.jpg)

### Deploying the model to your AWS DeepLens device

To deploy your model, complete the following steps:

On the AWS DeepLens console, under **Resources**, choose **Projects**.

Choose **Create new project**.

Choose **Create a new blank project**.

For **Project name**, enter a name.

Choose **Add model** and choose the model you imported earlier.

Choose **Add function** and choose the Lambda function you created earlier.

Choose **Create**.

![](/images/300_intermediate/330_guess_drawing/dl-project-1.jpg)

Select your newly created project and choose **Deploy to device**.

On the **Target device** page, select your device from the list.

On the **Review and deploy** page, choose **Deploy**.

The deployment can take up to 5 minutes to complete, depending on the speed of the network your AWS DeepLens is connected to. When the deployment is complete, you should see a green banner message that the deployment succeeded.

![](/images/300_intermediate/330_guess_drawing/dl-project-2.jpg)

To verify that the project was deployed successfully, you can check the text prediction results sent to the cloud via [AWS IoT Greengrass](https://aws.amazon.com/greengrass/). For instructions, see [Using the AWS IoT Greengrass Console to View the Output of Your Custom Trained Model (Text Output)](https://docs.aws.amazon.com/deeplens/latest/dg/deeplens-getting-started-connect-iot-greengrass.html).

In addition to the text results, you can view the pose detection results overlaid on top of your AWS DeepLens live video stream. For instructions, see [Viewing AWS DeepLens Output Streams](https://docs.aws.amazon.com/deeplens/latest/dg/deeplens-viewing-output.html).