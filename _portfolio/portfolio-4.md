---
layout: archive-wide
title: "Maze Navigating Robot"
excerpt: "A Navigation Robot using Ultrasonic Sensors<br/><img src='/images/navigator-robot.png'>"
collection: portfolio
author_profile: false
share: false
classes: wide
---

This was the final project of my ECE350 class at GMU; Embedded Systems and Hardware Interfaces. The purpose of the project was to create something using a Beaglebone Black board. We were given creative liberty with what the project was designed to do, and my groupmate and I decided we wanted to make something related to robotics.

In one of our more recent labs we experimented with using ultrasonic sensors, so with the lab relatively fresh in our mind, we decided to create a traversal robot using the ultrasonic sensor as a navigation assistant.

<hr>
## Components and Function

The components we used for this project were:
- Beaglebone Black
- Two Brushless Motors & wheels
- One pivot wheel
- L298N Motor Driver

<p align="center">
<img src='/images/navigator-video.gif' width=600>
<br>The robot navigating through custom-made course
</p>

The robot follows a simple procedure; Move foreward until there is a wall within a set distance -> Check left & right, compare distances to see which one has a further wall (to determine which side is a wall or not) -> turn the direction of chosen side until ultrasonic reading is sufficient -> Repeat.

If a wall is detected on both sides, the robot will use a different distance threshold to determine when it should stop rotating. We found this increases the consistency of 160 degree turns. While the whole behavior is a relatively simple procedure, it works to successfully navigate with relatively high consistency.

<hr>
## End Thoughts
Admittedly, calling this project a "maze navigator" is a little bit of a stretch, in reality while it has the ability to traverse through a maze, it does not hold any type of memory or pattern recognition to avoid entering previously explored areas.

The biggest issue we had with our robot was the size restrictions. Due to requiring a course to show how our robot functions, we had to make sure it was large enough to show the functions of the robot, while not taking up too much space because the room our class had reserved needed to house the whole class's projects. Because of this we opted for a three-wheel design with a pivot wheel in the front. After construction, it seems like the pivot wheel was slightly off-centered or misaligned as the robot would have a slight rightward drift.

Additionally, this project had to be completed within around a month, so we had a relatively small time-frame to do testing and modifying, but all things considered I think the project was successful at performing how we wanted it to.