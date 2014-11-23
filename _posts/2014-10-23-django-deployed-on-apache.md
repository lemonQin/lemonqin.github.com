---
layout: post
title: Apache上部署Django Web
description: 将Django部署在Apache上的步骤和配置。
tags: [Django, Apache]
image:
  background: triangular.png
comments: true
share: true
---

## 安装配置Apache

## 安装wsgi_mod模块

## 开放相应端口

{% highlight %}
{% raw %}
# Firewall configuration written by system-config-firewall
# Manual customization of this file is not recommended.
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8000 -j ACCEPT  ##注意位置
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
COMMIT
{% endraw %}
{% endhighlight %}


## 为Django网站配置wsgi

