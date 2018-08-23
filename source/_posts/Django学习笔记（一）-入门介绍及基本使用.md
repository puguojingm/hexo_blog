---
title: Django学习笔记（一）-入门介绍及基本使用
date: 2018-08-23 13:51:18
tags: [Python,Djiango]
categories: python
copyright:
top: 
---
&#8195;&#8195;


>《Python编程：从入门到实践》笔记。
> 记一次完成一个“学习笔记”的小网站。

### 1. 前言

-------

在本篇中，我们将：
* 用Django来开发一个名为“学习笔记”(Learning Log)的项目；
* 为这个项目制定规范，然后为应用程序使用的数据定义模型；
* 使用Django的管理系统来输入一些初试数据，再学习编写视图和模板，让Django能够为我们的网站创建网页。

&#8195;&#8195;不过在开始之前，请先新建一个虚拟环境并安装Django。如果没有虚拟环境，通过pip安装的所有库都会保存到python的site-packages目录下。开发多个项目时，都会用同一个python，而某些项目并不需要其中的所有第三方库，但如果将这些不需要库的删除，又会影响到其他项目。而且，如果A项目需要Django2.0.4，B项目需要Django2.0.0，这该怎么办？此时就需要虚拟环境。它其实就相当于一个新的文件夹，将新项目与其他项目隔离，本项目的库不与其他项目的库相关联，类似于操作系统的多用户概念。

如果使用的是Python 3，可以使用命令：

`python -m venv ll_env`

如果该命令不成功，可能是没有安装virtualenv模块：
`pip install virtualenv`

然后创建并激活虚拟环境：

```
#创建虚拟环境，ll_env代表着虚拟环境名称
virtualenv ll_env

# linux:
source ll_env/bin/activate
# windows:
ll_env\Scripts\activate

# 停用：
deactivate
```
管理虚拟环境的库还有很多，有兴趣的话可以到网上搜一搜。

如果你使用的是新版的PyCharm，那么它在新建项目的时候默认就会创建新的虚拟环境。

激活虚拟环境后就可以安装Django了：

`pip install django`

### 2. 建立项目
-------
#### 2.1 在Django中创建项目

-------
##### 2.1.1 生成项目
在虚拟环境中执行如下命令：

```
# 主要最后有个实心句号！
# 这个句点让新项目使用合适的目录结构，这样开发完成后可以轻松地将应用程序部署到服务器
django-admin startproject learning_log .
```
执行上述命令后，将多出一个<font color=#DC143C>manage.py</font>文件和一个<font color=#DC143C>learning_log</font>文件夹，当然还有本身的一个<font color=#DC143C>ll_env</font>文件夹。

而在<font color=#DC143C>learning_log</font>文件夹中，又有四个文件：<font color=#DC143C>__init__.py，settings.py，urls.py，wsgi.py</font>。

<font color=#DC143C>manage.py</font>是一个简单的程序，它接收命令并将其交给Django的相关部分去运行；
<font color=#DC143C>settings.py</font>指定Django如何与你的系统交互以及如何管理项目，其实就是配置文件；
<font color=#DC143C>urls.py</font>告诉Django应创建哪些网页来响应浏览器请求；
<font color=#DC143C>wsgi.py</font>是web server gateway interface(Web服务器网关接口)的缩写，帮助Django提供它创建的文件。
至于<font color=#DC143C>__init__.py</font>，它是个空文件，Python的每个模块下必须要有这个文件。

##### 2.1.2 创建数据库
Django将大部分与项目相关的信息都存储在数据库中，所有还需要创建一个供Django使用的数据库。依然是在虚拟环境下执行如下命令：

`python manage.py migrate`

&#8195;&#8195;在PyCharm中的话，可以通过点击工具栏Tools中的Run manage.py Task（Ctrl+Alt+R），在弹出的命令行中直接输入原命令中manage.py后面的部分，后面的命令也可以这样执行（[appname]是自动提示）。

