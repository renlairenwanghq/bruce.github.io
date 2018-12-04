## 函数

* python的函数可以有多个返回值
* python可以在函数内部定义函数

```
def 函数名（参数列表）:
    函数体
```



####参数传递中的注意事项

参数可以是 可更改(mutable)与不可更改(immutable)对象，两者作为参数会有一些区别

python 函数的参数传递：

- **不可变类型：**类似 c++ 的值传递，如 整数、字符串、元组。如fun（a），传递的只是a的值，没有影响a对象本身。比如在 fun（a）内部修改 a 的值，只是修改另一个复制的对象，不会影响 a 本身。
- **可变类型：**类似 c++ 的引用传递，如 列表，字典。如 fun（la），则是将 la 真正的传过去，修改后fun外部的la也会受影响

传递不可变类型

```
#!/usr/bin/python3
 
def ChangeInt( a ):
    a = 10

b = 2
ChangeInt(b)
print( b ) # 结果是 2
```

传递可变类型

```
#!/usr/bin/python3
 
# 可写函数说明
def changeme( mylist ):
   "修改传入的列表"
   mylist.append([1,2,3,4]);
   print ("函数内取值: ", mylist)
   return
 
# 调用changeme函数
mylist = [10,20,30];
changeme( mylist );
print ("函数外取值: ", mylist)
```

结果

```
函数内取值:  [10, 20, 30, [1, 2, 3, 4]]
函数外取值:  [10, 20, 30, [1, 2, 3, 4]]
```

#### 参数的几种类型

- 必需参数

- 关键字参数

  即明确指明那个参数对应那个值 

  ```
  #!/usr/bin/python3
   
  #可写函数说明
  def printme( str ):
     "打印任何传入的字符串"
     print (str);
     return;
   
  #调用printme函数
  printme( str = "菜鸟教程");
  ```

  ​

- 默认参数

- 不定长参数 

  参数前带一个 * 号，则可以传入不定长个参数，参数是一个元组

  ```
  #!/usr/bin/python3
  def func3(*arg):
      print(arg)

  func3(1,2,3)
  ```

  结果

  ```
  (1, 2, 3)
  ```

  参数前带两个 * 号，则可以传入不定长个参数，参数是一个字典

  ```
  #!/usr/bin/python3

  def func2(**arg1):
      print(arg1)

  func2(a=3, b = 4)
  ```

  结果

  ```
  {'a': 3, 'b': 4}
  ```

  ​

#### 匿名参数 lambda 创建函数的另一种方式

语法：

```
lambda [arg1 [,arg2,.....argn]]:expression
```



```
#!/usr/bin/python3

sum = lambda a1, a2, a3 : a1 + a2 + a3
print(sum(1, 2, 3))
```

**注意**

这种语句的目的是由于性能的原因,在调用时绕过函数的栈分配。

#### 偏函数

[偏函数](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143184474383175eeea92a8b0439fab7b392a8a32f8fa000)

当函数的参数个数太多，需要简化时，使用`functools.partial`可以创建一个新的函数，这个新函数可以固定住原函数的部分参数，从而在调用时更简单。

####global 和 nonlocal关键字 

当内部作用域想修改外部作用域的变量时，就要用到global和nonlocal关键字了。

**global关键字**

```
#!/usr/bin/python3
num = 1
def fun1():
    print(num) 
    num=2
    print(num)
fun1()
```

结果:

```
Traceback (most recent call last):
  File "b.py", line 8, in <module>
    fun1()
  File "b.py", line 5, in fun1
    print(num) 
UnboundLocalError: local variable 'num' referenced before assignment
```

修改

```
#!/usr/bin/python3
num = 1
def fun1():
    global num
    print(num) 
    num=2
    print(num)
fun1()
```

结果

```
1
2
```

**nonlocal关键字(python3)**

```
#!/usr/bin/python3
 
def outer():
    num = 10
    def inner():
        num = 100
        print(num)
    inner()
    print(num)
outer()
```

结果 并没有真正修改num的值

```
100
10
```

修改

```
#!/usr/bin/python3
 
def outer():
    num = 10
    def inner():
        nonlocal num
        num = 100
        print(num)
    inner()
    print(num)
outer()
```

结果 理论应该是 100, 100,但是我试验的时候竟然报语法错误了。

#### 注意

```
$ cat f.py 
#!/usr/bin/python3

def func():
    print('--------1--------')

def func():
    print('--------2--------')

func()
```

上述代码在python中是可行的，相当于修改了func的指向。

结果：

```
$ python f.py 
--------2--------
```

#### 闭包

[python闭包](https://www.cnblogs.com/JohnABC/p/4076855.html)

**定义**

如果在一个内部函数里，对在外部作用域（但不是在全局作用域）的变量进行引用，那么内部函数就被认为是闭包(closure).

```
>>> def ExFunc(n):
     sum=n
     def InsFunc():
             return sum+1
     return InsFunc

>>> myFunc=ExFunc(10)
>>> myFunc()
11
>>> myAnotherFunc=ExFunc(20)
>>> myAnotherFunc()
21
>>> myFunc()
11
>>> myAnotherFunc()
21
>>>
```

在这段程序中，函数InsFunc是函数ExFunc的内嵌函数，并且是ExFunc函数的返回值。我们注意到一个问题：内嵌函数InsFunc中
引用到外层函数中的局部变量sum，IronPython会这么处理这个问题呢？先让我们来看看这段代码的运行结果。当我们调用分别由不同的参数调用
ExFunc函数得到的函数时（myFunc()，myAnotherFunc()），得到的结果是隔离的，也就是说每次调用ExFunc函数后都将生成并保存一个新的局部变量sum。其实这里ExFunc函数返回的就是闭包。



**注意**

闭包中是不能修改外部作用域的局部变量的(python3中使用nonlocal关键字是可以的)

```
>>> def foo():  
...     m = 0  
...     def foo1():  
...         m = 1  
...         print m  
...  
...     print m  
...     foo1()  
...     print m  
...  
>>> foo()  
0  
1  
0 
```

####装饰器

装饰器仅仅是用来“装饰“(或者修饰)函数的包装,返回一个修改后的函数对象,将其重新赋值原来的标识符,并永久失去对原始函数对象的访问。