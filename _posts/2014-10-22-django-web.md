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

数据库一般使用的是mysql，在本地安装mysql的服务器和客户端后，需要安装myql的python包python-mysql，接着需要在settings.py中对数据库进行配置

{% highlight %}
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
{% endhighlight %}

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
  
</body>  
</html>  
{% endhighlight %}


首先，在urls.py中定义url规则，例如网站默认首页显示index.html

{% highlight %}
urlpatterns = patterns('',
    # Examples:
    # url(r'^$', 'DeviceWeb.views.home', name='home'),
    # url(r'^blog/', include('blog.urls')),

    url(r'^$', index),
)
{% endhighlight %}

意思是，输入网址会调用views.py中的index函数。
接着在views.py中定义index函数：

{% highlight %}
def index(request):
	name='Jack'
	items=['aaa', 'bbb', 'ccc']
    return render_to_response('index.html', {'person_name': name, 'item':items, })
{% endhighlight %}

最重要的依据是return，返回index.html页面，并且将后面的变量传递到index.html中，变量是json结构，可以传递多个。这样运行网站，输入http://localhost:8000就可以看到index.html并且显示了相应的变量信息。


### Django后台管理
1. 配置settings.py
{% highlight %}
INSTALLED_APPS = (
    'django.contrib.admin',  ###添加这句
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'DevWeb', #自己的项目
)
{% endhighlight %}
2. 配置urls.py
{% highlight %}
urlpatterns = patterns('',
    # Examples:
    # url(r'^$', 'DeviceWeb.views.home', name='home'),
    # url(r'^blog/', include('blog.urls')),

    url(r'^admin/', include(admin.site.urls)),
    url(r'^$', index),
)
{% endhighlight %}

3. 配置admin.py
对在models.py中创建的模型，在admin.py下进行注册，例如：
models.py
{% highlight %}
class CompanyNews(models.Model):
    title = models.CharField(max_length=30)
    public_time = models.DateTimeField('create date')
    content = models.TextField(default='')

    def __unicode__(self):
        return self.title

class CompanyNewsAdmin(admin.ModelAdmin):
	pass
{% endhighlight %}
admin.py
{% highlight %}
from django.contrib import admin
from DevWeb.models import CompanyNews, CompanyNewsAdmin

admin.site.register(CompanyNews, CompanyNewsAdmin)

{% endhighlight %}

这样运行项目，打开http://localhost:8000/admin就可以看到管理界面了，当然要输入用户名和密码

### Django中图片上传操作

后台的图片上传操作django默认是上传到MEDIA_ROOT目录下面的，因此要在settings.py中配置. 由于static下的东西一般是配置了可以在前台显示的，所以一般上传路径也在static下
settings.py
{% highlight %}
MEDIA_ROOT = 'static/media/'
MEDIA_URL = '/static/media/'
{% endhighlight %}

model.py中需要进行如下设置：
model.py
{% highlight %}
class StaticImage(models.Model):
    image = models.ImageField(default='', blank=True, upload_to='.')

    def __unicode__(self):
        return "/static/media/" + str(self.image)

    class Meta:    
        verbose_name = '图片文件'  ##用来定义在后台显示该类的名称
        verbose_name_plural = '图片文件'
{% endhighlight %}

则在后台，StaticImage的image域可以用来上传图片
<figure>
<img src="/images/django-static-image1.png" alt="">
</figure>



