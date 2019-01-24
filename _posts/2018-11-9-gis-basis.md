---
layout: post
title: GIS Reading
date: 2018-11-9
categories: blog
tags: [知识梳理]
description: GIS basic knowledge and QGIS tutorial
---

## EPSG Code/ID :monkey:

:penguin: EPSG (European Petroleum Survey Group) is founded in 1986 and then is renamed to OGP (Internation Association of Oil & Gas Producers). It is in charge of publish CRS (Coordinate Reference System) and coordinate transformations.  
目前已有的椭球体，投影坐标系等不同组合都对应着不同的ID号，这个号在EPSG中被称为EPSG code，它代表**特定的椭球体**(Spheroid)、**单位**、**地理坐标系或投影坐标系**等信息。  

:blowfish: **EPSG:4326**是一张世界地图，因为由美国主导的GPS系统就是在用它，它还有一个名气更大的别名叫作WGS84，WGS(World Geodetic System)是世界大地测量系统的意思，由于是1984年定义的，
所以叫WGS84。  
**EPSG:3857**也是一张世界地图，目前主要是各大互联网地图公司以它为基准，例如Google地图，Microsoft地图都在用它。  

## 投影坐标系 :bear:

:octopus: 1. **横轴墨卡托投影** (transverse Mercator projection)，又称**高斯-克吕格投影** (Gauss–Krüger projection)，简称高斯投影，是一种地图投影法，它由德国数学家高斯于1820年代创立，1912年经德国大地测量学家克吕格完善。它是一种**横轴等角切椭圆柱投影**：  
假想一个平面卷成圆筒套在球体外面，圆柱的中心轴线通过地球的中心且与赤道面夹角为零，球面上一根子午线与圆柱面相切。这样，该子午线在圆柱面上的投影为一直线，赤道面与圆柱面的交线是一条与该子午线投影垂直的直线。将圆柱面展开成平面，由这两条正交直线就构成**高斯-克吕格平面直角坐标系**。为减少投影变形，高斯-克吕格投影分为3°带和6°带投影。

:honeybee: 2. **通用横轴墨卡托投影** (Universal Transverse Mercator，简称UTM) 是一种国际标准化的地图投影法。The UTM system divides the Earth into 60 zones, each 6° of longitude in width. Zone 1 covers longitude 180° to 174° W; zone numbering increases eastward to zone 60, which covers longitude 174°E to 180°. The polar regions south of 80°S and north of 84°N are excluded. And the transverse Mercator is operated like the following:  

![Transverse Mercator](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/traverse_mercator.png)

## Install QGIS 2 and 3 :beetle:

