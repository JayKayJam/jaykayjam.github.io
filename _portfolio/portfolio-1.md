---
layout: archive-wide
title: "YOLO Drawing"
excerpt: "Using YOLO object detection to draw on UI <br/> <img src='/images/yolo-draw-picture.png' width=500>"
collection: portfolio
author_profile: false
share: false
classes: wide
---

This was a simple project, mainly just for learning purposes as I wanted to get more familiar with trying to create a unique GUI as well as a few other skills. The concept is simple; use a YOLO model to detect some object, and update pixel values on a GUI according to where the object is detected.

This project consists of a yolov8 model trained entirely from my own pictures, as I was planning on using a specific object for the model to look for. This was done using Roboflow, as it seemed like a fairly streamlined training process and all that was required was to make an account (as well as the fact that the dataset would be public, so now there's a bunch of pictures of me randomly holding a coke bottle cap online).  
The object I trained the model to detect was a small coke bottle cap, as the shape and red color seemed distinct enough to prevent false detections from the model, as well as the fact that it was small enough to not cause inconvenience when holding.
<p align="center">
<img src='/images/coke-bottle-cap.jpg'>
<br>Example Image for Training ( Ignore the unmade bed please :) )
</p>

The actual drawing part of the program simply finds where the object is detected from the camera, and colors a pixel of a 64x64 grid (or however large is specified) according to the middle x and y coordinate of the object.
<p align="center">
<img src='/images/yolo-draw.gif'>
</p>

<hr>
## End Thoughts
The scale for this project was initially a lot larger, but I stopped working on it once I started my internship back in August 2025. The original plan was to add additional colors that can be selected on the GUI, as well as creating a website that can interact with the drawing, or at least see what is being drawn. I wanted to do this to learn more full stack development but alas, it was not meant to be. Maybe another time.

One issue that I did realize is that the video feed is not mirrored, so it can be a little disorienting when trying to draw, but that's an issue for when/if I ever revisit this. Additionally if I were to revisit this project, I'd want to incorporate it into a future project where I plan on making a customizable LED array that can be mounted on a wall, finishing the LED project would probably motivate me to update this one.

GitHub link: <https://github.com/JayKayJam/YOLO-Drawing>