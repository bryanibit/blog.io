---
layout: post
title: ros tutorials
date: 2017-07-18
categories: blog
tags: [技术总结]
description: ROS 相关问题
---
## ros文件结构
- 自己用catkin_create_pkg创建的包需要使用
- source ~/catkin_ws/devel/setup.bash将自己建的包放在ros_package_path中
- package.xml中含有

```
<buildtool_depend>catkin</buildtool_depend>
<build_depend>roscpp</build_depend>
<build_depend>rospy</build_depend>
<build_depend>std_msgs</build_depend>
<run_depend>roscpp</run_depend>
<run_depend>rospy</run_depend>
<run_depend>std_msgs</run_depend>
```

- 这是由于catkin_create_pkg beginner_tutorials roscpp rospy std_msgs
- 使用rospack denpend1 beginner_tutorials查看

## ros节点
ROS client libraries allow nodes written in different programming languages to communicate:
*    rospy = python client library
*    roscpp = c++ client library
If roscore does not initialize and sends a message about lack of permissions,
probably the ~/.ros folder is owned by root, change recursively
the ownership of that folder with:

            $ sudo chown -R <your_username> ~/.ros

改变节点名称：

             rosrun turtlesim turtlesim_node __name:=my_turtle

新名称是my_turtle,原来名称是turtlesim

## ros topic

**查看各个节点topic关系**

             rosrun rqt_graph rqt_graph

**查看topic的数据类型**

             rostopic type /topic_name

**可以继续查看topic type的具体参数（float，double等）**

             rosmsg show /topic_type

## rosdep

rosdep是一个你可以用来安装ROS package系统依赖的工具

               rosdep install [package]

如果你是第一次使用rosdep，你会看到一个error

	             sudo rosdep init
   	           rosdep update

## package.xml

### build, Run, and Test Dependancies

Build tool dependencies
- 基本上只需要catkin

Build denpenencies
- include header from these packages at compilation time,linking against libraries from these packages at build time
- e.g. find_package()-ed in CMake

Run dependencies
- when depend on share libraries include headers
- e.g. catkin_package() in CMake

Test dependencies
- 可选的依赖，和build和run都不重复的依赖

在package.xml中四部分写法
```
<buildtool_depend>依赖名</buildtool_depend>
<build_depend>依赖名</build_depend>
<run_depend>依赖名</run_depend>
<test_depend>依赖名</test_depend>
```

## 通过命令行发送topic

```
rostopic pub [topic] [msg_type] [args]
```
e.g.控制乌龟运动

```
 rostopic pub -1(数字) /turtle1/command_velocity       turtlesim/Velocity     -- 2.0  1.8
                  topic_name                    (topic type /topic_name)    (rosmsg show /topic_type)
```

## rqt_plot绘制topic的曲线图

- add any topic to the plot.

## ROS service
- send a request and receive a response.

## ROS launch

````
roslaunch [package] [filename.launch]
```
在beginner_tutorials中新建一个launch的方法

```
roscd beginner_tutorials
mkdir launch
touch launch

--------------------------------------------------------------
<launch>
  <group ns="turtlesim1">
    <node pkg="turtlesim" name="sim" type="turtlesim_node"/>
  </group>
  <group ns="turtlesim2">
    <node pkg="turtlesim" name="sim" type="turtlesim_node"/>
  </group>

  <node pkg="turtlesim" name="mimic" type="mimic">
    <remap from="input" to="turtlesim1/turtle1"/>
    <remap from="output" to="turtlesim2/turtle1"/>
  </node>
</launch>
--------------------------------------------------------------

```
