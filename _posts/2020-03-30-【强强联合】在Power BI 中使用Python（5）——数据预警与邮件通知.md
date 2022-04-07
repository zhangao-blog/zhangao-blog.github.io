【重磅来袭】在Power BI 中使用Python（5）——数据预警与邮件通知

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82l5jJaadCCfQITRcKHc5gBJxseuLKWibuwl2IsBLNbYEJoXuawYSq9Gdw/640?wx_fmt=png)



*案例背景*



*某连锁门店的区域经理助理小朱为当前区域门店创建了多个重要指标看板，但无论是区域经理还是店长，因为日常工作太忙，经常没空细看所有数据看板。小朱希望对于重要指标，特别是有异常的重要指标，可以单独预警。*



在互联网+时代，一切都讲究“数据化”，而真正用好“数据”，不仅仅是“人看数据”，更要“数据追人”，才能让“数据落地”，如此才称得上将产品/运营/服务等实现“数据化”。



那么，如何做到“数据追人”，也就是设置数据预警条件，当满足条件时就会有邮件自动提醒呢？



这就是我们今天要讲的《在Power BI 中使用Python》系列的第五篇内容：

![image-20200513173700569](https://tva1.sinaimg.cn/large/007S8ZIlly1geqyjd9x2nj31by0qikjm.jpg)



首先我们需要知道的是，Microsoft自家的Flow可以设置预警条件并发送邮件，这是原生功能，但是很多朋友可能并没有使用权限使用Flow，很多连正版的office都没有使用，希望有条件的朋友还是使用Flow比较好，也更简洁方便。

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOhFSYQRT6gCntGhzicVysOxejiaKQBiaGLhpBro879yYU74UeD2o2vwlfa9PrZpWEC8DGd8hibibiamic33w/640?wx_fmt=png)



如果手头没有Flow的应用，那么只能采取如下的方式了。



好，我们来看下面的案例：

目前世界各国的新冠病毒确诊人数急剧增加，比如我想知道什么时候美国的感染人数超过15万（截止3.27是10万多了）或者全球的感染总数超过50万（截止3.27已经有42万了），一旦满足条件，我希望邮件能够通知我。



那么这个想法，Power BI和Python该如何帮我实现呢？



结合上一篇文章实现的数据导出功能，其实也很简单，只要添加一个判断条件和一个能够发送邮件的库就ok了：



首先是判断条件的代码：

for i in range(len(dataset)):
    if dataset.iat[i,0]=='unitedstates':
        if totalConfirmed>=150000:
            #美国人数超过15万的条件满足
            #以下添加发送邮件的代码



第二步，能发送邮件的库是smtplib，我们直接测试一下代码：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOhFSYQRT6gCntGhzicVysOxeAImyAPQUwFmlKt2DePHlYgtMVx2VPfudq5MAoaiaOJ6sG4U6zs1NITA/640?wx_fmt=gif)



这时，我的邮箱里就收到新邮件了：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOhFSYQRT6gCntGhzicVysOxeDEeBiaU2cO81ZxkibJ3PXv7VemNvcPnDCwKicO2vicibMBEXV7ibdnKkibeAA/640?wx_fmt=png)



这样，将条件判断代码和发送邮件的代码组合起来使用，我们就可以实现数据预警和邮件自动发送了。



不得不再次说一声：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOhFSYQRT6gCntGhzicVysOxen5x5xnzO5Zib2piclRHX7aoYrSZBkHsLVtSsvaOp1bgqMbvUlibP51XSQ/640?wx_fmt=jpeg)

*获取源代码，请关注【学谦数据运营】，回复关键字“powerbi-python-email”
*



有一点需要注意的是：不知道大家有没有认真看收件箱的邮件，同时来了3封相同的邮件，跟上一篇导入MySQL时相同的现象，都是在PQ处理时，Python代码被执行多次的问题（暂时没有查明原因）。



这个问题其实也好解决，我们将报表发布到云端，再点刷新：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOhFSYQRT6gCntGhzicVysOxeuPV822xOz1QLTKIBEYNtpFEvP1SueB1sXsotiatNt3gHQzd0uhbDN5g/640?wx_fmt=gif)



可以看到总共点击刷新按钮3次，每次刷新后增加一封新邮件，加上本身有一封未读邮件，一共是4封。也就是说在云端刷新的报告只会运行一次Python代码。



而实际上我们发布报表前，理论上设定的条件尚未满足，也就是说不会触发邮件发送。只有当云端刷新几次后满足条件了才会触发邮件发送开关。也就是说，发布到云端就可以解决以上问题。



小意思。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOgcmPd6SBjrlvB2EatBRdibj9g43Nq716V6k7E994NN8qjKiaGCTVAjI182Je7PUu8x6nPympnrniatg/640?wx_fmt=jpeg)





还有一个问题，不知道你想到没有：



当美国感染人数超过15万触发邮件发送后，后续的每一次刷新一定也会满足超过15万人的条件，也就是说，每一次刷新都会发送邮件。



