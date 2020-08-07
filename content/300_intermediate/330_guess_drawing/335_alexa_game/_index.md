---
title: "Creating an Alexa game"
weight: 335
chapter: true
draft: false
tags:
  - intermediate
---
In this section, you first create a Lambda function that queries a data stream and returns the sketches detected by AWS DeepLens to Alexa. Then, you create a custom Alexa skill to start playing the game.

### Creating a custom skill with Lambda

To create your custom skill in Lambda, complete the following steps:

On the Lambda console, create a new function.

The easiest way to create an Alexa skill is to create the function from the existing blueprints or serverless app repository provided by Lambda and overwrite the code with your own.

For **Create function**, choose **Browse serverless app repository**.

For **Public repositories**, search for and choose **alexa-skills-kit-color-expert-python**.

![](/images/300_intermediate/330_guess_drawing/alexa-lambda-1.jpg)

Under **Application settings**, enter an application name and `TopicNameParameter`.

Choose **Deploy**.

When the application has been deployed, open the Python file.

![](/images/300_intermediate/330_guess_drawing/alexa-lambda-2.jpg)

Download the [alexa-lambda-function.py](https://deeplens-public.s3.amazonaws.com/samples/doodle-game/alexa-lambda-function.py) file onto your computer.

Copy the Python code from the file and replace the sample code in the `lambda_function.py` file in the **Function code** section.

This function includes the entire game logic, reads data from the data stream, and returns the result to Alexa. Be sure to change the Region from the default (`us-east-1`) if you’re in a different Region. See the following code:

```python
kinesis = boto3.client(‘kinesis’, region_name=’us-east-1′
```

Set the **Timeout** value to 20 seconds.

You now need to give your Lambda function IAM permissions to read data from the data stream.In your Lambda function editor, choose **Permissions**.

Choose the **Role name** under the **Execution role.** You’re directed to the IAM role editor.

In the editor, choose **Attach policies**.

Enter `Kinesis` and choose **AmazonKinesisFullAccess**.

Choose **Attach policy**.

![](/images/300_intermediate/330_guess_drawing/alexa-lambda-3.jpg)

### Creating a custom skill to play the game

To create your second custom skill to start playing the game, complete the following steps:

Log in to the [Alexa Developer Console](https://developer.amazon.com/alexa/console/ask).

Create a new custom Alexa skill.

On the [Create a new skill](https://developer.amazon.com/alexa/console/ask/create-new-skill) page, for **Skill name**, enter a skill name

For **Choose a model to add to your skill**, choose **Custom**.

For **Choose a method to host your skill’s backend resources,** choose **Provision your own.**

Choose **Create skill**.

![](/images/300_intermediate/330_guess_drawing/alexa-skill-1.jpg)

On the next page, choose the default template to add your skill.

Choose **Continue with template**. After about 1–2 minutes, your skill appears on the console.

In the **Endpoint** section, enter the Amazon Resource Name (ARN) of the Lambda function created for the Alexa skill in the previous step

![](/images/300_intermediate/330_guess_drawing/alexa-skill-2.jpg)

Download [alexa-skill-json-code.txt](https://deeplens-public.s3.amazonaws.com/samples/doodle-game/alexa-skill-json-code.txt) onto your computer.

Copy the code from the file and paste in the Alexa skill JSON editor to automatically configure intents and sample utterances for the custom skill.

In the Alexa architecture, intents can be thought of as distinct functions that a skill can perform. Intents can take arguments that are known here as *slots*.

Choose **Save Model** to apply the changes.

Choose **Build Model**.

![](/images/300_intermediate/330_guess_drawing/alexa-skill-3.jpg)

On the Lambda console, open the Lambda function for the Alexa skill you created earlier.

You need to enable the skill by adding a trigger to the Lambda function.

Choose **Add trigger**.

Choose **Alexa Skills Kit**.

For **Skill ID**, enter the ID for the Alexa skill you created.

Choose **Add**.

![](/images/300_intermediate/330_guess_drawing/alexa-skill-4.jpg)

### Testing the skill

Your Alexa skill is now ready to tell you the drawings detected by AWS DeepLens. To test with an Alexa-enabled device (such as an Amazon Echo), register the device with the same email address you used to sign up for your developer account on the Amazon Developer Portal. You can invoke your skill with the wake word and your invocation name: “Alexa, Play Guess My Drawing with DeepLens.”

The language in your Alexa companion app should match with the language chosen in your developer account. Alexa considers English US and English UK to be separate languages.

Alternatively, the **Test** page includes a simulator that lets you test your skill without a device. For **Skill testing is enabled in**, choose **Development**. You can test your skill with the phrase, “Alexa, Play Guess My Drawing with DeepLens.”

Windows 10 users can download the free Alexa app from the [Microsoft Store](https://www.microsoft.com/en-us/p/alexa/9n12z3cctcnz) and interact with it from their PC.

![](/images/300_intermediate/330_guess_drawing/alexa-skill-5.jpg)

For more information on testing your Alexa skill, see [Test Your Skill](https://developer.amazon.com/en-US/docs/alexa/devconsole/test-your-skill.html). For information on viewing the logs, check [Amazon CloudWatch logs for AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/monitoring-cloudwatchlogs.html).

The following diagram shows the user interaction flow of our game.

![](/images/300_intermediate/330_guess_drawing/alexa-skill-arch.jpg)