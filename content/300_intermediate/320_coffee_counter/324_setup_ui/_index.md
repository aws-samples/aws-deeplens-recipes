---
title: "Set up the UI dashboard"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 324
tags:
  - intermediate
---


Now it’s time to deploy the leaderboard application using AWS Elastic Beanstalk. Elastic Beanstalk automatically orchestrates the required resources needed to deploy the web application. All you have to do is upload the code.

1. Download [coffee-counter-webapp.zip](/code/coffee-counter/coffee-counter-webapp.zip)
2. Go to the IAM console, and on the [IAM roles](https://console.aws.amazon.com/iam/home#/roles) page, attach the AmazonS3FullAccess and DynamoDBFullAccess managed policies to the aws-elasticbeanstalk-ec2-role. This allows Amazon EC2 instances provisioned by Elastic Beanstalk to access Amazon S3 and Amazon DynamoDB.
3. Go to the AWS Elastic Beanstalk console, and create a new application. Create a new web server environment. Enter a domain name, select Python as a platform, and upload **coffee-counter-webapp.zip** that includes the Flask application and requirements.txt file. Wait until Elastic Beanstalk provisions your environment.

![](/images/040_track_coffee_consumption/044_set_up_ui/coffee-counter-10.gif)

The URL of your application should be visible on the top of your screen. Click the URL to view your coffee leaderboard!

__Important:__ Deploying a project incurs costs for the various AWS services that are used.

## Conclusion

You are now able to track the number of coffees each individual person drinks. While this project focuses on a simple coffee leaderboard, the backbone of this architecture can be used for any application.

This project showcases the power of the AWS DeepLens device in introducing developers to machine learning and IoT. Using a combination of AWS services, we were able to build this app in a short amount of time, and so can you!


