---
title: "Update DeepLens Lambda Function"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 58
---
### Step 2 

We need to update the Lambda function in the Trash Classification project so that a message is sent to a new MQTT topic so the Raspberry Pi.  

**1. Navigate to AWS Lambda Functions and Select the deeplens_trash_classification function.**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416b_update_deeplens_lambda/416b_step1_choose_lambda.png)


**2. Update your Lambda Function to the code below.**

```python
#*****************************************************
#                                                    *
# Copyright 2018 Amazon.com, Inc. or its affiliates. *
# All Rights Reserved.                               *
#                                                    *
#*****************************************************
import os
import json
import time
import numpy as np
import awscam
import cv2
import mo
import greengrasssdk
from utils import LocalDisplay

def lambda_handler(event, context):
    """Empty entry point to the Lambda function invoked from the edge."""
    return

# Create an IoT client for sending to messages to the cloud.
client = greengrasssdk.client('iot-data')
iot_topic = '$aws/things/{}/infer'.format(os.environ['AWS_IOT_THING_NAME'])

# MQTT Topic for communication to the Raspberry Pi
pi_topic = 'deeplens/trash/infer'

INPUT_WIDTH = 224
INPUT_HEIGHT = 224

def infinite_infer_run():
    """ Run the DeepLens inference loop frame by frame"""
    
    try:
        # Number of top classes to output
        num_top_k = 2

        
        model_type = 'classification'
        model_name = 'image-classification'
        
        with open('labels.txt', 'r') as f:
	        output_map = [l for l in f]

        # Create a local display instance that will dump the image bytes to a FIFO
        # file that the image can be rendered locally.
        local_display = LocalDisplay('480p')
        local_display.start()

        # Optimize the model
        error, model_path = mo.optimize(model_name,INPUT_WIDTH,INPUT_HEIGHT)
        
        # Load the model onto the GPU.
        client.publish(topic=iot_topic, payload='Loading model')
        model = awscam.Model(model_path, {'GPU': 1})
        client.publish(topic=iot_topic, payload='Model loaded')
        
        while True:
            # Get a frame from the video stream
            # The for loop was added for the Raspberry Pi addition so the latest frame is used not a frame in the buffer
            for x in range(20):
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
            # Send the top k results to the Raspberry Pi via MQTT
            pi_output = {}
            pi_output['object'] = output_map[top_k[0]['label']]
            client.publish(topic=pi_topic, payload=json.dumps(pi_output))
           
    except Exception as ex:
      	print('Error in lambda {}'.format(ex))
        client.publish(topic=iot_topic, payload='Error in lambda: {}'.format(ex))

infinite_infer_run()
```


**3. Choose Save**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416b_update_deeplens_lambda/416b_step3_save_updated_lambda.png)

**4. Choose Actions and Select Publish new version, then Choose Publish**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416b_update_deeplens_lambda/416b_step4_publish_new_version.png)


**5. In the AWS DeepLens Projects, Choose the Trash-Classification project**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416b_update_deeplens_lambda/416b_step5_dl_project_choose.png)

**6. Choose Edit**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416b_update_deeplens_lambda/416b_step6_dl_project_edit.png)

**7. Expand the Function section of Project content.**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416b_update_deeplens_lambda/416b_step7_dl_project_expand.png)

**8. Select the Version pulldown and choose the latest version of the Lambda Function**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416b_update_deeplens_lambda/416b_step8_dl_project_version_pulldown.png)

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416b_update_deeplens_lambda/416b_step8_dl_project_version_pulldown_select.png)


**9. Choose Save**
![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416b_update_deeplens_lambda/416b_step9_dl_project_save.png)


**10. Select the Trash-Classification Project and choose Deploy to device**
![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416b_update_deeplens_lambda/416b_step10_dl_choose_project.png)


**11. Choose the Target device to deploy the updated Project, Choose Review**
![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416b_update_deeplens_lambda/416b_step11_dl_project_choose_target.png)


**12. Choose Deploy**
![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416b_update_deeplens_lambda/416b_step12_dl_project_choose_deploy.png)


**Wait for the Project to finish updating the DeepLens before continuing**
![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416b_update_deeplens_lambda/416b_step14_dl_project_deployed.png)


