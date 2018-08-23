---
title: Django学习笔记（二）-用户登录及注册
date: 2018-08-23 16:47:12
tags: [Python,Djiango]
categories: python
copyright:
top: 
---
&#8195;&#8195;
### 用户账户
-------
>此处只记录在项目中，制作登录功能时遇到的问题以及解决方法。

#### 3. 创建用户账户
-------

##### 3.2 登陆页面
-------
此处与书中不同，书中使用Django提供的默认登陆视图，而此处则是自己实现login登录视图。在users中的urls.py中添加如下代码：

```

urlpatterns = [
    # 登陆页面
    #path("login/", login, {"template_name": "users/login.html"}, name="login"),
    path('login/', views.login_view,name="login"),
    path("logout/", views.logout_view, name="logout"),
    path("register/", views.register, name="register"),
]
```
原文中使用views中自带的login，但是我的Django版本是2.1。views中没有login，于是自己在view中实现login。
views.PY代码如下：

```
from django.shortcuts import render, redirect, reverse
from django.contrib.auth import	authenticate,login,logout
from django.http import HttpResponseRedirect
from django.contrib.auth.forms import UserCreationForm


def login_view(request):
    if request.method == 'GET':
        return render(request, 'users/login.html')

    username = request.POST.get('username', '')
    password = request.POST.get('password', '')

    user = authenticate(request, username=username, password=password)
    if user is not None:
        login(request, user)
        return redirect(reverse('learning_logs:index'))
    else:
        return render(request, 'users/login.html', {
            'username': username,
            'password': password,
        })
```
login.html如下：

```
{% extends "learning_logs/base.html" %}

{% block content %}

    <form action="{% url 'users:login' %}" method="post">
    
        {% csrf_token %}
        {{ form.as_p }}
    
        <input type="text" id="username" name="username" value="{{ username }}" class="form-control" placeholder="用户名" required autofocus>

        <input type="password" id="password" name="password" value="{{ password }}" class="form-control" placeholder="密码" required>
        
        <button type="submit">登录</button>
    </form>
{% endblock %}
```
此处登录界面完成。

其它内容请参考:[原文链接](https://segmentfault.com/a/1190000015098802)

