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
  setTargetPositions(panStepsToDegrees(target_position[0]), tiltStepsToDegrees(target_position[1]), 0,0,0); //input is in deg/mm, converts into steps and sets target positions
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

## Circuitry

### Power Surges
When connecting the board to my laptop, power surges would sometimes occur due to the board being supplied with more than its rating (5V vs 3.3V). Adjustments were made by reinstalling the board's drivers, the slider worked perfectly fine after that. 

### Connections
Due to overusing the slider and over-rotating the axes, especially the pan axis where all the circuitry and connections are found, some connections may become undone, causing the circuit to short or the board to stop responding. Adjustments to tackle that were to solder some loose wires onto their pins or use crimps/ female headers to secure the connections in place (pinout files are available for the project). Periodically, I would check all connections on the circuit and replace the USB cable to prevent sudden damage of circuitry and wiring. Another fix would be to prevent the slider from excess movement, limiting range of motion to prevent wire twisting and loosening. This would be implemented by placing a small trigger on the desired position, overriding the code and stopping the specific motor from turning. This would also be useful in tracking the slider's location on that specific axis, by knowing that the end of the range of motion has been reached at a previously determined position. Better packaging would also be a viable solution, while screwing the board on the pan axis base made the problem worse.

### Motor Jams
Motor jams would occur frequently while testing and refining the slider. This is an easy fix and was almost every time caused by wiring being stuck under the pan axis base and the slider axis rails. I frequently went over those connections to fix loose or stuck ones. Again, better packaging and placement of the board is the solution. 

### Tilt Axis Collisions
If the board is not tightly secured to the bottom of the pan axis base, the tilt axis would collide with all the circuitry during its movement. The solution I implemented here was to go over the CAD files for the tilt axis side supports (arms that connect the pan axis mount to the tilt axis mount) and extend them by 50cm to provide a safe cleareance between the tilt axis and the board. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img width="365" alt="extended" src="https://user-images.githubusercontent.com/72527951/129777372-48c5b9c0-b632-46eb-896f-1732e6583d1e.png">
