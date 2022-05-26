---
layout:     post
header-img: img/post-bg-bigsmall.png
catalog: true
tags:
    - PowerBI
---



书接上文：

https://mp.weixin.qq.com/s/PTp1ZN1D5GptzwaXdOoSAQ

之前其实谈到过这个execute query，只不过放了一张图，大家纷纷表示不明觉厉：

![图片](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOjNLOoWLjia8diclOTzibNJEvic88snrmJUBbzacGkg2iaA3I4uun7Urqfm3DnyzrhJKyVqD4PLp6cmTEA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



因为一旦涉及到API获取token这些，就会让人觉得深不可测。并且写查询的json也比较麻烦。且之前的版本还有工作区的限制。

现在好了，官方直接给出了power automate的链接器，根本用不到任何的API设置与配置，查询也只要从evaluate开始写就行，就这一步：

![image-20220526125715583](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220526125715583.png)

输出：

![image-20220526125815330](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220526125815330.png)

与 Power BI 报告中一致：

![image-20220526130037543](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220526130037543.png)



下一步自然是输出了，添加一个创建CSV表格，选择first table rows：

![image-20220526130405909](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220526130405909.png)



写入ODB文件：

![image-20220526130911734](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220526130911734.png)

几秒种后在ODB中就会出现这个创建的文件：

![image-20220526130947286](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220526130947286.png)

打开发现乱码：

![image-20220526131011070](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220526131011070.png)

这个问题我们之前谈论过的：

https://mp.weixin.qq.com/s/PTp1ZN1D5GptzwaXdOoSAQ

现在有了更简单的办法。在创建CSV表格之后添加这么一个编辑，写入以下内容：

```
concat(uriComponentTostring('%EF%BB%BF'),body('创建_CSV_表格'))
```

这段代码的本质是在写入csv的时候添加文件头，以识别中文。

![image-20220526131418966](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220526131418966.png)



再次运行后，打开新建的文件，正常：

![image-20220526131337294](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220526131337294.png)



接下来我们就可以将手动触发改为重复周期，固定时间间隔下载一个文件：

![image-20220526131638592](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220526131638592.png)

然后在ODB中就会有一系列的文件生成：

![image-20220526133557089](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220526133557089.png)



正如前文所说的这个案例：

团队正在使用一个项目开发进度的软件（如Trello或Teambition ），记录着每一个子公司每一个项目的开展进度，每天软件自动或者项目管理人员手动更新进度，比如昨天是32%，今天是35%。

Power BI可以通过API获取这些数据，但是这些数据永远是最新的，而之前的进度就没有了。

那么如何获取每天的进度趋势，以为将来的分析需要呢？



使用execute query正好可以完美解决。

如果再配合“向数据集添加行”，可以实现什么呢？

![image-20220526132242920](https://picgo-1301351990.cos.ap-beijing.myqcloud.com/markdown/image-20220526132242920.png)

没错，数据回写！

Power BI 本身可以配置数据预警，但是配置过程相对比较麻烦，现在可以使用Power Automate运行execute query定时查询来解决。对于DirectQuery是很合适的。

对于按需刷新，或者定时刷新、异步刷新的报告来说，如果配置了API，可以在每次报告刷新结束后进行execute query检查，并立刻通知最新数据。

甚至execute query支持RLS，每个人只能获取属于自己权限内的数据。

甚至搭配powerapps实现云端报告手写度量值，实时在报表中返回想要的结果，而根本无需使用 Power BI Desktop 来操作，也就意味着MacBook可以间接实现度量值的编写。

好了，今天的文章就到这里了。

如果你还有什么奇思妙想，欢迎在留言区讨论。
