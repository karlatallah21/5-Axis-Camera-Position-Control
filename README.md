# 5-Axis Camera Position-Control
  The "Tasmanian Devil" is a 5-axis camera position control system to get all the video shots you could dream of. The system was designed by [isaac879](https://github.com/isaac879) and adjusted by [Macartney-Christopher](https://github.com/Macartney-Christopher) to add focus and zoom control functions (dynamic lighting control). Using Isaac's design and Christopher's adjustments, the system was tested extensively by myself and several changes were made to improve its functionality and reliability.
  
  Original Design: https://github.com/isaac879/Pan-Tilt-Mount 
  
  Dynamic Lighting Adjustments: https://github.com/Macartney-Christopher/5-Axis-Camera-Position-Control (https://www.youtube.com/watch?v=kPsWlgf-iiQ)
 

## Table of Contents
### 1. [Getting Started](Start.md)
### 2. [Mechanical Design](Mechanical.md)
### 3. [Hardware Design](Hardware.md)
### 4. [Software Design](Software.md)
### 5. [Adjustments](Adjustments.md)
### 6. [Comments](Comments.md)

## Setup 
### Using Gaming Controller
1. Plug the power cable to an outlet
2. Plug the Xbox controller and the USB cable to your computer
3. Download the repository and extract the folder [Tasmanian_Launch](Tasmanian_Launch).
4. Update [serial_port.txt](Tasmanian_Launch/serial_port.txt) in the folder with the port that the Nano is connected to. Go to Device Manager>Ports to find the port number (ex: COM4).
5. Double-click on [Tasmanian_Launch.bat](Tasmanian_Launch/Tasmanian_Launch.bat)
6. Enjoy!

### Using Arduino Serial Monitor
1. Compile the Arduino code [here](FlashStorage_PanTiltMount/FlashStorage_PanTiltMount.ino) 
3. Ensure all cables are connected
4. Compile and execute
5. Operate in Arduino IDE using [these commands](FlashStorage_PanTiltMount/5-Axis_Position_Control_Commands.pdf) in the serial monitor

## Credits
  The mechanical, hardware and software design is an adapted version of [isaac879](https://github.com/isaac879)'s [project](https://github.com/isaac879/Pan-Tilt-Mount). [Isaac879](https://github.com/isaac879) is credited with the complete ideation and design of the slider, a project well-suited for amateur engineers such as [Macartney-Christopher](https://github.com/Macartney-Christopher) and myself. [Macartney-Christopher](https://github.com/Macartney-Christopher) is credited with documenting key aspects of the design (sections 2, 3 and 4 of the table of contents), modifying it and adding the zoom and focus functionalites. 
  
## License
[Project license](LICENSE).
