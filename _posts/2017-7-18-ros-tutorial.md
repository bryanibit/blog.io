---
layout: post
title: ROS Tutorials
date: 2017-07-18
categories: blog
tags: [技术总结]
description: ROS 相关问题
---

## ROS Problems

* If roscore does not initialize and sends a message about lack of permissions,
probably the ~/.ros folder is owned by root, change recursively
the ownership of that folder with:
```
$ sudo chown -R <your_username> ~/.ros
```

## ROS Rules

- 一般**CMakeLists**中**find_package()**对应**package.xml**中的build或者run依赖

## ROS Commands

- Use ```catkin_create_pkg <pkg-name> <build-run-pkg>``` to create an empty pkg.  
- ```source ~/catkin_ws/devel/setup.bash``` will add itself to **ROS_PACKAGE_PATH**,
then environment variable will have catkin_ws's pkg. If you have multiple workspace,
you can choose to use different bash of different workspace especially for same pkg.  
- If using commands as ```catkin_create_pkg beginner_tutorials roscpp rospy std_msgs```, then use ```rospack depends1 beginner_tutorials``` to check the first class dependent pkg -- **roscpp rospy std_msgs**.  

- publish topic with commands
```
rostopic pub [topic_name] [topic_type] [args]
```
And the format of args is shown with ```rosmsg show /topic_type```  

e.g.控制乌龟运动
```
$ rostopic pub -r 10 /cmd_vel geometry_msgs/Twist  '{linear:  {x: 0.1, y: 0.0, z: 0.0}, 
angular: {x: 0.0,y: 0.0,z: 0.0}}'
```
And ```rosmsg show geometry_msgs/Twist``` exhibits 
```
geometry_msgs/Vector3 linear
  float64 x
  float64 y
  float64 z
geometry_msgs/Vector3 angular
  float64 x
  float64 y
  float64 z
```

## ROS Node

ROS client libraries allow nodes written in different programming languages to communicate:  
* rospy = python client library
* roscpp = c++ client library

Change node name:
```
$ rosrun turtlesim turtlesim_node __name:=my_turtle
```
Then the name is changed to *my_turtle* from *turtlesim*.

## ROS Topic

**查看各个节点topic关系**

             rosrun rqt_graph rqt_graph

**查看topic的数据类型**

             rostopic type /topic_name  ==> rostopic type(result)

**可以继续查看topic type的具体参数（float，double等）**

             rosmsg show /topic_type    ==> rostopic type format(result)

## rosdep

* rosdep是一个你可以用来安装ROS package系统依赖的工具, Use ```rosdep install [package1]``` to install the dependent pkgs of **package1** and **package1 itself**.  
rosdep is similar to pip in python. You can use ```apt-get install ros-<ros-version>-pkg``` to replace *rosdep*.
* Install dependency of all packages in the workspace
```
rosdep install --from-paths src --ignore-src -r -y
```
The above command will recursively parse all *package.xml* in workspace and install pkgs of *build_depand* or *run_depend*.

* 如果你是第一次使用rosdep，你会看到一个error, because of lacking initialization.  
```
sudo rosdep init
rosdep update
```

## package.xml

### build, Run, and Test Dependancies

* Build tool dependencies: 基本上只需要catkin

* Build denpenencies: *include header* from these packages at *compilation time*,linking against libraries from these packages at build time  
e.g. find_package()-ed in CMakeLists.txt

* Run dependencies: when depend on share libraries include headers  
e.g. catkin_package() in CMakeLists.txt

* Test dependencies: 可选的依赖，和build和run都不重复的依赖

在package.xml中四部分写法:
```
<buildtool_depend>依赖名</buildtool_depend>
<build_depend>依赖名</build_depend>
<run_depend>依赖名</run_depend>
<test_depend>依赖名</test_depend>
```

## ROS service
**pass**

## ROS launch

### 1.Basic Intro

