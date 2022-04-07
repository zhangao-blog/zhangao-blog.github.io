【强强联合】在Power BI 中使用Python（3）——数据可视化

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82l5jJaadCCfQITRcKHc5gBJxseuLKWibuwl2IsBLNbYEJoXuawYSq9Gdw/640?wx_fmt=png)



前两篇文章我们讲解了在Power BI中使用Python来获取数据的一些应用：

[【强强联合】在Power BI 中使用Python（1）](http://mp.weixin.qq.com/s?__biz=MzI2MDY3NDk1OA==&mid=2247483920&idx=1&sn=005dff4caa2c42cf33a0fe2ba130edb9&chksm=ea6746f1dd10cfe7a87d97e1589c11a9a701b771b01041993752821ab999ee3f2f6be92e3da0&scene=21#wechat_redirect)



以及如何在Power BI中使用Python进行数据清洗工作：

[【强强联合】在Power BI 中使用Python（2）](http://mp.weixin.qq.com/s?__biz=MzI2MDY3NDk1OA==&mid=2247483938&idx=1&sn=a67fdd9eb0cebec3217ae6c5b9739215&chksm=ea6746c3dd10cfd53342278ef91f873901e2fdd71e37894e0843b18b44310b5a2662e7c64faf&scene=21#wechat_redirect)



这一篇我们继续讲解如何在Power BI中使用Python进行可视化呈现工作。

![image-20200513155143459](https://tva1.sinaimg.cn/large/007S8ZIlly1geqvi0tmdoj31bw0qmkjm.jpg)



打开Power BI Desktop，在右侧可视化区域会看到一个“Py”的图标，打开该图标,并选择启用脚本视觉对象，拖动字段到“值”的位置：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOhFSYQRT6gCntGhzicVysOxe96W6WyzqeQMm7kjusLr2XlrG6twFB87R70IKwbiaJQG1Vd616xQWhvQ/640?wx_fmt=gif)



添加了字段之后，在Python脚本编辑器中，自动显示了几行内容：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOgNZYnvgSl4jznLias6MeovTZJFemUnjMzbkrRlZe6CalWpb8cwlEyy0wT5qUv90UFENPRgFZRzRAg/640?wx_fmt=png)

dataset = pandas.DataFrame(dead, country, confirm)

dataset = dataset.drop_duplicates()

*注意：这两行代码显示的是被“#”注释掉了，但是在后台有完全相同的两行代码被真实执行了。另外，第二行代码的意思是去重，需要注意。
*



为了确保图像能够正确显示，可以在python开发界面将代码调试无误后COPY过来，当然，如果你是大神，也可以在里面直接RUN。

![点击查看源网页](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOgNZYnvgSl4jznLias6MeovTS3VQlmz50S36RZIOAjGAWsx6v3jsdPMv7iafh0WNMtfic02x8hkGdTNw/640?wx_fmt=jpeg)



*反正我是不敢。溜了溜了~
*

废话不多说，我们直接举两个例子：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOgNZYnvgSl4jznLias6MeovTL3W48JciajEHaHAr3ia9hyhIJRO9YLfwQnReHOhibgbRTB5DroPFmpHeQ/640?wx_fmt=jpeg)



First  one：

在编辑区域输入代码：

import matplotlib.pyplot as plt
plt.plot(dataset["confirm"],dataset["dead"])
plt.show()



点击运行，发现并没有完整显示数据，且不够美观也不够直观。

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOgNZYnvgSl4jznLias6MeovTsyMTRDKdE5sbddLdW65EiaqSxK433Vh9okH94MIxj0PkuZU5J4ia304w/640?wx_fmt=png)



这里需要做一些处理，因为“confirm”和“dead”字段默认是以求和的方式显示的，所以只有一个点的数据。



在可视化的值这里对“confirm”和“dead”字段分别选择“不汇总”。再运行代码，这样出来的就是正常的图形了：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOgNZYnvgSl4jznLias6MeovTYCNLLyMWsBDtAz0TXnVPWibk2icfgiclr5y9ibMR0xVGq8rWRNib9GgttLg/640?wx_fmt=gif)



