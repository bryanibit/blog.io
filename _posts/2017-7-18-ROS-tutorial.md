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
## 自定义一个msg和srv

msgs are just simple text files with a field type and field name per line. The field types you can use are:

```
    int8, int16, int32, int64 (plus uint*)
    float32, float64
    string
    time, duration
    other msg files
    variable-length array[] and fixed-length array[C]
    Header
```
- The header contains a **timestamp** and **coordinate frame** information that are commonly used in ROS.
- srv files are just like msg files, except they contain two parts: a request and a response. The two parts are separated by a '---' line.
- e.g. Here is an example of a srv file:

```
int64 A
int64 B
---
int64 Sum
```

### 新建msg

这时候我们可以在beginner_tutorials中新建一个文件msg/Num.msg,也可以在根目录中新建

同时在该包的package.xml中加上两句话，以便msg可以编译为c++，python可以识别的样子

```
<build_depend>message_generation</build_depend>
<run_depend>message_runtime</run_depend>
```
同时在CmakeLists.txt中添加以下内容，此是为了buildDependencies

```
find_package(catkin REQUIRED COMPONENTS
   roscpp
   rospy
   std_msgs
   message_generation
)
```

Export the message runtime dependency

```
catkin_package(
  ...
  CATKIN_DEPENDS message_runtime ...
  ...)
```

CMake knows when it has to reconfigure the project after adding other .msg files
```
add_message_files(
  FILES
  Num.msg
)
```

最后产生message可以读到的message，申明依赖的message
```
generate_messages(
  DEPENDENCIES
  std_msgs
)
```

使用rosmsg show topic_type可以查看具体内容

### 新建srv

mkdir srv

touch ××.srv (roscp rospy_tutorials AddTwoInts.srv srv/AddTwoInts.srv)

package.xml添加和msg中相同内容，在cmakelists中添加相同内容，唯一不同的地方如下（msg是add_message_files）：

```
add_service_files(
  FILES
  AddTwoInts.srv
)
```

使用 rossrv show beginner_tutorials/AddTwoInts可以查看到以下具体内容

```
int64 a
int64 b
---
int64 sum
```

- Any .msg file in the msg directory will generate code for use in all supported languages. The C++ message header file will be generated in ~/catkin_ws/devel/include/beginner_tutorials/.
- The Python script will be created in ~/catkin_ws/devel/lib/python2.7/dist-packages/beginner_tutorials/msg.
- The lisp file appears in ~/catkin_ws/devel/share/common-lisp/ros/beginner_tutorials/msg/.

Similarly, any .srv files in the srv directory will have generated code in supported languages.
- For C++, this will generate header files in the same directory as the message header files.
- For Python and Lisp, there will be an 'srv' folder beside the 'msg' folders.

## Write publisher node

```
ros::init(argc, argv, "talker");
```
初始化Ros和赋node name（不能有/）


```
ros::NodeHandle n;
```
初始化node，新建句柄


```
ros::Publisher chatter_pub = n.advertise<std_msgs::String>("chatter", 1000);
```
告诉roscore，要publish一个topic，名叫chatter，类型叫std_msgs::String， 1000 是 the size of our publishing queue（buffer）.


```
 ros::Rate loop_rate(10);
```
配合Rate::sleep()使用，10指的是10Hz

Ctrl-C will cause ros::ok() to return false

```
ros::spinOnce();
```
If you were to add a subscription into this application, and did not have ros::spinOnce() here, your callbacks would never get called.










