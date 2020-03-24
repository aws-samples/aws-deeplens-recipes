---
title: "Train a model with Amazon SageMaker"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 414
tags:
  - advanced
---
This recipe uses Amazon SageMaker Jupyter notebooks as the development environment to train your models. Jupyter Notebook is an open-source web application that allows you to create and share documents that contain live code, equations, visualizations, and narrative text. A full Jupyter notebook has been prepared for you to follow along.

First, download the example notebook: [aws-deeplens-custom-trash-detector.ipynb](/code/trash-sorter/aws-deeplens-custom-trash-detector.ipynb) 

Then to create a custom image classification model, you need to use a graphics processing unit (GPU) enabled training job instance. GPUs are excellent at parallelizing the computations required to train a neural network. This tutorial uses a single ml.p2.xlarge instance. In order to access a GPU-enabled training job instance, you must submit a request for a service limit increase to the AWS Support Center. You can follow the [instructions here](/400_advanced/410_trash_sorter/411_setup/) to increase your limit. 

After you have received your limit increase, [Launch your Amazon SageMaker notebook instance](https://docs.aws.amazon.com/sagemaker/latest/dg/gs-setup-working-env.html). 

* Use a t2.medium instance type, which is included in the Amazon SageMaker free tier. For more information, see [Amazon SageMaker Pricing](https://aws.amazon.com/sagemaker/pricing/).
* When you create a role, reference the S3 bucket the project uses (prefix **deeplens-**).

Enter a name for your notebook instance, leave everything else the default except for the volume size. Enter volume size of *50 GB* or more because we'll first download the data to our notebook instance before uploading the data to Amazon S3.

![lab4-sagemaker-create-notebook-1](/images/400_train_a_custom_model/lab4-sagemaker-create-notebook-1.png)

![lab4-sagemaker-create-notebook-2](/images/400_train_a_custom_model/lab4-sagemaker-create-notebook-2.png)

If you use Amazon SageMaker for the first time, please create an IAM role by choosing "Create a new role" from the selection list.

![l400-lab0-4](/images/400_train_a_custom_model/lab4-sagemaker-create-notebook-6.png)

On the pop-up menu, select **Any S3 bucket** to allow the notebook instance to any S3 buckets in your account. Then, click on "Create role" button on the bottom.

Your notebook instance will take a minute to be configured. Once you see the status change to **InService** on the **Notebook instances** page, choose **Open Jupyter** to launch your newly created Jupyter notebook instance.

You should see the page below

![](/images/400_advanced/410_build_a_custom_ml/414_training_a_model/notebookupload.jpg)

Now upload the [aws-deeplens-custom-trash-detector.ipynb](/code/trash-sorter/aws-deeplens-custom-trash-detector.ipynb) file you downloaded earlier.

Once the notebook has uploaded, click on its name to open it.

If you’re new to Jupyter notebooks, you will notice that it contains mixture of text and code cells. To run a piece of code, select the cell and then press shift + enter. While the cell is running an *asterisk* will appear next to the cell. Once complete, an output number and new output cell will appear below the original cell.

Click on the **Run** button in the top toolbar to execute the code/text in that section. Continue clicking the run button for subsequent cells until you get to the bottom of the notebook. Alternatively, you can also use the keyboard shortcuts **Shift + Enter**.

As each section runs, it will spit out log output of what it's doing. Sometimes you'll see a **[ * ]** on the left hand side. This means that the code is still running. Once the code is done running, you'll see a number. This number represents the order in which the code was executed.

After you follow the notebook through to the end, you have a trained model to distinguish between different types of trash.

