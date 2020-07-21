---
title: "Detect hard hats"
date: 2020-03-04T10:15:55-07:00
chapter: true
draft: false
weight: 313
---
### Create a cloud Lambda function to detect hard hats with Amazon Rekognition

1. Go to Lambda in AWS Console at https://console.aws.amazon.com/lambda/
2. Click on Create function.
3. Under Create function, by default Author from scratch should be selected.
4. Under Author from scratch, provide the following details:

* Name: worker-safety-cloud
* Runtime: Python 3.7
* Role: Choose an existing role
* Existing role: RecognizeObjectLambdaRole
* Click Create function

1. Under Environment variables, add a variable:

* Key: iot_topic
* Value: worker-safety-demo-cloud

1. Copy the code from [cloud-lambda.py](/code/worker-safety/cloud-lambda.py) and paste it under the Function code for the Lambda function. 
2. Click Save.

1. Under Add triggers, select S3.
2. Under Configure triggers:

* Bucket: Select the S3 bucket you just created in earlier step.
* Event type: Leave default Object Created (All)
* Leave defaults for Prefix and Suffix and make sure Enable trigger checkbox is checked.
* Click Add.
* Click Save on the top right to save the changes to the Lambda function.
