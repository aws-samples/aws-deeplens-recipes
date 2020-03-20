---
title: "Deploy a face detection project"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 322
tags: 
  - intermediate
---
### 1. Deploy a sample project

Go to the AWS DeepLens console and create a new project.

For the project type, make sure Use a project template is highlighted and select Face detection from the project templates.

![](/images/040_track_coffee_consumption/041_prerequisites/coffee-counter-3-2.gif)

For the project name, start with "**coffee-counter-**", leave everything else at the default, and choose __Create__.

After the project is created, we need to deploy it to the AWS DeepLens device. On the __Projects page__, select your project name, and then choose __Deploy to device__. On the target device page, select your registered AWS DeepLens device.

Choose __Review__.

![](/images/040_track_coffee_consumption/041_prerequisites/coffee-counter-4.gif)

After you have reviewed, finalize by choosing __Deploy__.

### 2. Extend sample project

After you deploy the sample project, you need to change the inference Lambda function running on the device to upload images to Amazon S3. For this use case, we also added some messages on the screen to make the process more intuitive.

1. Create an [Amazon S3](https://console.aws.amazon.com/s3/home) bucket where the images will be uploaded. Use the default settings when setting up the bucket and choose the same AWS Region as the rest of your infrastructure.

2. Go to the AWS Lambda console and open the deeplens-face-detection function. Remove the function code and replace it with the lambda_inference code [here](https://github.com/aws-samples/aws-deeplens-coffee-leaderboard/blob/master/deeplens_inference_function.py). (Replace the bucket_name variable with your bucket name.)

With this step, we are changing the code to upload images to Amazon S3 when a face is detected. Plus we are adding features such as a cooldown period between uploads and a countdown before taking a picture.

3. Save the AWS Lambda function and publish a new version of the code. This allows you to go to your AWS DeepLens project and update the function on the device.

![](/images/040_track_coffee_consumption/042_create_aws_lambda/coffee-counter-6.gif)

Access your project in the AWS DeepLens console and edit the project updating the version of your AWS Lambda function and the timeout to 600 seconds:

![](/images/040_track_coffee_consumption/042_create_aws_lambda/coffee-counter-7.gif)

4) Redeploy the project to the device by selecting the project and choosing Deploy.

![](/images/040_track_coffee_consumption/042_create_aws_lambda/coffee-counter-8.gif)


