---
title: "Experiment with AWS DeepLens and GluonCV"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 422 
tags:
  - advanced
---
Normally, AWS developers use Jupyter notebooks hosted in [Amazon SageMaker](https://aws.amazon.com/sagemaker/) to experiment with GluonCV models. Jupyter notebook is an open-source web application that allows you to create and share documents that contain live code, equations, visualizations, and narrative text. In this tutorial you are going to create and run Jupyter notebooks directly on an AWS DeepLens device, just like any other Linux computer, in order to enable rapid experimentation.

Starting with version AWS DeepLens software version 1.4.5, you can run GluonCV pretrained models directly on AWS DeepLens. To check the version number and update your software, go to the [AWS DeepLens console](https://us-east-1.console.aws.amazon.com/deeplens/home), under **Devices** select your DeepLens device, and look at the **Device status** section. You should see the version number similar to the following screenshot.

![DeepLens version check](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-003.jpg)

To start experimenting with GluonCV models on DeepLens, complete the following steps:

1. SSH into your AWS DeepLens device.

To do so, you need the IP address of AWS DeepLens on the local network. To find the IP address, select your device on the AWS DeepLens console. Your IP address is listed in the **Device Details** section.

![DeepLens version check](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-005-2.jpg)

You also need to make sure that SSH is enabled for your device. For more information about enabling SSH on your device, see [View or Update Your AWS DeepLens 2019 Edition Device Settings](https://docs.aws.amazon.com/deeplens/latest/dg/deeplens-v11-device-view-or-edit-settings.html).

Open a terminal application on your computer. SSH into your DeepLens by entering the following code into your terminal application:

```bash
ssh aws_cam@<YOUR_DEEPLENS_IP>
```

When you see a password prompt, enter the SSH password you chose when you set up SSH on your device.

2. Install Jupyter notebook and GluonCV on your DeepLens. Enter each of the following commands one at a time in the SSH terminal. Press Enter after each line entry.

```bash
sudo python3 -m pip install –-upgrade pip

sudo python3 -m pip install notebook

sudo python3.7 -m pip install ipykernel

python3.7 -m ipykernel install  --name 'Python3.7' --user

sudo python3.7 -m pip install gluoncv
```

3. Generate a default configuration file for Jupyter notebook:

```bash
jupyter notebook --generate-config
```

4. Edit the Jupyter configuration file in your SSH session to allow access to the Jupyter notebook running on AWS DeepLens from your laptop.

```bash
nano ~/.jupyter/jupyter_notebook_config.py
```

5. Add the following lines to the top of the config file:

```bash
c.NotebookApp.ip = '0.0.0.0'
c.NotebookApp.open_browser = False
```

6. Save the file (if you are using the [nano editor](https://wiki.gentoo.org/wiki/Nano/Basics_Guide), press Ctrl+X and then Y).

7. Open up a port in the AWS DeepLens firewall to allow traffic to Jupyter notebook. See the following code:

```bash
sudo ufw allow 8888
```

8. Run the Jupyter notebook server with the following code:

```bash
jupyter notebook
```

You should see output like the following screenshot:

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-007.jpg)

9. Copy the link and replace the IP portion (DeepLens or 127.0.0.1). See the following code:

```bash
http://(DeepLens or 127.0.0.1):8888/?token=sometoken
```

For example, the URL based on the preceding screenshot is `http://10.0.0.250:8888/?token=7adf9c523ba91f95cfc0ba3cacfc01cd7e7b68a271e870a8`.

10. Enter this link into your laptop web browser.

You should see something like the following screenshot.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-009.jpg)

11. Choose **New** to create a new notebook.

12. Choose **Python3.7**.

### Capturing a frame from your camera

To capture a frame from the camera, first make sure you aren’t running any projects on AWS DeepLens.

1. On the AWS Deeplens console, go to your device page.
2. If a project is deployed, you should see a project name in the **Current Project** pane. Choose **Remove Project** if there is a project deployed to your AWS DeepLens.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-011.jpg)

3. Now go back to the Jupyter notebook running on your AWS DeepLens, enter the following code into your first code cell:

```python
import awscam
import cv2

ret,frame = awscam.getLastFrame()
print(frame.shape)
```

4. Press **Shift+Enter** to execute the code inside the cell.

Alternatively, you can press the Run button in the Jupyter toolbar as shown in the screenshot below:

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-013.jpg)

You should see the size of the image captured by AWS DeepLens similar to the following text:

```python
(1520, 2688, 3)
```

The three numbers show the height, width, and number of color channels (red, green, blue) of the image.

5. To view the image, enter the following code in the next code cell:

```python
%matplotlib inline
from matplotlib import pyplot as plt
plt.imshow(frame)
plt.show()
```

You should see an image similar to the following screenshot:

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-015.jpg)

