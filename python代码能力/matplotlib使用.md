# matplotlib使用

## 1.画板的创建

```python
#创建画布
import matplotlib.pyplot as plt
fig = plt.figure(1,figsize=(100,100),dpi=80) #1表示画布编号,figsize定义画布大小,dpi定义分辨率

# sub1 = fig.add_subplot(221)#创建绘图基准,即具体图标,221表示分为2行2列第一个位置
# ax.set(xlim=[0.5, 4.5], ylim=[-2, 8], title='An Example Axes',ylabel='Y-Axis', xlabel='X-Axis')# set设置图的样式

#可以一次性创建所有图,然后再单独设置样式
fig, axes = plt.subplots(nrows=2, ncols=2)
axes[0,0].set(title='Upper Left')
axes[0,1].set(title='Upper Right')
axes[1,0].set(title='Lower Left')
axes[1,1].set(title='Lower Right')
```

## 2.绘图

+ plot()连续线

  ```python
  #使用例1创建绘图模板
  import numpy as np
  # x = np.linspace(0, np.pi)#定义x范围
  y_sin = np.sin(x)
  y_cos = np.cos(x)
  axes[0,0].plot(x, y_sin)#传入x,y坐标集合,自动绘制出图形
  axes[0,1].plot(x, y_sin, 'go--', linewidth=2, markersize=12)
  axes[1,0].plot(x, y_cos, color='red', marker='+', linestyle='dashed')
  plt.show()
  #plt.savefig('aa.jpg',dpi=400,bbox_inches='tight')   #savefig保存图片，dpi分辨率，bbox_inches子图周边白色空间的大小
  ```

  - plot()可以设置的参数如下

    ```
    属性                      值类型
    alpha                   浮点值
    animated                [True / False]
    antialiased or aa       [True / False]
    clip_box                matplotlib.transform.Bbox 实例
    clip_on                 [True / False]
    clip_path               Path 实例， Transform，以及Patch实例
    color or c              任何 matplotlib 颜色
    contains                命中测试函数
    dash_capstyle           ['butt' / 'round' / 'projecting']
    dash_joinstyle          ['miter' / 'round' / 'bevel']
    dashes                  以点为单位的连接/断开墨水序列
    data                    (np.array xdata, np.array ydata)
    figure                  matplotlib.figure.Figure 实例
    label                   任何字符串
    linestyle or ls         [ '-' / '--' / '-.' / ':' / 'steps' / ...]
    linewidth or lw         以点为单位的浮点值
    lod                     [True / False]
    marker                  [ '+' / ',' / '.' / '1' / '2' / '3' / '4' ]
    markeredgecolor or mec  任何 matplotlib 颜色
    markeredgewidth or mew  以点为单位的浮点值
    markerfacecolor or mfc  任何 matplotlib 颜色
    markersize or ms        浮点值
    markevery               [ None / 整数值 / (startind, stride) ]
    picker                  用于交互式线条选择
    pickradius              线条的拾取选择半径
    solid_capstyle          ['butt' / 'round' / 'projecting']
    solid_joinstyle         ['miter' / 'round' / 'bevel']
    transform               matplotlib.transforms.Transform 实例
    visible                 [True / False]
    xdata                   np.array
    ydata                   np.array
    zorder                  任何数值
    ```

    

+ 散点图scatter()

  用法和plot()相同,绘制出的是只含点的不用线连接的图

  ```python
  x = np.arange(10)
  y = np.random.randn(10)
  plt.scatter(x, y, color='red', marker='+')
  plt.show()
  ```

  

+ 极坐标图

  ```python
  fig = plt.figure(2)                                #新开一个窗口
  ax1 = fig.add_subplot(1,2,1,polar=True)                  #启动一个极坐标子图
  theta=np.arange(0,2*np.pi,0.02)              #角度数列值
  ax1.plot(theta,2*np.ones_like(theta),lw=2)   #画图，参数：角度，半径，lw线宽
  ax1.plot(theta,theta/6,linestyle='--',lw=2)           #画图，参数：角度，半径，linestyle样式，lw线宽
  
  ax2 = fig.add_subplot(1,2,2,polar=True)                  #启动一个极坐标子图
  ax2.plot(theta,np.cos(5*theta),linestyle='--',lw=2)
  ax2.plot(theta,2*np.cos(4*theta),lw=2)
  
  ax2.set_rgrids(np.arange(0.2,2,0.2),angle=45)   #距离网格轴，轴线刻度和显示位置
  ax2.set_thetagrids([0,45,90])                   #角度网格轴，范围0-360度
  
  plt.show()
  ```

  ![1578556308534](C:\Users\月下海\AppData\Roaming\Typora\typora-user-images\1578556308534.png)





