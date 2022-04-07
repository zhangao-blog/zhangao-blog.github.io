[「强强联合」在Power BI 中使用Python（1）——导入数据](http://toutiao.com/item/6809844428987957772/)

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82l5jJaadCCfQITRcKHc5gBJxseuLKWibuwl2IsBLNbYEJoXuawYSq9Gdw/640?wx_fmt=png)

近几年，Python是越来越火了，就连地产大佬潘石屹都在年近不惑之时开始学习Python编程语言，我们做数据分析和运营的怎能不熟练运用呢？



![img](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOiaibqWZ7QG4T1lpAxic1jsOMxZZTxYNlIjw2DVqhz92wLv7Jpgm0SuEzKd6b24VzNbvp738XlYk0Gibg/640?wx_fmt=jpeg)



关于Python的教程，网上铺天盖地，9块9的，99的，999的甚至几千的上万的都有，妖魔鬼怪，乱象丛生，我们这里不去深究，因为Python作为一门胶水语言，各个方向都有成千上万的各种库，发展路径太多了。但是将Python和Power BI组合起来用的还真不多。



那么，我们今天就来讲一讲Python和Power BI组合起来使用，都有哪些场景。我列了如下三种：

![image-20200513153944998](https://tva1.sinaimg.cn/large/007S8ZIlly1geqv5cjsdsj31bm0q4kjm.jpg)

其中，关于第三种的Power BI的网页端刷新，完全突破Power BI的网页端刷新每天8次的限制，达到7×24任意时间、任意次数刷新，全方位满足您的需求，请查看以下两篇文章：



[如果雇一个人7d×24h每10秒刷新一次Power BI，我需要每月支付他多少钱？](http://mp.weixin.qq.com/s?__biz=MzI2MDY3NDk1OA==&mid=2247483812&idx=1&sn=1d99a342ad280eac128845382886debb&chksm=ea674545dd10cc534548c85c923390ceddb1ea3688d8212f0c5697dbfe863183c52cd1e91128&scene=21#wechat_redirect)

[如果雇一个人7d×24h每10秒刷新一次Power BI，我需要每月支付他多少钱？【2】](http://mp.weixin.qq.com/s?__biz=MzI2MDY3NDk1OA==&mid=2247483832&idx=1&sn=57165f8f1e91e463a26589e4652acc88&chksm=ea674559dd10cc4f57a04e48cf7696e54ae150e3a8df155489bdfc8ed69a67350d585f92848f&scene=21#wechat_redirect)



今天我们主要来讲讲第二种应用：**直接在Power BI中使用Python。**



Power BI 2018年8月8日的更新已经支持Python了，和之前支持R语言一样。之前接触过Power BI和R语言联合使用的朋友上手应该会快一些。



那么Power BI 中如何使用python呢？主要有以下5个地方：

![image-20200513154007813](https://tva1.sinaimg.cn/large/007S8ZIlly1geqv5qplt6j31c00qmkjm.jpg)

想要在Power BI 中使用python，我们需要先配置环境：



1、首先需要安装Python的运行环境，我在电脑中直接安装的的是Anaconda3，关于该包，大家自己在网上找来装吧，或者如果你安装了Visual studio2017的话可以通过VS的安装程序来配置：

![1](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lRTITm3B9HZ352sG11piaRKDiaASR7EXkicd6lrRAf5SxT3JllKaSj5ricg/640?wx_fmt=png)



2、如果你是直接安装了Anaconda，那么就不需要自己再单独安装pandas和matplotlib包了，因为这些常用的包anaconda早就给你配置好了，因此建议大家在学习Python的时候尽量直接安装anaconda，否则你还需要自己安装这2个包，打开cmd窗口：

pip install pandas

pip install matplotlib

3、默认情况下Power BI Desktop打开后是无法使用Python进行数据处理和绘图的，如果需要该功能，还需要对Power BI Desktop进行配置。



依次选择“选项和设置/选项/Python脚本编写”，配置Python3所在的目录位置，我这里是安装在C:\programdata\Anaconda3目录下的，点击确定即可：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lhUyIM614g4QGTF98XQF3hGoWEKgP9SJ3ib9TYzVv29QM3fmwroIhL6A/640?wx_fmt=png)



此外，我们可以使用Python自带的IDE或者安装第三方编辑器，比如PyCharm、Spider。如果使用第三方编辑器，应该做一些基本的配置，限于篇幅，这里不详细展开。



4、Python与Power BI的数据传递---Dataframe



Python支持5种常用数据类型，Power BI的M语言支持多种数据类型，两种语言直接以DataFrame数据类型进行传递。由于Python本身并没有支持DataFrame，因此Python会自动调用Pandas库。



M将其Table类型的数据传递给Python，Python会自动将Table转换为Dataframe；Python的处理结果以Dataframe形式输出，M会自动将Dataframe转换为Table格式。





好了，清楚了以上的配置，接下来我们就可以实操演练：

![image-20200513154051377](https://tva1.sinaimg.cn/large/007S8ZIlly1geqv6hs4hvj31bo0qakjm.jpg)

数据获取环节可以通过以下2种方式：

一、图形界面里找“Python脚本”选项；

二、空查询中使用Python.Execute()函数



我们首先看第一种运行方式：



1、在首页-获取数据或者Power Query编辑器中依次点击“新建源/更多…”，随后依次选择“其它/Python脚本”，点击确定按钮，显示Python脚本编辑窗口：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lk1BVtVsUCicNvXYMS6oUiamcyicicKIbiasdy3bfl2NqcoheHvoW17Dkusw/640?wx_fmt=gif)



在Python脚本窗口我们就可以将编写好的脚本粘贴并运行了。

如前所述，我们一般是先在第三方编辑器中编辑并运行代码无误之后再放到Power BI 中运行：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lAEW9BzBicHg6OI7ibibKNLsx5yRlGibaWWHLg7mRr7zQ7G1K6PNCM1NucQ/640?wx_fmt=png)



得到结果：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lqeBdBSnibGY4MQV23j84PCjBxPkesvl6IEkeqm1s0IkZesqNQwABfSg/640?wx_fmt=png)



*注意：最后一行print(df)并非是必需的，我只是为了在编辑环境里查看下输出的结果而已，在贴到Power BI Desktop的时候并不需要该行。Power BI Desktop会自动获取Python代码中数据类型是DataFrame的变量数据。*



我们将代码复制到Power BI Desktop的Python脚本编辑器中，并运行：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lLcaf8p0elE3B1l7QX8yfk5e9DphTou2dskFUrCOdhibTnFHwWxAt2aA/640?wx_fmt=gif)



