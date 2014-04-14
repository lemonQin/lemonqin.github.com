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

**较为通用的版本为0.3.0，使用的是old API，0.5.1使用的是new API，新老API差别很大，在参看使用[wiki](http://code.google.com/p/yaml-cpp/w/list)时，要看清楚，以免出现误用，耽误时间**

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


###程序中使用

[官网上有**old API**例子](https://code.google.com/p/yaml-cpp/wiki/HowToParseADocument)

由于安装的是0.5.1版本，用法参照[Tutorial](http://code.google.com/p/yaml-cpp/wiki/Tutorial)

monster.yaml

{% highlight html %}
- name: Ogre
  position: [0, 5, 0]
  powers:
    - name: Club
      damage: 10
    - name: Fist
      damage: 8
- name: Dragon
  position: [1, 0, 10]
  powers:
    - name: Fire Breath
      damage: 25
    - name: Claws
      damage: 15
- name: Wizard
  position: [5, -3, 0]
  powers:
    - name: Acid Rain
      damage: 50
    - name: Staff
      damage: 3
{% endhighlight %}


test_new_api.cpp
{% highlight cpp linenos%}
#include "yaml-cpp/yaml.h"
#include <iostream>
#include <fstream>
#include <string>
#include <vector>

// our data types
struct Vec3 {
   float x, y, z;
};

struct Power {
   std::string name;
   int damage;
};

struct Monster {
   std::string name;
   Vec3 position;
   std::vector <Power> powers;
};

namespace YAML {
   template<>
   struct convert<Vec3> {
      static Node encode(const Vec3& rhs) {
         Node node;
         node.push_back(rhs.x);
         node.push_back(rhs.y);
         node.push_back(rhs.z);
         return node;
      }

      static bool decode(const Node& node, Vec3& rhs) {
         if(!node.IsSequence() || node.size() != 3)
            return false;

         rhs.x = node[0].as<double>();
         rhs.y = node[1].as<double>();
         rhs.z = node[2].as<double>();
         return true;
      }
   };
   
   template<>
   struct convert<Power> {
      static Node encode (const Power& rhs) {
         Node node;
         node["name"] = rhs.name;
         node["damage"] = rhs.damage;
         return node;
      }
      
      static bool decode(const Node& node, Power& rhs) {
         rhs.name = node["name"].as<std::string>();
         rhs.damage = node["damage"].as<int>();
         return true;
      }
   };

   template<>
   struct convert<Monster> {
      static Node encode (const Monster& rhs){
         Node node;
         node["name"] = rhs.name;
         node["position"] = rhs.position;
         for(unsigned i = 0; i < rhs.powers.size(); i++) {
            Node power_node;
            power_node["name"] = rhs.powers[i].name;
            power_node["damage"] = rhs.powers[i].damage;
            node["powers"].push_back(power_node);
         }
         return node;
      }

      static bool decode(const Node& node, Monster& rhs) {
         rhs.name = node["name"].as<std::string>();
         rhs.position = node["position"].as<Vec3>();
         const YAML::Node& powers = node["powers"];
         for(unsigned i=0;i<powers.size();i++) {
            Power power;
            power = powers[i].as<Power>();
            rhs.powers.push_back(power);
         }
         return true;
      }
   };
}

int main() //测试程序  
{
   YAML::Node config = YAML::LoadFile("monsters.yaml");

   //添加新数据
   Monster mon;
   mon.name = "new_monster";
   mon.position.x = 1;
   mon.position.y = 2;
   mon.position.z = 3;
   Power power1;
   power1.name = "power1";
   power1.damage = 4;
   Power power2;
   power2.name = "power2";
   power2.damage = 5;
   mon.powers.push_back(power1);
   mon.powers.push_back(power2);
   config.push_back(mon);

   //读取数据
   std::vector<Monster> monsters;
   for(unsigned i=0;i<config.size();i++) {
      Monster monster;
      monster = config[i].as<Monster>();
      monsters.push_back(monster);
      std::cout << monster.name << "\n";
      std::cout << monster.position.x << " " << monster.position.y << " " << monster.position.z << "\n";
      for(unsigned j = 0; j < monster.powers.size(); j++){
         std::cout << monster.powers[j].name << " " << monster.powers[j].damage << "\n";
      }
      std::cout << "------------------------" << "\n";
   }
   return 0;
}
{% endhighlight %}

编译
{% highlight html %}
sudo g++ -o test_new_api -lyaml-cpp test_new_api.cpp 
{% endhighlight %}
运行
{% highlight html %}
./test_new_api.cpp
{% endhighlight %}
结果
{% highlight html %}
Ogre
0 5 0
Club 10
Fist 8
------------------------
Dragon
1 0 10
Fire Breath 25
Claws 15
------------------------
Wizard
5 -3 0
Acid Rain 50
Staff 3
------------------------
new_monster
1 2 3
power1 4
power2 5
------------------------
{% endhighlight %}
