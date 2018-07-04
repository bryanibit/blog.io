---
layout: post
title: Grid Map developed by ETHz
date: 2018-7-3
categories: blog
tags: [技术总结]
description: grid_map description
---

## Grid map can contain multiple map layers

The following map is constructed with one layer called "elevation".
```
using namespace grid_map;
GridMap map({"elevation"});
for (GridMapIterator it(map); !it.isPastEnd(); ++it){
  Position position;
  map.getPosition(*it, position);
  map.at("elevation", *it) = -0.04 + 0.2 * std::sin(3.0 * time.toSec() + 5.0 * position.y()) * position.x();
  }
```




