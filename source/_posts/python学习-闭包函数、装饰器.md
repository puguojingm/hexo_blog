---
title: python学习-闭包函数、装饰器
date: 2018-08-20 14:02:50
tags: [Python,闭包,装饰器]
categories: python
copyright:
top: 
---

&#8195;&#8195;
##### 1.不定长变量

```
def fun_1(*kargs,**kwargs):
    print(kargs)
    print(kwargs)
```
```
fun_1(1,2,3,row = 1,colum = 2)
```

```
输出结果：
(1, 2, 3)
{'row': 1, 'colum': 2}
```

##### 2.闭包函数
&#8195;&#8195;函数闭包是引用了自由变量的函数。这个被引用的自由变量将和这个函数一同存在，即使已经离开了创造它的环境也不例外。（维基百科）
```
def fun(x,y):
    def close_fun(a):
        return x+y+a #可以使用x,y
    return close_fun #返回函数对象
```
```
var= fun(1,2)#fun(1,2)返回的是一个close_fun函数对象
var(3)      #结果：6，相当于使用close_fun(3）
```




##### 3.装饰器函数


&#8195;&#8195;装饰器函数就是把被装饰的函数当做参数传入一个闭包函数中，使闭包函数中的内容先执行，然后在执行被装饰函数中的内容。

##### (1)被装饰的函数带参数

```
#装饰器函数
def my_decorator(fun):#fun代表被装饰的函数
    def close_fun(a,b):#内部函数的参数要和被装饰的函数fun的参数对应
        a += 1 #在执行被装饰的函数之前，先对参数做处理
        b += 1
        return fun(a,b) #返回被装饰的函数的 '调用'
    return close_fun    #返回闭包函数  '对象'
```



```
@my_decorator
def fun_add(x,y):#被装饰的函数fun_add是带参数的
    return x+y
```
```
#调用
fun_add(1,2)#结果是5
```

```
#调用过程
#1.把fun_add当做参数传入装饰器 res_fun = my_decorator（fun_add）
#2.由于my_decorator是一个闭包函数，返回的是一个colse_fun 函数对象，所以res_fun <-等价与->close_fun
#3.调用close_fun(a,b),对应的实参就是close_fun(1,2)
#4.close_fun内部对参数进行 '+= 1'的处理  所以传入的实参 1，2 变成了 2，3
#5.close_fun内部最后返回的是被装饰的函数fun_add调用，而此时参数已经变成了2，3，所以是fun_add(2,3),最终结果 return 2+3 为 5
```
##### (2)装饰器函数同时也带参数
```
#装饰器函数同时也带参数,只需要在外面再加一层嵌套即可
def my_decorator_arg(arg_1,arg_2):
    def my_decorator(fun):#fun代表被装饰的函数
        def close_fun(a,b):#内部函数的参数要和被装饰的函数fun的参数对应
            # 在执行被装饰的函数之前，先对参数做处理,根据装饰器装饰器函数不同的参数，对被装饰的函数做不同的处理
            if arg_1=='yes':#如果装饰器函数的第一个参数是yes,就对被装饰的函数的第一个参数进行 +1处理
                a += 1
            elif arg_1=='no':#如果装饰器函数的第一个参数是no,就对被装饰的函数的第一个参数进行 -1处理
                a -= 1

            if arg_2 == 0:#如果装饰器函数的第二个参数是0,就对被装饰的函数的第二个参数进行 +1处理
                b += 1
            elif arg_2 == 1:#如果装饰器函数的第二个参数是1,就对被装饰的函数的第二个参数进行 -1处理
                b -= 1
            return fun(a,b) #返回被装饰的函数的 '调用'
        return close_fun    #返回闭包函数  '对象'
    return my_decorator     #返回装饰器函数 '对象'

```

```
@my_decorator_arg('yes',0)
def fun_add(x,y):#被装饰的函数fun_add是带参数的
    return x+y

r = fun_add(1,2)#结果是5
```

```
@my_decorator_arg('no',0)
def fun_add(x,y):#被装饰的函数fun_add是带参数的
    return x+y

r = fun_add(1,2)#结果是3
```
##### (3)被装饰函数任意参数

```
def my_decorator(fun):#fun代表被装饰的函数
    def close_fun(*args,**kwargs):#内部函数的参数不定参                   a += 1 #在执行被装饰的函数之前，先对参数做处理
            res =  fun(*args,**kwargs)#'调用'被装饰的函数
            return res * res  #对结果再做处理
    return close_fun    #返回闭包函数  '对象'


@my_decorator_arg
def fun_add(x):#被装饰的函数fun_add参数不定
    return x

@my_decorator_arg
def fun_add(x,y,k):#被装饰的函数fun_add参数不定
    return x+y+k
```
##### (4)多个装饰函数

```
def my_decorator_1(fun):#第一个装饰器函数，求和
    def close_fun(*args,**kwargs):
            print("my_decorator_1 args:",args)
            print("my_decorator_1 kwargs:",kwargs)
            res =  fun(*args,**kwargs)#调用被装饰的函数
            return res + res
    return close_fun


def my_decorator_2(fun):##第二个装饰器函数，求平方
    def close_fun(*args,**kwargs):
            print("my_decorator_2 args:", args)
            print("my_decorator_2 kwargs:", kwargs)
            res =  fun(*args,**kwargs)
            return res * res  
    return close_fun  
```

```
@my_decorator_1
@my_decorator_2
def fun_add(x,y):#被装饰的函数fun_add是带参数的
    print('fun_add:',x,y)
    return x+y

r = fun_add(1,2)
print(r)

#输出结果：
#my_decorator_1 args: (1, 2)
#my_decorator_1 kwargs: {}
#my_decorator_2 args: (1, 2)
#my_decorator_2 kwargs: {}
#fun_add: 1 2
#18
```

```
顺序:先执行my_decorator_2，后执行顺序1中先执行my_decorator_1。用嵌套表示：my_decorator_1(my_decorator_2(fun_add))
具体分析：
1.my_decorator_1调用fun(1,2)得到res为3，然后return 3*3 = 9
2.然后调用my_decorator_2 来自my_decorator_1 中结果 是 9  然后return 9+9  得出18
```



```
@my_decorator_2
@my_decorator_1
def fun_add(x,y):#被装饰的函数fun_add是带参数的
    print('fun_add:',x,y)
    return x+y

r = fun_add(1,2)
print(r)

#输出结果：
#mmy_decorator_2 args: (1, 2)
#my_decorator_2 kwargs: {}
#my_decorator_1 args: (1, 2)
#my_decorator_1 kwargs: {}
#fun_add: 1 2
#36
```
```
顺序:先执行my_decorator_1，后执行顺序1中先执行my_decorator_2。用嵌套表示：my_decorator_2(my_decorator_1(fun_add))
具体分析：
1.my_decorator_2调用fun(1,2)得到res为3，然后return 3+3 = 6
2.然后调用my_decorator_1 来自my_decorator_1 中结果 是 6  然后return 6*6  得出36
```
`多个装饰器装饰一个函数时，装饰器的顺序也很重要。执行时总是先执行离被装饰函数最近的装饰函数，由近及远。`


