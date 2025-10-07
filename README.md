# Orange Pi 3B v2.1 and OV5647

This page shows how to use OV5647 camera module with Orange Pi 3B v2.1.

<img width="685" height="396" alt="image" src="https://github.com/user-attachments/assets/93f8dfb4-544e-4960-8eb4-d3a450a2d1c9" />


## Sources
- https://forum.banana-pi.org/t/bpi-f4-how-to-use-video-camera-ov5647-to-capture-image-then-to-open-image/24241

## Components

- raspberry pi camera ov5647: https://www.sunfounder.com/products/ov5647-camera-module
- (optional) camera extension cable: https://docs.arducam.com/Camera-Extension-Solution/Sensor-Extension-Cable/ 
- orange pi 3b v2.1

## Prerequisites

* virtual machine or laptop or PC running Ubuntu 22.04(or Ubuntu-based distributive) or newer. The host.
* Oragepi 3B v2.1, running "Orangepi3b_1.0.6_ubuntu_jammy_server_linux5.10.160"(you can download the tarball with the image from here: https://drive.google.com/drive/folders/1ZqiTNgbq2ezCTv_r0PT5wqFoSxN39zsr). The target.

NOTE!!! I've been trying to run Linux6.6-based images, but it did not work(my single board computer did not boot). According to the official docs(user manual: https://drive.google.com/drive/folders/18YyPnq_f0gbdNlbsNmzouFPxin08C_gf ) at the moment of writing this page only Linux5.10 system is well adapted to work with all the peripherals of the Orange Pi 3B v2.1. 

NOTE!!! The server version of the ubuntu for Orange Pi 3B is used for this tutorial. Because it is more stable, consumes less RAM and CPU, requires less space comparing to the desktop version. But you can use desktop versio too, e.g. "Orangepi3b_1.0.6_ubuntu_jammy_desktop_linux5.10.160".


## Step-by-step instructions

**1) Install necessary packages**

Gstreamer:

```
sudo apt install -y \
    libgstreamer1.0-dev \
    libgstreamer-plugins-base1.0-dev \
    gstreamer1.0-tools \
    gstreamer1.0-plugins-base \
    gstreamer1.0-plugins-good \
    gstreamer1.0-plugins-bad \
    gstreamer1.0-plugins-ugly \
    gstreamer1.0-libav
```

FFmpeg:

```
sudo apt-get -y install ffmpeg
```

v4l-utils:

```
sudo apt-get -y install v4l-utils
```

**2) Edit orangepiEnv.txt**

```
sudo nano /boot/orangepiEnv.txt
```

Add the next row:

```
overlays=ov5647-c1
```

**3) Poweroff the board and connect the camera to the board. Then poweron the board.**

**4) List devices(to make sure the device is discovered)**

Command:

```
v4l2-ctl --list-devices
```

You will see something like this:

```
orangepi@orangepi3b:~$ v4l2-ctl --list-devices
rkisp-statistics (platform: rkisp):
	/dev/video8
	/dev/video9

rkisp_mainpath (platform:rkisp-vir0):
	/dev/video0
	/dev/video1
	/dev/video2
	/dev/video3
	/dev/video4
	/dev/video5
	/dev/video6
	/dev/video7
	/dev/media0

```

**5) List available video formats.**

Command:

```
v4l2-ctl --list-formats-ext --device /dev/video0
```

You will see something like this:

