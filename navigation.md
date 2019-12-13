Setup the rover-robotics driver and controllers
Setup the tf transformation tree by publishing two static tf topics. 

```
rosrun tf static_transform_publisher 0 0 0 0 0 0 base_link velodyne 1000  
rosrun tf static_transform_publisher 0 0 0 0 0 0 /odom base_link 1000  
//Note that I've changed the name of the frame "odom_encoder", and the frequency. Please use them.  
```
Setup the Velodyne driver.  
Transfer the pointcloud to laserscan to prepare for 'gmapping'  
Run the gmapping package with parameters below: 
```
rosrun gmapping slam_gmapping _odom_frame:=odom _temporalUpdate:=0.01 _map_update_interval:=1.0 _transform_publish_period:=0.0001  
```
Launch the navigation stack from launch file:  
```
roslaunch shepherd move_base.launch  
```
Run the python file in folder shepherd/scrips/twisted_stamped.py to translate the /cmd_vel published by move_base node to /cmd_vel/managed. This is because move_base publishes Twist but the robot driver required TwistStamped. The python node acts a simple translator node between topics.  

Control the robot going forth and back with the xbox controller, so that gmapping could generate a dynamic map to use.  

If everything is correct, for example, you can see the global_costmap and local_costmap, manually set a target position from rostopic: rostopic pub /move_base_simple/goal geometry_msgs/PoseStamped "header ..." and set your frame_id and target position. Note: you can give a quite small number, eg. 0.001 for orientation of z, in case there are warning messages on quaternions from navigation stacks.  
