---
title: "Radar Inertial Datasets ICINS 2021"
layout: archive
permalink: /datasets/icins_2021_radar_inertial_odometry
---

This dataset can be downloaded [here](https://bwsyncandshare.kit.edu/s/75NYFkskLTfrGeG) and can be processed by our yaw
 aided radar inertial odometry pipeline [ekf_yrio](https://github.com/christopherdoer/rio/tree/main/ekf_yrio).

Feel free to contact me in case of questions or issues: [Christopher Doer](mailto:christopher.doer@kit.edu)

The dataset consists of 5 carried and 4 manual flight indoor datasets recorded in an office building.
Pseudo ground truth is available and was created using [VINS](https://github.com/HKUST-Aerial-Robotics/VINS-Fusion)  with loops closure and subsequent manual removal of scale errors.
A short summary of the datasets is given below:

|Name |Trajectory Length  | Duration | Environment |
  --- | --- | --- | ---
|Carried 1| 287m | 273s | Office floor: offices, computer labs, long corridor | 
|Carried 2| 451m | 392s | Office floor: offices, computer labs, long corridor|
|Carried 3| 235m | 171s | Office floor: labs, meeting room, long corridor|
|Carried 4| 311m | 220s | Office floor: labs, meeting room, long corridor|
|Carried 5| 228m | 172s | Basement: workshop, long corridor |
|Manual Flight 1| 147m | 180s | Office floor: furnished offices, computer labs, long corridor |
|Manual Flight 2| 49m  | 75s | Office floor: labs, long corridor|
|Manual Flight 3| 149m | 178s | Office floor: labs, long corridor|
|Manual Flight 4| 79m | 110s | Office floor: labs, long corridor|


# Cite
If you use this dataset for your academic research, please cite our related paper:
~~~[bibtex]
@INPROCEEDINGS{DoerICINS2021,
  author={Doer, Christopher and Trommer, Gert F.},
  booktitle={2021 28th Saint Petersburg International Conference on Integrated Navigation Systems (ICINS)}, 
  title={Yaw aided Radar Inertial Odometry uisng Manhattan World Assumptions}, 
  year={2021}
~~~


# Known Issues
- The pseudo ground truth starts with a slight time delay which is caused by the initialization of VINS. Thus the
 first few seconds (with motion) does not have ground truth.
- The pseudo ground truth is not aligned as we logged the output of VINS here.  Thus, alignment of the ground truth
 and estimated path is required for evaluation.
- The carried and flight datasets have different extrinsic calibrations, see below.


# Sensor Setup
The sensor platform is equipped with a FMCW radar sensor (TI IWR6843AOP) and an IMU (Analog Devices ADIS16448).
All sensors are synchronized using a microcontroller board which actively triggers measurements.
The IMU runs at 409Hz and the radar sensors at 10Hz.

The TI IWR6843AOP is an FMCW radar sensor which integrates also the antenna array (3Tx and 4Rx) on a single chip.
It operates at 60GHz and features a Field of View (FOV) of circa 120deg in azimuth and elevation.
All radar signal processing is done on chip providing the radar scan data.
The radar sensor is mounted at approx. 45deg pointing to the left.

## Sensor platform (carried datasets)
![image](../_datasets/icins_2021_radar_inertial_datasets/sensor_platform.jpg) 

***Extrinsic calibration of the sensor platform (carried datasets):***
~~~
## Extrinsic calibration of body frame to radar frame
# translation of radar frame expressed in body frame
l_b_r_x: 0.05
l_b_r_y: 0.08
l_b_r_z: 0.07

# rotation radar frame to body frame
q_b_r_w: 0.93354
q_b_r_x: -0.00502
q_b_r_y: 0.01127
q_b_r_z: -0.35827
~~~


## Quadcopter (manual flight datasets)
![image](../_datasets/icins_2021_radar_inertial_datasets/quad.jpg)   

***Extrinsic calibration of the quadcopter (flight datasets):***
~~~
## Extrinsic calibration of body frame to radar frame
# translation of radar frame expressed in body frame
l_b_r_x: 0.01
l_b_r_y: 0.10
l_b_r_z: 0.06

# rotation radar frame to body frame
q_b_r_w: -0.01185
q_b_r_x:  0.36648
q_b_r_y:  0.93033
q_b_r_z:  0.00560
~~~

# Data Format
The sensor data is provided with rosbags containing the following topics:
- /sensor_platform/imu (sensor_msgs/Imu): IMU measurements (ADIS16448)
- /sensor_platform/baro (sensor_msgs/FluidPressure): Barometer measurements (ADIS16448) 
- /sensor_platform/radar/trigger (std_msgs/Header): Radar trigger, marks the start of a radar scan
- /sensor_platform/radar/scan (sensor_msgs/PointCloud2): Radar scan (IWR6843AOPEVM) whereas each point consists of: x
, y, z, snr_db , v_doppler_mps, noise_db and range. The time stamp is already in sync with the corresponding trigger header.

The point cloud point type is sketched below, for an example implementation see [radar_point_cloud.h](https://github
.com/christopherdoer
/rio/blob/main/rio_utils/include/rio_utils/radar_point_cloud.h) and [radar_point_cloud.cpp](https://github.com
/christopherdoer/rio/blob/main/rio_utils/src/radar_point_cloud.cpp):

~~~
struct RadarPointCloudType
{
  PCL_ADD_POINT4D;      // position in [m]
  float snr_db;         // CFAR cell to side noise ratio in [dB]
  float v_doppler_mps;  // Doppler velocity in [m/s]
  float noise_db;       // CFAR noise level of the side of the detected cell in [dB]
  float range;          // range in [m]
  EIGEN_MAKE_ALIGNED_OPERATOR_NEW
} EIGEN_ALIGN16;
                                  
pcl::PointCloud<RadarPointCloudType> your_pcl;
~~~

The pseudo ground truth is provided with csv files (separator: whitespace).   
The format is: timestamp p^w_x p^w_y p^w_z q^w_x q^w_y q^w_z q^w_w   
with the global position p^w and attitude quaternion q^w.    
This file format is compatible e.g. with [rpg_trajectory_evaluation](https://github.com/uzh-rpg
/rpg_trajectory_evaluation) for trajectory evaluation as done in our [paper](../_publications/2021_05_ICINS2021.md).


# Pseudo Ground Truth Plots
### Carried 1 
![image](../_datasets/icins_2021_radar_inertial_datasets/carried_1_pseudo_ground_truth.png)

### Carried 2
![image](../_datasets/icins_2021_radar_inertial_datasets/carried_2_pseudo_ground_truth.png)

### Carried 3
![image](../_datasets/icins_2021_radar_inertial_datasets/carried_3_pseudo_ground_truth.png)

### Carried 4
![image](../_datasets/icins_2021_radar_inertial_datasets/carried_4_pseudo_ground_truth.png)

### Manual Flight 1
![image](../_datasets/icins_2021_radar_inertial_datasets/flight_1_pseudo_ground_truth.png)

### Manual Flight 2
![image](../_datasets/icins_2021_radar_inertial_datasets/flight_2_pseudo_ground_truth.png)

### Manual Flight 3
![image](../_datasets/icins_2021_radar_inertial_datasets/flight_3_pseudo_ground_truth.png)

### Manual Flight 4
![image](../_datasets/icins_2021_radar_inertial_datasets/flight_4_pseudo_ground_truth.png)