我们也可以对中间这行代码进行适当修饰：

plt.plot(dataset["confirm"],dataset["dead"],color="red",marker="o")

以获得我们想要的形状和信息：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOgNZYnvgSl4jznLias6MeovTYNRtmPqFeQusdLySRIKkOB5aEGic96H8xRVgSuaEjS11ftsxxrFH8oA/640?wx_fmt=jpeg)

*当然，还是比较丑陋……原谅我的审美。*



我们再举个美观一点的例子：柱状图。仍然是插入可视化对象-添加字段-输入Python代码：

import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
cams=dataset['cams'].values.tolist()
IncomePercents=dataset['IncomePercents'].values.tolist()
plt.figure(figsize=(60,20))
plt.yticks(fontsize=15)
plt.bar(np.arange(len(cams)),IncomePercents,label='课收完成率',width=0.8)
plt.show()

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOgNZYnvgSl4jznLias6MeovTfSSoHwAWpoqRFV0roicHtzEhyfibiajYWRXH1OduZCLR7EmMs8giac0zsw/640?wx_fmt=gif)



结果得到一个很丑陋的柱状图……还不如直接用Power BI做呢！

![点击查看源网页](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOgNZYnvgSl4jznLias6MeovTn1vsrzubatnZia2umHHxTicDj3WGnTJ4cIHpQS7yIXPXnv2wuFU4fbNg/640?wx_fmt=jpeg)



没关系，我们只要按照下面的步骤适当调整一下代码：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOgNZYnvgSl4jznLias6MeovTCGCxkg2iaTtamhCicktLDyl0z1JhF64gcCzEgQcW82hrhZ7IgZDudYbA/640?wx_fmt=jpeg)

就得到了我们想要的结果：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOgNZYnvgSl4jznLias6MeovTY5tlMfvotU22vbgPv65b2lozlpm293OYf8ibO1wyorjI3EpzrBQD0PQ/640?wx_fmt=png)



还是乖乖地双手奉上源代码：

import matplotlib.pyplot as plt
import numpy as np
import pandas as pd

cams=dataset['cams'].values.tolist()
IncomePercents=dataset['IncomePercents'].values.tolist()
plt.figure(figsize=(60,10))
plt.xticks(np.arange(len(cams)), cams)

colors=[]
cam_num=0
for cam in cams:
    cam_num+=1
    if cam!='整体':
        colors.append('r')
    else:
        break
colors.append('g')
cam_num+=1
while cam_num<=len(cams):
    colors.append('c')
    cam_num+=1

a=plt.bar(np.arange(len(cams)),IncomePercents,label='完成率',color=colors,width=0.8)

def autolabel(rects):
    for rect in rects:
        height = rect.get_height()
        plt.text(rect.get_x()+rect.get_width()/2.-0.3, 1.01*height, '{:.1%}'.format(float(height)),fontsize=15)

autolabel(a)
plt.xticks(rotation=30,fontsize=12)
plt.yticks(fontsize=15)
plt.ylabel('完成率',fontsize=18)

curr_time = datetime.datetime.now()
time_str = datetime.datetime.strftime(curr_time,'%Y-%m-%d %H:%M:%S')
plt.title('2020年03月各公司完成率\n'+time_str+'数据',fontsize=30)
def to_percent(temp, position):
    return '%1.0f'%(100*temp) + '%'
plt.gca().yaxis.set_major_formatter(FuncFormatter(to_percent))
plt.legend()
plt.show()

*这里面比较复杂的点有两个：一个是添加了整体值，并将高于、低于整体值的部分填充不同颜色，另一个是显示柱状图标签，用到了一个小技巧。*



当然，以上所说这些作图功能直接在Power BI默认视觉对象中就可以实现，甚至更简单便捷，所以上述内容都是些：

