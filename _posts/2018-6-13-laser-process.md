---
layout: post
title: Leetcode
date: 2018-6-13
categories: blog
tags: [技术总结]
description: Laser data Process
---

# Velodyne Laser

:octopus: Hardware requirements  
> 12V power supply  
> Gigabit Ethernet connection to an ECU, or network switch  
>> It’s strongly recommended to provide a direct connection from the LiDAR to the ECU  
> ECU with PolySync Core installed  
> Velodyne Interface Box  

:camel: Configuring the sensor  
* Velodyne provides a web interface to configure and verify the sensor is powered and working  
    ○ Enter the IP address of the sensor (default: 192.168.2.201) in the web browser URL

* To configure multiple HDL-32E sensors on the same machine or network, the UDP broadcast port on each sensor must be unique along with the Telemetry Port  
    ○ Under Network, edit the IP Address so that it is unique. Press the set button after you finish editing  
    ○ Under Host, edit the telemetry port so that it is unqiue. Press the set button after you finish editing  
  ![configure](http://docs.polysync.io/images/velodyne_web_interface_2.png)
    
    ○ Press the Save Configuration button.  
* You will need to power cycle the sensor and PolySync Dynamic Driver to see changes

:dog: Configuring the ECU

* Linux network set up

The Ethernet network must be properly configured before PolySync can communicate with the sensor on the target ECU.  
It’s highly recommended to use point-to-point Ethernet communication between the sensor and the ECU to lower the latency. You may connect the sensor to a switch but may be prone to high latency.  

* Setting the ECUs static IP address

Once the LiDAR’s IP address is known, set a static IP address for the ECUs NIC in the same IP subnet range as the LiDAR.  
For example if the LiDAR’s IP address is 192.168.2.201, it’s said to be on the 192.168.2 subnet, and the ECU should be configured with IP address 192.168.2.X.  


## Euler and Quaternion

### From Euler to Quaternion:

```
using namespace Eigen;
//Roll pitch and yaw in Radians
float roll = 1.5707, pitch = 0, yaw = 0.707;    
Quaternionf q;
q = AngleAxisf(roll, Vector3f::UnitX())
    * AngleAxisf(pitch, Vector3f::UnitY())
    * AngleAxisf(yaw, Vector3f::UnitZ());
std::cout << "Quaternion" << std::endl << q.coeffs() << std::endl;
```

### From Quaternion to Euler:

```
auto euler = q.toRotationMatrix().eulerAngles (0, 1, 2);
std::cout << "Euler from quaternion in roll, pitch, yaw"<< std::endl << euler << std::endl;
```

### Use Eigen::Isometry3d pose to describe translation and rotation

```
Eigen::Isometry3d pose; //4 by 4 matrix in fact
pose.linear() = q.normalized().toRotationMatrix();
pose(0,3) = pos_x;
pose(1,3) = pos_y;
pose(2,3) = pos_z;
```

## an affine transformation to an isometric transformation

b.rotation() extract the rotation part of the transformation. It involves a SVD, and thus it is read-only. On the left hand side, you have to use .linear():

```
Eigen::AffineCompact3f a;
Eigen::Isometry3f b;
b.translation() = a.translation();
b.linear() = a.rotation();
```

If you know that 'a' is an isometry and only want to cast it to an Isometry 3f, you can simply do:

```
b = a.matrix();
```
