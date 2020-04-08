---
layout: post
title: Self-Driving Specialization
data: 2019-5-5
categories: blog
tags: [技术总结]
description: a class on couresa by University of Toronto
---

## Glossary of terms

ACC: Adaptive Cruise Control  
Ego: self  
FMEA: Failure Mode and Effects Analysis  
LIDAR: Lidar Detection And Ranging  
MPC: Model Predictive Control  
LQR: Linear Quadratic Regulation  
RADAR: Radio Detection And Ranging 
SONAR: Sound Navigation And Ranging  

## Path Planning (upenn)

```python
## Dijkstra's algorithm

for node in graph:
    node.distance = Inf
L = []
startnode.distance = 0
L.append(startnode)
while L:

    # node is a node with smallest distance in L   

    current = node
    # at this moment, priority queue is the best choice   

    L.remove(current)
    
    for node in current.adjancent:
        if node.distance > current.distance + node_to_current_distance:
            node.distance = current.distance + node_to_current_distance
            node.parent = current
            # if node is not in L  
            L.append(node)
```

a broad class of best first search algorithm compared to uniformed search algorithm such as grassfire and Dijsktra's algorithms is called the following:

```python
# Conventional(traditional) A* algorithm

for node in graph:
    node.f = Inf
    node.g = Inf
L = []
# g denotes distance from current node to start node   

# f denotes the sum of g and the left distance of current node to goal node   

start.g = 0  
start.f = H(start)  
L.append(start)  
# On each iteration, the most likely to be on the shortest path from start(g) to destination(f)   

while L:
    # the node with smallest f value  
     
    current = node
    L.remove(current)
    if current == goal:
        report success
    for node in current.adjacent:
        if node.g > current.g + node_to_current_distance:
            node.g = current.g + node_to_current_distance
            node.f = node.g + H(node)
            node.parent = current
            # if node is not in L
            L.append(node)
```

## Hybrid A*

1. So from each node, you drive forward with a distance of d with three different angles, and if the car can **reverse**, you need to reverse with the same distance and three angles. So from each node, you end up with **6 child nodes**. For each child, you have to calculate the cost (g-cost) and heuristic (h-cost). The self-driving car Junior used a more complicated heuristic, but you can use the traditional Euclidean distance as the heuristic before you begin calculate something more advanced.  
2. You also need a few **extra costs**. You should also add an extra cost if the node is **close to an obstacle**, or if you are **reversing**.  
3. Make the Hybrid A star better. These methods include: Reeds-Shepp paths (faster), Flowfield (cost function), or Voronoi field (cost function).  


## Carla

### configuration for boot

```sh
./CarlaUE4.sh -quality-level=Epic
./CarlaUE4.sh -quality-level=Low
```
In Linux, you can force the simulator to run off-screen by setting the
environment variable DISPLAY to empty:`DISPLAY= ./CarlaUE4.sh -opengl`. This launches the simulator without simulator window, of course you can still
connect to it normally and run the example scripts.  

### no render no data

It is possible to completely disable rendering in the simulator by enabling
no-rendering mode in the world settings. This way is possible to simulate
traffic and road behaviours at very high frequencies without the rendering
overhead. Note that in this mode, cameras and other GPU-based sensors return
empty data.
```python
settings = world.get_settings()
settings.no_rendering_mode = True
world.apply_settings(settings)
```

### use config.py script

Some of the command-line options are not available in Linux due to the “Shipping” build. Therefore, the use of `config.py` script is needed to configure the simulation.  
Some configuration examples:
```sh
./config.py --no-rendering      # Disable rendering
./config.py --map Town05        # Change map
./config.py --weather ClearNoon # Change weather
```
To check all the available configurations, run the following command:`./config.py --help`.  

## Frenet Frame

Frenet坐标系又称Frenet–Serret公式,Frenet–Serret公式用于描述粒子在三维欧氏空间$R^3$内沿一条连续可微曲线的运动学特征。  
$\vec{T}$称为切向量（tangent），表示沿曲线运动方向的单位向量；$\vec{N}$称为法向量（normal），表示当前曲线运动平面内垂直于$\vec{T}$的单位向量；$\vec{B}$称为副法向量（binormal），表示同时垂直于$\vec{T}$和$\vec{N}$的单位向量。  
令$\vec{r}(t)$为欧氏空间内随*t*改变的一条非退化曲线。所谓非退化曲线就是一条不会退化为直线的曲线，亦即曲率不为0的曲线。令*s(t)*是t时刻时曲线的累计弧长，其定义如下：  
$$s(t) = \int_{0}^{t}||r^{'}(h)||dh$$  
假定$r^{'} \neq 0$，则意味着*s(t)*是严格单调递增函数。因此可将t表示为s的函数，从而有：$\vec{r}(s)=\vec{r}(t(s))$，这样我们就把曲线表示为弧长s的函数。  
定义切向量$\vec{T}$、法向量$\vec{N}$和副法向量$\vec{B}$如下：  
![frenet](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/frenet.JPG)  
基于上述定义可以得到：  
![frenet1](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/frenet1.JPG)  
其中$\kappa$、$\tau$分别表示曲线$\vec{r}(t)$的曲率（ curvature）和挠率（ torsion）。直观地讲，曲率是曲线不能形成一条直线的度量值，曲率越趋于0，则曲线越趋近于直线；挠率是曲线不能形成在同一平面内运动曲线的度量值，挠率越趋于0，则曲线越趋近于在同一平面内运动。  
