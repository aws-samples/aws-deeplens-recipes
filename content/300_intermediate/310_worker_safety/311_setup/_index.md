---
title: "Setup"
date: 2020-03-04T10:15:55-07:00
chapter: true
draft: false
weight: 311
---
### Setup an AWS IAM role for a cloud Lambda function

1. Go to AWS IAM in AWS Console at https://console.aws.amazon.com/iam
1. Click on Roles
1. Click create role
1. Under AWS service, select Lambda and click Next: Permissions
1. Under Attach permission policies
    * search for S3 and select AmazonS3FullAccess
    * search for Rekognition and select checkbox next to AmazonRekognitionReadOnlyAccess
    * search for cloudwatch and select checkbox next to CloudWatchLogsFullAccess and CloudWatchFullAccess
    * search for iot and select AWSIotDataAccess
    * search for lambda and select checkbox next to AWSLambdaFullAccess
1. Click Next: Tags and Next: Review
1. Name is “RecognizeObjectLambdaRole”
1. Click Create role


### Setup an AWS IAM role for AWS DeepLens Lambda function

1. Click create role
2. Under AWS service, select Lambda and click Next: Permissions
3. Under Attach permission policies
    * search S3 and select AmazonS3FullAccess
    * search lambda and select checkbox next to AWSLambdaFullAccess
4. Click Next: Tags and Next: Review
5. Name it “DeepLensInferenceLambdaRole”
6. Click Create role

### Update DeepLens AWSDeepLensGreengrassGroupRole IAM role for AWS DeepLens

1. Go to AWS IAM in AWS Console at https://console.aws.amazon.com/iam
2. Click on Roles
3. Serach for AWSDeepLensGreengrassGroupRole and click on the Role name
4. Under permissions, click on Attach Policies
5. Search S3, select AmazonS3FullAccess and click Attach policy

### Create an Amazon S3 bucket

1. Go to Amazon S3 in AWS Console at https://s3.console.aws.amazon.com/s3/
2. Click on Create bucket.
3. Under Name and region:
    * Bucket name: Enter a bucket name- your name-worker-safety (example: kashif-worker-safety)
    * Choose US East (N. Virginia)
    * Click Next
4. Leave the default values for Configure Options screen and click Next
5. Click Next, and click Create bucket.