![](http://pc59bkg3l.bkt.clouddn.comoz5hq3kw8.bkt.clouddn.com/196496455-5b0e52a6b4d9b.png)

&#8195;&#8195;"migrate"这个单词其实是迁移的意思，并不是“创建(create)”。之所以使用这个词，是因为一般将修改数据库的过程称为迁移数据库（笔者数据库学得渣，这段解释直接从书里照搬的，希望哪位大神在留言区解释一波）。如果是刚创建的项目，并且第一次执行，将会得到如下输出：


```
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  -- snip --
  Applying sessions.0001_initial... OK
```

从第2行结果可以看出，Django将创建和修改数据库看做是对数据库的迁移，Apply all migrations确保数据库结构与当前代码匹配（比如你修改了类的结构，添加了属性，这就相当于修改了数据表）。

执行命令后，项目的根目录下会多出一个名为<font color=#DC143C>db.sqlite3</font>的数据库文件。SQLite是一种使用单个文件的轻量级数据库，常用于开发简单应用程序，它让你不用太关注数据库管理的问题。

##### 2.1.3 运行项目

依然在项目的虚拟环境下输入如下命令：

```
python manage.py runserver
```
得到如下输出：

```
Performing system checks...

System check identified no issues (0 silenced).
April 21, 2018 - 20:46:48
Django version 2.0.4, using settings 'learning_log.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CTRL-BREAK.
```
现在在浏览器中地址栏输入localhost:8000 (或者127.0.0.1:8000)，将得到如下页面：
![](http://pc59bkg3l.bkt.clouddn.com/oz5hq3kw8.bkt.clouddn.com/1877928535-5b0e52af58542.png)

这是最新版的Django的默认启动界面。


##### 3.1 映射URL（此处与原文略有不同）

-------
Django创建网页的过程通常分三个阶段：定义URL、编写视图和编写模板。

URL模式描述了URL是如何设计的，让Django知道如何将浏览器请求与网站URL匹配，以确定返回哪个网页。每个URL都被映射到特定的视图——视图函数获取并处理网页所需的数据。视图函数通常调用一个模板，后者生成浏览器能够理解的网页。

目前，基础URL(http://localhost:8000/ )返回默认的Django页面，现在修改这个映射，将其映射到我们自己编写的主页。

打开<font color=#DC143C>learning_log</font>文件夹中的<font color=#DC143C>urls.py</font>，将看到如下内容：

```
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', admin.site.urls),
]

# 书中的内容和实际的内容有些出入，以下是书中内容：
# 新版Django简化了URL路由写法
# from django.conf.urls import include, url
# from django.contrib import admin
# 
# from django.conf.urls import include, url
# from django.contrib import admin
#
# urlpatterns = [
#    url(r'^admin/', include(admin.site.urls)),
# ]
```

变量urlpatterns包含项目中的APP的URL，admin.site.urls模块定义了可在管理网站中请求的所有URL。现在添加代码：


```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path("", include("learning_logs.urls")),
    # 书中代码：
    # url(r"", include("learning_logs.urls", namespace="learning_logs")),
]
```
注意：书中在此处的include()函数中传入了关键字参数namespace="learning_logs"，但在新版中，命名空间(namespace)是在APP的urls.py中设置的：在urlpatterns变量前新建一个值为"learning_logs"的app_name变量。

<font color=#DC143C>此处与我配置不同，我已经在urlpatterns变量前新建一个值为"learning_logs"的app_name变量。但是仍然会报错：无法找到namespace,于是把urlpatterns修改为：</font>


```

urlpatterns = [
    path('admin/', admin.site.urls),
    path("", include("learning_logs.urls",namespace = 'learning_logs')),#https://blog.csdn.net/mukvintt/article/details/80320027

```
还是添加了namesapce 参数，而learning_log/urls.py为：

```
#learning_logs的URL
from django.urls import path
from . import views

#添加命名空间
app_name = 'learing_logs'  #https://blog.csdn.net/qq_38504396/article/details/79687269
urlpatterns = [
    #主页
    path("",views.index,name = 'index'),

```
我的Django版本是2.1


......
......
......


[原文链接](https://segmentfault.com/a/1190000015098721)

