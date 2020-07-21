---
title: "Running the model locally on AWS DeepLens"
weight: 426
chapter: true
draft: false
tags:
  - advanced
---
## Visualizing your posture data over time with Amazon QuickSight

This next section shows you how to visualize your posture data with Amazon QuickSight. You first need to store the posture data in Amazon S3.

### Storing the posture data in Amazon S3

The following code example records posture data one time every second; you can adjust this interval to suit your needs. The code writes the records to a CSV file every 60 seconds and uploads the results to the Amazon S3 bucket you created earlier.

```python
  if len(physicalList) > 60:
    try:
      with open('/tmp/temp2.csv', 'w') as f:
        writer = csv.writer(f)
        writer.writerows(physicalList)
        physicalList = []
        write_to_s3('/tmp/temp2.csv', S3_BUCKET,
                    "Deeplens-posent/gluoncvpose/physicalstate-" + datetime.datetime.now().strftime(
                      "%Y-%b-%d-%H-%M-%S") + ".csv")
        except Exception as e:
          print(e)
```

Your Amazon S3 bucket now starts to fill up with CSV files containing posture data. See the following screenshot.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-036.jpg)

### Using Amazon QuickSight

You can now use Amazon QuickSight to create an interactive dashboard to visualize your posture data. First, make sure that Amazon QuickSight has access to the S3 bucket with your pose data.

1. On the Amazon QuickSight console, from the menu bar, choose **Manage QuickSight**.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-038.gif)

2. Choose **Security & permissions**.

3. Choose **Add or remove**.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-040.jpg)

4. Select **Amazon S3**.

5. Choose **Select S3 buckets**.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-042.jpg)

6. Select the bucket containing your pose data.

7. Choose **Update**.

8. On the Amazon QuickSight landing page, choose **New analysis**.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-044.jpg)

9. Choose **New data set**.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-046.jpg)

You see a variety of options for data sources.

10. Choose **S3**.

A pop-up window appears that asks for your data source name and manifest file. A manifest file tells Amazon QuickSight where to look for your data and how your dataset is structured.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-048.jpg)

11. To build a manifest file for your posture data files in Amazon S3, open your preferred text editor and enter the following code:

```json
{ "fileLocations": [ { "URIPrefixes": ["s3://YOUR_BUCKET_NAME/FOLDER_OF_POSE_DATA" ] } ], "globalUploadSettings": { "format": "CSV", "delimiter": ",", "textqualifier": "'", "containsHeader": "true" } }
```

12. Save the text file with the name `manifest.json`.

13. In the **New S3 data source** window, select **Upload**.

14. Upload your manifest file.

15. Choose **Connect**.

If you set up the data source successfully, you see a confirmation window like the following screenshot.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-050.jpg)

To troubleshoot any access or permissions errors, see [How do I allow Amazon QuickSight access to my S3 bucket when I have a deny policy?](https://aws.amazon.com/premiumsupport/knowledge-center/quicksight-deny-policy-allow-bucket/)

16. Choose **Visualize**.

You can now experiment with the data to build visualizations. See the following screenshot.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-052.jpg)

The following bar graphs show visualizations you can quickly make with the posture data.

![](/images/400_advanced/420_posture_tracker/wfh-posture-tracker-054.jpg)

For instructions on creating more complex visualizations, see [Tutorial: Create an Analysis](https://docs.aws.amazon.com/quicksight/latest/user/example-create-an-analysis.html).

