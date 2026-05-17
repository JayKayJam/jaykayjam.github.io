---
layout: archive-wide
title: "Active Car Monitor"
excerpt: "A way to monitor and analyze car metrics over the course of a drive<br/><img src='/images/Car-project-setup.jpg' width=500><img src='/images/heat-map-rpm.png' width=400>"
collection: portfolio
author_profile: false
share: false
classes: wide
order: 1
---

This project was designed to utilize metrics communicated through a car's On-Board Diagnostics (OBD) port to analyze the behavior and health of a car over the course of a drive. This was done as the final project for my ECE612 (Real-time Embedded Systems) class at GMU.

This project uses a Raspberry Pi 3B to analyze different metrics in a car such as RPM or speed and plots those points on a map according to those values. A GPS device communicates with the Pi to give it the vehicle's coordinates over time. This project additionally logs all the metrics, GPS coordinates and any fault codes from the car onto a SQLite database saved locally on the Pi.

<hr>
## System Architecture

The components and devices used for this project are:
- Raspberry Pi model 3B
- ELM327 OBD2 reader
- VK-162 External GPS mount

<p align="center">
<img src='/images/OBD2-flowchart.png' width=600>
<br>The flowchart of the project
</p>


### ELM327
The ELM327 is the device used to connect to the vehicle's OBD2 port in order to interpret the CAN communication of the vehicle. Because the OBD port uses CAN communication, the data must be formatted in a certain way as CAN communication is message-based rather than address-based. Data is requested from the vehicle's Engine Control Unit (ECU) by sending commands formatted with a service mode and Parameter ID (PID). This message is formatted as **"XXYY"** where **'XX'** represents the service mode and **'YY'** represents the PID. The service modes used in the project are the following:
- Mode 01: Request Engine Data (for example "010C" requests engine rpm)
- Mode 03: Request diagnostic trouble codes. This does not require a PID

The response from the ECU is formatted in a similar manner with the first byte always being 0x40 plus which ever mode is requested (01, 02 or 03). This is followed by the PID and one or more bytes of data. The data bytes may need to be calculated according to which metric is requested.


### VK-162 GPS
The Global Positioning System (GPS) is a method of navigation used to determine the position of a device by receiving signals from satellites to trangulate latitude and longitude. The VK-162 in particular uses these signals to output position data using **NMEA 0183** standard- a standard commonly used in marine electronics. Every NMEA output begins with the prefix **'$GP'** followed by one of six IDs, each having a different purpose. This project searches for **'$GPRMC'** as it contains the minimum GPS data for determining latitude, longitude and a verification value to ensure the data is valid.

The latitude and longitude values are expressed in the form **'DDMM.MMMM'** where 'D' values represent integer degrees and 'M' values represent minutes. Position can be found by applying the formula: *decimal degrees = degrees + (minutes/60)*

<p align="center">
<img src='/images/GPS-output-example.png' width=600>
<br>Example NMEA 0183 output, sourced from <a href="https://en.wikipedia.org/wiki/NMEA_0183">wikipedia</a>
</p>


### Raspberry Pi 3B
The Raspberry Pi 3B is the device for processing and analyis of the input from the two devices. Both the ELM327 and GPS module are connected via USB ports on the Pi, with the ELM enumerating as **/dev/ttyUSB0** and the GPS enumerating as **/dev/ttyACM0**. Two other GPS devices were tested using the Pi's UART GPIO pins (instead enumerating as /dev/ttyserial0), though those devices showed low consistency and easy failure and were thus not used. The data from these devices is acquired using POSIX **termios** API. The Pi itself is powered using the vehicle's internal 12 V socket which draws power from the car whenever the engine is running.

Both devices connected to the Pi are given their own thread to receive and transmit data simultaneously, and the readings from both devices are timestamped as soon as they are received. The data is stored locally to the Pi using an SQLite database named *trips.db*. Each reading includes the values from both devices as well as the timestamp. Fault codes are written in a separate table with their own time stamps.

The data from the SQLite database is pulled using a python script in order to generate n interactive HTML heat map using the **folium** library. The heatmap is saved as *trip_{TRIP_ID}_all_metrics.html* according to the trip number. This heat map allows for comparison of each recorded metric overlaying a street-view map, similar to one you would see from google maps.


<hr>
## Implementation

