# Testing low-latency live streams on a Raspberry Pi 5 and Camera Module 3 

I achieved a 200-millisecond delay in streaming by comparing several live stream types as outlined in the official documentation and by using mediaMTX.

# Youtube video

[![Watch the video](/thumbnail.png)](https://www.youtube.com/watch?v=rxtcyxV32nc)

## Native UDP Stream

### Rasberry PI command:

```bash
rpicam-vid -t 0 --width 1280 --height 720 --framerate 30 --inline -o udp://LAPTOP_IP_HERE:5555
```

### Player command

```bash
ffplay udp://RASPBERRY_PI_IP_HERE:5555 -fflags nobuffer -flags low_delay -framedrop
```

## Native TCP Stream

### Rasberry PI command:

```bash
rpicam-vid -t 0 --width 1280 --height 720 --framerate 30 --inline --listen -o tcp://0.0.0.0:5556
```

### Player command
```bash
ffplay tcp://RASPBERRY_PI_IP_HERE:5556 -vf "setpts=N/30" -fflags nobuffer -flags low_delay -framedrop
```

## LibAV TCP Stream

### Rasberry PI command:

```bash
rpicam-vid -t 0 --width 1280 --height 720 --framerate 30 --codec libav --libav-format mpegts --libav-audio -o "tcp://0.0.0.0:1234?listen=1"
```

### Player command
```bash
ffplay tcp://RASPBERRY_PI_IP_HERE:1234 -vf "setpts=N/30" -fflags nobuffer -flags low_delay -framedrop
```

## LibAV UDP Stream

### Rasberry PI command:
```bash
rpicam-vid -t 0 --width 1280 --height 720 --framerate 30 --codec libav --libav-format mpegts --libav-audio  -o "udp://192.168.1.66:5555"
```

### Player command

```bash
ffplay udp://RASPBERRY_PI_IP_HERE:5555 -fflags nobuffer -flags low_delay -framedrop
```

## MediaMTX

### MediaMTX Config (see video for details)

__Make sure that you have ffmpeg:__

```bash
sudo apt-get install ffmpeg
```

```bash
  cam1:
    runOnInit: bash -c 'rpicam-vid -t 0 --camera 0 --nopreview --codec yuv420 --width 1280 --height 720 --inline --listen -o - | ffmpeg -f rawvideo -pix_fmt yuv420p -s:v 1280x720 -i /dev/stdin -c:v libx264 -preset ultrafast -tune zerolatency -f rtsp rtsp://localhost:$RTSP_PORT/$MTX_PATH'
    runOnInitRestart: yes
```

### RTSP Player command

```bash
vlc rtsp://RASPBERRY_PI_IP_HERE:8554/cam1
```

### RTSP Player command

http://RASPBERRY_PI_IP_HERE:8889/cam1

# Licence:

Copyright 2024  Ievgenii Tkachenko

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

  http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
