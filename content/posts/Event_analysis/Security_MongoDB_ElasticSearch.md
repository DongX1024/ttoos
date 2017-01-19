---
categories:
- 开源
- 感悟
date: 2017-01-16T11:40:16+08:00
description: "如果将开源软件比喻为防盗门的话，使用开源软件的人经常将钥匙插在钥匙孔上，任贼随意出入家门。责任算谁的？损失算谁的？大多数时候，安全是一种意识！"
keywords:
- Open Source
- Culture
- Reading
- News
tags:
- 每周精选
- 开源之道
title: 也谈开源软件的安全——记MangoDB、ElasticSearch勒索事件
url: ""
---
## 事件回顾

2017年元旦过去没几天，绝大多数人还沉浸在总结过去一年得失，痛下决心准备来年大干一场了，可偏偏就在6号这一天发生一些很不愉快的事情，尤其是对于数据库管理员来说，那就是有一批勒索犯，声称拿到了使用MangoDB的数据，请支付0.25比特币赎回。据InfoQ中国的报道[MongoDB勒索软件已波及上万数据库](http://www.infoq.com/cn/news/2017/01/MongoDB-Tens-thousands-database)：
> 此番针对MongoDB的勒索行为最早是由GDI Foundation的安全研究人员Victor Gevers在2016年12月27日发现的，在这之后影响陆续扩大，目前至少有五个不同黑客组织控制了上万个数据库实例。

无独有偶，在MangoDB的硝烟还未散去的时候，这些勒索犯又盯上了大名鼎鼎的ElasticSearch，不过这次和不同的地方是每次勒索0.2个比特币。据台湾网站ITHome报道：[大風吹，MongoDB勒索手法吹到ElasticSearch伺服器了](http://www.ithome.com.tw/news/111196)
> 根據白帽駭客 Victor Gevers的統計，短短的3天就有2515個ElasticSearch伺服器上的資料遭移除與勒索，而網路上曝露在此一風險中的ElasticSearch伺服器數量則超過3.4萬個。目前已知有兩組人馬鎖定ElasticSearch。

## MongoDB 和 ElasticSearch 的共同之处在哪里？

MongoDB是一款跨平台的面向文档的非关系型数据库软件程序，MongoDB使用类似JSON的文档和模式。

而ElasticSearch是一款基于文本的搜索软件，采用Java语言编写，是Apache基金会的顶级项目之一。

它们二者之间有什么共同之处了呢？ 那就是均是使用非常广泛的开源软件，背后都有相应的商业公司支撑。为什么是开源软件？甚至安全界著名的大神：[Catalin Cimpanu](https://twitter.com/campuscodi)警告说：接下来可能受到波及的还有：Apache CouchDB、Redis、以及 Memcached等。这些所列出的开源软件甚至还不胜MongoDB安全。

开源软件到底惹谁了？ 其实，和开源没有任何的关系，而是使用这些开源软件的用户本身对于安全是疏忽大意所导致。

## 真的是这些勒索犯技术高超吗？

在Hacker的伦理当中，是不会去做违背社会正义的事情的。一般是脚本小子或者是破坏者、反社会人士才会做这些事情。哦，我们先暂时将这些搁置，不拿道德大棒打他们。我们先来审视一下这些人在上述事件中是如何得手的。

### MongDB 默认配置

**因为在安装原生的MongoDB时，默认条件下，MongoDB是不启用认证和访问控制功能，说白了就是MongoDB不会提示你设置账户密码，也不会提示你任何ip都能访问你的数据库。**

简书上的**辉仔SAMA**还重现了如何获得MongoDB的数据的简单方法：[谈谈最近MongoDB数据库勒索事件](http://www.jianshu.com/p/48d17a69e190)，随意的利用一些扫描工具，很轻松的就获取到了数据，居然还是随机的取了个地址。

完全没有用到什么端口扫描、绕过防火墙、寻找内网跳板、寻找过期的操作系统、利用缓冲区溢出获得超级用户权限、现场编写C语言、汇编语言代码......点击几下鼠标就完成了。将原先“酷炫”的入侵手段大跌眼镜。

用一句这些“脚本小子”的话就是 **“你tm在想什么？？？让数据库在公网上裸奔？？？”。**

### ElasticSearch 的问题出在哪里？

和上述的MongoDB几乎同出一辙，勒索犯扫描出暴露在公网上的ElasticSearch集群，然后利用暴力破解或者是密码字典来登陆后台。 听着是不是很不可思议？根本不是什么软件漏洞、没有检测边界所导致。仅仅是主动将后门告诉全世界而已，然后还无人看护。

## 安全意识须提高

软件的灵活性和安全性，似乎是天然的对头，足够的灵活，可能就损失一些安全上的特性。而开源的一大特征就是不去强制用户采用一种思路、一种方法。就是所谓的Unix哲学中的：“提供机制，而不是策略”。所以，安全的防护需要靠自己。

我们可以在[专家给出的意见中看到](http://code972.com/blog/2017/01/107-dont-be-ransacked-securing-your-elasticsearch-cluster-properly)：监听私有IP、使用代理、修改默认端口、禁用旧的脚本等等，一些常见的安全防护策略。中国有句俗话：“林子大了，什么鸟都有。” 防人之心不可无，所以一些最基本的安全措施是要做的。

### 安全的误区

我们经常听到这样的声音：“Linux／类Unix 很安全。” 是的，这样的话倒退二十年可能是真的，因为那时候，使用这些操作系统的人都是道德高尚有节操的少数人。现在时代变了，Linux 甚至是大学的必修课了，鼓捣些脚本也是引起青少年兴趣的手段。所以，要让Linux很安全（因为MongoDB、ElasticSearch通常／默认运行在Linux操作系统下。），必须自己去手动配置，比如设置防火钱、修改默认端口、设置SElinux规则等。

### 不要犯懒

安全通常是因为疏忽大意，在自己的实验室里的环境，习惯于“裸奔”，比如执行

```
systemctl stop firewalld
```

或者是

```
selinux = Disabled
```
是安装完成操作系统首先执行的操作。**请不要将这些习惯带到实际的工作、生产环境中。**

### 制定安全流程

宽松的约定也许对于企业未必真的有效，那么就应该去制定一系列的流程来规范。暴露在Internet上的服务，必须是有相应的审核的。使用应用软件默认的配置是不被允许的，比如本文开始部分的看起来蛮可笑的事件。

## 那些开源软件真正的安全事件

相比于此次勒索事件，开源软件的安全问题，最为著名的莫过于：[heartbleed](https://en.wikipedia.org/wiki/Heartbleed)、[Dirty COW](https://en.wikipedia.org/wiki/Dirty_COW)、[DROWN](https://en.wikipedia.org/wiki/DROWN_attack)等，这些漏洞一旦被利用，那后果才严重了，因为是所有的使用者都会波及，比如OpenSSL 的 heartbleed事件。正因为打击面太广，以至于才惊醒了世人。

在BlackDuck和NorthBridge联合做的2016年开源软件调查如下图显示：

![](https://raw.githubusercontent.com/OCselected/ttoos/master/content/public/OpenSource_used_in_2015.jpeg)

如此巨大的量，也有很多人洞察到了这一点，有开源社区、有商业产品，让我们看看他们现在都有些什么样的成果。

### 所做的一些应对措施

* Linux 基金会成立核心基础设施倡议（CII），针对FOSS的安全性做出一系列的措施，比如Google主导开发的[OSS-Fuzz](https://github.com/google/oss-fuzz)扫描工具就是成果之一。
* Mozilla 出钱资助软件扫描以防止下一个HeartBleed的出现。
* 商业产品如black Duck、WhiteSource、SourceClear等都有相应的解决方案。

## 结语

道高一尺，魔高一丈！这个世界总是在此消彼长中不断的完善，不断的变得更加美好！网络，从其诞生的第一天起，就是人类的“君子协定”，不法分子利用一些规则，而我们做的就是不要被这些人利用来，无论这些人处于什么样的目的。最不应该的就是什么也不做，坐以待毙！所以，谈安全，自己首先要做好第一层防护！
