---
title: "Deploy an object detection project"
date: 2020-03-04T10:15:55-07:00
chapter: true
draft: false
weight: 320
---
### Create an AWS DeepLens project

1. Using your browser, open the AWS DeepLens console at https://console.aws.amazon.com/deeplens/.
2. Choose Projects, then choose Create new project.
3. On the Choose project type screen

* Choose Create a new blank project, and click Next.

1. On the Specify project details screen

    * Under Project information section:
        * Project name: your-user-name-worker-safety (example: kashif-worker-safety)
    * Under Project content:
        * Click on Add model, click on radio button for deeplens-object-detection and click Add model.
        * Click on Add function, click on radio button for your lambda function (example: kashif-worker-safety-deeplens) lambda function and click Add function.
* Click Create. This returns you to the Projects screen.

### Deploy the project to AWS DeepLens 

1. From the AWS DeepLens console, on the Projects screen, choose the radio button to the left of your project name, then choose Deploy to device.
2. On the Target device screen, from the list of AWS DeepLens devices, choose the radio button to the left of the device where you want to deploy this project.
3. Choose Review. This will take you to the Review and deploy screen.
    If a project is already deployed to the device, you will see a warning message "There is an existing project on this device. Do you want to replace it? If you Deploy, AWS DeepLens will remove the current project before deploying the new project." Go ahead and choose Deploy.
4. On the Review and deploy screen, review your project and click Deploy to deploy the project to your AWS DeepLens. This will take you to the device screen, which shows the progress of your project deployment. Once your deployment is successful, proceed to the next step.

#### Create an AWS DeepLens inference Lambda function

1. Downoad [worker-safety-deeplens-lambda.zip](/code/worker-safety/worker-safety-deeplens-lambda.zip)
2. Go to AWS Lambda in AWS Console at https://console.aws.amazon.com/lambda/.
3. Go to AWS Lambda in your AWS console and click **Create Function**.
4. Select **Author from Scratch** and make sure the follow options are selected:
   1. Runtime: **Python 2.7**
   2. Choose or create an execution role: **Use an existing role**
   3. Existing role: **service-role/AWSDeepLensLambdaRole**
5. Once the function is created, scroll down on the function's detail page and choose **Upload zip** in *Code entry type*
6. Upload the **worker-safety-deeplens-lambda.zip**.
7. Open the **lambda_function.py**.
8. Go to line 34 and modify the line below with the name of your S3 bucket created in the earlier step.
   1. **`bucket_name = "REPLACE-WITH-NAME-OF-YOUR-S3-BUCKET"`**
9. Choose **Save** to save the code you entered.
10. From the **Actions** dropdown menu list, choose **Publish new version**. Publishing the function makes it available in the AWS DeepLens console so that you can add it to your custom project.
11. Enter a version number and click publish!

### Create an AWS DeepLens project

1. Using your browser, open the AWS DeepLens console at https://console.aws.amazon.com/deeplens/.
2. Choose Projects, then choose Create new project.
3. On the Choose project type screen, Choose Create a new blank project, and click Next.
4. On the Specify project details screen

* Under Project information section:
    * Project name: your-user-name-worker-safety (example: kashif-worker-safety)
* Under Project content:
    * Click on Add model, click on radio button for deeplens-object-detection and click Add model.
    * Click on Add function, click on radio button for your lambda function (example: kashif-worker-safety-deeplens) lambda function and click Add function.

* Click Create. This returns you to the Projects screen.
