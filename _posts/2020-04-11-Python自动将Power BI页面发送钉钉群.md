

前文说过，在很多个惬意的下午，我每每爽歪歪地喝着咖啡，看着Power BI每秒钟刷新一次，静静等待某个分公司完成本月绩效任务，自动调用Python在钉钉群中发送喜报：

![img](https://tva1.sinaimg.cn/large/007S8ZIlly1gdq52a63ugj30gu0kc3z6.jpg)



紧接着再次调用Python将Power BI云端报告中的各分公司最新完成率数据和柱状图截图发在群里：

![img](https://tva1.sinaimg.cn/large/007S8ZIlly1gdq51qiky4j30gy0gw3zo.jpg)



那么今天就来讲一讲如何使用Python自动将Power BI报表中的页面截图发送到钉钉群或企业微信群中。



首先我们来拆解一下整个过程：

首先需要用Python登录Power BI打开所要截图的页面，并截图保存到本地，是为第一步。

如果要发送图片到钉钉群或企业微信群中，需要以markdown格式发送，图片需要为链接而不是文件，这是第三步。

再来说中间的第二步，要实现本地图片到图片链接的转换，需要一个Python可调用的稳定图床，所以找到合适的图床很重要。



明白了这三步，我们就可以开始干活了。

一、登录Power BI并截图

我们在无限刷新Power BI的第一篇文章中讲过，使用selenium的webdriver就能实现，截图可以用selenium配合PIL库实现。当然，前提是需要提前获取所要截图的报表页面。



登录代码，马赛克区域替换为自己的用户名和密码：

![image-20200411213507847](https://tva1.sinaimg.cn/large/007S8ZIlly1gdq5lczrmnj30q8076n05.jpg)





截图代码：

![image-20200411213535014](https://tva1.sinaimg.cn/large/007S8ZIlly1gdq5lugwp1j30t60ag42g.jpg)



截图时首先截取了全部浏览器，然后用四个角的坐标获取报表范围。最后保存到本地图片文件。



二、将本地文件上传云端并获取链接

这里我们使用的是七牛云。注册一下，然后创建个自有空间，设置好后，参考下文这个链接设置好SDK。

https://developer.qiniu.com/kodo/sdk/1242/python



将文件路径和文件名作为参数传递给函数，获取链接：

![image-20200411214024056](https://tva1.sinaimg.cn/large/007S8ZIlly1gdq5qukr4dj30h406u76d.jpg)

第二步结束。



三、发送钉钉群

1.在钉钉群中添加自定义机器人，并获取Webhook（注意Webhook不要泄露）：

![image-20200411214924765](https://tva1.sinaimg.cn/large/007S8ZIlly1gdq6089s06j30mm0fgac1.jpg)



然后设置好markdown格式的消息，确定好要@的人即可：

![image-20200411220321669](https://tva1.sinaimg.cn/large/007S8ZIlly1gdq6es6b19j30jo0be41x.jpg)



好了，我们来看以下成品。

![image-20200411220254105](https://tva1.sinaimg.cn/large/007S8ZIlly1gdq6e9gen7j30mc0hoqad.jpg)



还是很简单的对吧。



获取源代码，请关注【学谦数据运营】，回复“开车”。