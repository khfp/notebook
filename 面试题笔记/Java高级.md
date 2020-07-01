<!--
 * @Author: your name
 * @Date: 2020-06-11 16:22:31
 * @LastEditTime: 2020-06-21 19:32:59
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \GitHubnotebook\面试题笔记\Java高级.md
--> 

## linux系统常用服务类相关命令

1. service(centos6)
   - 注册在系统中的标准化程序
   - 有方便统一的管理方式(常用的方法)
     - service 服务名 start
     - service 服务名 stop
     - service 服务名 restart
     - service 服务名 reload
     - service 服务名 status
   - 查看服务的方法 /etc/init.d/服务名
   - 通过chkconfig命令设置自启动
     - 查看服务 chkconfig --list|grep xxx
     - chkconfig --level 5 服务名 on
2. service(centos7)
   - 注册在系统中的标准化程序
   - 有方便统一的管理方式(常用的方法)
     - systemctl start 服务名(xxx.service)
     - service stop 服务名(xxx.service)
     - service restart 服务名(xxx.service)
     - service reload 服务名(xxx.service)
     - service status 服务名(xxx.service)
   - 查看服务的方法 /usr/lib/systemd/system
   - 查看服务的命令
     - systemctl list-unit-files
     - systemctl --type service
   - 通过systemctl命令设置自启动
     - 自启动systemctl enable service_name
     - 不自启动systemctl disable service_name

## git分支相关命令，实际应用

1. 创建分支
   - git branch <分支名>
   - git branch -v 查看分支
2. 切换分支
   - git checkout <分支名>
   - 一步完成 git checkout -b <分支名>
3. 合并分支
   - 先切换到主干 git checkout master
   - git merge <分支名>
4. 删除分支
   - 先切换到主干 git checkout master
   - git branch -D <分支名>

## JVM垃圾回收机制，GC发生在JVM哪部分，有几种GC，他们的算法是什么

1. 发生在堆(heap)
2. 常用算法
   1. 引用计数法
   2. 追踪回收算法
   3. 压缩回收算法
   4. 复制回收算法
   5. 按代回收算法

## elasticsearch和solr的区别

- 都是基于lucene搜索服务器基础上开发，一款优秀的，高性能的企业级搜索服务器。(他们都是基于分词技术构建的倒排索引的方式进行查询)
- 开发语言:java语言
- 诞生时间: solr:2004 es:2010
- 区别：
  1. 当实时建立索引的时候，solr会产生io阻塞，而es不会，es的查询性能要高于solr。
  2. 在不断动态添加数据的时候，solr的检索效率会变得低下，而es则没有变化。
  3. solr利用zookeeper进行分布式管理，而es自身带有分布式系统管理功能。solr一般都要部署到web服务器上，比如tomcat。启动tomcat的时候需要配置与solr的关联。(solr的本质是一个动态的web项目)
  4. solr支持更多的格式数据(xml,json,csv)，而es仅支持json文件格式
  5. solr是传统搜索应用的有力解决方法，但是es更适用于新兴的实时搜索应用服务
     - 单纯的对已有数据进行检索时，solr效率更高
     - solr官网提供的功能更多,而es本身更注重核心功能，高级功能多由第三方插件完成。

## 单点登录实现的过程

单点登录：一处登录，多处使用。
前提：单点登录多使用在分布式系统中

## 购物车实现过程

购物车：
   - 购物车跟用户的关系
     - 每个用户都有对应的购物车
     - 单点登录一定在购物车之前
   - 跟购物车有关的操作
     - 添加购物车
       - 未登录状态
         - 将数据保存在Redis中(京东)
         - 将数据保存在cookie中(自己开发项目)
       - 登录状态
         - Redis缓存(读写速度快)
         - 存在数据库中
     - 展示购物车
       - 未登录状态的展示
         - 直接从cookie中展示
       - 登录状态的展示
         - 合并数据库 redis cookie中的购物车数据

## 消息队列

背景：在分布式系统中是如何处理高并发
由于在高并发的环境下，来不及同步处理用户发送的请求，则会导致请求发生阻塞。比如说，大量的insert,update之类的请求同时到达数据库Mysql,直接导致巫术的行锁表锁，甚至会导致请求堆积。
消息队列的不确定性：延迟队列，轮询技术查询
