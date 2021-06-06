<h1 align="center">DBJ大宝剑 🗡</h1>

![](https://img.shields.io/badge/ReaTeam-%E6%AD%A6%E5%99%A8%E5%BA%93-red)![](https://img.shields.io/badge/license-GPL--3.0-orange)![](https://img.shields.io/badge/version-1.0.1-brightgreen)![](https://img.shields.io/badge/author-wintrysec%20%E6%B8%A9%E9%85%92-blueviolet)![](https://img.shields.io/badge/WgpSec-%E7%8B%BC%E7%BB%84%E5%AE%89%E5%85%A8%E5%9B%A2%E9%98%9F-blue)

![](https://gitee.com/wintrysec/images/raw/master/banner.jpg)

## 概述-信息收集和资产梳理工具

**项目处于测试阶段，代码会时常更新，各位师傅点个`Star`关注下**

```bash
技术栈如下

前端   Layui
服务端 Python-Flask
缓存   Redis
持久化 MongoDB
```

## 功能介绍&使用教程：

**bilibili视频地址（还没录制，哈哈哈）**

## 功能简介

### 企业组织架构查询

![](/data/readme/enscan1.png)

1、爬虫从爱企查查询企业的组织架构，一级单位、二级单位的首页和邮箱等信息

2、根据企业的公司名称whois反查域名、ICP备案反查域名（这里查到的是根域名）

所有一级单位二级单位都会进行ICP反查

先点查询架构结果出来后再点查询域名，域名结果需要自行去重

3、所有查询到的数据会在后台命令行输出预览

![image-20210310143948010](https://gitee.com/wintrysec/images/raw/master//image-20210310143948010.png)

4、所有查询到的数据会存入redis缓存数据库，并且可导出为CSV

点击右上角admin（用户菜单里的清除缓存），可清空Redis缓存数据（企业架构、ICON_Hash、POC漏扫等模块的结果都会清除）


### 子域名资产梳理

**这块下任务的格式注意下**

```bash
#填写一级域名
baidu.com
```

查询根域名相关的子域名资产

子域名会自动识别CDN，帮助你定位目标真实IP和C段。

### IP资产（Web资产）

**这块下任务的格式注意下**

```bash
#填写单个IP 或 C段
111.222.333.444
111.222.333.444/24
```

原理：调用FOFA数据（节约时间和VPS、代理池等资源）

扫描单IP或C段中Web应用，并识别Web指纹

![image-20210311161144314](https://gitee.com/wintrysec/images/raw/master//image-20210311161144314.png)

此模块的特点：

1、无分页，一拉到底

开着Burp，点一个链接测试一个，不用老去翻页了

2、指纹索引

统计出有哪些指纹命中了，点击相关指纹则下边的资产也只显示与指纹相关的资产

![image-20210311161921828](https://gitee.com/wintrysec/images/raw/master//image-20210311161921828.png)

3、导出URL

导出全部URL，或根据指纹索引匹配资产导出URL，然后扔到别的工具或漏扫里边跑

![image-20210311161942904](https://gitee.com/wintrysec/images/raw/master//image-20210311161942904.png)

### Web指纹识别

Web指纹识别时并未发送恶意请求所以无需代理，这样速度还快

指纹库在`flaskr->rules.py`，以一个`Dict`的形式存在，python中字典的索引比列表(list)快

收录常见且存在高危漏洞的应用指纹，不断更新中~

指纹的每个特征用 "|" 分割开来，前后不能有空格

![image-20210311170057173](https://gitee.com/wintrysec/images/raw/master//image-20210311170057173.png)

**指纹识别的速度配置**

文件位置：`flaskr->admin.py->第34行代码处`

```python
# 设置最大线程数
thread_max = threading.BoundedSemaphore(value=305)
```

### ICON_HASH计算

计算图标的哈希值，并自动匹配相关资产（适合瞄准目标时用）

![image-20210311164827806](https://gitee.com/wintrysec/images/raw/master//image-20210311164827806.png)

### POC插件漏扫

漏扫调用的`nuclei`，因为这个漏扫速度快性能好，各位可自行和市面上的相关工具产品对比下

![](/data/readme/pocscan.png)

POC会持续添加上去的.....

## 安装教程

### Docker 安装模式

```bash
git clone https://github.com/wgpsec/DBJ.git # 速度太慢可用gitee
cd DBJ
docker build . -t dbj
docker run -d -p 0.0.0.0:65000:5000 dbj # 映射到65000端口上
```
访问 http://ip:65000 

### 手动安装


一、安装第三方库

推荐使用`Python3.9`以上版本

```bash
pip install -r requirements.txt
```

二、安装配置数据库

> 先安装MonGoDB和Redis

```bash
#配置MongoDB
mongo
use webapp
db.createCollection("tasks")
db.createCollection("webs")
db.createCollection("subdomains")
db.createCollection("user")
db.user.insert({uid:1,username:'admin',password:'admin'})
exit
```

三、启动应用

```bash
#Windows系统 
点击start.bat

#Linux系统
sh start.sh
```

然后打开浏览器访问 IP:5000 登录即可（默认账户密码admin/admin，进去自己改）