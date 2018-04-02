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

**nonlocal关键字**

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

