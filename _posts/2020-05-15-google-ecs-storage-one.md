---
layout: post
title: 谷歌ECS和存储使用教程一
categories: GoogleCloud
description: 谷歌ECS和存储使用教程
keywords: Google
topmost: false
---

关于谷歌云平台的账号申请与结算功能开通这里就不提了，虽然不提，但这2个是使用谷歌ECS和存储的先决条件，如果解决不了，请自己阅读文档或使用你喜欢的搜索引擎自行查找。

## 虚拟机实例使用

### 创建实例

1.登录谷歌云平台https://console.cloud.google.com/，刚进入时系统会引导创建一个项目，因为后面所有的操作都建立在“项目”这个概念基础之上的。创建项目的地址是：https://console.cloud.google.com/projectcreate，这里创建演示项目名为example

![](https://blog.heping.tech/Images/GoogleCloud/1.png)

2.进入工作台https://console.cloud.google.com/home/dashboard，选择刚刚创建的项目example

![](https://blog.heping.tech/Images/GoogleCloud/2.png)

3.选择进入虚拟机实例界面

![](https://blog.heping.tech/Images/GoogleCloud/3.png)

![](https://blog.heping.tech/Images/GoogleCloud/4.png)

4.稍微等待1分钟左右，点击“创建”，并在创建实例界面中，根据实际需要填写实例信息和选择机器配置设置，我这里选择的是CentOS系统实例，因相对于Windows后面的运维操作可能会复杂一些。当然了，对于Windows我这里也简单提一下，如果创建的是Windows实例，完成实例创建之后在实例列表中可以找到设置远程桌面和登录密码，之后的操作就十分简单了。

对于我这里创建的实例，我直接在配置中的防火墙选项中允许HTTP和HTTPS流量，这样便省去了后面再专门为实例设置

![](https://blog.heping.tech/Images/GoogleCloud/5.png)

5.创建完成

![](https://blog.heping.tech/Images/GoogleCloud/6.png)

6.到这里十分简单，如果后续的运维操作可以直接通过上图中的SSH使用浏览器直接登录实例，那到这里Google ECS你已经会使用了

### SSH登录

谷歌提供了多种远程登录Linux实例的方法，比如直接通过浏览器SSH、使用gcloud命令，我这里提供一种相对安全而且方便的本地SSH登录的方式

条件：你的登录的客户端需要也是Linux主机，或者使用类似于Cygwin之类的工具

1.使用ssh-keygen生成密钥对，出现提示根据需要输入相关内容，我这里直接一路enter

```
ssh-keygen -t rsa -f .ssh/example -C admin
其中.ssh/example是密钥存放位置 admin是用户名,无须提前在服务器实例中创建这个用户
```

2.生成成功后执行cat .ssh/example.pub获得公钥内容，打开谷歌云实例控制台中的SSH密钥添加界面

![](https://blog.heping.tech/Images/GoogleCloud/7.png)

添加

![](https://blog.heping.tech/Images/GoogleCloud/8.png)

3.在本地使用ssh登录

```
ssh -i .ssh/example admin@你创建的实例公网ip
```

4.OK, Well done !!!

## Google Storage使用

### Bucket

1.通过Google云工作台打开Google Storage - 浏览器项

![](https://blog.heping.tech/Images/GoogleCloud/a.png)

最开始我一直在谷歌文档中找谷歌提供的对象存储在哪，每每看到这个叫“浏览器”的东西的时候，我本能的觉得这个应该不是，后台才发现原来它就是类似于国内云商提供的OSS。它之所以叫“浏览器”也许只是谷歌机器翻译的功能吧，这里只是说个笑话。

2.创建存储分区，根据需要填写bucket名称，这个名称如果后续涉及开发，还会用到，调协存储类别和访问权限，我这里希望把它当作普通文件服务器使用所以存储类别选择Standard，访问权限我希望对不对的文件对象使用不同的访问权限，所以这里选择傅精细控制

![](https://blog.heping.tech/Images/GoogleCloud/b.png)

3.搞定，到这里就搞定了Bucket创建，在浏览器你可以做查看文件，上传修改文件操作了。

4.当然，对于我们开发人员来说，只到这里肯定是不够的，关于文件上传和文件访问权限控制我这里不说了，具体请参考文档，都有。https://cloud.google.com/storage/docs?hl=zh_CN

### 自定义域名

对于大多数情况而言，做完上面的工作，关于OSS的使用可能已经足够了。但如果到此为止，用户在访问Cloud Storage上的文件里，需要使用谷歌提供的域名地址，长！暴露！。另外，如果然后在Storage上托管一些静态资源，也是需要使用自定义域名的。那么怎么进行呢？

1.创建存储分区，并且分区的名称使用你想指定的自定义域名，我这里使用file.heping.tech

![](https://blog.heping.tech/Images/GoogleCloud/c.png)

这里谷歌要求先验证您是否有权使用此网域。那这里也顺便提一个验证流程吧

- 打开https://search.google.com/search-console，使用你的谷歌账号登录，如果没有添加资源，登录后会自动弹出以下内容

  ![](https://blog.heping.tech/Images/GoogleCloud/d.png)

  如果曾经添加过，通过式上角的下拉菜单中选择添加资源

- 输入你的自定义域名，我的是file.heping.tech，然后下一步，会出现如下内容

  ![](https://blog.heping.tech/Images/GoogleCloud/e.png)

- 它要求你进入你的域名解析平台上添加一条txt类型的解析记录，添加完成后，完成验证

  需要注意的是，解析记录添加后可能需要一段时间后才会生效

- 做完以上步骤，便是向谷歌证明了你拥有域名的控制权

2.存储分区创建完成后，在域名解析平台中添加一个cname解析记录把你的自定义域名指向c.storage.googleapis.com

3.好了，到这里就完成了自定义域名绑定了，具体可以参考谷歌官方文档https://cloud.google.com/storage/docs/hosting-static-website

4.需要注意的是谷歌无法像国内的一个云商一样提供静态资源SSL证书托管功能，所以虽然通过谷歌域名访问资源是https的，但通过自定义域名，只能使用http

### 跨域设置

资源跨域访问是什么，我这里就不需啰嗦了？有人也许会问，对于存放在谷歌云上的资源什么时候会存在跨域问题呢？那我简单举一个栗子吧，你的业务网页中需要加载存放在谷歌云上的一个静态网页时，你就会碰到跨域问题

需要注意的是谷歌云存储的跨域设置也是存储级别的。而且云控制台中并没有提供跨域设置的图形化入口。但提供了配置跨域资源共享的三种方式。我这里只演示其中的gsutil命令行工具设置的方式，另外2种方式，如果你需要请自行研究实现，具体文档地址：https://cloud.google.com/storage/docs/configuring-cors

0.对于谷歌云的CentOS实例默认情况下已经完了了gsutil命令行工具集，并且安装好了Google Cloud SDK, 它依赖于python运行环境。所以在服务器机器上可以直接运行gsutil命令行，进行相关设置

1.查看当前bucket设置情况

```
gsutil cors get gs://example-bucket
```

2.编写cors配置文件cors.json

```
[
    {
      "origin": ["*"],
      "responseHeader": ["Content-Type"],
      "method": ["GET", "HEAD", "DELETE"],
      "maxAgeSeconds": 3600
    }
]
```

3.修改bucket跨域配置

```
gsutil cors set cors-json-file.json gs://example-bucket
```

4.在进行跨域配置的过程中，由于登录的账号可能没有资源控制权限而报以下错误

```
AccessDeniedException: 403 Insufficient Permission
```

解决办法：

- 在云机器上执行gcloud auth login
- 根据提示输入Y后会生成一个登录的完成链接，将它复制后粘粘到你的浏览器地址栏中，页面会跳转到一个Google Cloud SDK授权界面，授权让成功后会生成一段token
- 把token复制粘贴回命令行界面，完成
- 这之后便可以正常设置跨域了。