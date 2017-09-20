---
layout: post
title: OpenCV 3.0 + Install and Usage
date: 2017-7-5
categories: blog
tags: [技术总结]
description: Install OpenCV in ubuntu
---

## OpenCV3.×编译
以下方法是编译opencv3.2+contrib部分 使用同样版本的opencv and
opencv_contrib，其中（）中的内容一般没有用,可以去掉, () use CMake's BUILD_opencv_* options.

``` 
cmake -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_INSTALL_PREFIX=/usr/local -DWITH_TBB=ON -DBUILD_NEW_PYTHON_SUPPORT=ON -DWITH_V4L=ON -DBUILD_EXAMPLES=ON -DWITH_CUDA=OFF -DWITH_QT=ON -DWITH_GTK=ON -DOPENCV_EXTRA_MODULES_PATH=<opencv_contrib>/modules (-DBUILD_opencv_legacy=OFF) <opencv_source_directory>
```

## OpenCV install

```
cd opencv×.×
mkdir release
cd release
cmake ..
sudo make -j4
sudo make install
sudo /bin/bash -c 'echo "/usr/local/lib">/etc/ld.so.conf.d/opencv.conf'
sudo ldconfig
```

验证OpenCV安装成功：

```
pkg-config opencv --cflags --libs #查看opencv安装情况 参考pkg-config --help
pkg-confg --modversion opencv
```

## Python OpenCV

### Feature detect and match
**resize**
```
img = cv2.resize(img,(0,0), fx = 0.3, fy = 0.3)
```

```
img = cv2.resize(img,dsize=(0,0), fx = 0.3, fy = 0.3)
```

**color to gray**
```
gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
```
**new zero space**
```
image_matche = cv2.cvtColor(np.zeros((image1.shape[0], image1.shape[1] * 2), dtype=np.uint8), cv2.COLOR_GRAY2BGR)
```
**SIFT descriptor**

        if cv2.__version__.split('.')[0] == '2':
            detector = cv2.FeatureDetector_create('SIFT')
            descriptor = cv2.DescriptorExtractor_create('SIFT')
            detector.setDouble("contrastThreshold", 0.1)
            detector.setDouble('edgeThreshold', 10)
            points = detector.detect(gray)
            points, desc = descriptor.compute(gray, points)
        else:
            sift = cv2.xfeatures2d.SIFT_create()
            points, desc = sift.detectAndCompute(gray, None)

**two feature descriptor above match**
```
def match_low(feature1,feature2):
    FLANN_INDEX_KDTREE = 1
    flann_params = dict(algorithm=FLANN_INDEX_KDTREE, tree=4)
    flann = cv2.flann_Index(feature1, flann_params)
    idx, dist = flann.knnSearch(feature2, 2, params={})
    good1 = dist[:, 0] < dist[:, 1] * 0.8 * 0.8
    matches_good1 = zip(idx[good1, 0], good1.nonzero()[0])
    # (a, b) = np.array(matches_good, dtype=int)
    flann = cv2.flann_Index(feature2, flann_params)
    idx, dist = flann.knnSearch(feature1, 2, params={})
    good2 = dist[:, 0] < dist[:, 1] * 0.8 * 0.8
    matches_good2 = zip(idx[good2, 0], good2.nonzero()[0])
    matches1 = [(a, b) for a, b in np.array(matches_good1, dtype=int)]
    matches2 = [(b, a) for a, b in np.array(matches_good2, dtype=int)]
    matches = set(matches1).intersection(matches2)
    return np.array(list(matches), dtype=int)
```
> note that:
**cv2.flann_Index()**

        Summary:
        This method does a fast local approximate nearest neighbors (FLANN) calculation between two sets of feature vectors. The result are two numpy arrays the first one is a list of indexes of the matches and the second one is the match distance value. For the match indices or idx, the index values correspond to the values of td, and the value in the array is the index in td. I.
        I.e. j = idx[i] is where td[i] matches sd[j].
        The second numpy array (dist), at the index i is the match distance between td[i] and sd[j].
        Lower distances mean better matches.
  
        Parameters:
        sd - A numpy array of feature vectors of any size.         (t is template)
        td - A numpy array of feature vectors of any size, this vector is used for indexing
             and the result arrays will have a length matching this vector. (s is source)
  
        Returns:
        Two numpy arrays, the first one, idx, is the idx of the matches of the vector td with sd.
        The second one, dist, is the distance value for the closest match.

        try:
            import cv2
        except:
            logger.warning("Can't run FLANN Matches without OpenCV >= 2.3.0")
            return
        FLANN_INDEX_KDTREE = 1  # bug: flann enums are missing
        flann_params = dict(algorithm = FLANN_INDEX_KDTREE, trees = 4)
        flann = cv2.flann_Index(sd, flann_params)
        idx, dist = flann.knnSearch(td, 1, params = {}) # bug: need to provide empty dict
        del flann
        return idx,dist


## CPP OpenCV