![点击查看源网页](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOjmEvtIdpBRzBlEyC4jN82lXibiaVbwkHtHmiaibs1kPBY1c3GjicPy1huFWv3VwM8PoVaXic8gmHXhJmMw/640?wx_fmt=jpeg)





吗？



并！不！是！



还是上一篇的套路，以上举的例子只是简单地让大家认识一下如何在Power BI中调用Python作图，接下来我们介绍一些在Power BI中无法原生作图的例子：



比如数学制图，绘制sinx和cosx曲线：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOgNZYnvgSl4jznLias6MeovTWKDqsQFhwjFUTEJl5xLKbOJFY51YgCoXZrmWhYHZUicyYWQCO538hOw/640?wx_fmt=png)



比如绘制子图：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOgNZYnvgSl4jznLias6MeovTmtCVg1ZlT2POANBb0gTnhrogtagaZ8h8M7XweFMgLYhmjDDb30gECA/640?wx_fmt=png)



比如绘制特殊的柱状图：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOgNZYnvgSl4jznLias6MeovTNxmXDtnDGCoT1yG3ndlydYzbIGOFatWpYYJ0veoUTuaIZSuVicHfBVA/640?wx_fmt=png)



比如绘制三维散点图：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOgNZYnvgSl4jznLias6MeovT1oYgHPqTPbPc0Ptyic6cY8GEH3gWeqcj2rsYT6lhALiaP1rKvb1rdLCA/640?wx_fmt=png)



等等等等……

怎么样，Python还是有点用处的吧？

更多精彩作图，需要各位结合自己的业务场景，合理选择得到最优解。

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOgfUZfjnTP7I2MY4D4f0G78t3M7lpbYGCM81Z4GORa6QoGvrroWjE8vqdVQictms7hwlibicG6DxicYQQ/640?wx_fmt=gif)



总结：

Power BI的可视化功能本身就很强大（废话么，人家干什么的），但是毕竟可视化种类不是很多，很多特殊的可视化方法也没有办法直接实现，这时候我们就可以调用Python的matplotlib库进行作图了。



因为是几乎完全基于Python的作图，Power BI在这里仅起到了图床的作用，所以该部分内容对Python本身尤其是matplotlib库的要求较高，各位读者需要有较强的matplotlib代码编写能力才行。



好了，本文入门级地讲解了如何使用Python的matplotlib库在Power BI中进行可视化呈现，以补充Power BI自带可视化类型和第三方可视化插件无法实现的功能，想必大家一定能够通过这两个大神级软件的配合使用得到自己想要的可视化呈现。



——————————

以下是万众期待的下期预告环节：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOgNZYnvgSl4jznLias6MeovTCazgvzJicZHIfZd0JmsLw5uUTq2YNObW0BoLFbleLmFGcJZn6MIIYpA/640?wx_fmt=gif)



众所周知，Power BI对于数据的输出是有一定限制的，至少有这么两个点：



1.可视化对象导出CSV格式限制3万行数据，这对于数据量动辄上百万甚至上亿的表来说是不可接受的；

2.而一直广为诟病的powerquery数据困难的问题更是一时半会也得不到解决。



那应该怎么办呢？



对于第一个问题，我们推荐使用DAX Studio，轻松导出十万、百万条记录。

第二个问题，很可惜没有现成的工具可以直接解决，但是结合本系列《【强强联合】在Power BI 中使用Python》第二篇的内容：



**Python的处理结果以Dataframe形式输出，M将Dataframe自动转换为Table格式。M将其Table类型的数据传递给Python，Python会自动将Table转换为Dataframe。**



我们是否可以想到如何用Python将powerquery中的表输出为excel甚至实现回写到SQL中呢？



这就是下一篇文章要讲的内容了：

![image-20200513155636169](https://tva1.sinaimg.cn/large/007S8ZIlly1geqvmvq9vnj31c60qokjm.jpg)



感谢您对【学谦数据运营】的关注，支持与厚爱，如果您觉得本文对您有用，请不要吝惜您的点赞、转发、点亮在看，有任何问题欢迎大家在留言区留言，谢谢。

