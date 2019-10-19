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

### Dijkstra's algorithm  

**pseudo code**  
```python
for node in graph:
    node.distance = Inf
L = []
L.append(startnode)
while L:
    current = node #node is a node with **smallest distance** in L
    # at this moment, priority queue is the best choice.
    L.remove(current)
    for node in current.adjancent:
        if node.distance > current.distance + node_to_current_distance:
            node.distance = current.distance + node_to_current_distance
            node.parent = current
            L.append(node) # if node is not in L
```

### A* algorithm  

a broad class of best first search algorithm compared to uniformed search algorithm such as grassfire and Dijsktra's algorithms.  
**pseudo code**  
```python
for node in graph:
    node.f = Inf
    node.g = Inf
L = []
# g denotes distance from current node to start node
# f denotes the sum of g and the left distance of current node to goal node
start.g = 0
start.f = H(start)
L.append(start)
# on each iteration, the most likely to be on the shortest path from start(g) to destination(f)
while L:
    current = node # the node with **smallest f value**
    L.remove(current)
    for node in current.adjacent:
        if node.g > current.g + node_to_current_distance:
            node.g = current.g + node_to_current_distance
            node.f = node.g + H(node)
            node.parent = current
            L.append(node) # if node is not in L
```

