Using Ubuntu 16.04 and ros-kinetic.  

HARDWARE INSTRUCTIONS
=========================

The Velodyne LiDAR must be connected to the Laptop running ROS according to its instructions provided. To achieve mobility, a 9V mobile battery has been used. During the development phase, it has been discovered that the placement of the LiDAR is very important. Being a very precise sensor, even slight misalignment of the LiDAR can lead to erroneous calculations. LiDAR should be placed at a higher altitude than the laptop, so that the Lidar rays are not blocked by any part of the robot. Also, the LiDAR should be rigidly attached to the robot base, so that it does not change position or orientation as a result of robot motion, sudden jerks, turns, stops etc. Else, these will cause the LiDAR data to be in a frame which is different from the frame expected by the SLAM algorithm. The generated map will be erroneous and the resulting navigation commands are going to be wrong because the robot orientation is different from what the navigation stack was expecting.

Install the packages. Install ROS packages for controlling the robot. Here downloading and installing the source code is recommended for the convenience of revising the roslaunch file. https://github.com/RoverRobotics/rr_openrover_basic, for basic controlling nodes, and https://github.com/RoverRobotics/rr_control_input_manager for using Xbox controller nodes. Simply create a catkin workspace for the project,  
```
cd catkin_ws/src/
git clone https://github.com/gonultasbu/5551
```
copy rr_control_input_manager and rr_openrover_basic to catkin_ws/src and then  
```
cd ..
catkin_make

```
If you're experiencing issues, add the following code to your .bashrc for ROS.

```
source devel/setup.bash
```

Now need to run the packages. Separately run the two example packages above with the roslaunch file. No need for roscore.
```
roslaunch rr_openrover_basic example.launch
roslaunch rr_control_input_manager example.launch
```


Sometimes you need to change joystick's mod. Change the mod of the joystick in case by running: 
```
sudo rmmod xpad
```
and 

```
sudo xboxdrv
```
If xboxdrv is not installed, install it with 
```
sudo apt-get install xboxdrv
```
Note: Change the port of rr_openrover_basic launch file to /dev/ttyUSB0 if it's something else. 
The error from joystick saying something like "no force feedback" is not fatal, and may be safely ignored.
Reference: https://github.umn.edu/RSN-Robots/groundRobotDrive

If encountering port permission error:
```
sudo adduser <the username you're working under> dialout
```
and logout & login.

After these steps, you should be able to use the robot using the Xbox 360 controller. LIDAR setup instructions are as follows:  

LIDAR Installation Documentation  
========================
Tutorial link is below:
```
git clone https://github.com/ros-drivers/velodyne  
catkin_make
#connect powered on lidar
#check using the tutorial
roslaunch velodyne_pointcloud VLP16_points.launch
(on some computers, source devel/bash.sh would be needed for roslaunch to see the file)
#check data coming from lidar
rostopic echo --noarr /velodyne_points
rosrun rviz rviz -f velodyne
(add topic /velodyne_points using [Add] button)
```
OR
http://wiki.ros.org/velodyne/Tutorials/Getting%20Started%20with%20the%20Velodyne%20VLP16
```
sudo apt-get install ros-melodic-velodyne
```

After setting up LIDAR, we need to have SLAM. Follow these steps.

```
sudo apt install ros-kinetic-gmapping
roscore
```
After running roscore run this in another terminal to run using bags (you can also run using live data)
```
rosparam set use_sim_time true
```
Publish fixed frames for velodyne(lidar)->base_link and base_link->/odom_encoder for laserscan conversion, run in seperate terminals
```
rosrun tf static_transform_publisher 0 0 0 0 0 0 base_link velodyne 1000
rosrun tf static_transform_publisher 0 0 0 0 0 0 /odom_encoder base_link 1000

```
Convert PointCloud to LaserScan in a new terinal
```
rosrun pointcloud_to_laserscan pointcloud_to_laserscan_node cloud_in:=/velodyne_points
```
Start gmapping with temporal updates in a new terminal
```
rosrun gmapping slam_gmapping _odom_frame:=odom_encoder _temporalUpdate:=0.01 _map_update_interval:=1.0 _transform_publish_period:=0.0001
```
Play the recorded rosbag in a new terminal
```
rosbag play --clock mylaserdata3.bag
```
Visualize using rviz in a new terminal
```
rosrun rviz rviz -f velodyne
```
Select the according topics&visualize, you should be able to see the laserscan on rviz after selecting its topic.  

FOR ACTUATION
========================

twist_stamped.py is a publisher subscriber translator node that is ran with python on a new terminal. The base python environment should be capable of running it. It converts the Twist type messages to TwistStamped type. On a new terminal:
```
python twist_stamped.py
```
Keep it running for actuating the robot.

FOR NAVIGATION
========================

Launch the navigation stack from launch file:  
```
roslaunch shepherd move_base.launch  
```
If you did not previously, run the python file in folder shepherd/scrips/twisted_stamped.py to translate the /cmd_vel published by move_base node to /cmd_vel/managed. This is because move_base publishes Twist but the robot driver required TwistStamped. The python node acts a simple translator node between topics.  

Control the robot going forth and back with the xbox controller, so that gmapping could generate a dynamic map to use.  

If everything is correct, for example, you can see the global_costmap and local_costmap, manually set a target position from rostopic: rostopic pub /move_base_simple/goal geometry_msgs/PoseStamped "header ..." and set your frame_id and target position. Note: you can give a quite small number, eg. 0.001 for orientation of z, in case there are warning messages on quaternions from navigation stacks. The robot should be ready to navigate autonomously after reading a goal pose.

