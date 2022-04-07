Power BI数据回写SQL Server（2）——存储过程一步到位

![img](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvacduvV5NuS96W85vl9qE1pAWI7SiagNerELIeMNNtarI7icTSaLbRP4l9Q/640?wx_fmt=jpeg)

在上一讲：

[Power BI数据回写SQL Server（1）没有中间商赚差价](http://mp.weixin.qq.com/s?__biz=MzI2MDY3NDk1OA==&mid=2247484034&idx=1&sn=d2dcffec82178d3f88c8812cca68006c&chksm=ea674663dd10cf7556a00cfe8e8d150eb426c08de59605d956d97bdd7e5acbeb5fb0335e3052&scene=21#wechat_redirect) 中，



我们讲过，利用循环的方式将PQ中得到的table表逐行导入SQL Server中，有的朋友怀疑这种方式会不会造成数据量较大时运行慢、能耗大的问题，这种顾虑理论上是恰当的，所以今天再介绍一种能够直接一次性导入SQL的办法。



熟悉SQL的同学可能已经想到了——“存储过程”。我们可以通过创建一个存储过程来读取PQ生成的文件，然后解析到数据库中。



用过这两种语言的朋友应该知道，PQ可以将查询结果的table转化为XML二进制文件或者JSON格式，而SQL恰好也能支持这两种文件格式的输入，这就好办了。



*略微扫一下盲：*

*JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式。它基于JavaScript的一个子集；*

*XML（Extensible Markup Language）即可扩展标记语言，Xml是Internet环境中跨平台的，依赖于内容的技术，是当前处理结构化文档信息的有力工具。*

*两者的共同优点是都是文本表示的数据格式，可以跨平台、跨系统交换数据。*



一、XML篇：



首先我们写一个带xml文件参数的存储过程：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaGZViakwNlseG9GNLIs3189hs27oN0ic0ibLia7sChicEfTYeicNt6cUjJDB4MQS8xe2icL7yLOuudz6hqg/640?wx_fmt=png)



这样我们就可以通过在SQL Server中直接调用这个函数来达到我们预先设定的插入数据的过程。



我们看一下XMLbinary的格式：

<table>
    <row>
        <KeyValue>学谦数据运营</KeyValue>
        <NumberValue>35592</NumberValue>
        <DateValue>2020/3/31</DateValue>
    </row>
</table>



第二步，要将PQ返回的table转为以上的xml格式，我们需要在数据表中添加一列名为binary的自定义列，输入：

=Text.Format
  (                                        
    "<row><KeyValue>#[KeyValue]</KeyValue><NumberValue>#[NumberValue]</NumberValue><DateValue>#[DateValue]</DateValue></row> ",                                        
    [KeyValue=[KeyValue], NumberValue=[NumberValue], DateValue=[DateValue]]
  )



运行：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvacJKBr6fT8aQJO1o1A0vXmbtBt4bk7Prqr6J1fvuV0AaWh7MqJia8vh4g/640?wx_fmt=gif)



这样我们就得到了新的一列：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvacOF6xcib7fLMVa6ucCKibKeCQutuDTwDBTCeTeQHfgpxgdKI3M2ttKtjg/640?wx_fmt=png)



接着，我们只用这一列，将这一列文本前后分别加上一个“table”然后用Text.ToBinary()转为XMLbinary文件：

XmlBinary = Text.ToBinary("<table>" & Text.Combine(AddedCustom[binary]) & "</table>")



运行后我们就得到了一个XML二进制文件：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvacLEb7mbF884wL7EAwuvkgjrmLCz8ial55WqVKfejRCZQck2pQMa0cMeA/640?wx_fmt=gif)



最后，我们要操作的就是将这个文件作为参数传递给SQL Server的存储过程，简单的一行代码：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaGZViakwNlseG9GNLIs3189yIekU6PQJeIdmCXQ4EqMOdVMKfA5nZRV2Yf2ibsY4GzRlnJJicWov29g/640?wx_fmt=png)



运行一下看看效果：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOiaGZViakwNlseG9GNLIs31899LPibBicicxiaFym1U0KhQJVvvpBdGticGWXD08b0RW3EicCC1MibY6QK8hoA/640?wx_fmt=gif)



原表中数据为0，刷新一次后插入20行数据，多次刷新后，数据每次增加20行。



WOW，你们应该猜到我要说什么了：















![img](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOhFSYQRT6gCntGhzicVysOxen5x5xnzO5Zib2piclRHX7aoYrSZBkHsLVtSsvaOp1bgqMbvUlibP51XSQ/640?wx_fmt=jpeg)







