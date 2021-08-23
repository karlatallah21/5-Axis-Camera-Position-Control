# Adjustments

## Code

### Keyframe Array Size
The number of positions in a sequence of movements was adjusted by changing the variable KEYFRAME_ARRAY_LENGTH's value in the panTiltMount.h file. The user can change that to whatever value they want, allowing for a longer sequence of stored movements and camera shots. Constants like that are usually found in header files, where they're relatively easy to change and are used and referenced by other files in a project, in this case the source code panTiltMount written in C++. Another fix would be to declare KEYFRAME_ARRAY_LENGTH in panTiltMount.cpp and initiliaze it with the value of the user's choice, giving way for controls to be added to the program in which the user changes that value through the serial monitor or from the controller directly, instead of going through the code. 

### Homing

#### Offset
The homing function was previously inconsistent due to the hall effect sensor going off a few degrees before being centered on the magnet, on the pan and tilt axes, since their sensors' magnets should be diametric instead of being axial. This was solved by introducing an offset variable which would correct the insufficient homing. By trial and error, the offset was found to be around 10 degrees, so this was accounted for in the code after perfoming homing, as shown below.
```c++
float hall_pan_offset_degrees = -10; 
float hall_tilt_offset_degrees = -10;

setTargetPositions(hall_pan_offset_degrees * panHomingDir, hall_tilt_offset_degrees * tiltHomingDir, sliderPos,0,0);
multi_stepper.runSpeedToPosition();
stepper_pan.setCurrentPosition(0);
stepper_tilt.setCurrentPosition(0);
```
#### Hall Effect Sensor
While working on the project, homing stopped working on the slider axis, continuously working the motor when called. Debugging using basic print statements, I found out that the hall effect sensor on that axis malfunctioned.
```c++
while(multi_stepper.run()) {
  if(digitalRead(PIN_PAN_HALL) == 0) {
    printi(F("Home"));
    stepper_pan.setCurrentPosition(0);
    setTargetPositions(0, -45 * !tiltHomeFlag, sliderPos,0,0);
    panHomeFlag = true;
    panHomingDir = 1;
  }
}
```
Note that if the reading off the hall effect sensor is 0 or LOW, that means the home position is detected, or that the sensor is centered on the magnet. One solution was to replace the faulty hall effect sensor with a new one, but that would require taking the circuit apart and then rebuilding it. I opted for a quick fix, or a hack, which was to adjust the homing function in the code to no longer require the use of a hall effect sensor. This was done by countering the movements already done on each axis, since the position of every axis is already saved in .currentPosition() which is the last movement and could be benefitted from. This would essentially turn the system into an open loop control system, but wouldn't affect anything major as the slider still does not directly know where it is on every axis, relying on the last movement command. An example of the code I wrote is shown below.
```c++
//Slider Axis
if(homing_mode == 1 || homing_mode == 3){ 
  if(stepper_slider.currentPosition()) { 
    target_position[2] = target_position[2] - stepper_slider.currentPosition(); 
    multi_stepper.moveTo(target_position); 
    multi_stepper.runSpeedToPosition();
    stepper_slider.setCurrentPosition(0); 
  } 
  setTargetPositions(panStepsToDegrees(target_position[0]), tiltStepsToDegrees(target_position[1]), 0,0,0);
  sliderHomeFlag = true;
}
```
```c++
//Pan and Tilt Axes
if(homing_mode == 2 || homing_mode == 3) { 
  if(stepper_pan.currentPosition()) { 
    target_position[0] = target_position[0] - stepper_pan.currentPosition();
    multi_stepper.moveTo(target_position);
    multi_stepper.runSpeedToPosition();
    stepper_pan.setCurrentPosition(0);
  }
  setTargetPositions(0, tiltStepsToDegrees(target_position[1]), sliderStepsToMillimetres(target_position[2]), 0, 0);
  panHomeFlag = true;

  if(stepper_tilt.currentPosition()) { 
    target_position[1] = target_position[1] - stepper_tilt.currentPosition();
    multi_stepper.moveTo(target_position);
    multi_stepper.runSpeedToPosition();
    stepper_tilt.setCurrentPosition(0);
  }
  setTargetPositions(panStepsToDegrees(target_position[0]), 0, sliderStepsToMillimetres(target_position[2]), 0, 0);
  tiltHomeFlag = true;
}
```
The only obstacle with this is that the slider would have to be set at a predetermined position for the user to know where the origin is on each axis, as there is no set/ constant origin, which was perviously the case with the hall effect sensor. In other words, you would have to manually place the slider on the edge of the rail as a default homing position for that specific axis, when the system is not connected to power. It was not necessary to adjust the pan and tilt axes homing since their respective hall effect sensors were working fine after debugging, but I changed those functions to preserve consistency among all 3 axes.

## Controls

### Speed
Speed was the primary limiting factor in the project, so my focus was mainly on that. Changing the microstepping mode enabled higher speeds to be achieved. Caution should be exercised while testing the motor's max speed to avoid frying it. Through trial and error, the max speed was established and set so as to increase the overall capabilities of the slider in terms of speed.

## Circuitry

### Power Surges
When connecting the board to my laptop, power surges would sometimes occur due to the board being supplied with more than its rating (5V vs 3.3V). Adjustments were made by reinstalling the drivers, the slider worked perfectly fine after that. 

