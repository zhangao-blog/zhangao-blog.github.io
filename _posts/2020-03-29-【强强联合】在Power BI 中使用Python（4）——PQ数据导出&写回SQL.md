【重磅来袭】在Power BI 中使用Python（4）——PQ数据导出&写回SQL

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82l5jJaadCCfQITRcKHc5gBJxseuLKWibuwl2IsBLNbYEJoXuawYSq9Gdw/640?wx_fmt=png)

各位小伙伴们，大家好，我是学谦，咱们又见面了。



《在Power BI 中使用Python》系列的前三篇文章我们分别讲解了：



如何在Power BI中使用Python来获取数据：

[【强强联合】在Power BI 中使用Python（1）](http://mp.weixin.qq.com/s?__biz=MzI2MDY3NDk1OA==&mid=2247483920&idx=1&sn=005dff4caa2c42cf33a0fe2ba130edb9&chksm=ea6746f1dd10cfe7a87d97e1589c11a9a701b771b01041993752821ab999ee3f2f6be92e3da0&scene=21#wechat_redirect)



如何在Power BI中使用Python进行数据清洗：

[【强强联合】在Power BI 中使用Python（2）](http://mp.weixin.qq.com/s?__biz=MzI2MDY3NDk1OA==&mid=2247483938&idx=1&sn=a67fdd9eb0cebec3217ae6c5b9739215&chksm=ea6746c3dd10cfd53342278ef91f873901e2fdd71e37894e0843b18b44310b5a2662e7c64faf&scene=21#wechat_redirect)



如何在Power BI中使用Python进行可视化呈现：

[【强强联合】在Power BI 中使用Python（3）数据可视化](http://mp.weixin.qq.com/s?__biz=MzI2MDY3NDk1OA==&mid=2247483967&idx=1&sn=5c9551c80d9618219838dea0fbedbe80&chksm=ea6746dedd10cfc829141ab2e134ec66d4fb3dd9109c74252bcec48b75192fa5d8350c3c0e20&scene=21#wechat_redirect)



今天我们继续讲解第四篇——PQ**数据导出与写回SQL**



众所周知，Power BI对于数据的输出是有一定限制的，至少有以下两点：



1.可视化对象导出CSV格式限制3万行数据，这对于数据量动辄上百万甚至上亿的表来说是不可接受的；

2.而一直广为诟病的powerquery数据困难的问题更是一时半会也得不到解决。



那应该怎么办呢？



第一个问题，推荐使用DAX Studio，轻松导出十万、百万条记录；

第二个问题，没有现成的工具可以直接解决，但是结合本系列第二篇的内容，我们是否可以想到如何用Python将powerquery中的表输出为excel甚至实现数据回写到SQL中呢？



这就是我们今天要学习的内容：

![image-20200513155636169](https://tva1.sinaimg.cn/large/007S8ZIlly1geqvmvq9vnj31c60qokjm.jpg)



我们在第二讲中说过：



**Python的处理结果以Dataframe形式输出，M将Dataframe自动转换为Table格式。M将其Table类型的数据传递给Python，Python会自动将Table转换为Dataframe。**



M将其Table类型的数据传递给Python，Python会自动将Table转换为Dataframe。那么Python中Dataframe如何输出呢？



想必了解pandas库的战友们已经想到答案了。



只要一行简单的代码：

= Python.Execute("# 'dataset' 保留此脚本的输入数据#(lf)dataset.to_excel(r""C:\Users\金石教育\Desktop\abc.xlsx"")",[dataset=源])



简单吧！



![点击查看源网页](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOgNZYnvgSl4jznLias6MeovTmblzL1Zg57oic0DIazo4GP3xcCdibUE9IibqWUIgboadjkIuLnicXQW3hA/640?wx_fmt=jpeg)



运行一下：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOgNZYnvgSl4jznLias6MeovTYophiaFr3Y7O4CIX6PQic6DVRa29lu1JTnuuukl4mQYmQias5fOqRNomg/640?wx_fmt=gif)



OK啦！



关键是：



只有一行代码！

只要一行代码！

只需一行代码！



重要的事情强调三遍！



多年来powerquery广为人们诟病的——数据清洗后无法导出结果的问题就这么被一行代码轻松地解决，美滋滋。

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOhFSYQRT6gCntGhzicVysOxeVRDn8fY1wyWEydwqMicbMHOQCe8K2CK1MTndaOGwm1AcAYk0PoicO1iaA/640?wx_fmt=gif)



好了，既然知道了如何导出excel文件，那么各位，写回MySQL数据库的操作是否可以举一反三自行解决呢？



我们直接看下图的神操作：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOhFSYQRT6gCntGhzicVysOxeD2lTxxfIMGMayuPnhGt2GBfic4licKBOl53pXLkYk7dXia6fK6uH043ibA/640?wx_fmt=gif)



看到了吗，mysql数据库中本来是一张空表，我们在powerquery中运行了一段Python代码后，表中有了数据。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOhFSYQRT6gCntGhzicVysOxen5x5xnzO5Zib2piclRHX7aoYrSZBkHsLVtSsvaOp1bgqMbvUlibP51XSQ/640?wx_fmt=jpeg)



