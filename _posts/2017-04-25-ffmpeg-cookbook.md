---
layout: default
title: Custom Jeep Hardtop Lift
category: Dev
---

# FFmpeg Cookbook #

My personal scratch pad of ffmpeg receipes that I use.  Seems like everytime I need to convert some video...I end up googling for the same thing over and over again.

### Concatente (Join) Videos ###
Got a bunch of videos from an iPhone or iPad?  I use this one frequently for kids sports ... frequent start/stop recording of a game will annoyingly create several video files.
I just want 1 larger video to upload to YouTube or archive.

{% highlight ruby %}

Create a text file in the same directory as your videos, something like the following:

file 'IMG_0051.MOV'
file 'IMG_0052.MOV'
file 'IMG_0053.MOV'
file 'IMG_0054.MOV'
file 'IMG_0055.MOV'
file 'IMG_0056.MOV'
file 'IMG_0057.MOV'
file 'IMG_0058.MOV'
file 'IMG_0059.MOV'

If the above named text file is named something like 04MAR2017_BB_Game.txt, then the following ffmpeg command can be used to combine the individual MOV files into a single (larger) video file:

ffmpeg -f concat -safe 0 -i 04MAR2017_BB_Game.txt -c copy 04MAR2017_BB_Game.mov