二、JSON篇



第一步，在SQL Server中创建一个存储过程，调用json格式的文本为参数；



第二步，powerquery生成JSON格式其实更加简单，使用Json.FromValue()，直接将table转为JSON文件：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvac5J40WLAarBmLPROR3ViaA6RHVXhteyu0Fl0SHxJlEeeZ059qNJUHRpA/640?wx_fmt=gif)



第三步，由于SQL读取的是字符串格式的JSON数据，所以需要使用Text.FromBinary()来返回字符串结果：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaGZViakwNlseG9GNLIs3189VCDpVqEoUfTQngGSLKQM9zZdVkSHUMfKeYshAz5iaYqpEocB7CaOHQQ/640?wx_fmt=png)



最后依然是向存储过程传递参数，只不过这次传递的是text格式JSON数据。



好了，我们来看一下效果，舞动起来：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOiaGZViakwNlseG9GNLIs31896Zx0kGzjLFj0Nwldnrdpd8rGKddTWxU5tR6vtat6mTytPYQetPen3g/640?wx_fmt=gif)



我们需要注意到，Text.FromBinary()获得的JSON字符串中文显示了Unicode编码字符，但是导入SQL中显示是中文没问题的：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaGZViakwNlseG9GNLIs31890M6n588apKf7TYt7LnDKEaHImCgibaXOcBd5qFVxWkm8RibNgP7T95YA/640?wx_fmt=png)



*这里留给大家一个问题，如果我就想在powerquery中显示中文，应该怎么办呢？欢迎大家在留言区交流分享。
*





好了，关于如何Power BI如何向SQL回写数据，我们用了三篇文章来讲解。前两篇分别是：



[【重磅来袭】在Power BI 中使用Python（4）——PQ数据导出&写回SQL](http://mp.weixin.qq.com/s?__biz=MzI2MDY3NDk1OA==&mid=2247483996&idx=1&sn=65fbc72a39ba9168ef611110b3a16e54&chksm=ea6746bddd10cfab9ea97b3e0c418bc5f7c195ad5d7f75edfc11fd41242cbd87cf8ff6f37103&scene=21#wechat_redirect)



[Power BI数据回写SQL Server（1）没有中间商赚差价](http://mp.weixin.qq.com/s?__biz=MzI2MDY3NDk1OA==&mid=2247484034&idx=1&sn=d2dcffec82178d3f88c8812cca68006c&chksm=ea674663dd10cf7556a00cfe8e8d150eb426c08de59605d956d97bdd7e5acbeb5fb0335e3052&scene=21#wechat_redirect)



对这几篇文章做一个小总结：



Power BI (PowerQuery)向SQL回写数据本身是一个应用场景并不多的技巧，没想到发了第一篇文章后很多朋友反馈说正是目前能用到的：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaGZViakwNlseG9GNLIs3189rOwkG7MO2J0K5BtIQnAia6V91nDDhDUOgUaicxSKMVnh0pW9IRJPIMRQ/640?wx_fmt=png)

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaGZViakwNlseG9GNLIs3189xicb10oVpcib6k2xTKDicDTRCInUg5n5k2ibF1Qx1hC47hytngQGqLLrMg/640?wx_fmt=png)

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaGZViakwNlseG9GNLIs318976fFw5qwEc52M0icSoTgrcRNKB29ooMDZGFvbMpWSLg5YqPz8Pwicwlw/640?wx_fmt=png)



所以才有了后面的这两篇文章。



总结起来，方法有这么几个：

1、借助Python的相关库，在PQ中调用，以达到回写SQL的目的；

2、在PQ中循环按行导入SQL；

3、在SQL中创建存储过程，然后在PQ中调用存储过程，JSON或XML文件作为参数



同时，总结了几位朋友的案例，发现应用场景主要集中在这么两个方面：

①pq爬取的数据只是状态数据，转瞬即逝，无法变化记录；

②解决不同数据库之间的壁垒，比如要定期将数据从某个数据库中备份复制到另一个。



所以，如果你在日常工作学习中遇到了以上的应用场景，那么这三篇文章恰好能够帮助你。



不过，我在测试时用的都是几十行的小数据，这几种方法并没有感觉到任何区别，所以对于巨量数据是否会有差异并没有经过实际的测试，欢迎有兴趣的朋友对比一下这几种方式，比较一下优缺点，欢迎反馈。





------



感谢您对【学谦数据运营】的关注、支持与厚爱，如果本文对您有用，请不要吝惜您的点赞、转发和点亮在看，有任何问题欢迎大家在留言区询问或者直接加我个人微信，谢谢。

