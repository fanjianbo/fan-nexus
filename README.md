# fan-nexus
基于nexus的maven私有仓库创建

## 写在前面
最近看自己的github就觉得烦.....很多现在看来没用和小儿科的东西，一气之下竟然全都删了....

**悲剧的是，删完才发现github上的repository是无法恢复的.....**

嗯，善有善报，恶有恶报，天理循环，我爱钞票。

删了就删了吧，是福不是祸，是祸躲不过。借这个契机，复习一下好了 T_T


## 一、Nexus简介
### 1.1 Basic
Nexus: ['nɛksəs]  
n. 关系;连接，联系

官网地址:https://www.sonatype.com/  
打不开的小伙伴请找个VPN，写代码没VPN怎么行...

打开官网可以看到sonatype有五个产品  
* Nexus Firewall
* Nexus Lifecycle
* Nexus Auditor
* Nexus Repository Pro
* Nexus Repository OSS

我们用最后一个**Nexus Repository OSS**，因为它是***免费的***，\^_^

### 1.2 特性 Nature
* **Store 存储**  
能为每一个单独的组件单独创建一个源码库

* **Cache 缓存**  
帮助你缓存远程仓库中的依赖，如*maven中心仓库* 或 *阿里maven镜像仓库*  
其实就是一个另一种**maven本地仓库**，只不过将位置从你的电脑转移到了私有服务器上(如阿里云ECS)  
顺便打个广告：阿里云大使连接，点击链接购买阿里云产品有优惠哦...  
[阿里云产品优惠连接](https://promotion.aliyun.com/ntms/act/ambassador/sharetouser.html?userCode=yi00hzgl&utm_source=yi00hzgl)

* **Adapt 多样的适应性**  
Nexus不仅可以当做maven的仓库，它几乎能胜任任何类型的文件的存储仓库角色。除java之外，也可以当做js、scala等等等其他语言的仓库(虽然我没有试过,但了解它大概的原理后相信大家都能明白)

![](https://www.sonatype.com/hs-fs/hubfs/nexus%20vs%20artifactory.png?t=1505310412913&width=642&height=647&name=nexus%20vs%20artifactory.png)

* **Scale 可扩展的**  
你可以将nexus扩展为多节点服务实例，用户也可以是多个

### 1.3 Version 版本
至上传时间为止，sonatype官网主要提供两个版本的Nexus：**2.x** 和 **3.x**  
这两个版本的区别笔者其实是有点懵的，感兴趣或了解的朋友麻烦不吝赐教: [jianbo.fan2016@gmail.com]()  
本着一切向~~钱~~看的原则，我们以3.x版本为例，在阿里云ECS上创建一个Nexus私有maven仓库

## 二、Why
**简单来说就是方便,爽! 已经了解的同学请跳过本节**  
**简单来说就是方便,爽! 已经了解的同学请跳过本节**  
**简单来说就是方便,爽! 已经了解的同学请跳过本节**  
**简单来说就是方便,爽! 已经了解的同学请跳过本节**  

如果不使用仓库管理你的代码——**特别是在你有一个团队的时候**——将会产生大量**低效**的操作。如今的团队大量使用gradle、maven等构建project，若每个人都用到**Central Repository**去获取依赖的话，(**特别是在伟大的天朝**)，那其实使用构建工具获得的优势已经荡然无存了。我在2016年(天啊，2016年...)的时候甚至见到过一个*可爱*的小伙子问我：为什么要用maven，它那么慢？ 我当时的表情是酱事儿的:What?  
![](https://github.com/fanjianbo/picture/blob/master/res/what_haha.png?raw=true)

虽然maven获取依赖包的速度可以通过使用国内的镜像来提高，但我还是强烈建议应该尽快(如果没有的话)为你的team创建一个私有的maven repository，用于**将team的project模块化，特别是在微服务架构横行的今天。*或者至少* 你可以将你的commons-utils、cache、configuration等功能单独抽成独立的artifact供大家使用**

最后，引用Nexus3 document的一句话：**Thus us，once you start using a Nexus Repository Manager, you'll wonder how you ever functioned without it**

## 三、安装

### 3.1 环境要求
JDK1.8以上即可，下方的小版本请忽略
```
$  java -version
java version "1.8.0_60"
Java(TM) SE Runtime Environment (build 1.8.0_60-b27)
Java HotSpot(TM) 64-Bit Server VM (build 25.60-b23, mixed mode)
```
openJDK是不支持的，请注意

### 3.2 安装方式
> 以下安装方式任选其一即可。

**我们建议避免以root用户或类似的特权用户身份运行存储库管理器,因为这种做法对主机操作系统造成严重的不必要的安全风险。  
相反，我们建议遵循系统管理最佳实践，并使用仅具有最低必需访问权限的特定于服务的用户。**

为nexus创建单独的用户，group随你喜欢
```
groupadd admin
useradd -g admin nexus
passwd nexus
```

#### 3.2.1 使用docker安装Nexus(**推荐**)  
docker安装请参考:[fan-docker]()

安装：
```
docker run -d -p 8081:8081 --name nexus sonatype/nexus3
```

#### 3.2.2 在Linux手动安装  
//todo