---
layout: post
title: Laser Process
date: 2018-6-13
categories: blog
tags: [技术总结]
description: Laser data Process
---

## Velodyne Laser

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

## Affine transformation to an isometric transformation

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

## sensor_msgs::PointCloud2

```python
---------------sensor_msgs::PointCloud2-------------------
Header header
# If the cloud is unordered, height is 1 and width is the length of the point cloud.
uint32 height
uint32 width
# Describes the channels and their layout in the binary data blob.
PointField[] fields
bool    is_bigendian # Is this data bigendian?
uint32  point_step   # Length of a point in bytes
uint32  row_step     # Length of a row in bytes
uint8[] data         # Actual point data, size is (row_step*height)
bool is_dense        # True if there are no invalid points
---------------sensor_msgs::PointField--------------------
uint8 INT8    = 1
uint8 UINT8   = 2
uint8 INT16   = 3
uint8 UINT16  = 4
uint8 INT32   = 5
uint8 UINT32  = 6
uint8 FLOAT32 = 7
uint8 FLOAT64 = 8
string name      # Name of field
uint32 offset    # Offset from start of point struct
uint8  datatype  # Datatype enumeration, see above
uint32 count     # How many elements in the field
```
**For sensor_msgs::PointCloud2**:  
* **point_step** is the size of one point in bytes, which you need to manually calculate for your particular format as specified in **msg.fields**. (with pen & paper). For example if msg.fields[0] has datatype uint16 (2 bytes) and count 3, then your write down 23=6 bytes for that field. Plus the rest of msg.fields. A common use case would be for you to have 3 entries in msg.fields (for x, y and z) where each has datatype = FLOAT32, which is 4 bytes. So point_step would be 34=12.
* **row_step** is should be just *width*point_step*. Seems rather odd me, because that is redundant.
* **data** then contains your actual data as specified in fields. So in the simple use case from above it would just be x1, y1,z1, x2, y2, z3, x3, y3, z3, ....
* What "**is_dense** = true" means is: this dataset has *no invalid* points (e.g., NaN, Inf).  

The following laser packet PointCloud2 is shown:

```js
header:
  seq: 7647
  stamp:
    secs: 946657304
    nsecs: 802155000
  frame_id: "pandar"
height: 1
width: 43294
fields:

    name: "x"
    offset: 0
    datatype: 7
    count: 1

    name: "y"
    offset: 4
    datatype: 7
    count: 1

    name: "z"
    offset: 8
    datatype: 7
    count: 1

    name: "intensity"
    offset: 16
    datatype: 7
    count: 1

    name: "timestamp"
    offset: 24
    datatype: 8
    count: 1

    name: "ring"
    offset: 32
    datatype: 4
    count: 1
is_bigendian: False
point_step: 48
row_step: 2078112
```
From the above explanation, we can compute the value of *point_step*: according to *fields* value and the definition of *point_step*, the types of fields members are **7, 7 ,7 ,7 ,8 ,4 = 4B for 4 + 8B + 4B = 24B**, plus, 6 count values are summed to 24B. Therefore *point_step* is 48B.

## HDL64E-3S Packet Structure

The hardware sensor delivers a 360 horizontal Field of View (HFOV) and a 26.8 vertical FOV.  
From 64E-S3-Manual, we can conclude that a Ethernet packet of HDL64 consists of 1206 bytes for 12 blocks and 6 byte of other info. Such a clear description is as follow:  
![DataPacketConstruct](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/HDL-64_data_packet_format.jpeg)
What is a block? I think it means laser scan which points an emit and a receive of upper and lower laser. It is ....
The default spin rate is 600 RPM(10Hz). The changing spin do not change the data rate -- the unit will send out the same number of packets(at a rate of 1.3 million data points per second, and ethernet rate is 1 packet/100us) regardless of spin rate. The angular resolution is as follow:

| --RPM-- | --Points per Round-- | --Points per Round per laser-- | --Angle Resolution-- |
| :---------: | :----------------: | :--------------------------: | :----------------: |
| 300 | 266,627 | 4167 | 0.0864 |
| 600 | 133,333 | 2083 | 0.1728 |
| 900 | 88,889  | 1389 | 0.2592 |
| 1200 | 66,657 | 1042 | 0.3456 |

From above, we can get that upper block have more points and more accurate resolution than lower block.