```sh
sudo apt-get --purge remove # remove QGIS
sudo apt autoremove # remove qgis libs
```
In its [official website](https://qgis.org/en/site/forusers/alldownloads.html), find the correct source list. And add them to ```/etc/apt/source.list``` according to the format:
```
deb     *repository* *codename* main
deb-src *repository* *codename* main
```

In conclusion, use the following command for qgis3.0 on ubuntu 16.04：
```sh
sudo sh -c 'echo "deb https://qgis.org/ubuntugis xenial main" >> /etc/apt/sources.list'
sudo sh -c 'echo "deb-src https://qgis.org/ubuntugis xenial main" >> /etc/apt/sources.list'
sudo add-apt-repository ppa:ubuntugis/ubuntugis-unstable
```

And use the following for qgis 2.x on ubuntu 16.04:
```sh
sudo sh -c 'echo "deb https://qgis.org/ubuntu-ltr xenial main" >> /etc/apt/sources.list'
sudo sh -c 'echo "deb-src https://qgis.org/ubuntu-ltr xenial main" >> /etc/apt/sources.list'
```

If ```apt update``` echos ```The following signatures couldn't be verified because the public key is not available: NO_PUBKEY CAEB3DC3BDF7FB45```, then we need import pub key. The official document explains [the acceident](https://www.qgis.org/en/site/forusers/alldownloads.html#debian-ubuntu).

```sh
wget -O - http://qgis.org/downloads/qgis-2017.gpg.key | gpg --import
gpg --fingerprint CAEB3DC3BDF7FB45
```
If the key is successful to be imported, then the following should be displayed in terminal.
```
pub   2048R/BDF7FB45 2017-08-16 [expires: 2019-08-16]
      Key fingerprint = 61E0 A086 749E 463E DE50  2255 CAEB 3DC3 BDF7 FB45
uid                  QGIS Archive Automatic Signing Key (2017) <qgis-developer@lists.osgeo.org>
sub   2048R/E959BBCF 2017-08-16 [expires: 2019-08-16]
```
then use:
```sh
sudo apt-get update
```
```sh
gpg --export --armor CAEB3DC3BDF7FB45 | sudo apt-key add -
```
```sh
sudo apt-get update (again to see if there are still any errors)
sudo apt install qgis qgis-plugin-grass python-qgis
```

## 地球坐标系 :dolphin:

1. pass
2. pass

## QGIS Library of network analysis: description and examples :ram:

:ant: In QGIS, features mean point, line and polygon, which can be added :pencil: to separated layer.  
Algorithm application network-analysis library can be written in three steps:  
1. Get Count of geographic data  
2. Run Count Analysis  
3. Use the analysis results for their own purposes, for example, to visualize  

:crocodile: Writing and updating (to a limited extent) ESRI Shapefiles, and the associated a Converting from a vector layer to the graph([QgsGraph](https://qgis.org/api/classQgsGraph.html) objects) is done using the [*QgsGraphBuilder*](http://qgis.org/api/classQgsGraphBuilder.html) programming pattern with basic setting ordered by [*QgsLineVectorLayerDirector*](http://qgis.org/api/classQgsLineVectorLayerDirector.html).

```python
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

## Shapefile :tiger2:

What is Shapefile?  
The file format actually consists of three files.  
:kiss: XXX.shp - holds the actual vertices.  
:kiss: XXX.shx - hold index data pointing to the structures in the .shp file.  
:kiss: XXX.dbf - holds the attributes in xBase (dBase) format.  

## openstreetmap element :fish:

OSM consists of:  
1. nodes (defining points feature in space)  
2. ways (defining linear features and area boundaries)  
3. relations (which are sometimes used to explain how other elements work together)  

All of the above can have one or more associated tags.  

1. **Node**: Each node comprises at least an **id number** and a pair of **coordinates**.  
2. A **way** is an ordered list of between 2 and 2,000 nodes that define a polyline. **Ways** are used to represent linear features such as rivers and roads.   
3. **Relations** can have different meanings. The relation's meaning is defined by its tags. Typically, the relation will have a 'type' tag. Each element can optionally have a role within the relation. For example, a *turn restriction* would have members with "from" and "to" roles, describing the particular turn that is forbidden.
4. All types of data element (nodes, ways and relations) can have **tags**. Tags describe the meaning of the particular element to which they are attached. A tag consists of two free format text fields; **a 'key' and a 'value'**. Each of these are **Unicode strings** of up to 255 characters. For example, ```highway=residential``` defines the way as a road whose main function is to give access to people's homes. Every key is unique and there is no fixed dictionary of tags.  
**Not all elements** have tags. Nodes are often untagged if they are part of ways. Both ways and nodes may be untagged if they are members of a relation.

At least, how to navigate using OSM, please [check this link](https://www.mapbox.com/mapping/mapping-for-navigation/).

## OSRM Usage Method :boar:

:racehorse: OSRM (OpenStreetRoutingMachine) has high performance routing engine written in C++14 designed to run on OpenStreetMap data. It has 2 projects mainly: osrm-backend and osrm-frontend.  
:wolf: osrm-frontend is used to display tile map online and uses the local server to route a path on online map. It is written in javascript, therefore, not our today's key point. :koala: osrm-backend is written in C++ and all algorithms are summed up to:  
* Nearest - Snaps coordinates to the street network and returns the nearest matches  
* Route - Finds the fastest route between coordinates  
* Table - Computes the duration or distances of the fastest route between all pairs of supplied coordinates  
* Match - Snaps noisy GPS traces to the road network in the most plausible way  
* Trip - Solves the Traveling Salesman Problem using a greedy heuristic  
* Tile - Generates Mapbox Vector Tiles with internal routing metadata  

Let me introduce the one of all projects -- **Route**.

* Creating inputFile - data.osm  
We can download .osm dataset from Internet or We can create .osm via openstreetmap editor called JOSM(Java OpenSteetMap).  
When editing or creating .osm data, make sure add attributes to roads you create, for example, add ```highway=residential```. The shortcut keys for copy and paste attributes in JOSM are ```ctrl+c/ctrl+shift+v```. We can use ```ctrl+i``` to show osm node or line infomation.

* Install and Run OSRM

:camel: The installation tutorial could be found on [github project](https://github.com/Project-OSRM/osrm-backend).  Make sure you have installed OSRM-backend, and then keep track of the foollowing:

```
osrm-extract data.osm -p profiles/car.lua
osrm-partition data.osrm
osrm-customize data.osrm
```
The above commands would produce a serial of usable .osrm* files which are used for OSRM to route.  
For shortest path, we should modify original ```car.lua``` to
```
weight_name  = 'distance',
continue_straight_at_waypoint = false
```

:penguin: Use Docker method in order to avoid environment configuration.  

```
docker run -t -v $(pwd):/data osrm/osrm-backend:v5.20.0 osrm-extract -p /opt/car.lua /data/berlin-latest.osm.pbf
docker run -t -v $(pwd):/data osrm/osrm-backend:v5.20.0 osrm-partition /data/berlin-latest.osrm
docker run -t -v $(pwd):/data osrm/osrm-backend:v5.20.0 osrm-customize /data/berlin-latest.osrm
docker run -t -i -p 5000:5000 -v $(pwd):/data osrm/osrm-backend:v5.20.0 osrm-routed --algorithm mld /data/berlin-latest.osrm
```
Thanks to Docker. It is pretty beautiful. Let us route and check out the routing in firefox!  
```
http://127.0.0.1:5000/route/v1/driving/116.1139397,40.1705255;116.1029796,40.1641884?overview=full&annotations=nodes&geometries=geojson
```
You can see json result in firefox. Or you can also check in python commands:  
```
$ cd python-osrm # github project
$ python
>> import osrm
>> osrm.RequestConfig.host = "http://127.0.0.1:5000"
>> osrm.RequestConfig
>> osrm.nearest((14.4458221,35.8868013))
>> osrm.simple_route((14.4458221,35.8868013), (14.4296317, 35.9152936), overview="full", output="route", geometry="wkt")
```

* Produce routing from planning points  
Run executable file in osrm project, and produce position.txt saving routing points. The details are referred to [github project](https://github.com/bryanibit/RoutingWithOSRM)

* OSM2geojson/geojson2OSM  
Refer to the [project](https://github.com/tyrasd/osmtogeojson)  
Finish installation of the project, the following execution is shown as:  
```
geojsontoosm data.json > datatran.osm
osmtogeojson data.osm > datatran.json
```

From the foregoing commands, we can deduce no matter whether .osm or .json is just text file. QGIS(Quantum GIS) can load geojson as map tiles.

## Qt QML

Why is it here? I was enlightened by ArcGIS QML, so the content of qml is temporary here. Let's cut out the bullshit and start now!


