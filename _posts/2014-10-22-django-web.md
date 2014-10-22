---
layout: post
title: Django静态文件、文件上传与apache部署
description: Django中静态文件的配置，以及在需要上传图片文件时的操作，最后是将Django部署在Apache上的步骤和配置。
tags: [Django, Apache]
image:
  background: triangular.png
comments: true
share: true
---


### Django中静态文件配置

django构建网站的基本操作，这里就不说了。这里只说在我使用Django制作网站的过程中遇到过的一些小事情。

为了使Django能够识别CSS，JS和图片等静态文件，需要在项目根目录下建立static文件夹，并且在settings.py文件中设置如下：


{% highlight python linenos%}
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(os.path.dirname(__file__), 'static').replace('\\', '/')
STATICFILES_DIRS = (
    os.path.join(os.path.dirname(__file__), '../static').replace('\\', '/'),
)

{% endhighlight %}

并且在utls.py文件后面添加
{% hightlight python linenos %}
urlpatterns += static(STATIC_URL, document_root = STATIC_ROOT)
{% endhighlight %}
经过这样设置以后，就可以在HTML文件中引用了

{% highlight python linenos%}
<link rel = "stylesheet" href="/static/css/bootstrap.min.css">
<script type="text/javascript" src="/static/js/jquery-2.1.1.js"></script>
{% endhightlight %}

### 数据库相关操作

#### Django链接Mysql数据库

#### 数据库相关操作


### Django模板使用
1. 在settings.py中配置模板路径

2. 模板文件编写

3. url配置及参数传递

### Django中图片上传操作


### Django在Apache部署

#### 安装配置Apache

#### 安装wsgi_mod模块

#### 开放相应端口

#### 为Django网站配置wsgi