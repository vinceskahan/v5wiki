# Use Maxbotix ultrasonic sensor to measure water level

This is a guide to use a Maxbotix ultrasonic sensor to measure water level.  The ultrasonic sensors can measure the amount of water in a tank, the tide height, or even snow depth.

Maxbotix makes *many* different models of ultrasonic sensor.  The sensor capabilities vary in the resolution of measurements, the minimum distance between sensor and object, the maximum range, and the beam width.  Other options include the communication protocol and form factor.  There are even options for factory-applied coatings to inhibit corrosion.

https://maxbotix.com/

This guide uses the MB7363 HRXL-MaxSonar-WRLS, which has the following characteristics:
* resolution: 1 mm
* detection: first target
* range: 500-9999 mm
* beam angle: moderate
* read rate: 6 Hz
* communications: RS232

## What does it look like?

<img src="maxbotix-recipe/MB7363.png" width="200">
<img src="maxbotix-recipe/tide-sensor.png" width="200">
<img src="maxbotix-recipe/tank-sensor.png" width="200">

## What do you need?

| cost | description | source |
|---|---|---|
| $138 | MB7363 | https://maxbotix.com/products/mb7363 |
| $78 | RPi 3B+ | https://www.amazon.com/ELEMENT-Element14-Raspberry-Pi-Motherboard/dp/B07BDR5PDW/ |

Prices are US$ as of April 2023.

