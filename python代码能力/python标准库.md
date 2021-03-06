# 基础语法

    if...else、elif
    for...
    while...break、continue
    try...except
    for...if...break...else# break会连else一起跳出
    try...else
    列表、字典、集合、元组、数字、字符
    send函数使得yield关键字拥有了返回值返回给它的左值。

# python自带库
 + random
    ```
    import random
    random.shuffle(iter）# 打乱
    random.randint(0,20) #随机0-20整数
    random.random()#随机0-1之间浮点数
    random.choice(iter)#随机一个数
    random.choices(iter,k)# 进行K次放回取样
    random.sample(iter,k)# 进行K次无放回取样
    random.randrange(1,100,2)#随机1-100间步长2整数
    random.uniform(1.1,5.4)# 随机1.1-5.4之间浮点数
    ```
+ os

  ```
  import os
  os.getcwd() #获取当前工作目录
  os.chdir(path) #改变当前工作目录
  os.access(path,mode) #检验权限模式
      os.F_OK :path是否存在  
      os.R_OK :path是否可读 
      os.W_OK :path是否可写  
      os.X_OK :path是否可执行
  os.chmod(path,mode)#修改权限
  os.chown(path,uid,gid)#改变文件属主属组
  os.listdir(path)#返回指定文件夹包含的文件名称列表
  os.link(path,new) #创建硬链接
  os.symlink(path,new) #创建软链接
  
  os.mkdir(path)#创建文件夹
  os.mkdirs(path )#递归创建文件夹
  os.rename(src,dst)#重命名文件
  os.remove(path)#删除文件，是文件夹会抛出异常
  os.rmdir#删除文件夹，非空会抛出异常
  os.pardir#获取当前目录父目录
  os.rmovedirs(path) #d递归删除目录
  os.system(linux命令) #直接传入linux命令进行操作
  os.path.* #与文件路径相关
      os.path.exists(path) #查看文件是否存在
      os.path.join(path,paths) #将目录拼接起来
      os.path.split(path)   #将path分割为目录跟文件名以元祖返回
      os.path.abspath(file)  #返回文件的绝对路径
  ​```
  ```

  

+ file/open文件操作
    ```
    f = open(file, mode='r', buffering=-1, encoding=None)
    f.close()#关闭文件
    f.read(size)#读取size字节数据
    f.readline()#读取整行
    f.readlines()#读取所有行，并以一个列表形式返回
    f.tell（）#返回文件当前位置
    f.flush()#立刻写入缓冲区数据，而不是等待缓冲区写入
    f.write(str)#写入数据
    f.writes(sequence)#写入一个序列字符串列表，需要自己添加换行符
    f.truncate(size)#从开头截断size字符并删除，无size表示当前位置开始截断.并且删除其中所有数据
    
    for line in f：
        pass            #能够逐行处理文件，适合处理极大文件
    ```
    
+ socket
    ```
    import socket
    s = socket.socket(family,type,proto)
        # family是套接字家族，可以是AF_UNIX(本机通讯)或AF_INET（跨机器通过ip通讯）
        #type是连接类型，SOCK_STREM（面向连接给予TCP）或SOCK_DGRAM（非连接UDP）
    #  服务器端方法
    s.bind（（ip，port））#传入元祖，包含ip和端口
    s.listen(128) # 开启TCP监听，最大128连接数
    client，address = s.accept() #获取连接和连接地址
    
    # 客户端方法
    s.connect((ip,port)) # 与服务器建立TCP连接
    s.connect_ex() #connect扩展版本，出错返回错误代码而不抛出异常
      
    # 公共方法
    s.recv(1024)
    s.send()
    s.close()
    s.getpeername()#获取远程套接字ip与端口
    s.getsockname()#获取本地套接字ip与端口
    ```
    
+ threading  多线程
    ```
    import threading
    def demo（a，b，c）
        pass
    线程1 = threading.Thread(target = demo，args = （a，b，c）)# 元祖传参
    线程1 = threading.Thread(target = demo，kwargs = {'a':1,'b':2,'c':3}) # 字典传参
    线程1.Daemon(True) #设置为守护线程
    线程1.start（） # 启动线程
    线程1.join（）#设置主线程等待子线程结束，在start之后
    
    lock = threading.Lock #线程锁
    lock = threading.RLock #嵌套线程锁，要求acquire与release成对出现
    semaphore = threading.BoundedSemaphore(5) #允许同时存在5把锁 
    lock.acquire() # 加锁
    lock.release() # 释放锁
    
    event = threading.Event()# 事件，用于主线程控制其他线程直行
    event.set() # 设置flag为TRUE
    event.clear # 设置flag为false
    event.is_set #检测是否设置了flag
    event.wait #一直监听flag状态，为false会一直阻塞
    ```
    
+ multiprocess 多进程
    ```
    用法与threading相同
    进程1 = multiprocessing.Process(target = demo，args = （a，b，c）)# 元祖传参
    ```
+ json
    ```
    import json
    json.dumps() # 将python数据类型json编码
    json.loads() # json解码为python数据类型
    ```
+ global

  ```
  glob.glob(r’../*.py’) # 获取相对目录下所有py文件,返回对象是一个列表
  glob.iglob(r’../*.py’) # 获取相对目录下所有py文件,返回对象是一个迭代器
  ```

+ time
    ```
    import time
    #获取时间
    time.time() #获取时间戳
    time.ctime() # 返回人类可读的字符串格式时间
    time.gmtime() # 返回计算机可处理的时间格式，格林威治时间
    time.localtime() #返回当地时间
    #时间格式化
    strftime(ts,tpl) #time.strftime("%Y-%m-%d %H:%M:%S",time.gmtime())
    strptime(str,ts) # time.strptime('2018-01-26 12:55:20',"%Y-%m-%d %H:%M:%S")
    time.mktime(time.localtime()) # 将时间转化为时间戳
    #计时
    time.sleep(i) #休眠i秒 
    time.perf_counter() #获取从程序开始所经过的时间 
    ```
    
+ sys
    ```
    import sys
    sys.path #包含项目导包路径的列表
    sys.modules #一个全局字典，模块导入时会被加入字典，再次导入会先查询该字典
    sys.getfilesystemencoding() #文件系统使用编码方式
    sys.getdefaultencoding() # 获取默认编码格式
    sys.platform #获取当前系统平台
    ```

