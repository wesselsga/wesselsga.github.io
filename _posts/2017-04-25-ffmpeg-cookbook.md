---
layout: default
title: FFmpeg Cookbook
category: Dev
---

# FFmpeg Cookbook #

<div class="date">{{ page.date | date: "%-d %B %Y"}}</div>

My personal scratch pad for ffmpeg tasks that I use.  Seems like everytime I need to convert some video...I end up googling for the same thing over and over again.

### 1. Convert a video to H.264 (in MP4) ###
When creating a H.264 video file, there are a ton of options available - but this is a quick and dirty MP4 version.

```Batchfile
ffmpeg -i "input-video" -c:v libx264 -crf 18 -pix_fmt yuv420p "output.mp4"
```

---

### 2. Convert a video to H.264 (in MOV) ###
Pretty much the same as #1, but use an MOV container and mark it as 'fast start'. (faststart moves the MOOV atom to the beginning of the file versus. the end - better for streaming)

```Batchfile
ffmpeg -i "input-video" -c:v libx264 -crf 18 -pix_fmt yuv420p -movflags +faststart "output.mov"
```

---

### 3. Convert a video to lossless format ###
Not nearly as useful as the H.264 version, but there are times when I need a video in an lossless format.

```Batchfile
ffmpeg -i "input-video" -c:v rawvideo -pix_fmt bgr24 "output.avi"
```

If the source has an alpha channel - use a pixel format such as bgra to preserve it.

```Batchfile
ffmpeg -i "input-video" -c:v rawvideo -pix_fmt bgra "output.avi"
```

Another example if you want to use QTRLE (Quicktime Animation).

```Batchfile
ffmpeg -i "input-video" -c:v qtrle -pix_fmt argb "output.mov"
```



---

### 4. Convert several videos in a directory to another format ###

The following searches for all .avi files in a directory and converts them to H.264 in an MP4 container. (Windows .BAT file)

```Batchfile
@echo off

for /r %%f in (*.avi) do (
ffmpeg -i "%%~nxf" -c:v libx264 -crf 18 -pix_fmt yuv420p "%%~nf.mp4"
)
```

---

### 5. Trim an existing MP4 video ###

For example, to slice out part of an existing mp4 video - starting at the 1 minute mark and grab 30 seconds:

```Batchfile
ffmpeg -ss 00:01:00 -i "input-video.mp4" -t 30 -c copy -avoid_negative_ts 1 -y output.mp4
```

---

### 6. Extract all video frames as images ###

The following will dump all frames from the input video file into a subdirectory called 'frames' (in this example PNG is used; but JPG would also work)

```Batchfile
ffmpeg -i "input-video" -an -f image2 "frames/frame_%05d.png"
```

Note: if using a Windows .bat file, you will need to escape the %, so the above pattern would be `frame_%%05d.png` in a .bat file

---

### 7. Create a video from a directory of images ###

To create a video from a directory of images, the image files will need to be named with a pattern.  

The following will create a video file at 60fps from a series of still images:

```Batchfile
ffmpeg -r 60 -f image2 -i "frames/frame_%%05d.png" -c:v libx264 -crf 18 -pix_fmt yuv420p "output.mp4"
```

---

### 8. Extract audio track from a video file ###

If you wish to grab just the audio track from a source and encode it as MP3:

```Batchfile
ffmpeg -i "input-video" -vn -codec:a libmp3lame -b:a 256K "output.mp3"
```

---

### 9. Remove audio track from a video file ###

Remove the audio track from a file while retaining the video stream as is.

```Batchfile
ffmpeg -i "input-video" -an -codec:v copy "output-video"
```

---

### 10. Concatenate (or Combine) Videos ###
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

