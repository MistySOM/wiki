layout: page
title: "Streaming Video"

For streaming a live video feed from the camera, you need to be connected to be connected to a network using Ethernet or WiFi (see [here](SetupWifi.md)) and know the IP address of the destination computer.

# Stream Listener

The destination computer should be listening to incoming video on a port (in this example port 51372)

To setup the listening, you have different options:

## GStreamer

Use the command below:

````
$ gst-launch-1.0 -v udpsrc port=51372 \
    caps="application/x-rtp, media=video, clock-rate=90000, encoding-name=H264, payload=96" \
    ! rtph264depay ! decodebin ! videoconvert \
    ! autovideosink
````
- **Note:** In case you want to see the number of frames it receives per second, you can replace the `autovideosink` in the last line with:
    ````
    fpsdisplaysink text-overlay=0 video-sink=autovideosink
    ````

## VLC or FFmpeg

Create an SDP (in this example `stream.sdp`) file with the contents below:
````
v=0
o=- 0 0 IN IP4 0.0.0.0/32
s=ESP H264 STREAM
i=MistySOM
c=IN IP4 0.0.0.0/32
t=0 0
m=video 51372 RTP/AVP 96
a=rtpmap:96 H264/90000
````
Then you open this file by double clicking or command lines below:
````
$ vlc stream.sdp
$ ffplay -protocol_whitelist file,crypt,udp,rtp stream.sdp
````
- **Note:** In case you need no delay when using ``ffplay``, you can add the following arguments to the command:

    ````
    -fflags nobuffer -flags low_delay -framedrop -strict experimental
    ````
- **Note:** In case you need no delay when using VLC:
**TBD**


# Stream Sender (MistySOM)

First, make sure you have the modules below loaded in the kernel with the command below:
````
$ lsmod
Module                  Size  Used by
vspm_if                49152  0
vspm                  102400  1 vspm_if
uvcs_drv               40960  0
mmngrbuf               16384  0
mmngr                  24576  0
````

Second, make sure you have an active IP address network connection with the command below:
````
$ ip a | grep inet
    inet 127.0.0.1/8 scope host lo
    inet 192.168.100.154/24 brd 192.168.100.255 scope global dynamic wlan0
````

Then, you can send a video stream with **GStreamer**. Because the command is very long and it makes editing difficult, we recommend that you create a `stream.sh` bash file in your home directory (alongside the file `v4l2-init.sh`) with the following contents:
````
#!/bin/bash
[ $1 ] || { echo  "Please specify the destination IP address: ./stream.sh ip [width] [height]" >&2; exit 1; }
W="${2:-1920}"
H="${3:-1080}"

./v4l2-init.sh
echo "Streaming to ${1} with resolution ${W}x${H}..."

gst-launch-1.0 v4l2src device=/dev/video0 ! video/x-raw, width=1920, height=1080 \
	! vspmfilter dmabuf-use=true ! video/x-raw,format=NV12,width=$W,height=$H \
	! omxh264enc control-rate=2 target-bitrate=10485760 interval_intraframes=14 periodicty-idr=2 \
	! video/x-h264,profile=\(string\)high,level=\(string\)4.2 \
	! rtph264pay config-interval=-1 ! udpsink host=$1 port=51372
````
- **Note:** Remember to make the file executable using `chmod +x stream.sh`

Then start the stream to the listener computer (in this example with IP address of 192.168.100.132) with your desired resolution (in this example 640x360) with the command below:
```
$ ./stream.sh 192.168.100.132 640 360
```
