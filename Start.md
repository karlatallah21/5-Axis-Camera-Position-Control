# Getting Started 
As an intern who took over the project, I found it would be useful for future interns to have a concentrated guide for a better understanding on how the system works, so I decided to write one.

## Code
It is critical to get to know the libraries being used and how they generally work before reading, adjusting or writing any code. The [AccelStepper](http://www.airspayce.com/mikem/arduino/AccelStepper/index.html) library is used in this project. I went over different alternatives to this library, and did not find any better ones. The AccelStepper is the easiest and most convenient library to use to control stepper motors with Arduino. The reason it was chosen over the default Arduino stepper motor library is because it supports multiple simultaneous stepper motors and also supports acceleration and deceleration.
Documentation for the AccelStepper library can be found [here](http://www.airspayce.com/mikem/arduino/AccelStepper/classAccelStepper.html).
Documentation for the MultiStepper library (to control mutliple stepper motors) can be found [here](http://www.airspayce.com/mikem/arduino/AccelStepper/classAccelStepper.html).

### Understanding the AccelStepper Library
I found some difficulty using the library at first, so I documented a few of the misconceptions or misunderstandings I found were important for new people to know before starting to work on this project.

#### Moving the slider
In the following code, for example:
```c++
multi_stepper.moveTo(target_position); 
multi_stepper.runSpeedToPosition();
```
A common misconception is that .moveTo() is what makes the motors work. However, it just sets the target position (which is inputted in steps) for all stepper motors, while .runSpeedToPosition() is what drives the motors.

#### Then, What Is setTargetPositions()?
SetTargetPositions() is a method that facilitates setting target positions for all stepper motors by converting the inputs in degrees (pan, tilt, focus and zoom) and millimetres (slider) to steps and initializes them in the target_position array. MoveTo() is then used to set the target positions for all stepper motors using the target_position array as input.

#### Stopping the Motors
In the following code, for example:
```c++
multi_stepper.stop(); 
multi_stepper.runToPosition();
```
Stop() does not actually stop the motors, it only sets a new target at the current position. RunToPosition() is what actually stops the motors. 

#### Disabling the Motors
When the motors are not working, they consume power and produce a loud noise. One way to troubleshoot that is to connect the ENABLE pin on the driver to one of the Arduino’s digital outputs and pull it high to disable the motor:
```c++
digitalWrite(motorOffPin, HIGH);
```

#### Units
One important thing to note while working on the project is the use of different units throughout the code. Some functions, such as setTargetPositions, have to be passed values in degrees or millimetres, while other fields, such as the target_position array, contain values in steps, the language which the motors understand.