这样我们就将Python运行的结果在Power BI 中显示了。



接下来我们来看第二种方式，直接在空查询中运行函数Python.Execute()函数：



M语言中调用Python的主要函数是 Python.Execute，大家可以看看其基本语法：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaibqWZ7QG4T1lpAxic1jsOMxZuQVnVDZI21FUM9Ishoaqia5BlJ2FW1xFdorDkod0YUEL5OFDDGjmDQ/640?wx_fmt=png)



1、在Power Query管理器中依次点击“主页/新建源/空查询”，公式编辑栏输入Py（注意M语言强调大小写），将会自动出现M函数列表智能提示：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lEKf38ia797SduHGzcLNpjU7Dswy13IOtCzUMlWnYJ2060QYibWBHZX8w/640?wx_fmt=png)



2、该函数接受一个字符串参数，所以我们要用成对的双引号，然后再将Python代码粘贴到里面，然后按下回车键，此时会出现“编辑权限”按钮，点击之后，弹出“脚本之行”对话框，点击运行按钮即可：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82loshwqUbNBFppKUCe18ffO4nqnSMgIx4BU1CAeoRWflLRyzaPzsmKqQ/640?wx_fmt=gif)



运行Python脚本后，Power BI会提取所有数据类型为DataFrame的变量出来，我们上面只有一个变量df，我们改下代码来看看，直接拷贝第一个变量，然后改下2个变量的名字：

import pandas as pd
import numpy as np

df1 = pd.DataFrame(
    {
        'key1': list('aabba'),
        'key2': ['one', 'two', 'one', 'two', 'one'],
        'data1': np.random.randn(5),
        'data2': np.random.randn(5)
    });

df2 = pd.DataFrame(
    {
        'key1': list('aabba'),
        'key2': ['one', 'two', 'one', 'two', 'one'],
        'data1': np.random.randn(5),
        'data2': np.random.randn(5)
    });



运行一下代码：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82loVMicWLMkNIsJ12ACkm10ZTnqXJNn7164w8eCME3GNzgROY5rgB3Vog/640?wx_fmt=png)



分别右键-将两张表作为新查询添加即可转换为两张单独的表：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lIjTumtZU6FicNcPKkr5iaYtrr95y4ouIN5OnHQqxL6Ylt8xz9rj3vgWw/640?wx_fmt=gif)



OK！这样我们就成功地用Python来导入数据了。



*Python和R语言在Power BI中的应用要求是一样的，数据传递的类型都要求是DataFrame，具体的使用场景和使用要求完全相同，会R的朋友，也可以按上述思路进行操作。*



本篇文章将Power BI中数据获取环节的Python使用讲解完毕，下一篇我们将继续讲解如何使用Python在Power BI中进行数据清洗。

![image-20200513154130497](https://tva1.sinaimg.cn/large/007S8ZIlly1geqv77bn8rj31c60qkkjm.jpg)
