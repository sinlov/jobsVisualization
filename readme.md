# jobs
可视化筛选你中意的工作。

Inspired by [bokeh example app movies](https://github.com/bokeh/bokeh/tree/0.11.1/examples/app/movies)

# Usage(local/dev)
*  git clone  https://github.com/wwj718/jobsVisualization
*  virualenv jobs_env
*  . jobs_env/bin/activate
*  pip install -r  jobsVisualization/requirements.txt
*  bokeh serve --show  jobsVisualization


# demo
jobs.just4fun.site/sanchi

![demo](./jobs.gif)

# 开发环境
mac OSX python2.7

# 依赖
*  原始数据：[lagouSpider-newest.csv](https://github.com/wwj718/jobSpider/blob/master/lagouSpider-newest.csv)
    *  来自于我的爬虫项目:[jobSpider](https://github.com/wwj718/jobSpider)
*  pandas
*  bokeh 0.11.1


# 浏览器兼容性
*  在max osx下dev状态的应用兼容对html5友好的浏览器：chrome/firefox/safiri
*  在ubuntu14.04下部署的应用，仅支持chrome，尚不清楚原因，似乎是静态文件出现了乱码（怀疑是nginx的缘故） 

# todo
*  封装到docker，采用-v把目录映射进去
    *  自行编译dockerfile（基于pandas）,看看其他python库如何编译
	    *  https://github.com/Jim-Holmstroem/docker-bokeh
	    *  如何映射目录


### 优化
计算在server，交互在client，往返传输数据太大,响应迟钝

### 策略
1.  思路1：原始数据存在浏览器端，远程用pandas做分析，只传输item_id
2.  思路2：爬虫的爬取结果允许下载，项目跑在本地，远程响应速度太慢



# 生产环境/部署
*  ubuntu12.04
*  nginx
*  参考：
    *  http://bokeh.pydata.org/en/latest/docs/user_guide/server.html#userguide-server-deployment-automation
    *  https://github.com/bokeh/demo.bokehplots.com/tree/master/bokeh

收集静态文件：

```
cp -r `bokeh info --static` /home/wwj/jobsVisualization/static
```


启动进程：`bokeh serve  jobs --host jobs.just4fun.site --port 5100`

nginx反向代理：

```nginx
server {
    listen 80 ;
    server_name jobs.just4fun.site;

    access_log  /tmp/bokeh.access.log;
    error_log   /tmp/bokeh.error.log debug;

    location / {
        proxy_pass http://127.0.0.1:5100;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_http_version 1.1;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $host:$server_port;
        proxy_buffering off;
    }
    location /apps/static {
                alias /home/wwj/jobsVisualization/static;
            }

}
```




# 代码风格
采用gogole的yapf来统一代码风格

yapf -i filename.py
