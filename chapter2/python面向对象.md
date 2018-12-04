## python面向对象

#### object类

object类是Python中所有类的基类，如果定义一个类时没有指定继承哪个类，则默认继承object类。

**Python中类分两种：旧式类和新式类**：

➤新式类都从object继承，经典类不需要。

➤新式类的MRO(method resolution order 基类搜索顺序)算法采用C3算法广度优先搜索，而旧式类的MRO算法是采用深度优先搜索

➤新式类相同父类只执行一次构造函数，经典类重复执行多次。

#### python是动态语言

动态语言的定义：动态编程语言是高级程序设计语言的一个类别，它是一类在运行时可以改变其结构的语言：例如新的函数、对象、甚至代码可以被引进，已有的函数可以被删除或是其他结构上的变化。动态语言目前非常具有活力。众所周知的ECMAScript（JavaScript）便是一个动态语言，除此之外如PHP、Ruby、Python等也都属于动态语言。

  而静态语言是在编译时变量的数据类型即可确定的语言，多数静态类型语言要求在使用变量之前必须声明数据类型。 如JAVA,C/C++



动态语言的一个标志是，在程序执行过程中，可以添加属性和方法。

既可以给对象添加属性也可以给类添加属性。

#### 类方法

在类地内部，使用 def 关键字来定义一个方法，与一般函数定义不同，类方法必须包含参数 self, 且为第一个参数，self 代表的是类的实例。在类中调用类方法的时候也要加上self。

```
# -*- coding: utf8 -*-
#!/usr/bin/python3
class ClassName:
    i = 100;
    def func(self):
        print(self)
        print(self.__class__)
    def func2(self):
        self.func() #调用时加上self
a = ClassName() 
a.func2()
```



#### 类属性/实例属性

类属性在整个实例化的对象中是公用的。类变量定义在类中且在函数体之外。类变量通常不作为实例变量使用。

**注意区分类变量和实例变量**

```
# -*- coding: utf8 -*-
#!/usr/bin/python3
class ClassName:
    i = 100; # i 是类变量，可以通过对象访问，也能通过类名访问
    def func(self):
        print(self)
        print(self.__class__)
    def func2(self):
        self.func() #哈哈
a = ClassName()

a.func()
print(a.i)
print(ClassName.i)  ＃可以通过类名访问
a.j = 10  # j是实例变量，可以通过对象访问，但是不能通过类名访问
print(a.j)
#print(ClassName.j) #error
```

* 类属性，属于对象共有的属性，可以通过类名和对象名访问

* 实例属性，是对象自身的属性，只可以通过对象名访问

  ​

#### 私有属性和方法

两个下划线开头，声明该属性或方法为私有。

```
# -*- coding: utf8 -*-
#!/usr/bin/python3
class ClassName:
    i = 100
    __j = 90 
    def func(self):
        print(self.__j)

a = ClassName()
a.func()
#print(a.__j) #error
```

#### 多继承

