---
title: "Running the model locally on AWS DeepLens"
weight: 415
chapter: true
draft: false
tags:
  - advanced
---
An AWS DeepLens project consists of two pieces: a model and an inference function. Inference is when you apply the model to a new image that the model has not seen before and get predictions. The model consists of the algorithm and the parameters learned through the training process. You can use models trained with Amazon SageMaker or external models trained on your own machine. For this tutorial, you will use the Amazon SageMaker model you just trained.

## Import your model into AWS DeepLens

In your [AWS DeepLens console](https://console.aws.amazon.com/deeplens), go to **Models** and click on **Import model**.

![lab3-custom-model-1](/images/500_deploy_a_custom_model/lab3-custom-model-1.png)


Select **Amazon SageMaker trained model**. Then selection your job ID and model name. Then select **MXNet** as your model framework.

![lab3-custom-model-2](/images/500_deploy_a_custom_model/lab3-custom-model-2.png)

Then click **Import model**.


## Create the inference function

Inference is the step where the model is shown images it has never seen before and asked to make a prediction. The inference function optimizes the model to run on DeepLens and feeds each camera frame into the model to get predictions. For the inference function, you use [AWS Lambda](http://aws.amazon.com/lambda) to create a function that you deploy to AWS DeepLens. The Lambda function runs inference (locally on the AWS DeepLens device) over each frame that comes out of the camera. 

This tutorial provides an example inference Lambda function. 

First we need to create an AWS Lambda function to be deployed to AWS DeepLens

1.	Download the [deeplens-trash-lambda.zip](/code/trash-sorter/deeplens-trash-lambda.zip) onto your computer.
2.	Go to AWS Lambda in your AWS console and click **Create Function**.
3.	Then select **Author from Scratch** and make sure the follow options are selected:
  *	Runtime: **Python 2.7**
  *	Choose or create an execution role: **Use an existing role**
  *	Existing role: **service-role/AWSDeepLensLambdaRole**

![lab3-custom-lambda-1](/images/500_deploy_a_custom_model/lab3-custom-lambda-1.png)

4.	Once the function is created, scroll down on the function’s detail page and choose **Upload zip** in ***Code entry type***
5.	Upload the **deeplens-trash-lambda.zip** you downloaded earlier.
6.	Choose **Save** to save the code you entered.
7.	From the Actions dropdown menu list, choose Publish new version. Publishing the function makes it available in the AWS DeepLens console so that you can add it to your custom project.
8.	Enter a version number and click publish!

![lab3-custom-lambda-3](/images/500_deploy_a_custom_model/lab3-custom-lambda-3.png)

## Understanding the Lambda function

This section walks you through some important parts of the Lambda function.

First, there are two files you should pay attention to: **labels.txt** and **lambda_function.py**.

**labels.txt** contains the list of the human-readable labels for us to map the output of the neural network (integers) to.

**lambda_function.py** contains code for the function being called to generate predictions on every camera frame and send back results.

Here are the important parts of **lambda_function.py**.

```python
def lambda_handler(event, context):
	return
```

lambda_handler is typically this is the entry point for most Lambda functions, but this function will exit after it has been invoked once by GreenGrass, so we will use the next function: **infinite_infer_run**.

We start by defining the type of model and the number of outputs we want. We also load the labels file to get human readable outputs.

```python
	# Number of top classes to output
  num_top_k = 2
  model_type = 'classification'
  model_name = 'image-classification'

  with open('labels.txt', 'r') as f:
    output_map = [l for l in f]
```

Compared to the computational power of cloud virtual machines with a GPU, AWS DeepLens has less computing power. AWS DeepLens uses the **Intel OpenVino model optimizer** to optimize the model trained in Amazon SageMaker to run on AWS DeepLens. See the following code:

```python
# Optimize the model
model_type = 'classification'
model_name = 'image-classification'
error, model_path = mo.optimize(model_name,input_width,input_height)

# Load the model onto the GPU.
model = awscam.Model(model_path, {'GPU': 1})
```

Then you run the model frame-per-frame over the images from the camera. See the following code:

```python
ret, frame = awscam.getLastFrame()

# Resize frame to the same size as the training set.
frame_resize = cv2.resize(frame, (input_height, input_width))
parsed_inference_results = model.parseResult(model_type, model.doInference(frame_resize))

# Get top k results with highest probabilities
top_k = parsed_inference_results[model_type][0:3]
```

Finally, you send the text prediction results back to the cloud. Viewing the text results in the cloud is a convenient way to make sure the model is working correctly. Each DeepLens device has a dedicated iot_topic automatically created to receive the inference results. Alternatively you can overlay the results on the video stream or send these results to another device, such as a Raspberry Pi. See the following code:

```python
# Send the top k results back to the cloud
cloud_output = {}
for obj in top_k:
    cloud_output[output_map[obj['label']]] = obj['prob']
client.publish(topic=iot_topic, payload=json.dumps(cloud_output))
```

## Create a custom AWS DeepLens project

In your [AWS DeepLens console](https://console.aws.amazon.com/deeplens/home?#)’s **Projects** page, click on **Create Project**.

Choose the **Blank Project option**.

![lab3-custom-project-1](/images/500_deploy_a_custom_model/lab3-custom-project-1.png)

Name your project **your_name-trash-sorter**

Then click **Add model** and select the model you just created.

Then click **Add function** and search for the AWS Lambda function you created earlier by name.

![lab3-custom-project-3](/images/500_deploy_a_custom_model/lab3-custom-project-3.png)

Then click **Create** project.

## Deploy the custom AWS DeepLens project

In your [AWS DeepLens console](https://console.aws.amazon.com/deeplens/home?#)’s **Projects** page, select the project you want to deploy and then select **Deploy to device**.

![sample-project-list](/images/500_deploy_a_custom_model/lab1-sample-projects-1.png)

On the Target device screen, choose your device from the list, and click **Review.**

![](/images/500_deploy_a_custom_model/lab1-sample-deploy-1.png)

Then click **Deploy**. The deployment can take up to 10 minutes to complete, depending on the speed of the network your AWS DeepLens is connected to. Once the deployment is complete, you should see a green banner like the one below.

![](/images/500_deploy_a_custom_model/lab1-sample-deploy-3.png)

Congratulations, your model is now running locally on AWS DeepLens! 

To see the text output, scroll down on the device details page to the Project output section. Follow the instructions in the section to copy the topic and go to the AWS IoT console to subscribe to the topic.

![](/images/500_deploy_a_custom_model/lab1-sample-deploy-4.png)

You should see results coming through like the screenshot below.

![](/images/500_deploy_a_custom_model/lab1-sample-deploy-5.png)

For step-by-step instructions on how to view the video stream, see [here](/200_beginner/220_viewing_prediction_output/).


