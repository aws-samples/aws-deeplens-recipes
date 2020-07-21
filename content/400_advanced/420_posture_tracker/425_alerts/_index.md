---
title: "Sending text message reminders"
weight: 425
chapter: true
draft: false
tags:
  - advanced
---
## Sending text message reminders to stand and stretch

In this section, you use [Amazon Simple Notification Service](http://aws.amazon.com/sns) (Amazon SNS) to send reminder text messages when your posture tracker determines that you have been sitting or hunching for an extended period of time.

1. Register a new SNS topic to publish messages to.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-030.jpg)

2. After you create the topic, copy and save the topic ARN, which you need to refer to in the AWS DeepLens Lambda inference code.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-032.jpg)

3. Subscribe your phone number to receive messages posted to this topic.

Amazon SNS sends a confirmation text message before your phone number can receive messages.

You can now change the access policy for the SNS topic to allow AWS DeepLens to publish to the topic.

4. On the Amazon SNS console, choose **Topics**.

5. Choose your topic.

6. Choose **Edit**.

7. On the **Access policy** tab, enter the following code, be sure to replace YOUR_AWS_ACCOUNT_ID with your AWS account ID. See [How to find your Account ID](https://docs.aws.amazon.com/IAM/latest/UserGuide/console_account-alias.html).

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-034.jpg)

```json
{
  "Version": "2008-10-17",
  "Id": "lambda_only",
  "Statement": [
    {
      "Sid": "allow-lambda-publish",
      "Effect": "Allow",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Action": "sns:Publish",
      "Resource": "arn:aws:sns:us-east-1:your-account-no:your-topic-name",
      "Condition": {
        "StringEquals": {
          "AWS:SourceOwner": "YOUR_AWS_ACCOUNT_ID"
        }
      }
    }
  ]
}
```

8. Update the AWS DeepLens Lambda function with the ARN for the SNS topic. See the following code:

```python
def publishtoSNSTopic(SittingTime=None, hunchTime=None):
    sns = boto3.client('sns')
    
    # Publish a simple message to the specified SNS topic
    response = sns.publish(
    TopicArn='arn:aws:sns:us-east-1:YOUR_AWS_ACCOUNT_ID:deeplenspose', # update topic arn
    Message='Alert: You have been sitting for {}, Stand up and stretch, and you have hunched for {}'.format(
    SittingTime, hunchTime),
    )
    
    print(SittingTime, hunchTime)
```