### OBD2 Communication
The ELM327 was set up on the Raspberry Pi’s /dev/ttyUSB0 serial port with a Baude rate of 38400. This is the default rate for the device when operating at high-speed mode. The reading of both the car metrics and any error codes was implemented through C++ code in the *main.cpp* file. Before being able to read any information from the OBD port, the device was initialized with a set of commands first:
- ATZ: Reset the adapter to clear any leftover parameters
- ATE0: Turn off echo, this prevents unnecessarily printing commands sent through the ELM327
- ATL0: Turn of line feeds. This just cleans up the output formatting to make it easier to parse
- ATS0: Spaces off. This makes the output one continuous hex string to make parsing multiple bytes at once simpler.
- ATSP0: Auto detect which protocol to use. Most cars utilize the ISO 15765-4 CAN protocol, though some older cars may utilize a different protocol which changes how the ELM327 communicates with the vehicle’s ECU.

After initialization, the ELM327 sends commands to the OBD to read the vehicle’s current RPM, speed and throttle, as well as any error codes detected. To parse the OBD response, a function finds any data bytes after the header bytes (410C as a header, for example) and uses those in one of three defined formulas based on which metric is being requested.

<p align="center">
<img src='/images/OBD2-reading-code.png' width=600>
<br>Snippit of the parsing code and formulas
</p>

To determine any faults, a command is sent to the OBD to request any fault codes which are then parsed to check if any codes match a key in a pre-defined dictionary within the code which comes with a definition of what the code means. As some codes are manufacturer specific, default case will instead state the code and request the user to search up what the code means.


### GPS Communication
The VK-162 was connected to the Raspberry Pi’s /dev/ttyACM0 port with a Baude rate of 9600 to match the default rate of the device. Similar to the communication with the OBD, the GPS readings were read and determined through C++ code in the *main.cpp* file. When powered, the GPS device requires some time to establish communication between satellites before it can output any usable data. A verification variable is displayed by either the $GPRMC response or $GPGLL response to verify if the GPS response is valid or not. This variable is either a ‘V’ for not valid or an ‘A’ for valid.

<p align="center">
<img src='/images/GPS-no-fix.png' width=600>
<br>GPS displaying no fix
</p>


### Data Storage
The data for each trip is recorded using SQLite which saves information regarding the trip locally to the Raspberry Pi. In order to operate in a more time-critical fashion, the logging of this data was done in C++ code through the *main.cpp* file with behavior established in the *database.h* header file. There are three different tables which are saved to this database, those being:
- Trips table: Each trip ID as well as their start and end times
- Readings table: Each trip ID and each metric read from the OBD and GPS systems
- Faults table: The number of fault codes and their code values

<p align="center">
<img src='/images/database-table.png' width=600>
<br>Values from Readings table
</p>

On code launch, the trips table with be initialized with the ID of the trip and the start time. During the code loop, the main function will update the readings table every 500 ms to increment the table with the most recent readings from the OBD and GPS. Notably, if data fails to read or is invalid, the value added to the table will be the same as the last valid entry, as the code saves the most recent valid value that each device returns. When the trip is finished, the code will save the end time to the Trips table as well as increment the current_trip_id variable for the next trip.


### Heat Map
Due to the non-time-critical nature of generating the heat map and because of readily available libraries, the generation of the heat map was done in python. This script utilizes folium to generate a street map of the area centered at the middle of the trip path. The path is created using plotted points along the map, colored based on the values of their metrics.

To plot each point, the values of each metric is normalized from 0 to 1 based on their min and max values. The normalized value is then turned into a ratio of RGB values, with red aligning with 1 and blue aligning with 0 (green is in the middle). Circle markers are then placed at the longitude and latitude position on the map with their calculated color. The heatmap generated has three different layers: RPM, Speed and Throttle. Each layer can be toggled on and off to view the desired metric or to compare any trends. One comparison I like to show can be seen below, comparing a high RPM to an average speed with no acceleration, indicating a steep hill during the drive.

<p align="center">
<img src='/images/heat-map-RPM.png' width=600>
<br>
<img src='/images/heat-map-high-RPM-example.png' width=400> <img src='/images/heat-map-average-speed-example.png' width=400>
<br>Trip path (top) and comparison of high RPM (bottom left) to average speed (bottom right)
</p>

<hr>
## End Thoughts
This project introduced me to multiple topics that I do not have a lot of experience in such as saving information to databases using SQL and how to receive/send CAN messages. Although documentation for interfacing with the ELM327 is relatively sparse, especially for C++, it was very interesting to learn the deeper intricacies of the device such as how it interprets messages and how to read responses.

Overall this project was very interesting and taught me a lot of concepts and tecniques relevant to C++ and using devices such as a Raspberry Pi for utilizing various sensors and similar devices. I'm very happy with the results, particularly with the visuals, and I likely plan on expanding this system to allow for more autonomy of the device such as a more effective way to save or send resulting heat maps and potentially allowing this code to be run on startup of the Pi.