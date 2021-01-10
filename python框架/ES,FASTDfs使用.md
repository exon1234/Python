# 8. ES,FASTDfs

## 1.FASTDfs

+ 镜像部署

```
docker image pull delron/fastdfs #拉取镜像
docker run -dit --name tracker --network=host -v /var/fdfs/tracker:/var/fdfs delron/fastdfs tracker # 启动tracker

sudo docker run -dti --name storage --network=host -e TRACKER_SERVER=192.168.103.158:22122 -v /var/fdfs/storage:/var/fdfs delron/fastdfs  # 启动strage,ip为运行ip
```

+ 使用

```
#安装客户端文件
pip install fdfs_client-py-master.zip
$ pip install mutagen
$ pip install requests
#配置文件config设置
# base_path = FastDFS客户端存放日志文件的目录:
	base_path=/Users/meihao/Desktop

# tracker_server = 运行tracker服务的机器ip:22122
	tracker_server=172.16.238.128:22122
	
#上传方法
# 先导入我们安装的 fdfs_client.client 客户端
from fdfs_client.client import Fdfs_client

# 使用添加的客户端创建一个对象
# 这里需要注意: 添加的路径应该为配置文件的绝对路径: 
client = Fdfs_client('meiduo_mall/utils/fastdfs/client.conf')
# 这个是通过文件名上传文件
ret = client.upload_by_filename(文件名)
或
# 这个是通过文件内容上传文件:
ret = client.upload_by_buffer(文件bytes数据)
```

```
ret = {
'Group name': 'Storage组名',
'Remote file_id': '文件索引，可用于下载',
'Status': '文件上传结果反馈',
'Local file name': '上传文件全路径',
'Uploaded size': '文件大小',
'Storage IP': 'Storage地址'
 }
```



## 2.ES使用

+ 镜像部署

```
docker image pull delron/elasticsearch-ik=2.4.6-1.0#拉取镜像
#修改配置文件
/home/python/elasticsearc-2.4.6/config/elasticsearch.yml 将ip改为真实ip
# 运行镜像容器
docker run -dit --name=elasticsearh --network=host -v/home/python/elasticsearch-2.4.6/config:/usr/share/elasticsearch/config delron/elasticsearch-ik:2.4.6-1.0
```

+ Haystack对接django与搜索引擎

```
#注册扩展包
$ pip install django-haystack
$ pip install elasticsearch==2.4.1
INSTALLED_APPS = [
     # 全文检索
    'haystack', 
]
# Haystack 注册
url(r'^search/', include('haystack.urls')),


# 设置文件
HAYSTACK_CONNECTIONS = {
    'default': {
        'ENGINE': 'haystack.backends.elasticsearch_backend.ElasticsearchSearchEngine',
        'URL': 'http://172.16.238.128:9200/', # Elasticsearch服务器ip地址，端口号固定为9200
        'INDEX_NAME': 'meiduo_mall', # Elasticsearch建立的索引库的名称
    },
}

# 当添加、修改、删除数据时，自动生成索引
HAYSTACK_SIGNAL_PROCESSOR = 'haystack.signals.RealtimeSignalProcessor'
```

```
#对已有数据创建索引
from haystack import indexes

from .models import SKU


class SKUIndex(indexes.SearchIndex, indexes.Indexable):
    """SKU索引数据模型类"""
    text = indexes.CharField(document=True, use_template=True)

    def get_model(self):
        """返回建立索引的模型类"""
        return SKU

    def index_queryset(self, using=None):
        """返回要建立索引的数据查询集"""
        return self.get_model().objects.filter(is_launched=True)
```

- + 其中 `text` 字段我们声明为 `document=True`，表名该字段是主要进行关键字查询的字段。

  +  text`字段的索引值可以由多个数据库模型类字段组成，具体由哪些模型类字段组成，我们用 `use_template=True` 表示后续通过模板来指明。

```
# 新建具体在 templates/search/indexes/goods/sku_text.txt 文件,在文件中定义
{{ object.id }}
{{ object.name }}
{{ object.caption }}
# 此模板指明 SKU 的id、name、caption作为text字段的索引值来进行关键字索引查询。
$ python manage.py rebuild_index #迁移模型类生成索引
```

```
# 使用
class MySearchView(SearchView):
    '''重写SearchView类'''
    def create_response(self):
        page = self.request.GET.get('page')
        # 获取搜索结果
        context = self.get_context()
        data_list = []
        for sku in context['page'].object_list:
            data_list.append({
                'id':sku.object.id,
                'name':sku.object.name,
                'price':sku.object.price,
                'default_image_url':sku.object.default_image_url,
                'searchkey':context.get('query'),
                'page_size':context['page'].paginator.num_pages,
                'count':context['page'].paginator.count
            })
        # 拼接参数, 返回
        return http.JsonResponse(data_list, safe=False)
        
# 路由注册
url(r'^search/$', views.MySearchView()),
# 可以在 dev.py 中添加如下代码, 用于决定每页显示数据条数: 
HAYSTACK_SEARCH_RESULTS_PER_PAGE = 5
```



+ django分页器paginator的使用

```
# 导入: 
from django.core.paginator import Paginator, EmptyPage


class ListView(View):
    """商品列表页"""

    def get(self, request, category_id):
        """提供商品列表页"""
        # 当前页码:
        page_num = request.GET.get('page')
        # 每页数量:
        page_size = request.GET.get('page_size')


        # 判断category_id是否正确
        try:
            # 获取三级菜单分类信息:
            category = GoodsCategory.objects.get(id=category_id)
        except GoodsCategory.DoesNotExist:
            return http.HttpResponseNotFound('GoodsCategory 不存在')


        # 查询面包屑导航
        breadcrumb = get_breadcrumb(category)


        # 排序方式:
        sort = request.GET.get('ordering')
        try:
            skus = SKU.objects.filter(category=category,
                                      is_launched=True).order_by(sort)
        except SKU.DoesNotExist:
            return http.HttpResponseNotFound('GoodsCategory 不存在')

        paginator = Paginator(skus, 5)# 先拿到分页器对象，第一个参数：对象列表，第二个参数：每页显示的条数
        # 获取每页商品数据
        try:
            page_skus = paginator.page(page_num)# 取某一页，返回一个对象
        except EmptyPage:
            # 如果page_num不正确，默认给用户404
            return http.HttpResponseNotFound('empty page')
        # 获取列表页总页数
        total_page = paginator.num_pages

        # 定义列表: 
        list = []
        # 整理格式: 
        for sku in page_skus:
            list.append({
                'id':sku.id,
                'default_image_url':sku.default_image_url,
                'name':sku.name,
                'price':sku.price
            })
        # 拼接传给前端的数据: 
        dict = {
            # 面包屑导航
            'breadcrumb': breadcrumb,  
            'list':list,
            'count':total_page
        }
        # 把数据变为 json 发送给前端
        return http.JsonResponse(dict)
```



