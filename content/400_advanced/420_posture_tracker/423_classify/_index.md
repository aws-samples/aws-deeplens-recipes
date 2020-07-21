---
title: "Train a model with Amazon SageMaker"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 423
tags:
  - advanced
---
## Classifying postures with the GluonCV pose key points

After you have collected a few samples of different postures, you can start to detect bad posture by applying some rudimentary rules.

### Understanding the GluonCV pose estimation key points

The GluonCV pose estimation model outputs 17 key points for each person detected. In this section, you see how those points are mapped to human body joints and how to apply simple rules to determine if a person is sitting, standing, or hunching.

This solution makes the following assumptions:

- The camera sees your entire body from head to toe, regardless of whether you are sitting or standing
- The camera sees a profile view of your body
- No obstacles exist between camera and the subject

The following is an example input image. We’ve asked the actor in this image to face the camera instead of showing the profile view to illustrate the key body joints produced by the pose estimation model.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-019-2.jpg)

The following image is the output of the model drawn as lines and key points onto the input image. The cyan rectangle shows where the people detector thinks a person is in the image.

![](image021-3.jpg)

The following code shows the raw results of the pose detector. The code comments show how each entry maps to point on the a human body:

```python
array([[142.96875,  84.96875],# Nose
       [152.34375,  75.59375],# Right Eye
       [128.90625,  75.59375],# Left Eye
       [175.78125,  89.65625],# Right Ear
       [114.84375,  99.03125],# Left Ear
       [217.96875, 164.65625],# Right Shoulder
       [ 91.40625, 178.71875],# Left Shoulder
       [316.40625, 197.46875],# Right Elblow
       [  9.375  , 232.625  ],# Left Elbow
       [414.84375, 192.78125],# Right Wrist
       [ 44.53125, 244.34375],# Left Wrist
       [199.21875, 366.21875],# Right Hip
       [128.90625, 366.21875],# Left Hip
       [208.59375, 506.84375],# Right Knee
       [124.21875, 506.84375],# Left Knee
       [215.625  , 570.125  ],# Right Ankle
       [121.875  , 570.125  ]],# Left Ankle
```



