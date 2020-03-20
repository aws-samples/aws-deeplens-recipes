---
title: "Configure Raspberry Pi"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 61
---
### Step 5

Now that we have done all the configuration for the communication to the AWS DeepLens, the Raspberry Pi needs to be configured to use the information we have collected.

**1.	To get started we need to create basicDiscovery.py from the Python code below in the folder of your computer where you downloaded the IoT device certificates.  You can use your favorite text editor to paste the code below and save the file.**

```python
# /*
# * Copyright 2010-2020 Amazon.com, Inc. or its affiliates. All Rights Reserved.
# *
# * Licensed under the Apache License, Version 2.0 (the "License").
# * You may not use this file except in compliance with the License.
# * A copy of the License is located at
# *
# *  http://aws.amazon.com/apache2.0
# *
# * or in the "license" file accompanying this file. This file is distributed
# * on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either
# * express or implied. See the License for the specific language governing
# * permissions and limitations under the License.
# */


import os
import sys
import time
import uuid
import json
import logging
import argparse
from AWSIoTPythonSDK.core.greengrass.discovery.providers import DiscoveryInfoProvider
from AWSIoTPythonSDK.core.protocol.connection.cores import ProgressiveBackOffCore
from AWSIoTPythonSDK.MQTTLib import AWSIoTMQTTClient
from AWSIoTPythonSDK.exception.AWSIoTExceptions import DiscoveryInvalidRequestException


## RUNNING SenseHat
from sense_hat import SenseHat

AllowedActions = ['both', 'publish', 'subscribe']

# General message notification callback
sense = SenseHat()

def customOnMessage(message):
    
	message_from_core = json.loads(message.payload)
	
	O = (0, 0, 0)
	R = (255, 0, 0)
	C = (0, 255, 0)
	B = (0, 0, 255)
	X = (255, 103, 0)

	question_mark = [
	O, O, O, X, X, O, O, O,
	O, O, X, O, O, X, O, O,
	O, O, O, O, O, X, O, O,
	O, O, O, O, X, O, O, O,
	O, O, O, X, O, O, O, O,
	O, O, O, X, O, O, O, O,
	O, O, O, O, O, O, O, O,
	O, O, O, X, O, O, O, O
	]

	compost = [
	C, C, C, C, C, C, O, O,
	C, C, C, C, C, C, O, O,
	C, C, O, O, O, O, O, O,
	C, C, O, O, O, O, O, O,
	C, C, O, O, O, O, O, O,
	C, C, O, O, O, O, O, O,
	C, C, C, C, C, C, O, O,
	C, C, C, C, C, C, O, O
	]

    	recycle = [
	B, B, B, B, B, O, O, O,
	B, B, B, B, B, B, O, O,
	B, B, O, O, B, B, O, O,
	B, B, B, B, B, B, O, O,
	B, B, B, B, B, O, O, O,
	B, B, O, B, B, O, O, O,
	B, B, O, O, B, B, O, O,
	B, B, O, O, B, B, O, O
	]

	landfill = [
	R, R, O, O, O, O, O, O,
	R, R, O, O, O, O, O, O,
	R, R, O, O, O, O, O, O,
	R, R, O, O, O, O, O, O,
	R, R, O, O, O, O, O, O,
	R, R, O, O, O, O, O, O,
	R, R, R, R, R, R, O, O,
	R, R, R, R, R, R, O, O
	]


	sense.set_pixels(question_mark)

	item = message_from_core["object"]
	
	if "Recycling" in item:
       	sense.set_pixels(recycle)
		time.sleep(1)
		sense.set_pixels(question_mark)
	elif "Compost" in item:
       	sense.set_pixels(compost)
		time.sleep(1)
		sense.set_pixels(question_mark)
	elif "Landfill" in item:
       	sense.set_pixels(landfill)
		time.sleep(1)
		sense.set_pixels(question_mark)
	
	print('Received message on topic %s: %s\n' % (message.topic, message.payload))

MAX_DISCOVERY_RETRIES = 10
GROUP_CA_PATH = "./groupCA/"

# Read in command-line parameters
parser = argparse.ArgumentParser()
parser.add_argument("-e", "--endpoint", action="store", required=True, dest="host", help="Your AWS IoT custom endpoint")
parser.add_argument("-r", "--rootCA", action="store", required=True, dest="rootCAPath", help="Root CA file path")
parser.add_argument("-c", "--cert", action="store", dest="certificatePath", help="Certificate file path")
parser.add_argument("-k", "--key", action="store", dest="privateKeyPath", help="Private key file path")
parser.add_argument("-n", "--thingName", action="store", dest="thingName", default="Bot", help="Targeted thing name")
parser.add_argument("-t", "--topic", action="store", dest="topic", default="sdk/test/Python", help="Targeted topic")
parser.add_argument("-m", "--mode", action="store", dest="mode", default="both",
                    help="Operation modes: %s"%str(AllowedActions))
parser.add_argument("-M", "--message", action="store", dest="message", default="Hello World!",
                    help="Message to publish")

args = parser.parse_args()
host = args.host
rootCAPath = args.rootCAPath
certificatePath = args.certificatePath
privateKeyPath = args.privateKeyPath
clientId = args.thingName
thingName = args.thingName
topic = args.topic

if args.mode not in AllowedActions:
	parser.error("Unknown --mode option %s. Must be one of %s" % (args.mode, str(AllowedActions)))
	exit(2)

if not args.certificatePath or not args.privateKeyPath:
	parser.error("Missing credentials for authentication.")
	exit(2)

# Configure logging
logger = logging.getLogger("AWSIoTPythonSDK.core")
logger.setLevel(logging.DEBUG)
streamHandler = logging.StreamHandler()
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
streamHandler.setFormatter(formatter)
# logger.addHandler(streamHandler)

# Progressive back off core
backOffCore = ProgressiveBackOffCore()

# Discover GGCs
discoveryInfoProvider = DiscoveryInfoProvider()
discoveryInfoProvider.configureEndpoint(host)
discoveryInfoProvider.configureCredentials(rootCAPath, certificatePath, privateKeyPath)
discoveryInfoProvider.configureTimeout(10)  # 10 sec

retryCount = MAX_DISCOVERY_RETRIES
discovered = False
groupCA = None
coreInfo = None
while retryCount != 0:
	try:
		discoveryInfo = discoveryInfoProvider.discover(thingName)
		caList = discoveryInfo.getAllCas()
		coreList = discoveryInfo.getAllCores()

		# We only pick the first ca and core info
		groupId, ca = caList[0]
		coreInfo = coreList[0]
		print("Discovered GGC: %s from Group: %s" % (coreInfo.coreThingArn, groupId))

		print("Now we persist the connectivity/identity information...")
		groupCA = GROUP_CA_PATH + groupId + "_CA_" + str(uuid.uuid4()) + ".crt"
		if not os.path.exists(GROUP_CA_PATH):
			os.makedirs(GROUP_CA_PATH)
		groupCAFile = open(groupCA, "w")
		groupCAFile.write(ca)
		groupCAFile.close()

		discovered = True
		print("Now proceed to the connecting flow...")
		break
	except DiscoveryInvalidRequestException as e:
		print("Invalid discovery request detected!")
		print("Type: %s" % str(type(e)))
		print("Error message: %s" % e.message)
		print("Stopping...")
		break
	except BaseException as e:
		print("Error in discovery!")
		print("Type: %s" % str(type(e)))
		print("Error message: %s" % e.message)
		retryCount -= 1
		print("\n%d/%d retries left\n" % (retryCount, MAX_DISCOVERY_RETRIES))
		print("Backing off...\n")
		backOffCore.backOff()

if not discovered:
	print("Discovery failed after %d retries. Exiting...\n" % (MAX_DISCOVERY_RETRIES))
	sys.exit(-1)

# Iterate through all connection options for the core and use the first successful one
myAWSIoTMQTTClient = AWSIoTMQTTClient(clientId)
myAWSIoTMQTTClient.configureCredentials(groupCA, privateKeyPath, certificatePath)
myAWSIoTMQTTClient.onMessage = customOnMessage

connected = False
for connectivityInfo in coreInfo.connectivityInfoList:
	currentHost = connectivityInfo.host
	currentPort = connectivityInfo.port
	print("Trying to connect to core at %s:%d" % (currentHost, currentPort))
	myAWSIoTMQTTClient.configureEndpoint(currentHost, currentPort)
	try:
		myAWSIoTMQTTClient.connect()
		connected = True
		break
	except BaseException as e:
		print("Error in connect!")
		print("Type: %s" % str(type(e)))
		print("Error message: %s" % e.message)

if not connected:
	print("Cannot connect to core %s. Exiting..." % coreInfo.coreThingArn)
	sys.exit(-2)

# Successfully connected to the core
if args.mode == 'both' or args.mode == 'subscribe':
	myAWSIoTMQTTClient.subscribe(topic, 0, None)
time.sleep(2)

loopCount = 0
while True:
	if args.mode == 'both' or args.mode == 'publish':
		message = {}
		message['message'] = args.message
		message['sequence'] = loopCount
		messageJson = json.dumps(message)
		myAWSIoTMQTTClient.publish(topic, messageJson, 0)
		if args.mode == 'publish':
			print('Published topic %s: %s\n' % (topic, messageJson))
		loopCount += 1
	time.sleep(1)

```

