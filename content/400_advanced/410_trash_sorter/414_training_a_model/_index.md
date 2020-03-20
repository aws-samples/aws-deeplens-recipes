---
title: "Train a model with Amazon SageMaker"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 414
tags:
  - advanced
---
##### Download the [aws-deeplens-custom-trash-detector.ipynb](/code/trash-sorter/aws-deeplens-custom-trash-detector.ipynb) file. 

We will be using Jupyter notebooks hosted by Amazon SageMaker as our development environment to train our models. The Jupyter Notebook is an open-source web application that allows you to create and share documents that contain live code, equations, visualizations and narrative text.

From your Amazon Sagemaker console, select **Notebook instances** then **Create notebook instance**.

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

Run the notebook example through the end.



