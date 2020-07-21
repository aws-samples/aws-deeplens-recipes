---
title: "Running the model locally on AWS DeepLens"
weight: 424
chapter: true
draft: false
tags:
  - advanced
---
## Deploying pre-trained GluonCV models to AWS DeepLens

In the following steps, you convert your code written in the Jupyter notebook to an [AWS Lambda](http://aws.amazon.com/lambda) inference function to run on AWS DeepLens. The inference function optimizes the model to run on AWS DeepLens and feeds each camera frame into the model to get predictions.

This tutorial provides an example inference Lambda function for you to use. You can also copy and paste code sections directly from the Jupyter notebook you created earlier into the Lambda code editor.

To create a Lambda function to deploy to AWS DeepLens, complete the following steps:

1. Download [aws-deeplens-posture-lambda.zip](https://deeplens-public.s3.amazonaws.com/samples/posture-tracker/aws-deeplens-posture-lambda.zip) onto your computer.
2. On the Lambda console, choose **Create Function**.

3. Choose **Author from scratch **and choose the following options:
   1. For **Runtime**, choose **Python 3.7**.
   2. For **Choose or create an execution role**, choose **Use an existing role**.
   3. For **Existing role**, enter `service-role/AWSDeepLensLambdaRole`.
4. After you create the function, go to function’s detail page.
5. For **Code entry type**¸ choose **Upload zip**
6. Upload the aws-deeplens-posture-lambda.zip you downloaded earlier.
7. Choose **Save**.
8. In the AWS Lambda code editor, select the lambda_funtion.py file and enter an Amazon S3 bucket where you want to store the results.

```python
S3_BUCKET = '<YOUR_S3_BUCKET_NAME>'
```

9. Choose **Save**.
10. From the **Actions** drop-down menu, choose **Publish** **new version**.
11. Enter a version number and choose **Publish**. Publishing the function makes it available on the AWS DeepLens console so you can add it to your custom project.
12. Give your AWS DeepLens Lambda function permissions to put files in the Amazon S3 bucket. Inside your Lambda function editor, click on Permissions, then click on the AWSDeepLensLambda role name.

![](/images/400_advanced/420_posture_tracker/Screen-Shot-2020-07-13-at-9.00.53-AM-1024x304.png)

13. You will be directed to the IAM editor for the **AWSDeepLensLambda** role. Inside the IAM role editor, click Attach Policies.

![](/images/400_advanced/420_posture_tracker/Screen-Shot-2020-07-13-at-9.01.21-AM-1024x456.png)

14. Type in S3 to search for the AmazonS3 policy and check the AmazonS3FullAccess policy. Click **Attach Policy**.

![](/images/400_advanced/420_posture_tracker/Screen-Shot-2020-07-13-at-9.02.33-AM-1024x604.png)

### Understanding the Lambda function

This section walks you through some important parts of the Lambda function.

You load the GluonCV model with the following code:

```python
detector = model_zoo.get_model('yolo3_mobilenet1.0_coco', \
                pretrained=True, root='/opt/awscam/artifacts/')
pose_net = model_zoo.get_model('simple_pose_resnet18_v1b', \
                pretrained=True, root='/opt/awscam/artifacts/')

# Note that we can reset the classes of the detector to only include
# human, so that the NMS process is faster.

detector.reset_class(["person"], reuse_weights=['person'])
```

You run the model frame-per-frame over the images from the camera with the following code:

```python
ret, frame = awscam.getLastFrame()
img = mx.nd.array(frame)
x, img = data.transforms.presets.ssd.transform_test(img, short=200)

class_IDs, scores, bounding_boxs = detector(x)
pose_input, upscale_bbox = detector_to_simple_pose(img, class_IDs, scores, bounding_boxs)

predicted_heatmap = pose_net(pose_input)
pred_coords, confidence = heatmap_to_coord(predicted_heatmap, upscale_bbox)
```

The following code shows you how to send the text prediction results back to the cloud. Viewing the text results in the cloud is a convenient way to make sure the model is working correctly. Each AWS DeepLens device has a dedicated iot_topic automatically created to receive the inference results.

```python
# Send the top k results to the IoT console via MQTT
cloud_output = {
        'boxes': bounding_boxs,
        'box_scores': scores,
        'coords': pred_coords,
        'coord_scors': confidence
    }
client.publish(topic=iot_topic, payload=json.dumps(cloud_output))
```

Using the preceding key points, you can apply the geometric rules shown in the following sections to calculate angles between the body joints to determine if the person is sitting, standing, or hunching. You can change the geometric rules to suit your setup. As a follow-up activity to this tutorial, you can collect the pose data and train a simple ML model to more accurately predict when someone is standing or sitting.

#### Sitting vs. Standing

To determine if a person is standing or sitting, use the angle between the horizontal (ground) and the line connecting the hip and knee.

#### Hunching

When a person hunches, their head is typically looking down and their back is crooked. You can use the angles between the ear and shoulder and the shoulder and hip to determine if someone is hunching. Again, you can modify these geometric rules as you see fit. The following code inside the provided AWS DeepLens Lambda function determines if a person is hunching:

```python
def hip_and_hunch_angle(left_array):
    '''

    :param left_array: pass in the left most coordinates of a person , should be ok, since from side left and right overlap
    :return:
    '''
    # hip to knee angle
    hipX = left_array[-2][0] - left_array[-3][0]
    hipY = left_array[-2][1] - left_array[-3][1]

    # hunch angle = (hip to shoulder ) - (shoulder to ear )
    # (hip to shoulder )
    hunchX1 = left_array[-3][0] - left_array[-6][0]
    hunchY1 = left_array[-3][1] - left_array[-6][1]

    ang1 = degrees(atan2(hunchY1, hunchX1))

    # (shoulder to ear)
    hunchX2 = left_array[-6][0] - left_array[-7][0]
    hunchY2 = left_array[-6][1] - left_array[-7][1]
    ang2 = degrees(atan2(hunchY2, hunchX2))

    return degrees(atan2(hipY, hipX)), abs(ang1 - ang2)


def sitting_and_hunching(left_array):
    hip_ang, hunch_ang = hip_and_hunch_angle(left_array)
    if hip_ang < 25 or hip_ang > 155:
        print("sitting")
        hip = 0
    else:
        print("standing")
        hip = 1
    if hunch_ang < 3:
        print("no hunch")
        hunch = 0
    else:
        hunch = 1
    return hip, hunch
```

### Deploying the Lambda inference function to your AWS DeepLens device

To deploy your Lambda inference function to your AWS DeepLens device, complete the following steps:

1. On the AWS DeepLens console, under **Projects**, choose **Create new project**.
2. Choose **Create a new blank project**.
3. For **Project name**, enter `posture-tracker`.
4. Choose **Add model**.

To deploy a project, AWS DeepLens requires you to select a model and a Lambda function. In this tutorial, you are downloading the GluonCV models directly onto AWS DeepLens from inside your Lambda function so you can choose any existing model on the AWS DeepLens console to be deployed. The model selected on the AWS DeepLens console only serves as a stub and isn’t be used in the Lambda function. If you don’t have an existing model, [deploy a sample project](https://docs.aws.amazon.com/deeplens/latest/dg/deeplens-get-start-easy.html) and select the sample model.

5. Choose **Add function.**

6. Choose the Lambda function you created earlier.

7. Choose **Create**.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-024.jpg)

8. Select your newly created project and choose **Deploy to device**.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-026.jpg)

9. On the **Target device** page, select your device from the list.

10. Choose **Review**.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-028.jpg)

11. On the **Review and deploy** page, choose **Deploy**.

To verify that the project has deployed successfully, you can check the text prediction results sent back to the cloud via AWS IoT Greengrass. For instructions on how to view the text results, see [Viewing text output of custom model in AWS IoT Greengrass](https://docs.aws.amazon.com/deeplens/latest/dg/deeplens-getting-started-connect-iot-greengrass.html).

In addition to the text results, you can view the pose detection results overlaid on top of your AWS DeepLens live video stream. For instructions on viewing the live video stream, see [Viewing AWS DeepLens Output Streams](https://docs.aws.amazon.com/deeplens/latest/dg/deeplens-viewing-output.html).

The following screenshot shows what you will see in the project stream:

![](/images/400_advanced/420_posture_tracker/image3-1.png)