# Video Inference with DRPAI and GStreamer

MistySOM RZV2L contains the DRPAI hardware module which is able to run artificial Neural Networks 
with the focus on low power consumption. To check if this hardware module is present on your device,
you can look for both `/dev/drpai0` and `/dev/udmabuf0` devices on your linux shell. 
The Userland Direct Memory Access (UDMA) kernel module is required to provide the trained AI model and 
the input image to the DRPAI hardware. After activating the hardware, it will use the trained model to
generate the output which can be read by the UDMA module. While DRPAI is running, the running thread will
go to sleep. Of course the sleep time varies based on the size of the AI model.

MistyWest team has prepared a GStreamer plugin for DRPAI which can receive any kind of video input, 
such as a file (filesrc), a network stream (udpsrc), or a camera device (v4l2src) and outputs a video 
with bounding boxes on inferred objects using the DRPAI. Later, this video can be linked to any kind of 
output, such as the display (autovideosink), a network stream (udpsink), or a file (filesink).


<div style="display:flex;align-items: center; text-align: center">
<div>
Input / Source
<br>
<img src='https://raw.githubusercontent.com/MistySOM/wiki/drpai/files/img/gst-drpai-src.PNG' height='240' />
</div>
<div style="font-size: 50pt">&rarr;</div>
<div style="padding:30px;border:black ; background:white; height:min-content; color: black; border-radius: 20px">
<b>GStreamer <br> DRPAI Plugin</b> <br><br> (libgstdrpai.so)
</div>
<div style="font-size: 50pt">&rarr;</div>
<div>
Output / Sink
<br>
<img src='https://raw.githubusercontent.com/MistySOM/wiki/drpai/files/img/gst-drpai-sink.PNG' height=240 />
</div>
</div>

The plugin uses the following pad template capabilities for both **src** and **sink** which requires you
to prepare before your DRPAI element (for example, using a `videoconvert` element):

```
video/x-raw
    width: 640
    height: 480
    format: BGR
```

The plugin also provides you with the following parameters:

| Name                  | Type                | Default | Description                                                                                      |
|-----------------------|---------------------|--------:|--------------------------------------------------------------------------------------------------|
| **multithread**       | Boolean             |    true | Use a separate thread for object detection.                                                      |
| **log-detects**       | Boolean             |   false | Print detected objects in standard output.                                                       |
| **show-fps**          | Boolean             |   false | Render video and object detection frame rates at the corner of the video.                        |
| **stop-error**        | Boolean             |    true | Stop the gstreamer if kernel modules fail to open.                                               |
| **max-video-rate**    | Float [0.001 - 120] |     120 | Intentionally add thread sleeps to control the maximum video framerate.                          |
| **max-drpai-rate**    | Float [0 - 120]     |     120 | Intentionally add thread sleeps to control the maximum DRPAI framerate.<br/>Zero means disabled. |
| **smooth-video-rate** | Float [1 - 1000]    |       1 | Number of last video framerates to averages for a more smooth value.                             |
| **smooth-drpai-rate** | Float [1 - 1000]    |       1 | Number of last DRPAI framerates to averages for a more smooth value.                             |

## Some examples of running GStreamer with the DRPAI
### Read Camera and Show on Screen

```
gst-launch-1.0 v4l2src device=/dev/video0 \
    ! videoconvert \
    ! drpai show-fps=true log-detects=true smooth-video-rate=30 \
    ! videoconvert \
    ! autovideosink
```
If your camera supports the BGR format (such as the coral camera), you can modify the camera size in 
`~/v4l2init.sh` and skip the first `videoconvert` element like this:
```
gst-launch-1.0 v4l2src device=/dev/video0 ! video/x-raw, width=640, height=480, format=BGR \
    ! drpai show-fps=true log-detects=true smooth-video-rate=30 \
    ! videoconvert \
    ! autovideosink
```
### Read Camera and Stream on Network

In case you already have the streaming working based on [here](StreamingVideo.md), you can 
add the drpai element to the `stream.sh` file like this:

````
#!/bin/bash
[ $1 ] || { echo  "Please specify the destination IP address: ./stream.sh ip" >&2; exit 1; }

./v4l2-init.sh
echo "Streaming to ${1} with DRPAI..."

gst-launch-1.0 v4l2src device=/dev/video0 ! video/x-raw, width=640, height=480, format=BGR \
    ! drpai show-fps=true log-detects=true smooth-video-rate=30 \
    ! vspmfilter dmabuf-use=true ! video/x-raw, format=NV12 \
    ! omxh264enc control-rate=2 target-bitrate=10485760 interval_intraframes=14 periodicty-idr=2 \
    ! video/x-h264,profile=\(string\)high,level=\(string\)4.2 \
    ! rtph264pay ! udpsink host=$1 port=51372
````
