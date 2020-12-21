# mousehunter-edge

Keep your cat’s prey out of your home with a machine learning trained pet door.

## Requirements:

* Raspberry Pi 4A
* Google Coral
* Infrared camera
* Motion detector
* Relay
* Surepetcare Sureflap
* Python3 installed
* AWS S3 Bucket
* AWS credentials


## Setup:

Clone this repo on your raspberry and run:

```bash
echo "deb https://packages.cloud.google.com/apt coral-edgetpu-stable main" | sudo tee /etc/apt/sources.list.d/coral-edgetpu.list
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
sudo apt-get update
sudo apt-get install libedgetpu1-std
pip3 install awscli --upgrade --user
aws configure
cp config.yaml ~/.config/mousehunter-edge/config.yaml
```

Configure  ~/.config/mousehunter-edge/config.yaml with your AWS bucket name and ensure that aws credentials can read the objects in it.
Keep APNToken and certfile empty if you do not have it.
``` yaml
bucket: <your AWS S3 bucket>
curfewTime: 15
APNToken: <your Apple Push Notification Service (APNs) Token>
alertThreshold: 2.0
certfile: <your Apple certificate for APN for APN usage> 
```

```bash
sudo systemctl enable mausjaeger.service
sudo systemctl enable imagewatcher.service
sudo systemctl start mausjaeger.service
sudo systemctl start imagewatcher.service
```

## Retrain your own object detection model:

label you data with labelImg:
https://github.com/tzutalin/labelImg

Convert your labeled data to the TFRecord file format, e.g. with https://roboflow.com

Follow the coral.ai tuturials part "Set up the Docker container":
https://coral.ai/docs/edgetpu/retrain-detection/#set-up-the-docker-container

Copy your TFRecords to docker/object_detection/data

Configure train_input_reader, eval_input_reader and model.ssd.num_classes  the training pipeline:
```
docker/object_detection/out/ckpt/pipeline.config
```
Remove all lines following "PREPARING dataset" and execute the prepation script:
```
docker/object_detection/scripts/prepare_checkpoint_and_dataset.sh
```

Contine to follow the coral.ai tutorial part "Start training" and "Compile the model for the Edge TPU"
Copy the created model to the imagewatcher/model/ folder.






