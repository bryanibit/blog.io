---
layout: post
title: Something necessary to know about GIS
date: 2018-11-9
categories: blog
tags: [知识梳理]
description: GIS basic knowledge and QGIS tutorial
---

## EPSG Code/ID :monkey:

:penguin: EPSG (European Petroleum Survey Group) is founded in 1986 and then is renamed to OGP (Internation Association of Oil & Gas Producers). It is in charge of publish CRS (Coordinate Reference System) and coordinate transformations.  
目前已有的椭球体，投影坐标系等不同组合都对应着不同的ID号，这个号在EPSG中被称为EPSG code，它代表**特定的椭球体**(Spheroid)、**单位**、**地理坐标系或投影坐标系**等信息。  

:blowfish: EPSG:4326是一张世界地图，因为由美国主导的GPS系统就是在用它，它还有一个名气更大的别名叫作WGS84，WGS(World Geodetic System)是世界大地测量系统的意思，由于是1984年定义的，所以叫WGS84。  
EPSG:3857也是一张世界地图，目前主要是各大互联网地图公司以它为基准，例如Google地图，Microsoft地图都在用它。  

## 投影坐标系 :bear:

:octopus: 1. **横轴墨卡托投影** (transverse Mercator projection)，又称**高斯-克吕格投影** (Gauss–Krüger projection)，简称高斯投影，是一种地图投影法，它由德国数学家高斯于1820年代创立，1912年经德国大地测量学家克吕格完善。它是一种**横轴等角切椭圆柱投影**：  
假想一个平面卷成圆筒套在球体外面，圆柱的中心轴线通过地球的中心且与赤道面夹角为零，球面上一根子午线与圆柱面相切。这样，该子午线在圆柱面上的投影为一直线，赤道面与圆柱面的交线是一条与该子午线投影垂直的直线。将圆柱面展开成平面，由这两条正交直线就构成**高斯-克吕格平面直角坐标系**。为减少投影变形，高斯-克吕格投影分为3°带和6°带投影。

:honeybee: 2. **通用横轴墨卡托投影** (Universal Transverse Mercator，简称UTM) 是一种国际标准化的地图投影法。The UTM system divides the Earth into 60 zones, each 6° of longitude in width. Zone 1 covers longitude 180° to 174° W; zone numbering increases eastward to zone 60, which covers longitude 174°E to 180°. The polar regions south of 80°S and north of 84°N are excluded. And the transverse Mercator is operated like the following:  

![Transverse Mercator](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/traverse_mercator.png)

## 地球坐标系 :dolphin:

1. pass
2. pass

## QGIS Library of network analysis: description and examples

In QGIS, features mean point, line and polygon, which can be added :pencil: to separated layer.  

Algorithm application network-analysis library can be written in three steps:  
* Get Count of geographic data  
* Run Count Analysis  
* Use the analysis results for their own purposes, for example, to visualize  

Converting from a vector layer to the graph([QgsGraph](https://qgis.org/api/classQgsGraph.html) objects) is done using the [*QgsGraphBuilder*](http://qgis.org/api/classQgsGraphBuilder.html) programming pattern with basic setting ordered by [*QgsLineVectorLayerDirector*](http://qgis.org/api/classQgsLineVectorLayerDirector.html).

```
-------省略初始化-----------
pStart = QgsPoint(116.104052, 40.166027)
pStop = QgsPoint(116.1052922, 40.1661006)
pStop_final = QgsPoint(116.106188, 40.166182)
tiedPoints = director.makeGraph(builder, [pStart, pStop, pStop_final])
# We build a graph, and "attached" the foregoing points to it in terms.
# In tiedPoints recorded coordinates of the "bound" points.
# We got the final result graph attached with "bound" points.
graph = builder.graph()
tStart = tiedPoints[0]
tStop = tiedPoints[1]
tStop_final = tiedPoints[2]
idStart = graph.findVertex(tStart)
idStop = graph.findVertex(tStop)
idStop_final = graph.findVertex(tStop_final)
(tree, cost) = QgsGraphAnalyzer.dijkstra(graph, idStart, 0)
```
