## Installation
1. Clone this repo
2. Install pygame: sudo apt install python3-pygame
3. launch with sudo

## Gotchas
* Watch out for your CPU usage. If you start getting ffmpeg errors or stream delays its beacuse grabbing and merging data from the framebuffer is too slow.
* * This can be fixed by lowering the resolution of your framebuffer with raspi-config

This python script uses pygame to create a video stats overlay for use on https://LetsRobot.TV

FYI: To access /dev/fb0 you must run this python script with sudo!

## Configuragion with the new letsrobot controller

add a line to ffmpeg.py around line 268 so it looks like:


videoCommandLine = ('{ffmpeg} -f {input_format} -framerate 25 -video_size {xres}x{yres}'

' -r 25 {in_options} -i {video_device} {video_filter}'

**' -f fbdev -framerate 2 -i /dev/fb0 -filter_complex "[1:v]colorkey=0x000000:0.1:0.0[ckout];[0:v][ckout]overlay[out]" -map "[out]"'**

' -f mpegts -codec:v {video_codec} -b:v {video_bitrate}k -bf 0'

' -muxdelay 0.001 {out_options}'

' http://{video_host}:{video_port}/{stream_key}/{xres}/{yres}/')




To get this to show up on LetsRobot.tv launch this script (with sudo)

## Configuring with the old runmyrobot code:
Modify the send_video.py script on line 177 to include the framebuffer.

This is the full line:

videoCommandLine2 = 'ffmpeg -f v4l2 -thread_queue_size 64 -threads 4 -video_size {xres}x{yres} -i /dev/video{video_device_number} {rotation_option} -f fbdev -framerate 2 -i /dev/fb0 -filter_complex "[1:v]colorkey=0x000000:0.1:0.0[ckout];[0:v][ckout]overlay[out]" -map "[out]" -f mpegts -framerate 25 -codec:v mpeg1video -b:v {kbps}k -bf 0 -muxdelay 0.001 http://{video_host}:{video_port}/{stream_key}/{xres}/{yres}/'.format(video_device_number=robotSettings.video_device_number, rotation_option=rotationOption(), kbps=robotSettings.kbps, video_host=videoHost, video_port=videoPort, xres=robotSettings.xres, yres=robotSettings.yres, stream_key=robotSettings.stream_key)

I am using a colorkey filter to cut out all the black pixels from the framebuffer
-filter_complex "[1:v]colorkey=0x000000:0.1:0.0[ckout];[0:v][ckout]overlay[out]" -map "[out]"

<img src="https://raw.githubusercontent.com/RyAndrew/letsrobot_pygame_overlay/master/screenshot%20video%20text%20overlay%20with%20pygame%20and%20ffmpeg.jpg">
