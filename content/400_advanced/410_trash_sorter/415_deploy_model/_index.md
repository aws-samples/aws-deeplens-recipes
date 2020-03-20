---
title: "Deploy a custom model"
weight: 415
chapter: true
draft: false
tags:
  - advanced
---
# Deploy a custom model to DeepLens

## 1. Create the AWS Lambda function to be deployed to AWS DeepLens

**Download the [deeplens-trash-lambda.zip](/code/trash-sorter/deeplens-trash-lambda.zip) onto your computer.**

DeepLens makes it easy to deploy custom code to process the image through a AWS Lambda function that lives on DeepLens. You'll create your Lambda function in the AWS cloud console.

Go to AWS Lambda in your AWS console and click **Create Function**.

Then select **Author from Scratch** and make sure the follow options are selected:

Runtime: **Python 2.7**

Choose or create an execution role: **Use an existing role**

Existing role: **service-role/AWSDeepLensLambdaRole**

![lab3-custom-lambda-1](/images/500_deploy_a_custom_model/lab3-custom-lambda-1.png)

Once the function is created, scroll down on the function's detail page and choose **Upload zip** in *Code entry type*

Upload the **[deeplens-trash-lambda.zip](/code/trash-sorter/deeplens-trash-lambda.zip)** you downloaded earlier.

Choose **Save** to save the code you entered.

From the **Actions** dropdown menu list, choose **Publish new version**. Publishing the function makes it available in the AWS DeepLens console so that you can add it to your custom project.

![lab3-custom-lambda-3](/images/500_deploy_a_custom_model/lab3-custom-lambda-3.png)

Enter a version number and click publish!

Let's dive deeper into the important parts of the Lambda package so you know what is happening.

First, there are two files you should pay attention to: **labels.txt** and **lambda_function.py**.

**labels.txt** contains the list of the human-readable labels for us to map the output of the neural network (integers) to.

**lambda_function.py**

You will see two functions in the file.

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

Then we load and optimize the model. Models trained in the cloud are often too computationally intensive to run on a device such as DeepLens. The Intel model optimizer optimizes the model to run well on the Intel GPU.

```python
# Optimize the model
error, model_path = mo.optimize(model_name,INPUT_WIDTH,INPUT_HEIGHT)

# Load the model onto the GPU.
model = awscam.Model(model_path, {'GPU': 1})
```

Then we loop through the camera frames one by one, sending the image to our model and getting predictions back.

```python
 while True:
    # Get a frame from the video stream
    ret, frame = awscam.getLastFrame()
    if not ret:
      raise Exception('Failed to get frame from the stream')
      # Resize frame to the same size as the training set.
      frame_resize = cv2.resize(frame, (INPUT_HEIGHT, INPUT_WIDTH))
      # Run the images through the inference engine and parse the results using
      # the parser API, note it is possible to get the output of doInference
      # and do the parsing manually, but since it is a classification model,
      # a simple API is provided.
      parsed_inference_results = model.parseResult(model_type,
                                                   model.doInference(frame_resize))
      # Get top k results with highest probabilities
      top_k = parsed_inference_results[model_type][0:num_top_k]
      # Add the label of the top result to the frame used by local display.
      # See https://docs.opencv.org/3.4.1/d6/d6e/group__imgproc__draw.html
      # for more information about the cv2.putText method.
      # Method signature: image, text, origin, font face, font scale, color, and thickness
      output_text = '{} : {:.2f}'.format(output_map[top_k[0]['label']], top_k[0]['prob'])
      cv2.putText(frame, output_text, (10, 70), cv2.FONT_HERSHEY_SIMPLEX, 3, (255, 165, 20), 8)
      # Set the next frame in the local display stream.
      local_display.set_frame_data(frame)
      # Send the top k results to the IoT console via MQTT
      cloud_output = {}
      for obj in top_k:
        cloud_output[output_map[obj['label']]] = obj['prob']
        client.publish(topic=iot_topic, payload=json.dumps(cloud_output))
```




## 2. Import your model into AWS DeepLens

In your AWS DeepLens console, go to **Models** and click on **Import model**

![lab3-custom-model-1](/images/500_deploy_a_custom_model/lab3-custom-model-1.png)


Select **Amazon SageMaker trained model**. Then selection your job ID and model name. Then select **MXNet** as your model framework.

![lab3-custom-model-2](/images/500_deploy_a_custom_model/lab3-custom-model-2.png)

Then click **Import model**.



## 3. Create a custom AWS DeepLens project

Inside the AWS DeepLens console, click on **Create Project**.

Then select **Create a blank project**.

![lab3-custom-project-1](/images/500_deploy_a_custom_model/lab3-custom-project-1.png)

Then click **Add model** and select your model.

![lab3-custom-project-2](/images/500_deploy_a_custom_model/lab3-custom-project-2.png)



Then click **Add function** and search for your AWS Lambda function by name.

![lab3-custom-project-3](/images/500_deploy_a_custom_model/lab3-custom-project-3.png)

Then click **Create** project.



## 5. Deploy the custom AWS DeepLens project

On your Project list page, selet the project you want to deploy and then select **Deploy to device**.

![sample-project-list](/images/210_deploy_a_sample_project/lab1-sample-projects.png)

On the Target device screen, choose your device from the list, and click **Review.**

![](/images/210_deploy_a_sample_project/lab1-sample-deploy-1.png)

Then click **Deploy**

![](/images/210_deploy_a_sample_project/lab1-sample-deploy-2.png)

You should be taken to your device's page and see the banner "Deploy Successful"

![](/images/210_deploy_a_sample_project/lab1-sample-deploy-3.png)



