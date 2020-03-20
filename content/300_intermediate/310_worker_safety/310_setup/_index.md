---
title: "Setup"
date: 2020-03-04T10:15:55-07:00
chapter: true
draft: false
weight: 310
---
### Setup an AWS IAM role for a cloud Lambda function

1. Go to AWS IAM in AWS Console at https://console.aws.amazon.com/iam
2. Click on Roles
3. Click create role
4. Under AWS service, select Lambda and click Next: Permissions
5. Under Attach permission policies
    1. search for S3 and select AmazonS3FullAccess
    2. search for Rekognition and select checkbox next to AmazonRekognitionReadOnlyAccess
    3. search for cloudwatch and select checkbox next to CloudWatchLogsFullAccess and CloudWatchFullAccess
    4. search for iot and select AWSIotDataAccess
    5. search for lambda and select checkbox next to AWSLambdaFullAccess
6. Click Next: Tags and Next: Review
7. Name is “RecognizeObjectLambdaRole”
8. Click Create role


### Setup an AWS IAM role for AWS DeepLens Lambda function

1. Click create role
2. Under AWS service, select Lambda and click Next: Permissions
3. Under Attach permission policies
    1. search S3 and select AmazonS3FullAccess
    2. search lambda and select checkbox next to AWSLambdaFullAccess
4. Click Next: Tags and Next: Review
5. Name it “DeepLensInferenceLambdaRole”
6. Click Create role

### Update DeepLens AWSDeepLensGreengrassGroupRole IAM role for AWS DeepLens

1. Go to AWS IAM in AWS Console at https://console.aws.amazon.com/iam
2. Click on Roles
3. Serach for AWSDeepLensGreengrassGroupRole and click on the Role name
4. Under permissions, click on Attach Policies
5. Search S3, select AmazonS3FullAccess and click Attach policy