### Serial Communication 
Serial communication with the board was oftentimes absent. Through extensive testing, it was established that longer cables were not working with the laptop I'm currently using probably due to high line capacitance. Other serial communication troubleshooting procedures can be found [here](https://www.arduino.cc/en/Main/Troubleshooting) and [here](https://forum.arduino.cc/t/loop-back-test-instructions/73046). 

### Connections
Due to overusing the slider and over-rotating the axes, especially the pan axis where all the circuitry and connections are found, some connections may become undone, causing the circuit to short or the board to stop responding. Adjustments to tackle that were to solder some loose wires onto their pins or use crimps/ female headers to secure the connections in place (pinout files are available for the project). Periodically, I would check all connections on the circuit and replace the USB cable to prevent sudden damage of circuitry and wiring. Another fix would be to prevent the slider from excess movement, limiting range of motion to prevent wire twisting and loosening. This would be implemented by placing a small trigger on the desired position, overriding the code and stopping the specific motor from turning. This would also be useful in tracking the slider's location on that specific axis, by knowing that the end of the range of motion has been reached at a previously determined position. Better packaging would also be a viable solution, while screwing the board on the pan axis base made the problem worse.

### Motor Jams
Motor jams would occur frequently while testing and refining the slider. This is an easy fix and was almost every time caused by wiring being stuck under the pan axis base and the slider axis rails. I frequently went over those connections to fix loose or stuck ones. Again, better packaging and placement of the board is the solution. 

### Tilt Axis Collisions
If the board is not tightly secured to the bottom of the pan axis base, the tilt axis would collide with all the circuitry during its movement. The solution I implemented here was to go over the CAD files for the tilt axis side supports (arms that connect the pan axis mount to the tilt axis mount) and extend them by 50cm to provide a safe cleareance between the tilt axis and the board. 
<p align="center">
<img width="365" alt="extended" src="https://user-images.githubusercontent.com/72527951/129777372-48c5b9c0-b632-46eb-896f-1732e6583d1e.png">
</p>
Issues I faced with redesigning that part and the idle-side tilt support were generally due to the CAD files not being dimensioned properly, which made it a hassle to perform any slight changes. I went over the CAD files I wanted to redesign and defined most of the dimensions found, as a more robust way of fixing broken part designs.
Some tips to keep in mind for future design:

- Always make sure parts and features you are designing are fully defined before moving on to others, this makes future changes easy to apply
- Direct editing could be useful to edit some parts which would otherwise take a long time, since most of the provided CAD files are highly undefined
- When importing STL files into SolidWorks, apply import diagnostics on the parts to recognize and ultimately be able to edit all features without any restrictions

After redesigning the mounts, I 3D printed them using a PLA printer and faced many obstacles throughout the process which will be discussed later in the 3D Printing section.

## 3D Printing

After many unsuccessful prints, I started to notice common problems occuring which I will detail along with their solutions.

### Stringing and Retraction
Especially with PLA FDM printers, stringing is common and reduces print quality. Thin strings can be observed in features like holes and can be solved by enabling retraction or increasing retraction distance if it is already enabled in the slicer software. This makes the nozzle pull back a bit to prevent any material from dripping and thus reduces stringing. Other fixes I tried were to increase retraction speed and reduce nozzle temperature to prevent oozing of material. Reducing nozzle temperature is done in small increments in order to ensure that the material can still melt properly during extrusion. A good online test I used to figure out the optimal printer settings can be found [here](https://teachingtechyt.github.io/calibration.html#retraction). Parameters on the website are easily adjustable to figure out the best printer configuration. I set my retraction speed at 40 mm/s and the temperature at 225 degrees, but these differ depending on the printer being used. The full configuration file for the printer I used is attached in the repository.

### Distorted Prints
Randomly distorted prints may be due to many reasons. One fix I found was efficient regardless of the problem is to reduce print speed. Reducing print speed makes prints more accurate, I set mine at around 90 mm/s. Printing a [benchy](https://www.thingiverse.com/thing:763622) is very useful in figuring out the optimal print parameters since printing one tests a variety of possible print problems. Many of my prints failed since they did not stick properly to the bed during the start. Staying near and observing the print as it's happening is vital, until the first layer is done, to stop it immediately in case it fails. This is because most problems surface while printing the first layer, and stopping the print in case it fails prevents any nozzle malfunction that might happen if it contnues running. Cleaning the bed and the nozzle is also very important in ensuring high quality prints. Another fix I used to ensure that the prints stick is to dampen a glue stick and apply a bit on the bed. 

### Slicer Software
For my prints, I used the Prusa slicer software as it is very convenient and easy to use. When slicing, I set the overhang threshold at 60 degrees to add more support material to the print. Parts should always be positioned at approximately 30 degrees from the bed in the slicer, with the finer details of the part being closer to the bed. This is because the print accuracy of the part decreases as it moves farther from the bed (above other layers), so we would ideally want our most important pieces towards the bottom. Also, support materials ruin the surface finish of the part slightly, so the more intricate pieces of the part should be placed close to the bed (no surface material attached) as I previously stated. The general rule of thumb is to avoid overhangs as much as possible to maximize the print quality of the part, since overhangs are usually very tricky for the printer. To make the print faster, the overhang threshold and the infill percentage could be decreased to reduce the amount of material used in the print. 
