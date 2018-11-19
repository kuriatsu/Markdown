TC2018 Robot Setup
===

Autoware: 1.7.0  
Robot: NUIV, MAD_PICKLE

## Menu

1. Set PC in the Robot

2. Power on the Robot

3. Run robot control

4. Run Autoware

5. Setup Autoware

6. How to control Robot

## 1. Set PC in the Robot

## 2. Power on the Robot

## 3. Run robot control


```bash
#when you use yp_spur
$ cd path/to/yp_kerberos
$ ./kerberos.sh

#when you use sh_spur
$ cd path/to/sh_kerberos
$ ./kerberos.sh
```
## 4. Run Autoware

```bash
$ cd path/to/Autoware
$ ./run
```

## 5. Setup Autoware
### Setup

<image src="picture/setup.png" width=50%>  

1. **Localizer**  
    Select localization sensor

2. **TF**  
    Input LiDAR position on Robot

3. **Vehicle Model**  
    Default car model is set if you chose nothing.

### Map

<image src="picture/map.png" width=50%>  

1. **Point Cloud**  
    Input path to point_cloud

2. **Vector Map**  
    If you use vector_map to set white line and traffic light on the map, input path to vector_map. If not, ignore this console.

3. **TF**  
    Input path to tf file

### Sensing

<image src="picture/sensing.png" width=50%>  
1. **Velodyne VLP-16**  

    Input calibration setting file like   
    `/path/to/Autoware/ros/src/sensing/drivers/lidar/packages/velodyne/velodyne_pointcloud/params/VLP16db.yaml`
     If you use oster, see [here]().

     <image src="picture/lidar_vlp16.png" width=50%>  


2. **voxel grid filter**  
    Creates a 3D voxel grid (think about a voxel grid as a set of tiny 3D boxes in space) over the input point cloud data. Then, in each voxel, all the points present will be approximated (i.e., downsampled) with their centroid.  

    <image src="picture/vox_grid_filter.png" width=50%>  

    + **leaf size [m]**  
        Voxel’s length of each side. The bigger this value is, the faster computer is. 0.2 is best value for outside. When you use inside, 0.1 is best.

    + **Measurement Range [m]**  
        Restrict the range of point cloud data. The smaller this value is, The faster computer is.

3. **ring_ground_filter**  
    Remove ground points not to be detected as objects. This filter is best choice compered to other filters. Out put data is broadcast to `/points_no_ground`.

    <image src=picture/ring_ground_filter.png width=50%>  

### Computing

<image src="picture/computing.png" width=50%>  
1. **ndt_matching**

    <image src="picture/ndt.png" width=70%>  
    + **Error Thres**  

    + **Resolution**  
        It may good to have the same value with *leaf size*

    + **Step Size**  
        0.08 is the best value for this Robot’s speed!! This value is my secret spice to make ndt_maching stable ^o^.

    + **Transformation Epsilon**  

    + **Maximum iteration**  
        We need not tune this value. Iteration times is actually up to 12~20.

2. **vel_pose_connect**  

    <image src="picture/vel_pose_connect.png" width=50%>


3. **lidar_euclidean_cluster_detect**  

    <image src="picture/lidar_euclidean_cluster.png" width=50%>

    + **input_point_node**  
        If you use ground_filter, change input node from `/points_raw` to `/points_no_ground`

    + **keep_only_lanes_points**  
        Restrict detection range to alongside the road. This function reduce the nunber of detected obstacles and makes dp_planner work well.

    + **use_gpu**  
        *lidar_euclidean_cluster_detect* is heavy burden for computer. By distributing the calculation to GPU make computer work faster.

4. **way_planner**  
    Select vector map.  
    If you use one which created in Hatem’s original map tool (which file extension is `.kml` ), check `KML` in **Map Source**.    
    If you use one which created in Autoware map tools, (which file extension is `.csv` ) ,check `Autoware` in **Map Source**.

    <image src="picture/way_planner.png" width=50%>


5. **dp_planner**  

    <image src="picture/dp_planner.png" width=50%>  
    <image src="picture/dp_passes.png" width=50%>

    + **Follow Distance [m]**  
    The distance to obstacles in front of the robot. The Robot follow obstacles keeping this distance.

    + **Avoiding Distance [m]**  
        If the distance to obstacles is this value, the robot will avoid the obstacles.

    + **Avoidance Limit [m]**  
        If the distance to obstacle is under this value, the robot will stop.

    + **Lateral Safety [m]**  
        Keep lateral distance from tf origin to obstacles.

        <image src="picture/lateral_safezone.png" width=50%>

    + **Longitudinal Safety [m]**  
        Keep longitudinal distance from tf origin to obstacles.

6. **pure_pursuit**
    <image src="picture/pure_pursuit.png" width=50%>

    + **Waypoint**  
        Run according to the speed described in the Waypoint.

    + **Dialog**  
        Run according to the speed set in the slider.

7. **twist_filter**

    <image src="picture/twist_filter.png" width=50%>

## 6. How to control Robot

<image src="picture/joy.png" width=70%>

## 7. Edit code for robot

1. **Run time manager**  
    Lower the minimum of the slider in *runtime_manager* (*dp_planner* [app])  to match the scale for the robot.  
    Change min and max value in
    `/path/to/Autoware/ros/src/util/packages/runtime_manager/scripts/computing.yaml`
    *ln 2480~*

2. **Obstacle Detection**  
    Change condition value in  
    `/path/to/Autoware/ros/src/computing/planning/motion/packages/dp_planner/nodes/RosHelpers.cpp` *ln 565~*

3. **Robot Motion**
    `/path/to/Autoware/ros/src/computing/planning/motion/packages/dp_planner/nodes/dp_planner_core.cpp`
