【强强联合】在Power BI 中使用Python（2）——数据清洗

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82l5jJaadCCfQITRcKHc5gBJxseuLKWibuwl2IsBLNbYEJoXuawYSq9Gdw/640?wx_fmt=png)



上一篇文章我们讲解了在Power BI中使用Python来获取数据的一些应用：

[【强强联合】在Power BI 中使用Python（1）](http://mp.weixin.qq.com/s?__biz=MzI2MDY3NDk1OA==&mid=2247483920&idx=1&sn=005dff4caa2c42cf33a0fe2ba130edb9&chksm=ea6746f1dd10cfe7a87d97e1589c11a9a701b771b01041993752821ab999ee3f2f6be92e3da0&scene=21#wechat_redirect)



这一篇我们将继续讲解如何在Power BI中使用Python进行数据清洗工作。

![image-20200513155130832](https://tva1.sinaimg.cn/large/007S8ZIlly1geqvhlymbwj31bw0qmkjm.jpg)

其实我们仔细看一下场景1和场景2，它们之间是个逆过程，场景1是从Python获取数据传递到Power BI，而场景2是Power BI或者Power Query获取了数据，用python来处理。



那么这个逆过程应该如何操作呢？话不多说，抓紧上车：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOj0HyV1WmPicWepF16gTHP4tWic1wHOZHEmYGGHIbToUekJ4PWrRIJ9B4GWH6yq4HpHA9goacBN0a3g/640?wx_fmt=png)



前文我们讲过，Python与Power BI的数据传递是通过Dataframe格式的数据来实现的。



**Python的处理结果以Dataframe形式输出，M将Dataframe自动转换为Table格式。M将其Table类型的数据传递给Python，Python会自动将Table转换为Dataframe。**



举个简单的例子：

首先我们进入Power Query管理器界面，通过新建一个空查询，并建立一个1到100的列表，再将其转换为表：

= {1..100}

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82ly7QWzfz9VT0bicEicpYMEXutkIMTQKBXuV5WPCXbmjaEH5zUmicytKwjw/640?wx_fmt=gif)



然后点击“转换-运行Python脚本”：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lgTYfIwPorh4SrgpfVY2kuf3hibRRkfjoiaDWraxKtftFpaJQpqfBiapgQ/640?wx_fmt=png)



脚本编辑器中自带一句话：

''# 'dataset' 保留此脚本的输入数据



一行以“#”开头的语句，在Python的规范中表示注释，所以这句话并不会运行，它的意思是将你要进行修改的表用dataset来表示，也就是说Python是通过dataset变量来访问数据的。



理论上我们需要在这个地方键入：

import pandas as pd

以表示我们要使用pandas库，但是Power BI在调用Python时，自动导入了pandas和matplotlib库，所以这一行写不写都一样，我们知道下面的代码是在调用pandas库即可。

在脚本编辑器输入框中输入以下代码：

dataset.insert(loc=1,column="add_100",value=dataset["Value"]+100)

dataset就是源数据表自动换换的dataframe格式数据，“loc=1”代表在第一列数据后插入一列，列名是“add_100”，值是“Value”的值+100，第一行是1，add_100列第一行就是101，以此类推：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lrz9W88Ab580ZoRBE0k0RhT8nFlt0UF3PF12UMOYbXzOuclXQ2t4JiaQ/640?wx_fmt=gif)



点击运行，得到的是一个子表，将其展开：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lFH54XntsH2cBNia0oadrr0cvBwQGVv3uqdDtTDD59YvtHCvf9YPFR9g/640?wx_fmt=png)

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82liancrweuxaP7kWwC1H775s5wxHt3CIIFq82lFHoweFmVAgtjHicdE4Jw/640?wx_fmt=png)



准确无误。



当然，我们也可以继续在这个表里进行一系列操作，比如复制一张表，再创建一个新dataframe表：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lnaUmAfYnZ6v6ibI5VCPC8PEUyKFK4H3bFEQU4goIMgG0iaRIjuBa1tcQ/640?wx_fmt=png)



运行，得到结果：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82l8Rm4WAcM5d40nxib5DCBvD24ndxI5F3uFatRZ6CqQFTV20fqRseQ5OA/640?wx_fmt=png)



