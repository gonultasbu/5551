Publish fixed frame for laserscan conversion.
```
rosrun tf static_transform_publisher 0 0 0 0 0 0 velodyne base_link 50
```
Play the recorded rosbag
```
rosbag play mylaserdata3.bag
```
Convert PointCloud to LaserScan.
```
rosrun pointcloud_to_laserscan pointcloud_to_laserscan_node cloud_in:=/velodyne_points
```
