# Pandas使用

# 1.数据创建

+ Series(一维数组)

  ```python
  #pd.Series(data=None, index=None, dtype=None)
  #color_count = pd.Series({'red':100, 'blue':200, 'green': 500, 'yellow':1000})
  #使用字典创建,key为索引
  ```

  + data：传入的数据，可以是ndarray、list等
  + index：索引，必须是唯一的，且与数据的长度相等。如果没有传入索引参数，则默认会自动创建一个从0-N的整数索引。
  + dtype：数据的类型

  ```python
  import pandas as pb
  temp = pb.Series([1,2,3,4])
  temp.index#返回索引
  temp.values#返回值
  temp[2]#根据索引获取值
  ```

  + Series中提供了两个属性index和values

+ DataFrame二维数组

  ```python
  #pd.DataFrame(data=None, index=None, columns=None)#index为行索引,columns为列索引
  ```

  + data直接传入二维数组
  + DataFrame提供了index,shape,columns,values,T(转置)五种属性

  
```
  #data.index = 新的行索引   ,修改索引值
  data=data.rename(index={"学生3":"学生_3"},columns={"体育":"物理"})#修改指定位置的索引
  data.reset_index(drop = False)#重设索引,原索引保留,True时删除
data.set_index(key),将某列设为新的索引,可以以列表形式传入多个值设置多个索引
  
  ```
  
  

+ MultiIndex三维数组

  

