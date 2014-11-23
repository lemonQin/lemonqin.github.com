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
{% highlight python linenos %}
urlpatterns += static(STATIC_URL, document_root = STATIC_ROOT)
{% endhighlight %}
经过这样设置以后，就可以在HTML文件中引用了

{% highlight python linenos%}
<link rel = "stylesheet" href="/static/css/bootstrap.min.css">
<script type="text/javascript" src="/static/js/jquery-2.1.1.js"></script>
{% endhighlight %}

### 数据库相关操作

数据库一般使用的是mysql，在本地安装mysql的服务器和客户端后，需要安装myql的python包python-mysql，接着需要在setting.xml中对数据库进行配置

{% hightlight %}
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'djangodb',
        'USER': 'root',
        'PASSWORD': 'root',
        'HOST': '127.0.0.1',
        'PORT': '3306',
    }
}
{% endhightlight %}

### Django模板使用
1. 在settings.py中配置模板路径
{% highlight %}
TEMPLATE_DEBUG = True
TEMPLATE_DIRS = (os.path.join(os.path.dirname(__file__), 'templates').replace('\\', '/'),)
{% endhighlight %}

就可以在项目主目录的templates下创建模版了

2. 模板文件编写

模板本质上是html文件，就是要要成先的网页页面，因此html相应的标签都要有，不同的是模板通过添加变量和模版标签来充实html的内容

* 变量用{{}}包围，html中会显示该变量的值，传递 
* 块标签用{%%}包围，html会显示该块标签中定义的内容

一个简单的例子说明一下，html页面是怎么呈现的：
在template目录下定义index.html页面
{% highlight html %}
<html>  
<head><title>模板实例</title></head>  
<body>  
<p>Dear {{ person_name }},</p>  
<p>Here are the items you've ordered:</p>  

<ul>  
{% for item in item_list %}  
<li>{{ item }}</li>  
{% endfor %}  
</ul>  

{% if ordered_warranty %}  
<p>Your warranty information will be included in the packaging.</p>  
{% endif %}  
 
</body>  
</html>  
{% endhighlight %}


3. url配置及参数传递

### Django中图片上传操作


### Django后台管理

