---
layout: archive-wide
title: "Autonomous Search-And-Locate Blimp <img src='/images/star.png' width=20>"
excerpt: "Building an autonomous vehicle to compete in the &quot;Defend the Republic&quot; competition<br/><img src='/images/dtr.png' width=750>"
collection: portfolio
author_profile: false
share: false
classes: wide
order: 2
---

This project was a year long project that myself and 5 other group members developed as a part of our Capstone project. The goal of this project was to create an autonomous "blimp" (I'm using the word blimp loosely as our project is a lighter-than-air vehicle and for lack of a better word) to compete in a multi-university competition sponsored by Office of Naval Research **(ONR)** called "Defend The Republic" **(DTR)**

<hr>
## Competition Overview
The goal of the competition is to score small balloons inside any of the enemy nets. Whichever team has the most amount of points at the end of the time period wins the round. The winner of the entire tournament is decided based on whichever team has the most wins or in the case of a tie, whoever has the most points in total.

<p align="center">
<img src='/images/DTR-scoring.png' width=600>
<br>Our modified blimp attempting to score
</p>

There are three different methods of scoring, each granting their own amount of points:
- Fully Autonomous: 5 Points
- Semi-Autonomous: 2 Points
- Fully Manual: 1 Point

There are 30 second manual periods where each team can manually control one blimp. This only occurs once every 5 minutes. If a team member assists in a score during the manual period, but the blimp finishes the score in autonomous mode, that is considered semi-autonomous scoring.

See [This Video](https://www.youtube.com/watch?v=8Bwf-izAjlc) to see a video briefing of the event in a previous year.

<hr>
## Our Design
There were multiple iterations of this project, as we had to decide what type of role our blimp was to achieve; did we want an offensive blimp focused on scoring? Maybe a defensive blimp focused on preventing the enemy from being able to score? Or maybe an experimental blimp focused on trying to invent a new strategy?

<p align="center">
<img src='/images/blimp-iterations.png' width=600>
<br>A few iterations of our blimp
</p>

Our team agreed on using a two-balloon design for enhanced mobility, as we decided to focus on developing a more unique form of motion previously unseen in the competition. The main idea behind our design was to centralize both the **center of gravity** and the **center of mass** on a single point to allow for complete freedom of mobility. The complication of doing this is the need to try to stabilize a naturally unstable body, which is something we have come to realize was pretty tricky, but more on that later.

One of our goals was to increase the number of degrees of motion through our design. We included roll control functionality with rotatable wings which allowed for a new array of movement capabilities when compared to the standard blimp design of using additional propellors on different axes. We were unable to include proper pitch control due to our two-wing design, as four-wings proved to be unrealistic to include with our limited weight budget, but already including the ability to roll allowed for much more spontaneous motion which is something we were looking for. We additionally used a Motion Capture (MoCap) lab to better picture our movement compared to more traditional movement.

<p align="center">
<img src='/images/blimp-motion.png' width=400>
<br>Example of more spontaneous motion
</p>

The blimp is entirely controllable through use of a ps4 controller, though it also has autonomous function if the mode is selected. The full funtionality of the blimp will be covered later in the page.

<hr>
## Hardware
To actually make the blimp able to fly and utilize different functions such as vision we had to add electronics. The full list of electrical components we incorporated are as follows:
- x1 Pi Zero
- x1 IMU
- x1 Barometer
- x1 Pi Camera & Camera Connector
- x2 Battery (300 mAh)
- x1 Wiring Harness
- x1 Voltage Regulator
- x2 ESC

Most of these components were chosen as they proved to be fairly reliable and lightweight enough for our purposes and have been used in other blimps in our as well.

Aside from electronics, there was a large amount of components designed in CAD, either for structure or for assisting in attaching components to one another. Some examples can be seen below:

<p align="center">
<img src='/images/pi-attachment.png' width=400> <img src='/images/servo-attachment.png' width=275> <img src='/images/blimp-cad.png' width=330>
<br>Pi board attachment (left), wing-to-servo attachment (middle) and full blimp model (right)
</p>

<hr>
## Functions
In the latter half of our project, we had agreed that our design was mostly an experimental one, focusing on the movment of our blimp above all else, but we also wanted to participate in the game by being a defensive agent, disrupting enemies from being able to score. To do so, we needed some way to identify who we need to disrupt.

### Object detection
We initially wanted to use true object detection with YOLO to identify friendly and enemy agents, while also being able to identify the goals of each side in case we wanted to use that in our movement algorithm or some other way, but using a fairly underpowered microprocessor such as the Pi Zero made that difficult, as we only managed around 3-4 frames-per-second during our tests which was not ideal for our defensive scenario.
<br> As such, we decided to utilize color-detection instead for the competition, as friendly and enemy agents would be opposite colors (blue and red) and would allow us a much higher frame rate.

- **Side Note**: The functions of the blimp were originally coded in C++ for better performance, but the lack of library support for object detection made us decide to switch to Python.

<br>
### Center of Gravity Mover
Because our system is inherently unstable with an overlapped center of gravity and center of mass, we wanted a system to stabilize the blimp when it was desired. To do this we mounted a weight to an arm hooked to a servo which could move up or down for when we wanted more stability or more freedom of movement. This could be activated through a button on the ps5 controller we used to control the blimp.

<p align="center">
<img src='/images/cg-mover.gif' width=500>
<br>CG-Mover moving the attached weight
</p>


<br>
### (Blueprint of) Localization
There were a few ideas for localization, though each had their own drawbacks, especially with the scope of our project. The three we had dabbled with were:
- <u>The Extended Kalman Filter</u>, which is a very math intensive algorithm to understand one's movement over time. Issue with this method is that it typically requires multiple sensors in conjunction for accurate measurements, which we did not have.
- <u>Simultaneous Localization and Mapping (SLAM)</u>, which tends to be a reliable method of localization, but seeing as we were using a low powered Pi Zero board, this was almost immediately off the table as the processing power needed for this method was way out of our abilities.
- <u>Visual based localization</u>, which would involve using our visual system to determine the position of objects in relation to our blimp and triangulate our position according to those. Given our shoddy visual system, this was likely to be unreliable, but was something we developed the framework of for a potential future team.

Of all of these, we worked mostly on the Kalman Filter method, as we had found formulas online on how to use the barometer + IMU to get a general understanding of our position. The idea of localization was mostly at the end of our project, so finishing it's implementation was not our highest priority, and we rather wanted to develop a framework that could be used for future semesters.

<br>
### Navigation and Defense
Because our blimp was designed to be defensive, it needed a way of searching for enemy blimps and interrupting them. During our competition, the searching was done using color detection of the enemy color as stated earlier. We developed simple search and locate protocol as follows:
<p align="center"><b>Rotate (search) -> Locate enemy -> Adjust positioning -> Approach -> Strike when within range</b>
<br><img src='/images/blimp-chase.gif' width=400>
<br> Our blimp approaching target</p>

The blimp would periodically adjust it's height to match a range within the goal height to try to attack any targets potentially trying to score. This was done based on an altitude measured by the blimp's barometer when it was within goal height.


<hr>
## Biggest Challenges and Takeaway
Through our experience in the competition we were able to get a better understanding of where our blimp would shine, and where it was lacking. The actual competition was our biggest part of testing as we were in the proper environment and could understand variables that were not present in our other testing environments such as the air currents in the arena. Our blimp proved to be very mobile as well as fast, but had a hard time stabilizing itself with the added air currents causing it to sway and drift.

If we were to have more time with the project, a few things I would like to change would be to spend more time implementing the C++ version of our code, especially with there being many improvements in libraries having been done, and expanding our use of localization and mobility. Seeing our blimp occasionally experiencing trouble with stability can be unsatisfying, so if we had more time to adjust our mobility, I believe we could have been a lot more effective in our role.

Ultimately this was a really good experience for me to better understand working on team projects over a long span of time. The biggest challenge our team faced was not fully understanding the direction we were expected to go with this project, as our goal and scope changed multiple times due to requirements from our advisor or changes in direction from the whole GMU team. Other than that this project allowed for a lot of learning regarding project scope and management and understanding of new hardware/software.

Oh yeah, and we ended up getting **3rd Place** in the competition. Considering our enemies had some real scoring powerhouses, I'm happy with our placement. Here's some celebratory spins (we really liked making our blimp do barrel rolls):

<p align="center">
<br><img src='/images/blimp-spin.gif' width=400>
</p>