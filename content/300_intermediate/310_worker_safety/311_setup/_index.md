---
title: "Setup"
date: 2020-03-04T10:15:55-07:00
chapter: true
draft: false
weight: 311
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

### Create an Amazon S3 bucket

1. Go to Amazon S3 in AWS Console at https://s3.console.aws.amazon.com/s3/
2. Click on Create bucket.
3. Under Name and region:

* Bucket name: Enter a bucket name- your name-worker-safety (example: kashif-worker-safety)
* Choose US East (N. Virginia)
* Click Next

4. Leave the default values for Configure Options screen and click Next
5. Click Next, and click Create bucket.

### Create a cloud Lambda function

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

1. Copy the code from [cloud-lambda.py](./code/cloud-lambda.py) and paste it under the Function code for the Lambda function. 
2. Click Save.

1. Under Add triggers, select S3.
2. Under Configure triggers:

* Bucket: Select the S3 bucket you just created in earlier step.
* Event type: Leave default Object Created (All)
* Leave defaults for Prefix and Suffix and make sure Enable trigger checkbox is checked.
* Click Add.
* Click Save on the top right to save the changes to the Lambda function.

### Create an AWS DeepLens inference Lambda function

1. Go to AWS Lambda in AWS Console at https://console.aws.amazon.com/lambda/.
2. Click on Create function.
3. Under Create function, select Blueprints.
4. Under Blueprints, type greengrass and hit enter to filter blueprint templates.
5. Select greengrass-hello-world and click Configure.
6. Under Basic information, provide the following details:

* Name: name-worker-safety-deeplens (example: kashif-worker-safety-deeplens)
* Role: Choose and existing role
* Existing role: DeepLensInferenceLambdaRole
* Click Create function.

1. Copy the code from [deeplens-lambda.py](code/deeplens-lambda.py) and paste it under the Function code for the Lambda function. 
2. Go to line 34 and modify the line below with the name of your S3 bucket created in the earlier step.

* bucket_name = "REPLACE-WITH-NAME-OF-YOUR-S3-BUCKET"

1. Click Save.
2. Click on Actions, and then "Publish new version".
3. For Version description enter: Detect a person and push frame to S3 bucket. and click Publish.
