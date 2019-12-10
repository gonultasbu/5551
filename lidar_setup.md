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
sudo apt-get install ros-kinetic-velodyne
```
(skip the lan connections)