尤其是学习了这篇：[如果雇一个人7d×24h每10秒刷新一次Power BI，我需要每月支付他多少钱？](http://mp.weixin.qq.com/s?__biz=MzI2MDY3NDk1OA==&mid=2247483812&idx=1&sn=1d99a342ad280eac128845382886debb&chksm=ea674545dd10cc534548c85c923390ceddb1ea3688d8212f0c5697dbfe863183c52cd1e91128&scene=21#wechat_redirect)，假设设定了10分钟更新一次数据，邮件就会每10分钟发给我们一次，这很显然不是我们想要的。



好人做到底，解决办法这里也一并告诉大家：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOhFSYQRT6gCntGhzicVysOxeQmbvCEBDFeVxEhMUzicSoYR3uicKGGoUOXU0icjj1gmQ7NCjtMZNOpJNg/640?wx_fmt=jpeg)



手动创建一个如下的excel文件：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOhFSYQRT6gCntGhzicVysOxe8mA34csb4dgSINianibibDCZ49U8PJ6M49W29h2ibgkzcZD0631GUCibCxQ/640?wx_fmt=png)



修改发送邮件的条件，添加一条，pandas读取这个值，只有当这个值为0时才运行后面的内容；

当发送邮件的条件满足时，0修改为1，并保存；

这样，当满足一次条件后，条件就不再满足，后续也就不会再发送了：

io=r"C:\Users\学谦数据文化\Desktop\1.xlsx"
df=pd.read_excel(io, sheet_name=0, header=0, names=None)
if df.iat[0,0]==0:
    for i in range(len(dataset)):
        if dataset.iat[i,0]=='unitedstates':
            if totalConfirmed>=150000:
                df.iat[0,0]=1
                df.to_excel(r'C:\Users\学谦数据文化\Desktop\1.xlsx', sheet_name='Sheet1')
                #以下添加发送邮件的代码
            



完美解决！详细得不能再详细了。

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOhFSYQRT6gCntGhzicVysOxel0sQYebVU8KQ07D5BeMvk9TjjZsTbrc8vVd32NjyvRXGbxQgqDUZQg/640?wx_fmt=gif)

*（对于最后这个问题，如果大家有更好的解决方案，欢迎在留言区留言或公众号内回复）*





终于，我们通过5篇文章大体讲完了在Power BI中使用Python的几个场景：

1、导入数据；

2、清洗数据；

3、数据可视化；

4、PQ数据导出；

5、数据预警与邮件自动发送。



当然，我们需要注意的一点是，这些功能的使用其实很多时候并不是最合理的，也并不是鼓励大家一定要去这样操作，只是提供了一些新的思路。



比如我们一般情况下都是Python爬取数据直接导入MySQL，然后再用pq导入Power BI中建模处理。但是在一些建造时间比较久了的模型中，原本就用pq爬取的数据并进行过大量处理，如果再转移到python，恐怕还得重新编写一遍代码，那么用本系列文章中的操作就会尽可能少地改动原来的代码，并节省不少时间。



所以大家一定要择优选取使用。



这几篇文章细节的点特别多，大家一定要自己动手操作几遍，有问题及时沟通，后续我会逐步安排相关的视频讲解，大家请注意关注本号，跟上队伍。



------



以下仍然是下期预告环节：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOgNZYnvgSl4jznLias6MeovTCazgvzJicZHIfZd0JmsLw5uUTq2YNObW0BoLFbleLmFGcJZn6MIIYpA/640?wx_fmt=gif)



不少公司的老板不习惯看报表，而是喜欢直接在群里看图片，尤其是每天定时几个时间点，看看完成率，看看PV、UV等，那么我们能不能设置一下，Power BI做好的报表，定时发送到钉钉群中呢？



下一篇我们将继续介绍将Power BI和Python结合使用的第三种场景：



**Python定时自动将Power BI报告截图发送钉钉群。**



关于使用Python操控Power BI的网页端刷新，请阅读以下两篇文章：

[如果雇一个人7d×24h每10秒刷新一次Power BI，我需要每月支付他多少钱？](http://mp.weixin.qq.com/s?__biz=MzI2MDY3NDk1OA==&mid=2247483812&idx=1&sn=1d99a342ad280eac128845382886debb&chksm=ea674545dd10cc534548c85c923390ceddb1ea3688d8212f0c5697dbfe863183c52cd1e91128&scene=21#wechat_redirect)

[如果雇一个人7d×24h每10秒刷新一次Power BI，我需要每月支付他多少钱？【2】](http://mp.weixin.qq.com/s?__biz=MzI2MDY3NDk1OA==&mid=2247483832&idx=1&sn=57165f8f1e91e463a26589e4652acc88&chksm=ea674559dd10cc4f57a04e48cf7696e54ae150e3a8df155489bdfc8ed69a67350d585f92848f&scene=21#wechat_redirect)



![image-20200513174014244](https://tva1.sinaimg.cn/large/007S8ZIlly1geqympfu2gj31bi0q6kjm.jpg)

------



感谢您对【学谦数据运营】的关注、支持与厚爱，如果本文对您有用，请不要吝惜您的点赞、转发和点亮在看，有任何问题欢迎大家在留言区询问，谢谢。