再比如，我们想提取数据的某列，比如上面这张表的“key2”列，我们可以点击运行Python脚本，并写入如下的代码：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lI38nIuV4w1KBo0fRU3moWZcBeqSmKKll5CfrNt5VSBoSelsf5cK75A/640?wx_fmt=png)

*（power query自动对Python添加 #(lf) 用来进行转义）*



当然，以上所说这些功能直接在powerquery中就可以实现，甚至更简单便捷，所以上述内容都是些：

![点击查看源网页](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lXibiaVbwkHtHmiaibs1kPBY1c3GjicPy1huFWv3VwM8PoVaXic8gmHXhJmMw/640?wx_fmt=jpeg)





吗？



并！不！是！以上只是在循序渐进地告诉大家，powerquery中是可以用Python进行数据清洗的，并且清楚地告诉大家调用Python的方法，大家应该很熟练了吧。



以下才是重点（当然上面也是）：

![点击查看源网页](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lu33zMjkRXZdBMsUyNaUx4pA9Lpib9CdibiclbWXTxmogTYe53X11rJqFA/640?wx_fmt=jpeg)



在powerquery数据清洗中使用较多的Python功能一定会有正则，因为powerquery本身是没有正则的，所以这时候调用Python来进行正则就显得尤为重要，否则你可能需要在powerquery中添加很多步骤也不一定能得到想要的结果。



比如下面这个例子：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lScTmdWSE1nR3VNDIiaKCkz30OZTz7IsRh1AB1awuCSdXFicVxgb1fkDg/640?wx_fmt=png)

*真实情况可能远远比这个复杂。*



这种数据如果已经导入到Power BI中，在powerquery里是没有办法直接进行处理的，这时候就可以调用Python的re正则表达式了：

import re
import json

# 自定义获取文本电子邮件的函数
def get_find_emails(text):
    emails = re.findall(r"[a-z0-9\.\-+_]+@[a-z0-9\.\-+_]+\.[a-z]+", text)
    emails=';'.join(emails)
    return emails

# 自定义获取文本手机号函数
def get_findAll_mobiles(text):
    mobiles = re.findall(r"1\d{10}", text)
    mobiles =';'.join(mobiles)
    return mobiles

email_list=[]
tele_list=[]
for i in range(len(dataset)):
    text=dataset.iat[i,1]
    email=get_find_emails(text)
    email_list.append(email)
    tele=get_findAll_mobiles(text)
    tele_list.append(tele)
    
dataset['email']=email_list
dataset['tele']=tele_list

*正则表达式的使用，大家可以进行相关搜索和学习，网上资源还是很多的。*



这段代码定义了两个函数：get_find_emails（自定义获取文本电子邮件的函数）和get_find_mobiles（自定义获取文本手机号函数），得到两个list，最后再放入dataset数据表中。



在IDE中运行无误后复制到powerquery的Python脚本编辑器中：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lzw69mfeAyuX8mlCVaYoNSdWWSpZ7NryCNMiaufSSxVTkdMgnRP2jPQg/640?wx_fmt=png)



点击确定，返回结果：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lhdZLEMbBwGnqVicPZ2wUW0b6Uzibe8kMfP1auN1zhe6ZIRxEdvtsucew/640?wx_fmt=png)



后面两列就是我们想要的手机号和邮箱了。



这样我们就实现了在powerquery中使用正则表达式对数据进行清洗的目的。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOgfUZfjnTP7I2MY4D4f0G78AMoT4SOmNGgzwibEHtMYDvSyib7HEicU4D6EDlsx6PicyniavYZ5oD0qAicg/640?wx_fmt=jpeg)



*当然，也可以调用R、PHP或者js来实现相同的目的，方法大同小异，各位读者可以自行研究。
*



本文讲解了在powerquery中进行数据清洗工作时如何运用Python来实现一些特定的功能。当然，数据清洗的整个流程是复杂多变的，结合本文所讲的内容，希望大家都能充分挖掘powerquery和Python在数据清洗过程中的优缺点，结合起来使用，势必能事半功倍。



下一篇我们将继续讲解如何使用Python的matplotlib库在Power BI中进行可视化呈现。

![image-20200513154636119](https://tva1.sinaimg.cn/large/007S8ZIlly1geqvch60a1j31c00qekjm.jpg)



感谢您对本号【学谦数据运营】的关注，支持与厚爱，如果您觉得本文对您有用，请不要吝惜您的点赞、转发、点亮在看，有任何问题欢迎大家在留言区留言，谢谢。