+ 柱状图(条形图)bar()

  ```python
  plt.figure(3)
  x_index = np.arange(5)   #柱的索引
  x_data = ('A', 'B', 'C', 'D', 'E')
  y1_data = (20, 35, 30, 35, 27)
  y2_data = (25, 32, 34, 20, 25)
  bar_width = 0.35   #定义一个数字代表每个独立柱的宽度
  
  rects1 = plt.bar(x_index, y1_data, width=bar_width,alpha=0.4, color='b',label='legend1')            #参数：左偏移、高度、柱宽、透明度、颜色、图例
  rects2 = plt.bar(x_index + bar_width, y2_data, width=bar_width,alpha=0.5,color='r',label='legend2') #参数：左偏移、高度、柱宽、透明度、颜色、图例
  #关于左偏移，不用关心每根柱的中心不中心，因为只要把刻度线设置在柱的中间就可以了
  plt.xticks(x_index + bar_width/2, x_data)   #x轴刻度线
  plt.legend()    #显示图例
  plt.tight_layout()  #自动控制图像外部边缘，此方法不能够很好的控制图像间的间隔
  plt.show()
  ```

  



+ 直方图hist()

  

  ```python
  fig,(ax0,ax1) = plt.subplots(nrows=2,figsize=(9,6))     #在窗口上添加2个子图
  sigma = 1   #标准差
  mean = 0    #均值
  x=mean+sigma*np.random.randn(10000)   #正态分布随机数
  ax0.hist(x,bins=40,normed=False,histtype='bar',facecolor='yellowgreen',alpha=0.75)   #normed是否归一化，histtype直方图类型，facecolor颜色，alpha透明度
  ax1.hist(x,bins=20,normed=1,histtype='bar',facecolor='pink',alpha=0.75,cumulative=True,rwidth=0.8) #bins柱子的个数,cumulative是否计算累加分布，rwidth柱子宽度
  plt.show()  #所有窗口运行
  ```

  

+ 饼图pie()

  ```python
  labels = 'Frogs', 'Hogs', 'Dogs', 'Logs'
  sizes = [15, 30, 45, 10]
  explode = (0, 0.1, 0, 0)  # only "explode" the 2nd slice (i.e. 'Hogs')
  
  fig1, (ax1, ax2) = plt.subplots(2)
  ax1.pie(sizes, labels=labels, autopct='%1.1f%%', shadow=True)
  ax1.axis('equal')
  ax2.pie(sizes, autopct='%1.2f%%', shadow=True, startangle=90, explode=explode,
      pctdistance=1.12)
  ax2.axis('equal')
  ax2.legend(labels=labels, loc='upper right')
  
  plt.show()
  ```

  

饼图自动根据数据的百分比画饼.。`labels`是各个块的标签，如子图一。`autopct=%1.1f%%`表示格式化百分比精确输出，`explode`，突出某些块，不同的值突出的效果不一样。`pctdistance=1.12`百分比距离圆心的距离，默认是0.6.

+ 箱型图boxplot()

  fig, (ax1, ax2) = plt.subplots(2)
  ax1.boxplot(data)
  ax2.boxplot(data2, vert=False) #控制方向

+ 多边形图,定义多边形,添加到图中

  ```
  fig = plt.figure(6)   #创建一个窗口
  ax=fig.add_subplot(1,1,1)   #添加一个子图
  rect1 = plt.Rectangle((0.1,0.2),0.2,0.3,color='r')  #创建一个矩形，参数：(x,y),width,height
  circ1 = plt.Circle((0.7,0.2),0.15,color='r',alpha=0.3)  #创建一个椭圆，参数：中心点，半径，默认这个圆形会跟随窗口大小进行长宽压缩
  pgon1 = plt.Polygon([[0.45,0.45],[0.65,0.6],[0.2,0.6]])  #创建一个多边形，参数：每个顶点坐标
  
  ax.add_patch(rect1)  #将形状添加到子图上
  ax.add_patch(circ1)  #将形状添加到子图上
  ax.add_patch(pgon1)  #将形状添加到子图上
  
  fig.canvas.draw()  #子图绘制
  plt.show()
  ```

+ 等高线contourf()(轮廓图)

  ```
  fig, (ax1, ax2) = plt.subplots(2)
  x = np.arange(-5, 5, 0.1)
  y = np.arange(-5, 5, 0.1)
  xx, yy = np.meshgrid(x, y, sparse=True)
  z = np.sin(xx**2 + yy**2) / (xx**2 + yy**2)
  ax1.contourf(x, y, z)
  ax2.contour(x, y, z)
  ```

  

![1578558150406](C:\Users\月下海\AppData\Roaming\Typora\typora-user-images\1578558150406.png)