After running roscore run this in another terminal to run using bags
```
rosparam set use_sim_time true
```
Publish fixed frames for velodyne(lidar)->base_link and base_link->/odom_encoder for laserscan conversion, run in seperate terminals
```
rosrun tf static_transform_publisher 0 0 0 0 0 0 base_link velodyne 50
rosrun tf static_transform_publisher 0 0 0 0 0 0 /odom_encoder base_link 50

```
Convert PointCloud to LaserScan in a new terinal
```
rosrun pointcloud_to_laserscan pointcloud_to_laserscan_node cloud_in:=/velodyne_points
```
Start gmapping with temporal updates in a new terminal
```
rosrun gmapping slam_gmapping _odom_frame:=odom_encoder _temporalUpdate:=0.1
```
Play the recorded rosbag in a new terminal
```
rosbag play mylaserdata3.bag
```
Visualize using rviz in a new terminal
```
rosrun rviz rviz -f velodyne
```
Select the according topics&visualize
