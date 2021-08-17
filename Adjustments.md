# Adjustments

## Code

### Keyframe Array Size
The number of positions in a sequence of movements was adjusted by changing the variable KEYFRAME_ARRAY_LENGTH's value in the panTiltMount.h file. The user can change that to whatever value they want, allowing for a longer sequence of stored movements and camera shots. Constants like that are usually found in header files, where they're relatively easy to change and are used and referenced by other files in a project, in this case the source code panTiltMount written in C++. Another fix would be to declare KEYFRAME_ARRAY_LENGTH in panTiltMount.cpp and initiliaze it with the value of the user's choice, giving way for controls to be added to the program in which the user changes that value through the serial monitor or from the controller directly, instead of going through the code. 

### Homing
The homing function was previously inconsistent due to the hall effect sensor going off a few degrees before being centered on the magnet, on the pan and tilt axes. This was solved by introducing an offset variable which would correct the insufficient homing. By trial and error, the offset was found to be around 10 degrees, so this was accounted for in the code after perfoming homing, as shown below.
```c++
float hall_pan_offset_degrees = -10; 
float hall_tilt_offset_degrees = -10;

setTargetPositions(hall_pan_offset_degrees * panHomingDir, hall_tilt_offset_degrees * tiltHomingDir, sliderPos,0,0);
multi_stepper.runSpeedToPosition();
stepper_pan.setCurrentPosition(0);
stepper_tilt.setCurrentPosition(0);
```

## Circuitry

### Power Surges
When connecting the board to my laptop, power surges would sometimes occur due to the board being supplied with more than its rating (5V vs 3.3V). Adjustments were made by reinstalling the board's drivers, the slider worked perfectly fine after that. 

### Connections
Due to overusing the slider and over-rotating the axes, especially the pan axis where all the circuitry and connections are found, some connections may become undone, causing the circuit to short or the board to stop responding. Adjustments to tackle that were to solder some loose wires onto their pins or use crimps/ female headers to secure the connections in place (pinout files are available for the project). Periodically, I would check all connections on the circuit and replace the USB cable to prevent sudden damage of circuitry and wiring. Another fix would be to prevent the slider from excess movement, limiting range of motion to prevent wire twisting and loosening. This would be implemented by placing a small trigger on the desired position, overriding the code and stopping the specific motor from turning. This would also be useful in tracking the slider's location on that specific axis, by knowing that the end of the range of motion has been reached at a previously determined position. Better packaging would also be a viable solution, while screwing the board on the pan axis base made the problem worse.

