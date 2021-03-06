+  **URL地址尽量使用名词复数，不要使用动词**。 

+ **访问同一个URL地址，采用不同的请求方式，代表要执行不同的操作**。 

| 请求方式 | 说明                     |
| -------- | ------------------------ |
| GET      | 获取资源数据(单个或多个) |
| POST     | 新增资源数据             |
| PUT      | 修改资源数据             |
| DELETE   | 删除资源数据             |

+ **过滤参数可以放在查询字符串中**。 

+ **针对不同操作，服务器向用户返回不同的响应数据**。

  一般遵循以下规范：

  ```http
  1. 获取一组数据，返回一组数据
  2. 获取指定数据，返回指定数据
  3. 新增数据，返回新增的数据
  4. 修改数据，返回修改的数据
  5. 删除数据，返回空
  ```

+  **服务器返回的响应数据格式，应该尽量使用JSON**。 
+  **应该尽量将API部署在专用域名之下。** 
+  **应该将API的版本号放入URL。**

```
http://www.example.com/api/1.0/foo
https://api.example.com
```

