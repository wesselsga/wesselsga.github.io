---
layout: default
title: FFmpeg Cookbook
category: Dev
---

# FFmpeg Cookbook #

My personal scratch pad for ffmpeg tasks that I use.  Seems like everytime I need to convert some video...I end up googling for the same thing over and over again.

### 1. Convert a video to H.264 (in MP4) ###
When creating a H.264 video file, there are a ton of options available - but this is a quick and dirty MP4 version.

```Batchfile
ffmpeg -i "input-video" -c:v libx264 -crf 18 -pix_fmt yuv420p "output.mp4"
```


### 2. Convert a video to H.264 (in MOV) ###
Pretty much the same as #1, but use an MOV container and mark it as 'fast start'. (faststart moves the MOOV atom to the beginning of the file versus. the end - better for streaming)

```Batchfile
ffmpeg -i "input-video" -c:v libx264 -crf 18 -pix_fmt yuv420p -movflags +faststart "output.mov"
```


### 3. Convert a video to uncompressed AVI ###
Not nearly as useful as the H.264 version, but there are times when I need a video in an uncompressed format.

```Batchfile
ffmpeg -i "input-video" -c:v rawvideo -pix_fmt bgr24 "output.avi"
```


### 4. Convert several videos in a directory to another format (Windows batch) ###

The following searches for all .avi files in a directory and converts them to H.264 in an MP4 container.

```Batchfile
@echo off

for /r %%f in (*.avi) do (
ffmpeg -i "%%~nxf" -c:v libx264 -crf 18 -pix_fmt yuv420p "%%~nf.mp4"
)
```


### 5. Concatenate (or Combine) Videos ###
Got a bunch of videos from an phone or iPad?  I use this one for kids sports ... frequent hitting of start & stop record during a game will create several video files.
I often just want 1 larger video file of the game to upload to YouTube or archive.

To give ffmpeg the list of videos to combine, create a text file in the same directory as the individual video files - something like the following:

```Batchfile
file 'IMG_0051.MOV'
file 'IMG_0052.MOV'
file 'IMG_0053.MOV'
file 'IMG_0054.MOV'
file 'IMG_0055.MOV'
file 'IMG_0056.MOV'
file 'IMG_0057.MOV'
file 'IMG_0058.MOV'
```

If the above text file is named `04MAR2017_BB_Game.txt`, then the following ffmpeg command can be used to combine the individual MOV files into a single (larger) video file:

```Batchfile
ffmpeg -f concat -safe 0 -i 04MAR2017_BB_Game.txt -c copy 04MAR2017_BB_Game.mov
```