[ROS官方解释](http://wiki.ros.org/roslaunch/XML)了每个参数的含义。launch文件采用深度优先搜索的方法，如果一个变量被多次定义，则采用最后一次定义的参数。  
最简单的配置如下：
```
<launch>
  <node name="you_define_node_name" pkg="package_name" type="exe_name" args="$(arg a) $(arg b)" />
</launch>
```
启动上面的launch文件就会启动package_name包下的exe_name执行文件,you_define_node_name自定义的node_name，即rosnode list的name，
等效为：```rosrun [package_name] [you_define_node_name] a:=1 b:=5```.  

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
### 2.private param

* **\<arg\>**
* \<arg\> specifying values that are passed via the command-line, passing in via an <include>, or declared for higher-level files.
* Args are not global. An arg declaration is specific to a single launch file.
* passed via command-line with ```roslaunch my_file.launch <arg-name>:=arg_value```.
* **\<param\>**
* Instead of value, you can specify a textfile, binfile or command attribute to set the value of a parameter.
* The \<param\> tag can be put inside of a \<node\> tag, in which case the parameter is treated like a private parameter. Such as,

```
<node name="sensor_node" pkg="pkg-name" type="sensor_node" output="screen">
    <param name="nml" value="$(arg nml_path)"/>
    <param name="buffer" value="P_**INFO"/>
</node>
```

The foregoing params is private and use ```rosparam list``` can check them out:

```
/sensor_node/buffer
/sensor_node/nml
```

* Passing private params to node needs private NodeHandle rather than normal NodeHandle,likely,

```
------------------src file--------------------
ros::NodeHandle n, pn("~");
pn.param("nml", m_name_nml, std::string("NONMLSET"));
------------------launch file-----------------------
<arg name="nml_path" default="/home/bryan/*.nml">
<param name="nml" value="$(arg nml_path))">
```

## 自定义一个msg和srv

msgs are just simple text files with a field type and field name per line. The field types you can use are:

```
    int8, int16, int32, int64 (plus uint*, e.g. uint8 unsigned 8-bit int)
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

这时候我们可以在beginner_tutorials中新建一个文件msg/Num.msg,也可以在根目录中新建.  
同时在该包的package.xml中加上两句话，以便msg可以编译为c++/python可以识别的样子.  
既然*package.xml*中有以下两句，则分别对应*find_package* and *catkin_package*.(check [package.xml](http://www.yhc201.com/blog/2017/07/18/ros-tutorial/#packagexml))

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

```
mkdir srv
touch ××.srv (roscp rospy_tutorials AddTwoInts.srv srv/AddTwoInts.srv)
```
package.xml添加和msg中相同内容，在cmakelists中添加相同内容，唯一不同的地方如下（msg是*add_message_files*）：

```
add_service_files(
  FILES
  AddTwoInts.srv
)
```

使用```rossrv show beginner_tutorials/AddTwoInts```可以查看到以下具体内容
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

A simple example
```
#include "ros/ros.h"  
#include "std_msgs/String.h"  
#include <sstream>  
int main(int argc, char **argv)
{
  // init Ros node(forbid using "/"）
  ros::init(argc, argv, "talker");
  /**
   * NodeHandle is the main access point to communications with the ROS system.   

   * The first NodeHandle constructed will fully initialize this node, and the last  

   * NodeHandle destructed will close down the node.  
   */
  ros::NodeHandle n;
  /**
   * The advertise() function is how you tell ROS that you want to  
   * publish on a given topic name. This invokes a call to the ROS  

   * master node, which keeps a registry of who is publishing and who  
   * is subscribing. After this advertise() call is made, the master  

   * node will notify anyone who is trying to subscribe to this topic name,  
   * and they will in turn negotiate a peer-to-peer connection with this  
   * node.  

   * advertise() returns a Publisher object which allows you to  
   * publish messages on that topic through a call to publish().  Once

   * all copies of the returned Publisher object are destroyed, the topic
   * will be automatically unadvertised.

   * The second parameter to advertise() is the size of the message queue
   * used for publishing messages.  If messages are published more quickly

   * than we can send them, the number here specifies how many messages to
   * buffer up before throwing some away.
   */
  ros::Publisher chatter_pub = n.advertise<std_msgs::String>("chatter", 1000);
  ros::Rate loop_rate(10);
  /**
   * A count of how many messages we have sent. This is used to create
   * a unique string for each message.
   */
  int count = 0;
  while (ros::ok())
  {
    std_msgs::String msg;
    std::stringstream ss;
    ss << "hello world " << count;
    msg.data = ss.str();
    ROS_INFO("%s", msg.data.c_str());
    /**
     * The publish() function is how you send messages. The parameter
     * is the message object. The type of this object must agree with the type

     * given as a template parameter to the advertise<>() call, as was done
     * in the constructor above.
     */
    chatter_pub.publish(msg);
    ros::spinOnce();
    loop_rate.sleep();
    ++count;
  }
  return 0;
}
```

## Write a subscribe node

A simple instance:
```
#include "ros/ros.h"
#include "std_msgs/String.h"
void chatterCallback(const std_msgs::String::ConstPtr& msg)
{
  ROS_INFO("I heard: [%s]", msg->data.c_str());
}
int main(int argc, char **argv)
{
  /**
   * You must call one of the versions of ros::init() before using any other
   * part of the ROS system.
   */
  ros::init(argc, argv, "listener");
  /**
   * NodeHandle is the main access point to communications with the ROS system.
   * The first NodeHandle constructed will fully initialize this node, and the last
   * NodeHandle destructed will close down the node.
   */
  ros::NodeHandle n;
  /**
   * The subscribe() call is how you tell ROS that you want to receive messages
   * on a given topic.  This invokes a call to the ROS master node, which keeps a registry of who is publishing and who
   * is subscribing.  Messages are passed to a callback function, here

   * called chatterCallback.  subscribe() returns a Subscriber object that you
   * must hold on to until you want to unsubscribe.  When all copies of the Subscriber
   * object go out of scope, this callback will automatically be unsubscribed from
   * this topic.

   * The second parameter to the subscribe() function is the size of the message
   * queue.  If messages are arriving faster than they are being processed, this

   * is the number of messages that will be buffered up before beginning to throw
   * away the oldest ones.
   */
  ros::Subscriber sub = n.subscribe("chatter", 1000, chatterCallback);

  /**
   * ros::spin() will enter a loop, pumping callbacks.  With this version, all
   * callbacks will be called from within this thread (the main one).  ros::spin()

   * will exit when Ctrl-C is pressed, or the node is shutdown by the master.
   */
  ros::spin();
  return 0;
}
```
1. If you want to capsulate subscriber or publisher with C++ class, the main idea can be summed up with the following:
* The class should include ```ros::Subscriber ros::NodeHandle``` for subscriber and ```ros::NodeHandle ros::Publisher```.
* You should initialize the member variables in class constructor.
2. If your class has **createTimer()**, then object ```ros::Timer``` should be included in class constructor, too.
And if the member variables have pointers, like *boost::shared_ptr*, do not forget :smile: to allocate memory for it in class constructor.


## ros::spinone() and ros::spin()

```
ros::init(argc, argv, "my_node");
ros::NodeHandle nh;
ros::Subscriber sub = nh.subscribe(...);
...
//all user callbacks will be called from within the
//ros::spin() call. ros::spin() will not return
//until the node has been shutdown
ros::spin();
-----------------------------------------
ros::Rate r(10); // 10 hz
while (ros::ok())
{
  ... do some work, publish some messages, etc. ...
  //ros::spinOnce() will call all the callbacks
  //waiting to be called at this point in time periodically.
  ros::spinOnce();
  r.sleep();
}
```

## ROS Graph Resource Names

There are four types of Graph Resource Names in ROS: base, relative, global, and private, which have the following syntax:  
- base  
- relative/name  
- /global/name  
- ~private/name  

## ROS MSG

总结ros msg [网页](wiki.ros.org/msg)  
- Message descriptions are stored in .msg files in the msg/ subdirectory of a ROS package.

msg名称：  
- the file geometry_msgs/msg/Twist.msg is commonly referred to as geometry_msgs/Twist.

msg基本格式：  
- Fixed- or variable-length arrays (lists) of the above, such as "float32[] ranges" or "Point32[10] points"
- the special Header type, which maps to std_msgs/Header

**Build-in type:**
```
Primitive Type          Serialization            C++             Python  

  bool                unsigned 8-bit int        uint8_t           bool  
  int8                  signed 8-bit int         int8_t            int  
  uint8               unsigned 8-bit int        uint8_t            int   0-255  
  int16                 signed 16-bit int        int16_t           int  
  uint16              unsigned 16-bit int       uint16_t           int  
  ---32                    ----32------           --32--           int
  ---64                 -------64------           --64--           long  
  float32             32-bit IEEE float         float              float  
  float64             64-bit IEEE float         double             float  
  string               ascii  string            std::string        str  
```

其中，uint8在Python中有特殊意义，uint8[]在Python中视为Python bytes,在ros msg中弃用的别名char(uint8弃用的别名)和byte(int8弃用的别名)

**Array handling**

```
Array Type            Serialization                        C++                             Python

fixed-length       no-extra serialization     0.11+:boost::array,otherwise:std::vector     tuple
variable-length    uint32 length prefix              std::vector                           tuple
uint8[]               see above                      std::vector                           bytes
bool[]                see above                   std::vector<uint8_t>                    list of bool
```

- rospy把uint8视为bytes，在Python2中，同str
- rospy将arrays反序列化为元组，同struct.unpack return 一个元组
- Header is not a built-in type (it's defined in std_msgs/msg/Header.msg

## ROS在多台机器上的配置

- You only need one master. Select one machine to run it on.  
- All nodes must be configured to use the same master, via ROS_MASTER_URI.  
- There must be complete, bi-directional connectivity between all pairs of machines, on all ports (see ROS/NetworkSetup).  
- Each machine must advertise itself by a name that all other machines can resolve (see ROS/NetworkSetup).  

### ROS文件结构 C++ & Python

```
package_name
             --include
                   --package_name
                            --××.h
             --src
		    --package_name
                            --××.py
                            --××.cpp
 	      --CMakeLists.txt
	      --package.xml
	      --setup.py
```

1. ××.h定义namespace space_name,××.cpp using namespace space_name,完成C++类和函数/命名空间的定义  
2. 有两个包装类，一个是C++，一个是Python  

- C++包装类将输入的序列化内容==>C++ message
- C++包装类将输出的C++ message==>序列化的内容
- Python包装类将输入的Python message==>序列化的内容
- Python包装类将输出的序列化内容==>Python message
- [**现在有些懵逼**](http://wiki.ros.org/ROS/Tutorials/Using%20a%20C%2B%2B%20class%20in%20Python)

对于以上懵逼内容的见解：摘自 [github](https://github.com/luator/boost_python_catkin_example)

1. When using ROS, there is one important thing to keep in mind: Assume you have a ROS node written in Python, that uses some C++ code via Boost::Python. If the C++ code needs a ros::NodeHandle, for example to fetch some parameters from the parameter server, it will crash, because the rospy.init_node() does not initialize roscpp!  
To get around this, you need the MoveIt ROS planning interface which is available in the ROS repos (sudo apt-get install ros-groovy-moveit-ros-planning-interface). Once installed, add the following code to your Python node:
```
from moveit_ros_planning_interface._moveit_roscpp_initializer import roscpp_init
roscpp_init('node_name', [])
```
Now everything should work fine. Note that: ros::NodeHandle在与master交互时候使用.  
2. CMakeLists:  
uncomment ```catkin_python_setup()```. This will set up the destination path of the python module. You also need a basic *setup.py* in the packages root directory.  
3. CMakeLists: add_libraries 添加的生成的库文件使用set_target_properties命令应该放在
```
/catkin_ws/devel/lib/python2.7/dist-packages/
```
so it can be found by python.

## Dynamic reconfigure

:rocket: Add file ```Tutorials.cfg``` to ```/node_file/cfg/Tutorials.cfg```  

```
#!/usr/bin/env python
# ---Tutorials.cfg---
PACKAGE = "dynamic_tutorials"
from dynamic_reconfigure.parameter_generator_catkin import *
gen = ParameterGenerator()
gen.add("int_param",    int_t,    0, "An Integer parameter", 50,  0, 100)
gen.add("double_param", double_t, 0, "A double parameter",    .5, 0,   1)
gen.add("str_param",    str_t,    0, "A string parameter",  "Hello World")
gen.add("bool_param",   bool_t,   0, "A Boolean parameter",  True)
# add a enum
size_enum = gen.enum([ gen.const("Small",      int_t, 0, "A small constant"),
                       gen.const("Medium",     int_t, 1, "A medium constant"),
                       gen.const("Large",      int_t, 2, "A large constant"),
                       gen.const("ExtraLarge", int_t, 3, "An extra large constant")],
                     "An enum to set size")
gen.add("size", int_t, 0, "A size parameter which is edited via an enum", 1, 0, 3, edit_method=size_enum)
exit(gen.generate(PACKAGE, "dynamic_tutorials", "Tutorials"))
```
Now that we have a generator (```gen```) we can start to define parameters. The add function adds a parameter to the list of parameters. It takes a few different arguments:  
* name - a string which specifies the name under which this parameter should be stored
* paramtype - defines the type of value stored, and can be any of int_t, double_t, str_t, or bool_t
* level - A bitmask which will later be passed to the dynamic reconfigure callback. When the callback is called all of the level values for parameters that have been changed are ORed together and the resulting value is passed to the callback.
* description - string which describes the parameter
* default - specifies the default value
* min - specifies the min value (optional and does not apply to strings and bools)
* max - specifies the max value (optional and does not apply to strings and bools)

For using cfg file, make it executable like ```chmod a+x cfg/Tutorials.cfg```.  
we need to add the following lines to our CMakeLists.txt.
```python
#add dynamic reconfigure api
#find_package(catkin REQUIRED dynamic_reconfigure)
generate_dynamic_reconfigure_options(
  cfg/Tutorials.cfg
  #...
)

# make sure configure headers are built before any node using them
add_dependencies(example_node ${PROJECT_NAME}_gencfg)
```

:herb: */src/dynamic_reconfig/src/server.cpp*

```
#include<ros/ros.h>
#include<dynamic_reconfigure/server.h>
// The header file generated by dynamic_reconfigure from our config file Tutorials.cfg
#include <dynamic_tutorials/TutorialsConfig.h>
// called when the dynamic_reconfigure server is sent a new configuration
// The first param is new config
void callback(dynamic_tutorials::TutorialsConfig &config, uint32_t level) {
  ROS_INFO("Reconfigure Request: %d %f %s %s %d",
            config.int_param, config.double_param,
            config.str_param.c_str(),
            config.bool_param?"True":"False",
            config.size);
}
int main(int argc, char **argv) {
  ros::init(argc, argv, "dynamic_tutorials");
// As long as the server lives (in this case until the end of main())
// the node listens to reconfigure requests.
  dynamic_reconfigure::Server<dynamic_tutorials::TutorialsConfig> server;
// we define a variable (f) to represent our callback and then send it to the server
// Now when the server gets a reconfiguration request it will call our callback function
  dynamic_reconfigure::Server<dynamic_tutorials::TutorialsConfig>::CallbackType f;
  f = boost::bind(&callback, _1, _2);
  server.setCallback(f);
  ROS_INFO("Spinning node");
  ros::spin();
  return 0;
}
```
You can modify param in the process of code running:
```
# /cloud_to_map_node is node name
rosrun dynamic_reconfigure dynparam set /cloud_to_map_node
"{'int_param':1, 'double_param':5.0, 'double_param':'velodyne_top'}"
```



## Package tf

**tf** is a package that lets the user keep track of multiple coordinate frames over time. **tf** maintains the relationship between coordinate frames in a tree structure buffered in time, and lets the user transform points, vectors, etc between any two coordinate frames at any desired point in time.

The tf package provides an implementation of a **TransformBroadcaster** to help make the task of **publishing** transforms easier.

The tf package provides an implementation of a **TransformListener** to help make the task of **receiving** transforms easier.

The TransformListener object should be scoped to persist otherwise it's cache will be unable to fill and almost every query will fail. A common method is to make the TransformListener object a member variable of a class.

```
rosrun tf view_frames
rosrun rqt_tf_tree rqt_tf_tree # 查看frame ID之间的关系
```

The Listener module walks up the edges of the tree until a common parent node is found forming a spanning set. If no common parent is found the **look up** fails and will return an error. If the look up succeeds the Listener will compute the net transform of the edges from the **source frame to the target frame** along the spanning set.

The tf library enables sensor data, or any data with a Stamp, to be transmitted in its original frame across the network as well as to be stored in its original frame. When an algorithm wants to use data in the coordinate frame most relevant to the computation, it can query the tf library for the transform from the coordinate frame of the Stamped data to the desired coordinate frame.

For convenience, to get the latest data available, a request at time zero will return the latest common time across the queried values. If such a time does not exist the Listener will raise an exception, in the same way as if an unavailable time was queried outside of the cached history. For example, use Ros::Time::Now() to replace Ros::Time(0), there may be an error. Because the Time::Now() responding to the frame, which is not added to the list.

### Transform from map to base_link

**earth** --> **map** --> **odom** --> **base_link**

The transform from map to base_link is computed by a localization component. However, the localization component does not broadcast the transform from map to base_link. Instead, it first **receives** the transform from odom to base_link, and uses this information to **broadcast** the transform from map to odom.


### tf and rosbag

在其他节点打开之前，使用下面的语句，ros将设置*use_sim_time*为true，也就是运行一个Clock Serve。
ros::Time(0) means "the latest available" transform in the buffer.

```
rosbag paly --clock ...
```

对于一个回放的ros包，使用下面的tf代码

```
listener.waitForTransform("/map", "/base_link",ros::Time::now(), ros::Duration(3.0));// 可以使用ros::Time(0)
listener.lookupTransform("/map", "/base_link", ros::Time::now(), transform);
```

或者

```
listener.lookupTransform("/map", "/base_link", ros::Time(0), transform);
```

## Public param or private param

**略**

## Nodelet

### 1.Nodelet Intro

The main goal is to set up a nodelet library and a wrapping node executable that can be run standalone. Thanks to pluginlib, you don't even need to link your node executable against your nodelet library in CMakeLists.  
**nodelet usage:**
```
nodelet load pkg/Type manager - Launch a nodelet of type pkg/Type on manager manager
```
```
nodelet standalone pkg/Type   - Launch a nodelet of type pkg/Type in a standalone node
```
```
nodelet unload name manager   - Unload a nodelet a nodelet by name from manager
```
```
nodelet manager               - Launch a nodelet manager node
```
For example, the first usage can be shown as:
```
<node pkg="nodelet" type="nodelet" name="$(arg LiDAR_top_id)_rcswriter_nodelet"
args="load rcs_msg_wrapper/RCSWriterHDL64Nodelet $(arg LiDAR_top_manager)" >
</node>
```
In using nodelet, manager is responsable for managing thread pool. A nodelet will be run inside a NodeletManager. A nodelet manager is a c++ program which is setup to listen to ROS services and will be the executable in which the nodelet is dynamically loaded.

### 2.Threading Model

* A nodelet manager has a pool of threads which is shared across all nodelets run within the manager. This is set by the parameter "num_worker_threads".  
* There are two possible threading APIs to use in code running in nodelets. The default threading model has a single thread for all callbacks. There is a multithreaded API as well.  
* Using the methods *getNodeHandle()* and *getPrivateNodeHandle()* will guarantee that all callbacks arrive serially.(single thread); Using the methods *getMTNodeHandle()* and *getMTPrivateNodeHandle()* callbacks will be distributed over the thread pool of the manager.  
* The tutorial can be found in [github online](https://github.com/bryanibit/nodelet_tutorial)

## 3.An example of nodelet

```xml
<!--nodelet manager-->
		<node pkg="nodelet" type="nodelet" name="$(arg a_manager)"
    args="manager" output="screen"/>
<!--nodelet for HDL-->
		<node pkg="nodelet" type="nodelet" name="a_nodelet"   
			args="load a_pkg/EXE_Nodelet $(arg a_manager)" >
           <param name="nml" value="$(arg nml_path)"/>
			     <param name="buffer" value="P_**_INFO"/>
			     <param name="process" value="a_string"/>
			     <rosparam param="cal">[0,0,0,0,0,0,1.0]</rosparam>
			     <remap from="msg_topic_name" to="/pandar_points"/>
		</node>
```
From the above, the process of loading a nodelet:  
* run a nodelet manager called *a_manager*  
* run *EXE_Nodelet* in *a_manager*  
