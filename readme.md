#Joker

## what is joker
joker 是一个轻量级的微服务容器，专为微服务开发而生。
相较于tomcat加载war包，joker加载的entry下的jar包始终保持在一个classloader下面，借助于vertx，他们之间可以通过eventbus、服务发现等进行通讯

![image](https://raw.githubusercontent.com/jsenjobs/joker/master/third/joker-entry-server.png)

## Get Start
## Joker目录结构：
#### bin/ 
命令工具 其中joker.sh是核心命令工具 joker.sh 需要joker-core-plugin-manager 插件的支持
#### conf/ 
核心配置文件 目前 该目录下所有配置文件都不是必须的，joker启动时会加载所有properties文件到一个Map下，verticle可以通过config()获取该属性，joker.properties 配置集群信息，joker-core-plugin-manager服务器端口，entry.server.properties文件配置 joker-core-plugin-entry-server插件的属性，用于上传和下载文件，在joker下载文件的joker-core-plugin-manager插件中也会读取这里的地址属性来下载文件。config.json 是配置文件服务器插件的配置文件。除了集群信息，所有属性文件都是配置插件使用的，不是joker必须的。
##### conf/cluster 
配置 集群manager的属性
#### entry/ 
类似于 tomcat的webapps 这里存放jar包、js格式的微服务模块，一般来说，一个jar包，一个js文件就应该是一个可独立运行的微服务。放入这里的entry joker将自动检测并加载，建议如果entry包更新，可以重启服务器，如果添加jar包，不用重启，由于jar包的加载没有顺序（还未支持），所以可能还要考虑服务间的依赖关系。
#### joker-boot.jar
joker bootstrap 包
#### lib/
joker 运行的依赖包
#### logs/
joker 日志
#### static/
joker 静态资源目录


 

## Version
#### version 0.0.1-SNAPSHOT 
实现动态加载Jar包 自动从服务器下载需要的Jar包
####  version 0.0.2-SNAPSHOT 
将Entry server，和文件下载删除API从joker核心提出，作为两个独立的entry模块，为Entry server 提供默认界面，静态页面在static中
### version 0.0.3-SNAPSHOT
一、将所有非核心代码从joker-core剥离出来
二、实现对静态资源的支持，静态资源必须在jar包的webroot目录下才会在加载的时候被解压，默认解压目录是static，webroot也可放在entry目录，但不建议这样做。建议entry只实现json格式API不要提供任何网页信息，建议直接使用react、angular等实现静态页面，与entry交互。
三、js脚本支持，可以查看vertx上java加载其他语言的examples实现js语言的verticle，部署方式直接拖入即可发现，同jar包保持一致，建议用vertx的trans工具全部转换成jar格式统一加载。
四、集群模式支持，joker.properties添加core.cluster和core.cluster.manager属性，core.cluster为yes表示使用集群模式，core.cluster.manager为集群模式的manager，默认支持hazelcast和zookeeper。
五、vertx-boot-class 节点增加instances属性，对应部署时的副本数量，未设置默认为1个副本。



## deploy
cd bin
./joker.sh install
./startup.sh
./shutdown.sh
如果要查看joker执行情况，请使用 ./joker.sh run命令

集群模式：
一、修改core.cluster为yes
二、在2181端口启动zookeeper
三、启动joker

joker的核心插件：joker-core-plugin-entry-server-1.0.3-SNAPSHOT.jar entry上传保存到这里， 数据库地址在config.json的mysql1中设置。启动后可访问9090查看网站。
joker-core-plugin-manager-1.0-SNAPSHOT.jar 调用上面的API可以从entry-server下载jar到entry目录自动部署，目前还未完善，不建议使用。joker的stop命令也依赖于这个插件。
joker-core-plugin-entry-server的Mysql数据库：在third下有sql文件


