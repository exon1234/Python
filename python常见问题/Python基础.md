+ **使用sorted对字典排序**

  ```
  sorted(dict.items,key = lambda x:x[1]) #返回结果是一个列表
  ```

+ **python是动态强类型解释性语言**

+ **什么是元类**

  类本质是由元类创建的对象,type()是python内建的元类,继承自object类型type,type()可以用来创建自己写的元类metaclass

  + type() 的功能是找到, 输入对象的 "**父类**", 咱平时说的 int, str, list, dict ... 都表现为一个类.

  例子：

  ``` python
  # 元类继承自type
  class LowercaseMeta(type):
      """ 修改类的书写名称为小写的元类"""
      def __new__(mcs, name, bases, attrs):
          lower_attrs = {}
          for k, v in attrs.items():
              if not k.startswith('__'):
                  lower_attrs[k.lower()] = v
              else:
                  lower_attr[k] =v
          return type.__new__(mcs, name, bases, lower_attrs)
  
  class LowercaseClass(metaclass=LowercaseMeta):
      BAR = True
  
      def Hello(self):
          print('hello')
  
  print(dir(LowercaseClass))
  
  LowercaseClass().hello()
  ```

  参考链接:

+ **什么是可变与不可变类型**

  在python值是靠内存引用来传递的，例如a = 100是a存放了100的引用，可变则是可以直接修改数据，id（）内存地址不发生改变，不可变则会改变内存地址，不能直接修改数值。

  可变：列表、集合、字典；不可变：数字、元组、字符串

+ **什么是匿名函数lambda**

  定义函数没有名字则为匿名函数，可以减少代码量，形如：

  ```
  lambda [形参1], [形参2], ... : [单行表达式] 或 [函数调用]
  如：fun = lambda a,b:a+b
  则：fun（10，8）=18
  ```

+ **推导式**

  ```
  列表推导式：a = [x for x in range(20) if x%2 == 0]
  生成器表达式：a = (x for x in range(20))
  字典推导式：a = {i:j for i,j in zip(range(20),range(20))}
  集合推导式： lst = [1,2,3,-1,-3,-7,9]
  s = {abs(i) for i in lst}
  ```

+ **线程与进程与协程以及GIL锁**

  + 线程是cpu调度的基本单位，每一个进程至少有一个线程，线程之间共享全局变量，因此有数据出现错误的问题，需要加锁解决。
  + 进程是操作系统分配资源的基本单位，每启动一个进程都会分配一块内存，每个程序至少有一个进程，进程间变量独立。
  + 协程是比线程更轻量级的存在，一个线程可以存在多个协程，协程的切换由程序控制，线程是一个特殊的函数，这个函数可以在某个地方挂起，并且可以重新在挂起处外继续运行。 一个线程内有多个协程可以自由切换，但是协程是串行运行的，无法运用cpu的多核能力。 协程的切换过程只有用户态，即没有陷入内核态，因此切换效率高。 

  +  GIL（ 全局解释器锁 ）保证同一时刻只有一个线程执行代码，一个进程只有一把GIL锁，每个线程在执行过程中都要先获取GIL ，执行完毕后才会释放，因此无法利用多核cpu。GIL只有cpython才有，jpython没有。
    + 可以通过多进程、Jpython解决GIL问题
  
+ **python函数是如何传参的**

  总结一下：根据对象的引用来传递，根据对象是可变对象还是不可变对象，得到两种不同的结果。如果是可变对象，则直接修改。如果是不可变对象，则生产新对象，让形参指向新对象

+ **自省**

  自省，也可以说是反射，自省在计算机编程中通常指这种能力：检查某些事物以确定它是什么、它知道什么以及它能做什么。

  与其相关的主要方法:

  - hasattr(object, name)检查对象是否具体 name 属性。返回 bool.
  - getattr(object, name, default)获取对象的name属性。
  - setattr(object, name, default)给对象设置name属性
  - delattr(object, name)给对象删除name属性
  - dir([object])获取对象大部分的属性
  - isinstance(name, object)检查name是不是object对象
  - type(object)查看对象的类型
  - callable(object)判断对象是否是可调用对象