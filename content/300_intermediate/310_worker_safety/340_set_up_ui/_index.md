---
title: "Set up the UI dashboard"
date: 2020-03-04T10:15:55-07:00
chapter: true
draft: false
weight: 340
---
### View output in AWS IoT

1. Go to AWS IoT console at https://console.aws.amazon.com/iot/home
2. Under Subscription topic, enter the topic name that you entered as an environment variable for the Lambda function you created in the earlier step (example: worker-safety-demo-cloud) and click Subscribe to topic.
3. You should now see JSON message with a list of people detected and whether they are wearing safety hats or not.

### View output in Amazon CloudWatch

* Go to Amazon CloudWatch Console at https://console.aws.amazon.com/cloudwatch
* Create a dashboard called “worker-safety-dashboard-your-name”
* Choose Line in the widget
* Under Custom Namespaces, select “string”, “Metrics with no dimensions”, and then select PersonsWithSafetyHat and PersonsWithoutSafetyHat.
* Next, set “Auto-refresh” to the smallest interval possible (1h), and change the “Period” to whatever works best for you (1 second or 5 seconds)

### View output in the web dashboard

1. Go to AWS Cognito console at https://console.aws.amazon.com/cognito
2. Click on Manage Identity Pools
3. Click on Create New Identity Pool
4. Enter “awsworkersafety” for Identity pool name
5. Select Enable access to unauthenticated identities
6. We are using Unauthenticated identity option to keep things simple in the demo. For real world applications where you only want authorized users to access the app, you should configure Authentication providers.
7. Click Create Pool
8. Expand View Details
9. Under: Your unauthenticated identities would like access to Cognito, expand View Policy Document and click Edit.
10. Click Ok for Edit Policy prompt.
11. Copy JSON from [cognitopolicy.json](/code/worker-safety/cognitopolicy.json) and paste in the text box.
12. Click Allow
13. Make a note of the Identity Pool as you will need it in the following steps.
14. Go to AWS IoT in AWS Console at: https://console.aws.amazon.com/iot
15. Click on settings and make a note of the endpoint, you will need this the following step.
16. Download [webdashboard.zip](/code/worker-safety/webdashboard.zip) and unzip on your local drive.
17. Edit aws-configuration.js and update poolId with Cognito Identity Pool Id and host with IoT EndPoint you got in the earlier steps.
18. From the terminal go to the root of the unzipped folder and run “npm install”
19. Next, run “./node_modules/.bin/webpack —config webpack.config.js”
20. This will create a build that we can easily deploy.
21. Go to Amazon S3 bucket, and create a folder titled "web"
22. From the web folder in S3 bucket, click upload and select bundle.js, index.html and style.css.
23. From Set permission, Choose Grant public read access to the objects. and click Next
24. Leave default settings for following screens and click Upload.
25. Click on index.html and click on the link to open the web page in browser.
26. In the address URL append ?iottopic=NAME-OF-YOUR-IOT-TOPIC. This is the same value you added to the Lambda environment variable and hit Enter.
27. You should now see images coming in from AWS DeepLens with a green or red box around the person. Green indicates the person is wearing a safety hat and red indicates a person who is not. 

This completes the application- you have now learnt to build an application that detects if a person at a construction site is wearing a safety hat or not. You can quickly extend this application to detect lab coats or helmets etc. 

Before you exit, make sure you clean up any resources you created as part of this lab. 

## Clean up
Delete Lambda functions, S3 bucket and IAM roles.
