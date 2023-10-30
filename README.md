# AMRL Changes

This code has been tested on Ubuntu 20.04 with the Ouster 128 and VectorNav VN-300 IMU. 

# imu_lidar_calibration
## Target-free Extrinsic Calibration of a 3D Lidar and an IMU

## Overview

This repository is a toolkit for calibrating the 6-DoF rigid transformation between a 3D LIDAR and an IMU. It's based on an Extended Kalman Filter based algorithm which exploits the motion based calibration constraint for state update. This algorithm does not depend on any calibration target or special environmental features, like planes, for determining the extrinsic calibration between a 3D-Lidar and an IMU.

[Presentation Video](https://youtu.be/VIc8XxNrymQ)

[Paper](https://arxiv.org/abs/2104.12280)

## Prerequisites 
This code base was tested and implemented in a Ubuntu 16.04 system.
- [ROS](http://wiki.ros.org/ROS/Installation) (tested with Kinetic)
- [GTSAM - 4.0.3](https://gtsam.org/build/)  (This is used only to initialize the rotation between the sensors)
- [Ceres - 1.14.0](http://ceres-solver.org/installation.html)
- [ndt_omp](https://github.com/koide3/ndt_omp) [Caution: This has been found to be unstable in newer ubuntu/pcl, you can use the package that already exists in this particular repository, I made it work in Ubuntu 20.04 with PCL 1.12]

Still, I realize that it may be difficult to build this on newer Ubuntu, hence [docker](https://hub.docker.com/repository/docker/smishra30/imu-lidar-calib-docker-app). Although I have provided the script to run this, basic knowledge of docker is necessary.

## Install

- Clone the source code for [ndt_omp](https://github.com/APRIL-ZJU/ndt_omp) and build it in your catkin workspace
- Clone this code-base and build it in your catkin workspace

## Sensor suite

As far the 3D Lidar is considered, currently this code-base supports `Ouster-128` but it is easy to expand for other 3D Lidars. We have tested this code-base by downsampling our `Ouster 128` lidar to 64, 32, 16 channel modes. The important pre-requisite is that the points in lidar pointcloud must come with a measurement/firing timestamp. We use a Vectornav VN 300 IMU. In our setup the Lidar outputs scans at 10 Hz and the IMU outputs measurements at 400 Hz, however, we have also tested our algorithm with IMU running at 50 Hz, 100 Hz & 200 Hz.

![alt text](https://github.com/SubMishMar/imu_lidar_calibration/blob/main/figures/lidar_imu_setup.png?raw=true)

## Procedure
### Intrinsic Calibration of IMU
First of all, we need to determine the noise characterisitcs of the IMU biases. Several toolboxes are available online to determine this. We used https://github.com/rpng/kalibr_allan . This requires MATLAB. Other alternatives are: https://github.com/mintar/imu_utils , https://github.com/ori-drs/allan_variance_ros

### Data collection for extrinsic calibration
We need to excite all degrees of freedom during collecting data required for extrinsic calibration. An example video can be found here: 
<a href="http://www.youtube.com/watch?feature=player_embedded&v=2IX5LVTDkLc
" target="_blank"><img src="http://img.youtube.com/vi/v=2IX5LVTDkLc/0.jpg" 
alt="Data collection procedure" width="240" height="180" border="10" /></a>

Please find a sample dataset here to try out the algorithm yourself: https://drive.google.com/file/d/1o20lcmXU1HxOP4KsLXrXbjT2jTfjeaJh/view?usp=sharing

### Inter-sensor rotation estimation
![alt text](https://github.com/SubMishMar/imu_lidar_calibration/blob/main/figures/RotHEC.png?raw=true)

`roslaunch linkalibr ros_calib_init.launch`

I am working on making this code more generic, until then please take care to change the file path names.

### Inter-sensor translation estimation 
![alt text](https://github.com/SubMishMar/imu_lidar_calibration/blob/main/figures/KFBlock.png?raw=true)

`roslaunch linkalibr linkalibr_ouster_vectornav.launch`

I am working on making this code more generic, until then please take care to change the file path names.

The results are stored in folder `linkalibr/data` as a homogenous transformation matrix in text file `I_T_L_final.txt`

## Plots
We can plot the results of the EKF based estimation process by using the MATLAB plot files available in folder `linkalibr/data`. 

The plot for calibration parameters is shown below:

#### Estimated inter-sensor translation parameter
![alt text](https://github.com/SubMishMar/imu_lidar_calibration/blob/main/figures/calibXYZ.jpg?raw=true)

#### Estimated Inter-sensor rotation parameter
![alt text](https://github.com/SubMishMar/imu_lidar_calibration/blob/main/figures/calibEulerXYZ.jpg?raw=true)

Some more figures...

#### Estimated IMU Trajectory
![alt text](https://github.com/SubMishMar/imu_lidar_calibration/blob/main/figures/trajectoryXYZ.jpg?raw=true)

#### Estimated IMU Velocity
![alt text](https://github.com/SubMishMar/imu_lidar_calibration/blob/main/figures/IMUVelocityXYZ.jpg?raw=true)

#### Result of scan matching using motion compensated scans
![alt text](https://github.com/SubMishMar/imu_lidar_calibration/blob/main/figures/map.png?raw=true)


## Acknowledgement
This codebase was developed under the [OpenVINS](https://github.com/rpng/open_vins) framework.
