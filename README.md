# 5551

ROVER ROBOTICS
Assembling the robot. Install the cables and the interface board following the tutorial: https://roverrobotics.com/blogs/guides/starter-kit-getting-started-guide. Plug in the battery when using the robot.
Install the packages. Install ROS packages for controlling the robot. Here downloading and installing the source code is recommended for the convenience of revising the roslaunch file. https://github.com/RoverRobotics/rr_openrover_basic, for basic controlling nodes, and https://github.com/RoverRobotics/rr_control_input_manager for using Xbox controller nodes. Simply create a catkin workspace for the project, and git clone ... two pacakges above into the src folder, and catkin_make
Run the packages. Separately run the two example packages above with the roslaunch file. roslaunch rr_openrover_basic example.launch, roslaunch rr_control_input_manager example.launch
Sometimes you need to change joystick's mod. Change the mod of the joystick in case by running: sudo rmmod xpad, and sudo xboxdrv. If xboxdrv is not installed, install it with sudo apt-get install xboxdrv
Note: Change the port of rr_oepnrover_basic launch file to /dev/ttyUSB0. And the warning from joystick saying something like "no force feedback" is not fatal, and could be ignored.
Reference: https://github.umn.edu/RSN-Robots/groundRobotDrive

if port permission error sudo adduser <the username you're working under> dialout
