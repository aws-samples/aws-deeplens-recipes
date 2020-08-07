---
title: "Creating an AWS DeepLens inference Lambda function"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 322 
tags:
  - intermediate
---
In this section, you create an inference function that you deploy to AWS DeepLens. The inference function isolates the drawing area, optimizes the model to run on AWS DeepLens, and feeds each camera frame into the model to generate predictions.

To create your function, complete the following steps:

Download [aws-deeplens-pictionary-lambda.zip](https://deeplens-public.s3.amazonaws.com/samples/doodle-game/aws-deeplens-doodle-lambda.zip).

On the Lambda console, choose **Create function**.

Choose **Author from scratch** and choose the following options:

* For **Runtime**, choose **Python 2.7**.

* For **Choose or create an execution role**, choose **Use an existing role**.
  * For **Existing role**, enter `service-role/AWSDeepLensLambdaRole`.

After you create the function, go to the **Function code**

From the **Actions** drop-down menu in **Function code**, choose **Upload a .zip file***.*

Upload the `aws-deeplens-pictionary-lambda.zip` file you downloaded earlier.

Choose **Save**.

From the **Actions** drop-down menu, choose **Publish** **new version**.

Enter a version number and choose **Publish**.

![](/images/300_intermediate/330_guess_drawing/dl-lambda-1.jpg)

Publishing the function makes it available on the AWS DeepLens console so you can add it to your custom project.

## Understanding the Lambda function

You should pay attention to the following two files:

- **labels.txt** – This file is for the inference function to translate the numerical result from the model into human readable labels used in our game. It contains a list of 36 objects on which the model has been trained to recognize sketches.
- **lambda_function.py** – This file contains the preprocessing algorithm and the function being called to generate predictions on drawings and send back results.

Because the model was trained on digital sketches with clean, white backgrounds, we have a preprocessing algorithm that helps isolate the drawing and remove any clutter in the background. You can find the algorithm to do this in the `isolate_image()` function inside the `lambda_function.py` file. In this section, we walk you through some important parts of the preprocessing algorithm.

#### Fisheye calibration

AWS DeepLens uses a wide-angle lens to get as much information as possible in the frame. As a result, any input frame is distorted, especially for the rectangular shape of a whiteboard. Therefore, you need to perform fisheye calibration to straighten the edges. As part of this post, we provide the calibration code to undistort your AWS DeepLens images. The following code straightens the edges and eliminates the distortion:

```python
def undistort(frame): 
    frame_height, frame_width, _ = frame.shape
    K=np.array([[511.98828907136766, 0.0, 426.48016197546474], 
                [0.0, 513.8644747557715, 236.89875770956868], 
                [0.0, 0.0, 1.0]])
    D=np.array([[-0.10969105781526832], [0.03463562293251206], 
                [-0.2341226037892333], [0.34335682066685935]])
    DIM = (int(frame_width/3), int(frame_height/3))
    frame_resize = cv2.resize(frame, DIM)
    map1, map2 = cv2.fisheye.initUndistortRectifyMap(K, D, np.eye(3), \
                                                     K, DIM, cv2.CV_16SC2)
    undistorted_img = cv2.remap(frame_resize, map1, map2, \
                                interpolation=cv2.INTER_LINEAR, \
                                borderMode=cv2.BORDER_CONSTANT)
    return undistorted_img
```

The following screenshots shows the raw image captured by AWS DeepLens.

![](/images/300_intermediate/330_guess_drawing/imgprocess-1.jpg)

The following screenshot shows the results of the undistort function with fisheye calibration.

![](/images/300_intermediate/330_guess_drawing/imgprocess-2.jpg)

The next code section enhances the images to eliminate the effects caused by different lighting conditions:

```python
enh_con = ImageEnhance.Contrast(img_colored)
contrast = 5.01
img_contrasted = enh_con.enhance(contrast)
image = img_contrasted
image = np.array(image)
```

The following screenshot shows the results of the contrast enhancement.

![](/images/300_intermediate/330_guess_drawing/imgprocess-3.jpg)

#### Canny Edge Detection

The next part of the preprocessing algorithm uses OpenCV’s Canny Edge Detection technique to find the edges in the image. See the following code:

```python
# these constants are carefully picked
    MORPH = 9
    CANNY = 84
    HOUGH = 25
    img = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    cv2.GaussianBlur(img, (3,3), 0, img)
    # this is to recognize white on white
    kernel = cv2.getStructuringElement(cv2.MORPH_RECT,(MORPH,MORPH))
    dilated = cv2.dilate(img, kernel)
    edges = cv2.Canny(dilated, 0, CANNY, apertureSize=3)
    lines = cv2.HoughLinesP(edges, 1,  3.14/180, HOUGH)
    for line in lines[0]:
        cv2.line(edges, (line[0], line[1]), (line[2], line[3]), (255,0,0), 2, 8)

```

The following screenshot shows the results from applying the Canny Edge Detector.

![](/images/300_intermediate/330_guess_drawing/imgprocess-4.jpg)

For more information about how Canny Edge Detection works, see the [Canny Edge Detection tutorial](https://docs.opencv.org/3.4.10/d7/de1/tutorial_js_canny.html) on the OpenCV website.

#### Finding contours

After the edges are found, you can use OpenCV’s `findContours()` function to extract the polygon contours from the image. This function returns a list of polygon shapes that are closed and ignores any open edges or lines. See the following code:

```python
contours, _ = cv2.findContours(edges.copy(), cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
contours = filter(lambda cont: cv2.arcLength(cont, False) > 100, contours)
contours = filter(lambda cont: cv2.contourArea(cont) > 10000, contours)
 result = None
for idx, c in enumerate(contours):
      if len(c) < Config.min_contours:
          continue
      epsilon = Config.epsilon_start
      while True:
            approx = cv2.approxPolyDP(c, epsilon, True)
            approx = approx.reshape((len(approx), 2))
            new_approx = []
            for i in range(len(approx)):
                if 80 < approx[i][0] < 750:
                    new_approx.append(approx[i])
            approx = np.array(new_approx)
            if (len(approx) < 4):
                break
            if math.fabs(cv2.contourArea(approx)) > Config.min_area:
                if (len(approx) > 4):
                    epsilon += Config.epsilon_step
                    continue
                else:
                    # for p in approx:
                    #  cv2.circle(binary,(p[0][0],p[0][1]),8,(255,255,0),thickness=-1)
                    approx = approx.reshape((4, 2))
                    # [top-left, top-right, bottom-right, bottom-left]
                    src_rect = order_points(approx)
                    cv2.drawContours(image, c, -1, (0, 255, 255), 1)
                    cv2.line(image, (src_rect[0][0], src_rect[0][1]), (src_rect[1][0], src_rect[1][1]),
                             color=(100, 255, 100))
                    cv2.line(image, (src_rect[2][0], src_rect[2][1]), (src_rect[1][0], src_rect[1][1]),
                             color=(100, 255, 100))
                    cv2.line(image, (src_rect[2][0], src_rect[2][1]), (src_rect[3][0], src_rect[3][1]), 
                             color=(100, 255, 100))
                    cv2.line(image, (src_rect[0][0], src_rect[0][1]), (src_rect[3][0], src_rect[3][1]),
                             color=(100, 255, 100))
```

For more information, see [Contours: Getting Started](https://docs.opencv.org/3.4/d4/d73/tutorial_py_contours_begin.html).



#### Perspective transformation

Finally, the preprocessing algorithm does perspective transformation to correct for any skew. The following code helps achieve perspective transformation and crop a rectangular area:

```python
M = cv2.getPerspectiveTransform(src_rect, dst_rect)
warped = cv2.warpPerspective(image, M, (w, h))	
```

The following image is the input of the preprocessing algorithm.

![](/images/300_intermediate/330_guess_drawing/imgprocess-5.jpg)

The following image is the final result.

![](/images/300_intermediate/330_guess_drawing/imgprocess-6.jpg)

## Performing inference

In this section, you learn how to perform inference with an ML model and send back results from AWS DeepLens.

AWS DeepLens uses the Intel OpenVino model optimizer to optimize the ML model to run on DeepLens hardware. The following code optimizes a model to run locally:

```python
error, model_path = mo.optimize(model_name, INPUT_WIDTH, INPUT_HEIGHT)
```

The following code loads the model:

```python
model = awscam.Model(model_path, {'GPU': 1})
```

The following code helps run the model frame-per-frame over the images from the camera:

```python
ret, frame = awscam.getLastFrame()
```

Viewing the text results in the cloud is a convenient way to make sure the model is working correctly. Each AWS DeepLens device has a dedicated `iot_topic` automatically created to receive the inference results. The following code sends the messages from AWS DeepLens to the IoT Core console:

```python
# Send the top k results to the IoT console via MQTT
cloud_output = {}
for obj in top_k:
    cloud_output[output_map[obj['label']]] = obj['prob']
client.publish(topic=iot_topic, payload=json.dumps(cloud_output))
```

