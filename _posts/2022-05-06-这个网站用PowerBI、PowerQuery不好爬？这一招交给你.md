---
layout:     post
header-img: img/post-bg-parefreshpowerbi.jpg
catalog: true
tags:
    - PowerBI
    - PowerQuery
---

公众号：PowerBI生命管理大师学谦，同步更新，敬请关注



有同学想用powerbi爬这个网址

https://flk.npc.gov.cn/fl.html

但是发现它跟其他网址不太一样，因为翻页的时候地址栏还是一样的地址。

遇到这种情况该怎么办呢？

今天教你一招来搞定，此方法适用于很多网站，并且也是一项网爬的基本技能。



# 一、获取真正的url链接

## 1、打开网页，右键空白处-检查，选择网络：

![image-20220506185954314](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506185954314.png)

## 2、点击翻页，下方会出现一个新的链接：

![image-20220506190114051](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506190114051.png)



## 3、点击链接，右方默认会出现如图所示的栏目，选择标头，复制下方的请求URL，记住方法为GET：

![image-20220506190208537](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506190208537.png)



## 4、分析URL

https://flk.npc.gov.cn/api/?page=5&type=flfg&searchType=title;accurate&sortTr=f_bbrq_s;desc&gbrqStart=&gbrqEnd=&sxrqStart=&sxrqEnd=&sort=true&size=10&_=1651834715885

page=5代表第5页，size=10代表一页有10行内容，最后的那个& =之后内容是个时间戳，一般无所谓的，删掉即可，其他内容代表一些筛选项

所以链接可以简化为：

https://flk.npc.gov.cn/api/?page=5&type=flfg&searchType=title;accurate&sortTr=f_bbrq_s;desc&gbrqStart=&gbrqEnd=&sxrqStart=&sxrqEnd=&sort=true

## 5、将简化的链接输入地址栏

返回了10条记录

![image-20220506194615862](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506194615862.png)



并且我们在这里发现，该筛选一共有613条记录，每页10条，也就是62页：

![image-20220506194712655](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506194712655.png)

我们可以将上方链接替换为62看看，果然这一页上只有2条记录：

![image-20220506194854360](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506194854360.png)



# 二、PowerBI或PowerQuery获取数据

## 1、创建一个文本参数

![image-20220506200735671](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506200735671.png)

## 2、新建源-web：

![image-20220506200122111](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506200122111.png)

将此参数替换掉链接中的那个数字5：

![image-20220506200709607](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506200709607.png)



## 3、展开得到数据：

![image-20220506200913015](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506200913015.png)



## 4、创建参数：

在刚刚得到的这个表上右键创建函数：

![image-20220506201100783](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506201100783.png)



## 5、新建一个空查询：

![image-20220506201153247](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506201153247.png)



写入= {1..70}获取一个列表，并转换为表：

一共是62页数据，为了预备以后更新，设置为70页

![image-20220506201345944](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506201345944.png)



## 6、调用自定义函数：



![image-20220506201442175](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506201442175.png)

每一列都生成了一个table：

![image-20220506201513375](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506201513375.png)

## 7、展开table：

![image-20220506201550047](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506201550047.png)

## 8、关闭并上载即可得到想要的数据：

![image-20220506195604707](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506195604707.png)



ok！搞定！

你学会了吗，以后遇到这种翻页时地址不变的网页，就可以采用相同的办法解决！

# 推广：

想要获取更多PowerBI的精彩内容分享以及全年365天不限次提问，以及公众号文章的示例文件，请加入学谦PowerBI知识星球，现在更有20元优惠券可以领取，数量有限，欲购从速！

![海报 (3)](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/%E6%B5%B7%E6%8A%A5%20(3).png)





另外，学谦的PowerAautomate知识星球全新上线，目前已集结40+小伙伴，测试阶段50个白菜价名额马上就用完，机不可失，失去了永远不再来！

![image-20220506202008626](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220506202008626.png)