关键代码解释：

db = pymysql.connect("localhost","用户名","密码","nc" )
#连接数据库
query = 'insert into `全球疫情_country`(id,displayName,areas,totalConfirmed,totalDeaths,totalRecovered,lastUpdated,lat,long,parentId)values(%s,%s,%s,%s,%s,%s,%s,%s,%s,%s)'
#键入数据

for r in range(len(dataset)):
    #按行获取数据
    id0=dataset.iat[r,0]
    displayName=dataset.iat[r,1]
    areas=dataset.iat[r,2]
    ……  
    values = (id0,displayName,areas,totalConfirmed,totalDeaths,totalRecovered,lastUpdated,lat,long,parentId)
    cursor.execute(query, values)

cursor.close()
db.commit()
db.close()
#提交数据并关闭数据库

*获取完整源代码，请关注本号【学谦数据运营】，回复关键字“powerbi-python-mysql”
*



代码没什么难度，用的是Python的一个常用库：pymysql，将dataset中的数据按行导入MySQL中。



但是有一个大BUG一点小问题：



因为全球只有200左右个国家和地区，country层面的数据应该只有200左右。但是，我习惯性地瞥了一眼MySQL右下角，发现：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOhFSYQRT6gCntGhzicVysOxeuX1dm3WdGv1lWAJzBcfL3ibvecuKoSHqM9I5OYcR2oqeuB9wPdsnhWQ/640?wx_fmt=png)



难道最近的国际局势变化这么大，已经有567个国家和地区了？不可能吧。抓紧查询一下，发现果然有问题：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOhFSYQRT6gCntGhzicVysOxe84Qgg5EJJuA75cytFJfP75rD1v5IxAO8xybfCiaibGXZWLRPhIia2NVKA/640?wx_fmt=png)



全球每一个国家和地区的数据都显示了三次，567/3=189，这还差不多。



而且清空表后再刷新运行，就会发现有的时候是2次，有的时候5次，这意思就是Python代码运行了多次，造成了数据重复，这背后的原因我们无从得知。这样可不行啊，总不能每次使用的时候先去重吧，不太现实，如果有时候疏忽了就麻烦了，那该怎么办呢？



这个问题先一放，我们来看另一个问题：



每个国家的每日数据我们只保留一次，即便powerquery每次刷新只向MySQL数据库写入一次，但我们也不能保证编写模型的时候只刷新一次吧，因为一旦人工刷新多次，造成的结果和上面被动造成的结果一致，所以，只要我们解决了人工刷新造成数据重复的问题，查询刷新时被动写入多次的问题也就顺带解决了。



我们看一下数据，有一列“lastupdated”，是时间格式，也就是查询的时间，由于我们只关心日期数据，因此只取出日期就可以。所以只要每次写回MySQL之前，先判断一下数据库中是否已经存在当日的数据，如果有，就先删除，再将新的数据写入，这样就达到我们的目的了。



添加以下代码：

#添加一列日期
dataset.insert(loc=10,column="updateday",value=dataset["lastUpdated"].str[0:10])

#获取日期
today_date=dataset.iat[0,10]

#删除当日的已有数据
query_delete='DELETE FROM `全球疫情_country` WHERE updateday='+today_date



运行一下代码：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOhFSYQRT6gCntGhzicVysOxejhh5QEjgVibs05YbSfMX6EE9MwfC8tsCWbU2y9hQY6sFeMiaC03GJk6Q/640?wx_fmt=gif)



MySQL数据库的表中初始有378条数据（因为包含了3月27日和3月28日两天的数据，共189个国家和地区的数据），运行代码后，仍然是378条，之前已有的3月28日的数据被删除，然后添加了刚刚查询到的最新数据。



完美解决！



好了，写回MySQL数据库的全部操作和遇到的问题与解决措施到这里就讲解完毕了。你学会了吗？



写这篇文章的时候不知道怎么的，远程计算机的MySQL数据库总是出问题，导致我这边文章前前后后写了五六个小时。



本节内容细节的点特别多，大家一定要自己动手操作几遍，后续我会逐步安排相关的视频讲解，大家请注意关注本号号，跟上队伍。



------



以下仍然是下期预告环节：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOgNZYnvgSl4jznLias6MeovTCazgvzJicZHIfZd0JmsLw5uUTq2YNObW0BoLFbleLmFGcJZn6MIIYpA/640?wx_fmt=gif)



下一篇我们将继续介绍一个重磅功能——**数据条件触发预警并邮件通知**：



说到数据预警，微软自家的Flow可以设置预警条件并发送邮件，这是原生功能，有兴趣的朋友可以去了解。

![image-20200513173700569](https://tva1.sinaimg.cn/large/007S8ZIlly1geqyjd9x2nj31by0qikjm.jpg)



------



感谢您对【学谦数据运营】的关注、支持与厚爱，如果本文对您有用，请不要吝惜您的点赞、转发和点亮在看，有任何问题欢迎大家在留言区询问，谢谢。

