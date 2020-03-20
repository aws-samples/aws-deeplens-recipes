---
title: "Update DeepLens Greengrass Group"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 59
---
### Step 3

In this step, you add an AWS IoT devices to your existing DeepLens Greengrass group. This process includes registering the device and configuring certificates and keys to allow connectivty to AWS IoT Greengrass.
 
**1.	In the AWS IoT console, choose Greengrass, and then choose Groups.**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416c_update_deeplens_gg/416c_step1a_choose_gg.png)
![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416c_update_deeplens_gg/416c_step1b_choose_gg_groups.png)


**2. Choose the existing DeepLens group.**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416c_update_deeplens_gg/416c_step2_choose_gg_group.png)

**3. On the group configuration page, choose Devices, and then choose Add Device.**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416c_update_deeplens_gg/416c_step3_choose_add_device.png)

**4. On the Add a Device page, choose Create New Device.**

 ![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416c_update_deeplens_gg/416c_step4_create_new_device.png)

**5. On the Create a Registry entry for a device page, register this device as RaspberryPi_SenseHAT, and then choose Next.**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416c_update_deeplens_gg/416c_step5_create_registry_device.png)

**6. On the Set up security page, for 1-Click, choose Use Defaults. This option generates a device certificate with an attached AWS IoT policy and public and private key.**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416c_update_deeplens_gg/416c_step6_setup_security.png)

**7. Create a folder on your computer. Download the certificate and keys for your device into the folder.**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416c_update_deeplens_gg/416c_step7_save_security_creds.png)

**8. Make a note of the common hash component in the file names for the RapsberryPi_SenseHAT device certificate and keys (in this example, a72ab238d3). You need it later. Choose Finish.**


**9. Decompress the hash-setup.tar.gz file. For example, this can be done by running the following command:**

```bash
tar -xzf hash-setup.tar.gz
```

**Note**
On Windows, you can decompress .tar.gz files using a tool such as 7-Zip or WinZip.

**10. Save the root CA certificate as root-ca-cert.pem in the same folder as the RapsberryPi_SenseHAT device certificates and keys. All these files should be in one folder on your computer.  For ATS endpoints (preferred), download the appropriate ATS root CA certificate, such as Amazon Root CA 1.**

**Note**
If you're using a web browser on the Mac, you will see this certificate is already installed as a certificate authority, open a Terminal window and download the certificate into the folder that contains the device certificates and keys. For example, you can run the following command to download the Amazon Root CA 1 certificate.

```bash
cd path-to-folder-containing-device-certificates

curl -o ./root-ca-cert.pem https://www.amazontrust.com/repository/AmazonRootCA1.pem
```

Run the command below to ensure that the file is not empty. If the file is empty, check the URL and try the curl command again.

```bash
cat root-ca-cert.pem
```
