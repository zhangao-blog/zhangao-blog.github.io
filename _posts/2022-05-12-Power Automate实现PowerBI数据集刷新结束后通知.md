---
layout:     post
header-img: img/post-bg-parefreshpowerbi.jpg
catalog: true
tags:
    - PowerAutomate
    - PowerBI
---



本文为PowerBI REST API高级应用教程，需要有REST API基础，并且能够自行获取token的基础上进行操作。

实际的业务场景往往纷繁复杂，比如某个时候你需要将最新的数据呈现给甲方爸爸，在按了一次刷新之后，在漫长的数据集刷新过程中，可能需要一次次点开网页刷新，看看是否已经刷新结束，往往消磨了人们的耐性。示例的文件刷新15分钟已经够客气了。

![image-20220511174430302](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220511174430302.png)

当然，你可以在每次刷新时设置一个15分钟的闹钟，以便提醒，但是未免太过繁琐。并且不是每次的刷新都是15分钟，往往有些时候可能需要更长的时间。

如果能有一个办法在每次刷新结束时自动提醒我就好了！

有人说可以通过数据预警，但是数据预警只能设置每天或者每小时发通知一次，而且设置思路并不是很明确。

### 一、本文提供的思路是：

**当前时刻，以往每次刷新的状态是可以获取的，通过API。**

刷新状态一共有三个，Completed（成功），Failed（失败），Unknown（未知，即正在刷新）。

也就是说，可以通过刷新状态的变化，来确定什么时间刷新结束。

比如一次刷新大约需要15分钟，那么我可以设置一个10分钟一次检测的flow(该时间间隔一定要小于数据刷新的时间，否则有一定几率漏掉)，获取最后一次刷新的状态。

如果状态不为Unknown，跳过；

否则进入小循环，5秒检测一次，直到状态转为Completed，结束，发送邮件通知。

### 二、具体设置过程：

#### 1、触发

Power BI刷新开始并没有直接或间接的触发条件（可能是我孤陋寡闻了，如有高见，请不吝指教），如果是每天固定的计划刷新，那么可以可以设置在某个时间段开始运行flow；如果是手动触发，也是有办法的，比如报告上使用一个flow来触发，dataset刷新启动后下一步就是这个操作。具体可以参考这篇文章。

但是很多时候可能既有手动刷新也有定时刷新，并且定时刷新时间也会有所变动， 此时上面的办法就不行了。

flow的触发可以使用定时，比如10分钟检测一次，如果状态不为Unknown则跳过。但是这里面有个逻辑，比如一个dataset刷新从14:02刷新到14:17，那么如果在14:05定时触发检测到状态为Unknown，则进入小循环，等到14:17刷新结束时一定会收到提醒邮件，这个没问题；但是14:15时定时运行的flow又会再一次运行另一个进程，依然会检测到Unknown，依然会进入小循环，并在14:17时发送另一个提醒邮件。

也就是说， 通过这种方式定时运行flow会有一定的小问题。

那么我们如何改善这一点呢？

答案是，手动触发。

有同学会问了，手动触发不就是一次性的吗？难道每次刷新都需要手动触发？

并不是！

我们这里使用了一个技巧，套用了两个loop，每个loop设置5000次（这也是limits的最大值），这样就是25000000次循环：

![image-20220512173834139](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220512173834139.png)



按照设定的10分钟delay一次，大约可以运行475.6年……嗯，基本够用。

如果你非说不够用，就再套个5000次！

#### 2、获取状态

API获取刷新状态是一个基本的操作：

```
GET https://api.powerbi.com/v1.0/myorg/groups/{groupId}/datasets/{datasetId}/refreshes?$top={$top}
```

这篇文章中有所介绍：

https://mp.weixin.qq.com/s/sb22c-bKgy7XsN_rlOQwdg

此处用了一个$top=1，即获取最后一次的刷新即可。

获取的内容是一个json，关于json的处理这篇文章有所介绍：

https://mp.weixin.qq.com/s/pNWqakSGXQdEyc6KRWzaRA

```
first(body('HTTP_获取刷新历史')?['value'])?['status']
```



![image-20220512175314234](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220512175314234.png)



#### 3、一旦识别了Unknown，进入小循环

加一个条件判断，如果最后一次刷新状态是Unknown，进入小循环，5秒获取一次，直到状态改变：

![image-20220512181827410](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220512181827410.png)



状态改变代表着刷新结束，当然，结束有多种方式，Completed只是其中一种，也可能会失败。但是不管刷新结果是什么，我们都会收到邮件的提醒。

#### 4、实操展示

我分别在17:12，17:28和17:51进行了刷新：

![img](https://images.zsxq.com/FrzcsN6Cw6_xcGPI-pY-JA0KeJxA?imageMogr2/auto-orient/quality/100!/ignore-error/1&e=1656604799&token=kIxbL07-8jAj8w1n4s9zv64FuZZNEATmlU_Vm6zD:9wdJWQGqOiEBAsiXgG4EwTh0wqI=)

刷新结束时都收到了邮件提醒，3次刷新都成功：

![img](https://images.zsxq.com/FpF3cv1fCm_IJlnlfGRya0fumpYZ?imageMogr2/auto-orient/quality/100!/ignore-error/1&e=1656604799&token=kIxbL07-8jAj8w1n4s9zv64FuZZNEATmlU_Vm6zD:Qc9KcGtGuewDlCHhnheJsdTcO7Q=)



### 三、总结

本文讲解了使用PowerBI REST API配合PowerAutomate实现PowerBI报告刷新结束时邮件通知的方法。前提是能够使用Azure AD设置应用程序，调用API，并且需要PowerAutomate的高级版应用。

有需要的朋友可以自行测试。本文提供PowerBI API付费技术指导，请直接联系学谦。



### 四、广告时间

1、学谦PowerBI知识星球：

全年365天无休，随时提问答疑，与众多伙伴一起学习交流PowerBI技术，更有大佬嘉宾坐镇，可以直接提问。

![海报 (6)](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/%E6%B5%B7%E6%8A%A5%20(6).png)







2、加入PowerAutomate知识星球，学习PowerAutomate的最新知识与技能，与众多伙伴一起探讨新的玩法，优惠转瞬即逝，还不快大力把握它@！

![海报 (5)](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/%E6%B5%B7%E6%8A%A5%20(5).png)
