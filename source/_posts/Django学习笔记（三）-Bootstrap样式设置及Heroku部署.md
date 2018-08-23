---
title: Django学习笔记（三）-Bootstrap样式设置及Heroku部署
date: 2018-08-23 17:11:41
tags: [Python,Djiango]
categories: python
copyright:
top: 
---
### 1. 前言

--
在本篇中，我们将：
·使用Bootstrap库设置样式；
·把项目部署到Heroku上。

### 2. 设置项目“学习笔记”的样式
--


之前关注的都是项目的功能，现在来为项目添加样式。

我们将使用django-bootstrap3来设置样式。首先请在虚拟环境中安装这个第三方库。


```
pip install django-bootstrap3
```
然后像之前在项目settings.py中注册我们自己编写的APP一样，注册bootstrap3这个应用程序。

还需要包含django-bootstrap3包含jQuery，在settings.py末尾添加如下代码：


```
...
LOGIN_URL = '/users/login/'

# django-bootstrap3的设置
BOOTSTRAP3 = {
    "include_jquery": True,
}
```
2.1-2.2参看[原文链接](https://segmentfault.com/a/1190000015098870)
### 2.3 设置登录页面样式
--
此处与文中不同，我的<font color=#DC143C>login.html</font>中的<font color=#DC143C>form</font>无法通过`{% bootstrap_form form %}`引用Bootstrap的样式.因此我的<font color=#DC143C>login.html</font>如下：

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>登录</title>
</head>
<body>

{% extends "learning_logs/base.html" %}
{% load bootstrap3 %}

{% block header %}
    <h2>登录您的账户</h2>
{% endblock %}

{% block content %}
 <form action="{% url 'users:login' %}" method="post" class="form-signin">
        {% csrf_token %}

        <h2 class="form-signin-heading">请登录</h2>

        <input type="text" id="username" name="username" value="{{ username }}" class="form-control" placeholder="用户名" required autofocus>

        <input type="password" id="password" name="password" value="{{ password }}" class="form-control" placeholder="密码" required>
        <div class="checkbox">
          <label>
            <input type="checkbox" value="remember-me"> Remember me
          </label>
        </div>
        <button class="btn btn-lg btn-primary btn-block" type="submit">登录</button>
      </form>

{% endblock %}

</body>
</html>
```
2.4-2.6参看[原文链接](https://segmentfault.com/a/1190000015098870)

### 3. 部署“学习笔记”
--
3.1-3.2参看[原文链接](https://segmentfault.com/a/1190000015098870)
#### 3.3 为部署到Heroku而修改settings.py
--
此处<font color=#DC143C>setting.py</font>中的<font color=#DC143C></font>与文中不同，文中如下:

```
-- snip --
# 书中的判断语句是 if os.getcwd() == '/app': 
# 现在估计是Heroku升级了，改为了下面的语句，否则待会儿部署的时候会出错
if os.environ['HOME'] == "/app":
    import dj_database_url

    DATABASES = {
        "default": dj_database_url.config(default="postgres://localhost")
    }

    # 让request.is_secure()承认X-Forwarded-Proto头
    SECURE_PROXY_HEADER = ("HTTP_X_FORWARDED_PROTO", "https")

    # 支持所有的主机头（host header）
    ALLOWED_HOSTS = ["*"]

    # 静态资产配置
    BASE_DIR = os.path.dirname(os.path.abspath(__file__))
    # 书中设置是这样的： STATIC_ROOT = "staticfiles"
    STATIC_ROOT = os.path.join(BASE_DIR, 'static')
    STATICFILES_DIRS = (
        os.path.join(BASE_DIR, "static"),
    )
```
按照此种方式设置报错，提示<font color=#DC143C>STATICFILES_DIRS</font> 的<font color=#DC143C>STATIC_ROOT</font>不正确。


```
?: (staticfiles.E002) The STATICFILES_DIRS setting should not contain the STATIC_ROOT setting.
```
修改为:

```
...
 # 静态资产配置
    BASE_DIR = os.path.dirname(os.path.abspath(__file__))
    STATIC_ROOT = "staticfiles"#书中设置是这样的：
    STATIC_URL = '/static/'
    #STATIC_ROOT = os.path.join(BASE_DIR, '/static/')#错误
    #STATIC_ROOT = os.path.join(BASE_DIR, "static/")  # 错误(staticfiles.E002) The STATICFILES_DIRS setting should not contain the STATIC_ROOT setting.
    STATICFILES_DIRS = (
        os.path.join(BASE_DIR, "static"),

    )
```
最终执行成功。
#### 3.4 创建启动进程的Procfile
--
<font color=#DC143C>Procfile</font>告诉<font color=#DC143C>Heroku</font>启动那些进程，以便能够正确地提供项目支持的服务。这个文件只包含一行，文件名为<font color=#DC143C>Procfile</font>，不带扩展名，保存到项目根目录。


```
web: gunicorn learning_log.wsgi --log-file -
```

这段代码让<font color=#DC143C>Heroku</font>将<font color=#DC143C>gunicorn</font>用过服务器，并使用<font color=#DC143C>learning_log/wsgi.py</font>中的设置来启动应用程序。标志<font color=#DC143C>log-file</font>告诉<font color=#DC143C>Heroku</font>应将哪些类型的时间写入日志。注意最后的“-”不能省略

#### 3.14 部署总结
--
从前面这些例子可看出，开发与部署的过程如下：

①修改项目。如果创建了新文件，使用命令 `git add . `（最后有个句点！）将它们加到Git仓库中。如果要迁移数据库，也需要执行该命令，因为每个迁移都生成了新的迁移文件。

②执行 `git commit -am "commit message"`，将修改提交到仓库。

③执行 `git push heroku master` 将修改推送到服务器。

④如果本地迁移了数据库，也需要迁移在线数据库，可以使用一次性命令 `heroku run python manage.py migrate `，也可以使用 `heroku run bash`打开一个远程终端会话，再执行迁移。

#### 3.15 设置SECRET_KEY
--
Django根据<font color=#DC143C>settings.py</font>中的<font color=#DC143C>SECRET_KEY</font>来实现大量的安全协议。本项目中设置的<font color=#DC143C>SECRET_KEY</font>对一个练习项目来说已经足够了，但是对于生产网站，请务必认真对待这个值。

#### 3.16 将项目从Heroku删除

```
(ll_env)learning_log$ heroku apps:destroy --app appname
```

现在可以访问这个网站 https://pgj-learning-log.herokuapp.com

