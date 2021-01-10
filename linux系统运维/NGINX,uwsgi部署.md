# 9.NGINX,uwsgi部署

## 1.uwsgi使用

```
pip install uwsgi#安装
uwsgi --http :9090 --wsgi-file demo.py #运行一个http服务器
uwsgi --http :9090 --wsgi-file demo.py --master --processes 4 --threads 2#四进程,每个进程有2线程

```

```
#可以把命令弄成配置文件形式 yourfile.ini 
[uwsgi]
socket = 127.0.0.1:3031
chdir = /home/foobar/myproject/
wsgi-file = myproject/wsgi.py
processes = 4
threads = 2
stats = 127.0.0.1:9191

#启动
uwsgi yourfile.ini
```