**2. Now that we have saved the code into the folder, make sure your directory listing looks like the output below.**

```bash
ls -al
```

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416e_configure_raspberry_pi/416e_directory_listing.png)


**3. With all the files verified, now the files need to be copied over to the Raspberry Pi.  We have chosen to go up one folder level, and tar the directory before secure copying the one file to the Raspberry Pi.  You can copy the files other ways to the Raspberry Pi if you choose.**

```bash
cd ..
tar -cvf r_pi.tar <path-to-folder-containing-device-certificates>
```

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416e_configure_raspberry_pi/416e_create_tar.png)


```bash
scp r_pi.tar pi@<raspberry_pi_ip address>:.
```

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416e_configure_raspberry_pi/416e_scp_to_pi.png)


**4. Connect to the Raspberry Pi using either ssh or by opening a terminal window if you are working directly connected.**

In your user directory, there should be a r_pi.tar file that was secure copied in the previous step.  Use the tar command below to extract the certificates and the Python code.

```bash
tar -xvf r_pi.tar
```

**5. After extracting the file, use the following commands to install the AWS IoT Device SDK for Python on the Raspberry Pi:**

```bash
cd ~
git clone https://github.com/aws/aws-iot-device-sdk-python.git
cd aws-iot-device-sdk-python
sudo python setup.py install
```

