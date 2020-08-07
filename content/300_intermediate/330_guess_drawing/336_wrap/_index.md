---
title: "Wrap Up"
weight: 336
chapter: true
draft: false
tags:
  - intermediate
---
The following images show the prediction outputs of our model with the name of an object and its probability. You need to have your AWS DeepLens located in front of a rectangular-shaped whiteboard or a piece of white paper to ensure that the edges are visible in the frame.

![](/images/300_intermediate/330_guess_drawing/dl-preview-1.jpg)

![](/images/300_intermediate/330_guess_drawing/dl-preview-2.jpg)

### Conclusion

In this tutorial, you learned about the preprocessing algorithm to isolate a drawing area and how to deploy a pre-trained model onto AWS DeepLens to recognize sketches. Next, you learned how to send results from AWS IoT to Kinesis Data Streams. Finally, you learned how to create a custom Alexa skill with Lambda to retrieve the detected objects in the data stream and return the results to players via Alexa.

**Don't forget to turn off your Kinesis Stream and delete AWS resources created as part of this tutorial to avoid being charged.**

