---
layout:     post
header-img: img/post-bg-parefreshpowerbi.jpg
catalog: true
tags:
    - PowerAutomate
    - PowerBI
---

公众号：PowerBI生命管理大师学谦，同步更新，敬请关注



json是powerautomate云端flow中常常出现的一种数据形式，有时需要手动生成，有时需要自动获取后进行获取其中的内容。

json的增删改查熟练对于快速构建一个有效的flow大有裨益。



我们以一个云端流为例简单地说一下关于json的操作。



1、增addProperty

首先我们需要先创建一个变量-json示例：

![image-20220510190826485](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220510190826485.png)

此处的{}是有必要的，否则会运行不成功。

设置有一个编辑：

```
addProperty(variables('json示例'),'姓名','张三')
```

![image-20220510191008303](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220510191008303.png)

接着我们还得将此结果返回到变量中：

![image-20220510191021559](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220510191021559.png)

输出结果为：

![image-20220510192242047](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220510192242047.png)

不过很多时候，我们想要往里添加的内容不止这么简单，我们可能想要添加另一个json到这个json中，形成嵌套。方法也很简单，再设置一个变量地址

![image-20220510191747592](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220510191747592.png)

再次使用addProperty：

```
addProperty(variables('json示例'),'地址',variables('地址'))
```

输出：

![image-20220510192304559](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220510192304559.png)

我们还可以继续往里添加一些内容，比如邮编：

```
addProperty(outputs('编辑_2'),'邮编', '266500')
```

输出：

![image-20220510192319479](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220510192319479.png)



2、删removeProperty

某些时候我们需要删除json结构中的某些字段，就可以使用removeProperty来实现，用法如下：

```
removeProperty(outputs('编辑_3'),'姓名')
```

输出：

![image-20220510192340472](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220510192340472.png)



3、改setProperty

如果要对json中的某项内容进行修改，可以使用setProperty，比如要修改邮编为266555：

```
setProperty(outputs('编辑_4'),'邮编','266555')
```

输出：

![image-20220510195453765](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220510195453765.png)

如果json结构中没有setProperty设置的字段，那么会添加一个新的字段，效果与addProperty一致：

```
setProperty(outputs('编辑_5'),'姓名','学谦')
```

输出：

![image-20220510193108702](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220510193108702.png)

4、查

如果我们想由此json结构得到里面姓名字段的值，可以有多种办法，可以使用“分析json”这个独立的功能，

![image-20220510200347287](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220510200347287.png)

然后选取“姓名”字段：

![image-20220510200449844](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220510200449844.png)



我们也可以直接按照如下的写法（本质与分析json相同）：

```
outputs('编辑_6')?['姓名']
```

输出：

![image-20220510193117886](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220510193117886.png)



如果想获取子结构中的字段的值也是可以的：

```
outputs('编辑_6')?['地址']?['城市']
```

输出：

![image-20220510200839253](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220510200839253.png)



以上就是powerautomate云端flow的json结构增删改查的全部内容，通过本文的学习，想必你一定会对json结构的数据处理更加得心应手。



加入知识星球，学习PowerAutomate的最新知识与技能，与众多伙伴一起探讨新的玩法，优惠转瞬即逝，还不快大力把握它@！

![海报 (5)](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/%E6%B5%B7%E6%8A%A5%20(5).png)