**6. Make sure that the Raspberry Pi and the AWS DeepLens are connected to the internet using the same local network.**


**7. On Raspberry Pi, run the following command using the IP address of the DeepLens that we looked up in the IoT console in the previous step. You can use Ctrl + C to stop the ping command.**

```bash
ping <ip-address-of-DeepLens>
```

Output similar to the following indicates successful communication between the computer and the AWS DeepLens (0% packet loss):

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416e_configure_raspberry_pi/416e_ping_deeplens_from_pi.png)


 
**8. Still on the Raspberry Pi, we need to go into the hash-setup folder that was created when we extracted the tar file.  The basicDiscovery.py script attempts to collect information on the location of the AWS IoT Greengrass core at its endpoints. This information is stored after the device has discovered and successfully connected to the core. This allows future messaging and operations to be executed locally (without the need for an internet connection).**

**Note**
You can run the following command from the hash-setup folder that contains the basicDiscovery.py file for detailed script usage information:

```bash
python basicDiscovery.py –help
```

**9. Substitute the AWS_IOT_ENDPOINT that we gathered at the end of the previous step and the hash for your certificates in the example below.  This Python script will perform the discovery of the Greengrass Core (DeepLens) and start listening on the topic specified.**  

```bash 
python basicDiscovery.py --endpoint AWS_IOT_ENDPOINT --rootCA root-ca-cert.pem --cert hash.cert.pem --key hash.private.key --thingName RaspberryPi_SenseHAT --topic 'deeplens/trash/infer' --mode subscribe
```

**10. If everything has gone to plan, you should see output similar to below, and should also see the Sense HAT on the Raspberry Pi changing based on the object placed in front of the DeepLens camera.**

![](/images/400_advanced/410_build_a_custom_ml/416_connect_iot/416e_configure_raspberry_pi/416e_raspberry_pi_output.png)


**Congratulations! You are now ready to join the pursuit of sustainability by using the AWS DeepLens Trash Classification project to give you that extra confidence on choosing the correct bin to toss your item.**

