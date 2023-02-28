# Homework 6

# If you do not yet have your AWS credits, complete and submit part 1 of the homework.  For part 2, please just comment that you do not have any AWS credits.


This homework covers some use of GStreamer and model optimization.  It builds on the week 6 lab and completing the lab first is hightly recommended.   

This is an ungraded assignment


2-14-2023 Note: It appears a recent update broke a number of packages with Ubuntu 20.04, at least on the ARM based machines, e.g. the Apple M1.  It is recommended that your VM be based on Ubuntu 22.04.  If you are running on a M1/M2 and using VMWare Fusion, you will need to first install 20.04 and then upgrade to 22.04 as there have been reports of Fusion not booting the installer.  There are no issues with 22.04 if you are using Parallels.

See [here](https://ubuntu.com/tutorials/upgrading-ubuntu-desktop#1-before-you-start) to upgrade from 20.04 to 22.04.

Due to this issue and the overall cameral peformace, for this lab/homework, I strongly recommend using Parallels over VMWare Fusion if you are using a M1 or M2 mac.


## Part 1: GStreamer

1. What is the difference between a property and a capability?  How are they each expressed in a pipeline?

2. Explain the following pipeline, that is explain each piece of the pipeline, desribing if it is an element (if so, what type), property, or capability.  What does this pipeline do?

```
gst-launch-1.0 v4l2src device=/dev/video0 ! video/x-raw, framerate=30/1 ! videoconvert ! agingtv scratch-lines=10 ! videoconvert ! xvimagesink sync=false
```

3. GStreamer pipelines may also be used from Python and OpenCV.  For example:
```
import numpy as np
import cv2

# use gstreamer for video directly
camSet='v4l2src device=/dev/video2 ! videoconvert ! video/x-raw, format=BGR ! appsink sync=false'
cap= cv2.VideoCapture(camSet)

#cap = cv2.VideoCapture(0)

while(True):
    # Capture frame-by-frame
    ret, frame = cap.read()

    # Display the resulting frame
    cv2.imshow('frame',frame)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

# When everything done, release the capture
cap.release()
cv2.destroyAllWindows()
```
In the lab, you saw how to stream using Gstreamer.  Using the lab and the above example, write a Python application that listens for images streamed from a Gstreamer pipeline.  You'll want to make sure your image displays in color.  Feel free to add some "transformations" to the stream!

For part 1, you'll need to submit:
- Answer to question 1
- Answer to question 2
- Source code and Gstreamer "server" pipeline used.


## Part 2: Model optimization and quantization

In lab, you saw to how use leverage TensorRT with TensorFlow.  For this homework, you'll look at another way to levarage TensorRT with Pytorch.

1. Procure a virtual machine in AWS - we recommend a T4 or A10 GPU and 32 GB of RAM (e.g. g4dn.2xlarge). f you are interested in the VM options, see this [link](https://docs.aws.amazon.com/dlami/latest/devguide/gpu.html). Use the Nvidia Deep Learning AMI so that the pre-requisites are pre-installed for you. We recommend using the latest [nvidia pytorch container](https://catalog.ngc.nvidia.com/orgs/nvidia/containers/pytorch).  When starting the container, make sure that port 8888 is bound to the host, e.g. `-p 8888:8888` and that your firewall allows ingress on port 8888.
2. Clone this reposisotry: `git clone https://github.com/MIDS-scaling-up/v3a`.
3. Start Jupyter Lab with the command: `jupyter lab --ip 0.0.0.0 -allow-root`.  
4. Login to your lab instance.
5. Navigate to and open v3a/week06/hw/torch-trt.ipynb.
6. Run the notebook.

What was your average throughput for native model, for the tensor-rt FP32 and FP16 models.

Next, you'll want to leverge transfer learning to train your own model.  See https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html for an example. You may also look at [training.md](training.md) for addional examples.

Once you have your model trained, use torch-trt.ipynb notebook as starting point and optimize your model, producing both FP32 and FP16 models.  How does the peformace compare to your native model?  Note, if you wish to use TensorFlow and TF-TRT instead, you may.



For part 2, you'll need to submit:
- your average throughput for native model, for the tensor-rt FP32 and FP16 models from the torch-trt.ipynb notebook
- A description of your data set
- How long you trained your model, how many epochs you specified, and the batch size.
- Native Pytorch baseline
- TensorRT FP32 and FP16 performance numbers.

**DELETE YOUR VM when the homework is complete!**