```
orangepi@orangepi3b:~$ v4l2-ctl --list-formats-ext --device /dev/video0
ioctl: VIDIOC_ENUM_FMT
	Type: Video Capture Multiplanar

	[0]: 'UYVY' (UYVY 4:2:2)
		Size: Stepwise 32x32 - 2592x1944 with step 8/8
	[1]: '422P' (Planar YUV 4:2:2)
		Size: Stepwise 32x32 - 2592x1944 with step 8/8
	[2]: 'NV16' (Y/CbCr 4:2:2)
		Size: Stepwise 32x32 - 2592x1944 with step 8/8
	[3]: 'NV61' (Y/CrCb 4:2:2)
		Size: Stepwise 32x32 - 2592x1944 with step 8/8
	[4]: 'YM16' (Planar YUV 4:2:2 (N-C))
		Size: Stepwise 32x32 - 2592x1944 with step 8/8
	[5]: 'NV21' (Y/CrCb 4:2:0)
		Size: Stepwise 32x32 - 2592x1944 with step 8/8
	[6]: 'NV12' (Y/CbCr 4:2:0)
		Size: Stepwise 32x32 - 2592x1944 with step 8/8
	[7]: 'NM21' (Y/CrCb 4:2:0 (N-C))
		Size: Stepwise 32x32 - 2592x1944 with step 8/8
	[8]: 'NM12' (Y/CbCr 4:2:0 (N-C))
		Size: Stepwise 32x32 - 2592x1944 with step 8/8
	[9]: 'YU12' (Planar YUV 4:2:0)
		Size: Stepwise 32x32 - 2592x1944 with step 8/8
	[10]: 'YM24' (Planar YUV 4:4:4 (N-C))
		Size: Stepwise 32x32 - 2592x1944 with step 8/8

```


**6) List available configurable controls**

You can change the parameters that will be listed to configure the camera.

Command:

```
v4l2-ctl -d /dev/video0 --list-ctrls
```

You will see something like this:

```
User Controls

                       exposure 0x00980911 (int)    : min=4 max=1964 step=1 default=1104 value=1104

Image Source Controls

              vertical_blanking 0x009e0901 (int)    : min=24 max=30823 step=1 default=24 value=24
            horizontal_blanking 0x009e0902 (int)    : min=252 max=252 step=1 default=252 value=252 flags=read-only
                  analogue_gain 0x009e0903 (int)    : min=16 max=248 step=1 default=248 value=248

Image Processing Controls

                 link_frequency 0x009f0901 (intmenu): min=0 max=0 default=0 value=0 (210000000 0xc845880) flags=read-only
                     pixel_rate 0x009f0902 (int64)  : min=0 max=84000000 step=1 default=84000000 value=84000000 flags=read-only
                   test_pattern 0x009f0903 (menu)   : min=0 max=4 default=0 value=0 (Disabled)
                   digital_gain 0x009f0905 (int)    : min=0 max=16383 step=1 default=1024 value=1024
```

**7) Capturing the picture**

Make sure the pixelformat that will be used is listed on command execution "v4l2-ctl --list-formats-ext --device /dev/video0"

Command:

```
v4l2-ctl -d /dev/video0 --set-fmt-video=width=1920,height=1080,pixelformat=UYVY --stream-mmap=3 --stream-to=/home/orangepi/ov5647.raw --stream-skip=9 --stream-count=1
```

**8) Show the captured image**

Make sure -pixel_format corresponds to the pixelformat of the previous command.

Command:

```
ffplay -f rawvideo -pixel_format uyvy422 -video_size 1920x1080 /home/orangepi/ov5647.raw
```

**9) Playing the video**

Make sure the width, height and pixelformat are the same in both options.

(OPTION 1, gstreamer, very effective)

```
v4l2-ctl -d /dev/video0 --set-fmt-video=width=640,height=480,pixelformat=UYVY
```

```
gst-launch-1.0 v4l2src device=/dev/video0 io-mode=mmap ! video/x-raw,format=UYVY,width=640,height=480,framerate=15/1 ! videoconvert ! autovideosink sync=false
```


(OPTION 2, gstreamer, explicit format conversion, less effective)

```
v4l2-ctl -d /dev/video0 --set-fmt-video=width=640,height=480,pixelformat=UYVY
```

```
gst-launch-1.0 v4l2src device=/dev/video0 io-mode=mmap ! video/x-raw,format=UYVY,width=640,height=480,framerate=15/1 ! videoconvert ! autovideosink sync=false
```

(OPTION 3, ffmpeg)

```
v4l2-ctl -d /dev/video0 --set-fmt-video=width=640,height=480,pixelformat=UYVY
```

```
v4l2-ctl -d /dev/video0 --stream-mmap=3 --stream-skip=3 --stream-count=-1 --stream-to=- | ffplay -f rawvideo -pixel_format uyvy422 -video_size 640x480 -i -
```


