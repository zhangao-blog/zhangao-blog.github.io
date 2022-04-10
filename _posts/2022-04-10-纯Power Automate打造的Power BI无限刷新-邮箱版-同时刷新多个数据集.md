---
layout:     post
header-img: img/post-bg-parefreshpowerbi.jpg
catalog: true
tags:
    - PowerAutomate
    - PowerBI
---

公众号：PowerBI生命管理大师学谦，同步更新，敬请关注

![image-20220410142819091](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220410142819091.png)

powerbi的刷新问题一直是很多朋友关注的，多年以来，多种无限刷新的办法提供给大家，像：插件、selenium、python等，但是这些办法有一个大前提，必须电脑开机。

直到使用了powerautomate的出现，打破了这一束缚。现在你无需开机即可实现这一切，甚至这一切根本不需要你自己来布置。你只需要按照要求做就可以了。



之前的forms填表方式进行无限刷新已经有几十位朋友进行了测试，对于其中反映出来的问题都一一进行了修正。

有朋友反映每次只能提交只能刷新一个报告，能不能一次性提交刷新多个数据集？

而且每次都要登录一个陌生账号才能登录，比较麻烦，能不能提供一个简单的办法？

今天就来解决它！

不过提交办法从forms变为了邮箱提交。

基本过程还是差不多：

首先登录Power BI首页：

国际版**https://app.powerbi.com/home**

世纪互联版**https://app.powerbi.com/home**

鼠标右键点击“检查”，选择**网络**，然后F5刷新页面，点击第一个请求，也就是这个**home**：

![image-20220410142846278](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220410142846278.png)

一般默认会选择**标头**，此时往下滑找到**请求标头**，在里面找到**cookie**，在cookie的值上右键选择复制值。

此时，在电脑的任何位置上新建一个Excel文件，注意是xlsx格式的，打开：

在A1、B1、C1单元格上分别写上cookie、数据集名称和刷新周期：

![image-20220410142903914](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220410142903914.png)

然后将刚才复制的cookie写在A2单元格，如上图。

然后将想要刷新的数据集名称写在B2，如果有多个，就继续往下写。

每一个数据集的C列都填上刷新周期，也即是间隔，单位还是秒。300的意思就是5分钟，同理600代表10分钟。时间间隔一般不要太小，比如30以下，否则会刷新不成功，或者刷新一段时间就停止了。

文件命名可以随意，一般备注数据集的名就行了。

然后，将此文件作为附件发送至邮箱：

**ultimate_refreshes@outlook.com**

主题和内容都可以不写。这是一个自动处理的邮箱。

剩下的操作就交给Power Automate来处理了：（一个整整写了三天，又来来回回改了几十次的flow）

![截图](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/%E6%88%AA%E5%9B%BE.png)