[Python中多继承与super()用法](https://blog.csdn.net/qingzhou4122/article/details/53502635)

Python同样有限的支持多继承形式。多继承的类定义形如下例:

```
class DerivedClassName(Base1, Base2, Base3):
    <statement-1>
    ...
```

`__mro__方法` ：打印多继承过程中，搜索某个方法的顺序

```
class A(object):
    def __init__(self):
        print '    -> Enter A'
        print '    <- Leave A'

class B(A):
    def __init__(self):
        print '    -> Enter B'
        # A.__init__(self)
        super(B, self).__init__()
        print '    <- Leave B'

class C(A):
    def __init__(self):
        print "    -> Enter C"
        # A.__init__(self)
        super(C, self).__init__()
        print "    <- Leave C"

class D(B, C):
    def __init__(self):
        print "    -> Enter D"
        # B.__init__(self)
        # C.__init__(self)
        super(D, self).__init__()
        print "    <- Leave D"

if __name__ == "__main__":
    d = D()
    print "MRO:", [x.__name__ for x in D.__mro__]
    print type(D.__mro__)
```

结果

```
    -> Enter D
    -> Enter B
    -> Enter C
    -> Enter A
    <- Leave A
    <- Leave C
    <- Leave B
    <- Leave D
MRO: ['D', 'B', 'C', 'A', 'object']
<type 'tuple'>
```

####多态

面向对象的语言三要素：

* 封装
* 继承
* 多态

#### 构造函数和析构函数

`__init__` :构造函数，在生成对象时调用

`__del__`:析构函数，释放对象时使用  

没有任何引用（引用计数为0）指向对象的时候，才会调用 `__del__`函数。

**`__str__函数`** ：对应print(对象)时输出的内容

```
# -*- coding: utf8 -*-
#!/usr/bin/python3
class ClassName:
    i = 100;

    def __str__(self):
        return 'haha'
a = ClassName()
print(a)
```

结果

```
haha
```

#### 隐藏属性

使用方法set 和 get属性值

#### 调用父类被重写的方法

[调用父类的方法](https://www.cnblogs.com/seirios1993/p/6601823.html)

第一种是直接法。使用父类名称直接调用，形如 parent_class.parent_attribute(self)，对应例程即语句：

```
Student.__init__(self,name)
```

​      第二种是通过super函数，形如 super(child_class, child_object).parent_attribute(arg)。第一个参数表示调用父类的起始处，第二个参数表示类实例（一般使用self），父类方法的参数只有self时，参数args不用写。此外，类内部使用时，child_class, child_object也可省略。



#### 实例方法、类方法、静态方法

```
# -*-coding:utf-8-*-
# 普通方法,类方法,静态方法的区别

__metaclass__ = type

class Tst:
    name = 'tst'

    data = 'this is data'

    # 普通方法(实例方法)
    def normalMethod(self, name):
        print(self.data)
        print(self.name)

    # 类方法,可以访问类属性， 可以使用对象 或者 类名调用
    @classmethod
    def classMethod(cls, name):
        print(cls.data)
        print(cls.name)

    # 静态方法,不可以访问类属性, 可以通过类名 或者 对象调用
    @staticmethod
    def staticMethod(name):
        print name
```



动态语言的一个标志是，在程序执行过程中，可以添加属性和方法。

既可以给对象添加属性也可以给类添加属性。



####`__new__方法` 

创建对象的方法，使用该方法创建的对象

```
# -*- conding:utf8 -*-
#!/usr/bin/python3

class A(object):
    def __init__(self):
        print("haha")

    def __new__(cls): #参数是cls，代表的是类
        print(id(cls))
        return object.__new__(cls)

print(id(A))
a = A()
```

结果

```
19040416
19040416
haha
```

#### 创建单例对象

```
Class Dog(object):
	__instance = None;
	def __new__(cls): #参数是cls，代表的是类
    	if cls.__instance == None:
        	cls.__instance = object.__new__(cls)
        	return cls.__instance
        else:
        	return cls.__instance	
```

**考虑如果要求只初始化一次该如何做？**

#### 异常

[异常](https://www.runoob.com/python/python-exceptions.html)

**try-except**

```
#!/usr/bin/python
# -*- coding: UTF-8 -*-

try:
    fh = open("testfile", "w")
    fh.write("这是一个测试文件，用于测试异常!!")
except IOError:
    print "Error: 没有找到文件或读取文件失败"
else:
    print "内容写入文件成功"
    fh.close()
```

**try-finally**

```
try:
<语句>
finally:
<语句>    #退出try时总会执行
```



#### 模块/包

每个py文件都是一个模块。

一个包含多个模块的文件夹中，包含`__init__.py`文件，表示这是一个包。导入包的时候会执行该文件。

**`__all__`**

在模块中表示模块中可以被导出的函数或者变量

在包的`__init__.py`中表示可以被导出的模块名



####setup.py安装包

[python 编写简单的setup.py](http://www.cnblogs.com/lyrichu/p/6818008.html)

命令

```
python3 setup.py build
python3 setup.py sdist
python3 setup.py install
```

#### 给程序传参数

```
#!/usr/bin/python3
import sys

print(sys.argv)
```

**sys.path 打印搜索路径**



#### reload 重新导入模块

程序执行过程中，模块被修改了，有时需要重新导入模块

```
form imp import *
reload(模块名)
```

#### 循环导入模块引入问题

设计过程中注意不要出现模块间相互引入，这样会引起问题。