### Detecting people and poses

Now that you have an image, you can use GluonCV pre-trained models to detect people and poses. For more information, see [Predict with pre-trained Simple Pose Estimation models](https://gluon-cv.mxnet.io/build/examples_pose/demo_simple_pose.html) from the GluonCV model zoo.

1. In a new code cell, enter the following code to import the necessary dependencies:

```python
import mxnet as mx
from gluoncv import model_zoo, data, utils
from gluoncv.data.transforms.pose import detector_to_simple_pose, heatmap_to_coord
```

2. You load two pre-trained models, one to detect people (yolo3_mobilenet1.0_coco) in the frame and one to detect the pose (simple_pose_resnet18_v1b) for each person detected. To load the pre-trained models, enter the following code in a new code cell:

```python
people_detector = model_zoo.get_model('yolo3_mobilenet1.0_coco', pretrained=True)
pose_detector = model_zoo.get_model('simple_pose_resnet18_v1b', pretrained=True)
```

3. Because the `yolo_mobilenet1.0_coco` pre-trained model is trained to detect many types of objects in addition to people, the code below narrows down the detection criteria to just people so that the model runs faster. For more information about the other types of objects that the model can predict, see the [GluonCV MSCoco Detection source code](https://gluon-cv.mxnet.io/_modules/gluoncv/data/mscoco/detection.html).

```python
people_detector.reset_class(["person"], reuse_weights=['person'])
```

4. The following code shows how to use the people detector to detect people in the frame. The outputs of the people detector are the **class_IDs** (just “person” in this use case because we’ve limited the model’s search scope), the confidence **scores**, and a **bounding box** around each person detected in the frame.

```python
img = mx.nd.array(frame)
x, img = data.transforms.presets.ssd.transform_test(img, short=256)
class_IDs, scores, bounding_boxs = people_detector(x)
```

5. Enter the following code to feed the results from the people detector into the pose detector for each person found. Normally you need to use the bounding boxes to crop out each person found in the frame by the people detector, then resize each cropped person image into appropriately sized inputs for the pose detector. Fortunately GluonCV comes with a`detector_to_simple_pose` function that takes care of cropping and resizing for you.

```python
pose_input, upscale_bbox = detector_to_simple_pose(img, class_IDs, scores, bounding_boxs)

predicted_heatmap = pose_detector(pose_input)
pred_coords, confidence = heatmap_to_coord(predicted_heatmap, upscale_bbox)
```

6. The following code overlays the results of the pose detector onto the original image so you can visualize the result:

```python
ax = utils.viz.plot_keypoints(img, pred_coords, confidence,
                              class_IDs, bounding_boxs,scores, box_thresh=0.5, keypoint_thresh=0.2)
plt.show(ax)
```

After completing steps 1-6, you should see an image similar to the following screenshot.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-017-2.jpg)

If you get an error similar to the *ValueError* output below, make sure you have at least one person in the camera’s view.

```python
ValueError: In HybridBlock, there must be one NDArray or one Symbol in the input. Please check the type of the args
```

So far, you experimented with a pose detector on AWS DeepLens using Jupyter notebooks. You can now collect some data to figure out how to detect when someone is hunching, sitting, or standing. To collect data, you can save the image frame from the camera out to disk using the built-in OpenCV module. See the following code:

```python
cv2.imwrite('output.jpg', frame)
```