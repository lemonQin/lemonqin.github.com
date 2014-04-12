---
layout: post
title: yaml-cpp安装及使用
description: yaml-cpp安装及在C++中的简单使用
tags: [yaml, yaml-cpp, C++]
image:
  background: triangular.png
comments: true
share: true
---

###yaml-cpp在linux下的安装

[下载yaml-cpp的最新版本](http://code.google.com/p/yaml-cpp/downloads/list), 目前的最新版本是yaml-cpp-0.5.1

yaml-cpp需要CMake提供跨平台编译，因此如果没有安装CMake，需要首先安装CMake

{% highlight html %}
wget http://www.cmake.org/files/v2.8/cmake-2.8.4.tar.gz
tar zxvf cmake-2.8.4.tar.gz
./configure
make
sudo make install
{% endhighlight %}

安装yaml-cpp
{% highlight html %}
tar zxvf yaml-cpp-0.5.1.tar.gz
mkdir build
cd build

#yaml-cpp默认编译产生的是静态库，使用-DBUILD_SHARED_LIBS=ON来产生动态库
cmake -DBUILD_SHARED_LIBS=ON ..
make
sudo make install
#使库生效
sudo ldconfig
{% endhighlight %}

cmake -DBUILD_SHARED_LIBS=ON ..运行效果
{% highlight html %}
-- The C compiler identification is GNU
-- The CXX compiler identification is GNU
-- Check for working C compiler: /usr/bin/gcc
-- Check for working C compiler: /usr/bin/gcc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Boost version: 1.33.1
-- Found the following Boost libraries:
-- Performing Test FLAG_WEXTRA
-- Performing Test FLAG_WEXTRA - Success
-- Configuring done
-- Generating done
-- Build files have been written to: XXX/yaml-cpp-0.5.1/build
{% endhighlight %}

make运行效果
{% highlight html %}
Scanning dependencies of target yaml-cpp
[  2%] Building CXX object CMakeFiles/yaml-cpp.dir/src/node_data.cpp.o
[  5%] Building CXX object CMakeFiles/yaml-cpp.dir/src/emitterstate.cpp.o
[  7%] Building CXX object CMakeFiles/yaml-cpp.dir/src/scanscalar.cpp.o
[ 10%] Building CXX object CMakeFiles/yaml-cpp.dir/src/scantag.cpp.o
[ 12%] Building CXX object CMakeFiles/yaml-cpp.dir/src/parser.cpp.o
[ 15%] Building CXX object CMakeFiles/yaml-cpp.dir/src/emitter.cpp.o
.....
.....
[ 97%] Built target read
Scanning dependencies of target sandbox
[100%] Building CXX object util/CMakeFiles/sandbox.dir/sandbox.cpp.o
Linking CXX executable sandbox
[100%] Built target sandbox
{% endhighlight %}
make之后会看到生成的libyaml-cpp.so.0.5.1
