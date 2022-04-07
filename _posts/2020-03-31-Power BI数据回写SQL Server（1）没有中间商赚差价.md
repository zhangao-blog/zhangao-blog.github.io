Power BI数据回写SQL Server（1）没有中间商赚差价

![img](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvacduvV5NuS96W85vl9qE1pAWI7SiagNerELIeMNNtarI7icTSaLbRP4l9Q/640?wx_fmt=jpeg)



我们在[【重磅来袭】在Power BI 中使用Python（4）——PQ数据导出&写回SQL](http://mp.weixin.qq.com/s?__biz=MzI2MDY3NDk1OA==&mid=2247483996&idx=1&sn=65fbc72a39ba9168ef611110b3a16e54&chksm=ea6746bddd10cfab9ea97b3e0c418bc5f7c195ad5d7f75edfc11fd41242cbd87cf8ff6f37103&scene=21#wechat_redirect) 讲过如何在Power BI中调用Python实现powerquery获取和处理的数据回写到MySQL中。



有不少朋友提问，能否回写到SQL SERVER中呢？

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOgcmPd6SBjrlvB2EatBRdibj7pzCkMKwV27K2pR5cNSNjwtvH6WfAY2Z7R9hJibfa1mZFOMNWCHLIibA/640?wx_fmt=png)



答案是肯定的。有两个大的解决方案：



第一个，由于本质上我们调用的是Python脚本，所以回写入哪个数据库由Python来决定。写入MySQL的库是pymysql，而如果要写入SQL SERVER我们需要更换一个库：

pip install pymssql



从名字上我们也能看出，这两个库的作者是同一个人，因此用法几乎完全一致。只不过在对待表名是中文时处理方式不太一样，MySQL需要在表名上加“`表名`”符号，SQL SERVER则不需要。



点击：转换-运行Python脚本，编辑代码，运行。

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvacViasO7NR5icg9TMfhGQbXo2rI3h1JRXOUsxOp7L89ZIFatIXbGiakCypQ/640?wx_fmt=gif)



可以看到在运行Python脚本前，SQL数据库共378条数据，运行后是578条，增加了200条，这说明前几天只有189个国家和地区的数据，而今天更新有200个国家和地区的数据，这也直接说明病毒还在继续向更多国家蔓延，防控形势不容乐观。



*获取完整源代码，请关注【学谦数据运营】，回复关键字“powerbi-python-sqlserver”
*

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvacic6463kveFzUWFRicV4DiarGpJVLtCjqTmxABJmhNIQw5jhbtYXDPzWhA/640?wx_fmt=png)





第二个办法，其实更简单一些，而且直接跳过了Python，因为Power BI和SQL Server都是微软家的拳头产品，自家人，肯定办事方便些。



我们先从SQL Server导入一张表到powerquery中：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvacjDqN1Sk5dTJjpicSMNwpvKrdW6yXd57cMZMpfWXuOJYhrgYZib0CuoMg/640?wx_fmt=gif)



点开高级编辑器：

let    
    源 = Sql.Database("DESKTOP-NLIOB2L\MSSQLSERVER1", "test1"),    
    dbo_Sheet1 = 源{[Schema="dbo",Item="Sheet1"]}[Data]
in    dbo_Sheet1



将以上代码适当进行修改：

let    
    源 = Sql.Database("DESKTOP-NLIOB2L\MSSQLSERVER1", "test1",[Query="select * from Sheet1"])
in    源



点运行：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvacburAhDf9LROVgbEQXAkRNa1IhMZs3865Apzegia669zUKUHiagHp0kvA/640?wx_fmt=png)



两种查询方式得到的结果完全一致。



但是修改后的代码意义却变了：



[Query="select * from Sheet1"]



这实现了在PowerQuery中直接输入SQL Server代码并运行：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvacoIKzcF7gCicQvNpwKRYzzWbKRY9hUu0YB1ux1oEOUZy7jU3aoVTYYiaA/640?wx_fmt=png)



这就代表着我们可以通过编写SQL语句向SQL Server插入数据了：

let    
    Source = Excel.CurrentWorkbook(){[Name="表1"]}[Content],    
    ChangedType = Table.TransformColumnTypes(Source,{{"KeyValue", type text}, {"NumberValue", Int64.Type}, {"DateValue", type date}}),    
    insert=Sql.Database("DESKTOP-NLIOB2L\MSSQLSERVER1", "test1",[Query="INSERT INTO Sheet1(KeyValue,NumberValue,DateValue)VALUES('A',3,'2019/1/1')"])
in    insert



看一下运行过程：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvacN32FonLpSBVdsEkUHWowiawSiaR1WZ4dsQxCusRf7L34Gcm4eWAAQ2IA/640?wx_fmt=gif)



可以看到原表中只有2017年的数据，运行后增加了5行2019/1/1的数据，查询一次却增加多行的原因我们在[【重磅来袭】在Power BI 中使用Python（4）——PQ数据导出&写回SQL](http://mp.weixin.qq.com/s?__biz=MzI2MDY3NDk1OA==&mid=2247483996&idx=1&sn=65fbc72a39ba9168ef611110b3a16e54&chksm=ea6746bddd10cfab9ea97b3e0c418bc5f7c195ad5d7f75edfc11fd41242cbd87cf8ff6f37103&scene=21#wechat_redirect)中也说过，尚未明确知晓什么原理，只能通过其他办法来处理，稍后再说。



当然我们也可以同时插入多行数据：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvacyPk74CffOBFHedEJ4fCL5gn05xV0WSecWf9R1hbmYqkUvGytynic2Qw/640?wx_fmt=png)



结果：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvacg5FPG3qBGhobFrsVdhYHpjhv29sds4sap5G9L3tsxicic8cxzLG8Gsibw/640?wx_fmt=png)



但是这样我们只能实现自己手动填写数据写入SQL语句去运行，而无法将PQ查询的结果写入SQL。



所以还得想别的办法。



我们再来试试Value.NativeQuery方法，是将一条record记录数据直接插入数据库中：

Value.NativeQuery
        (
        Sql.Database("DESKTOP-NLIOB2L\MSSQLSERVER1", "test1"),            
        "INSERT INTO Sheet1 VALUES(@KeyValue,@NumberValue,@DateValue)",            
        [KeyValue="NativeQuery",NumberValue=3,DateValue="2019/1/1"]        
        )

运行结果：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvacpq4srUgbP87rP0AhEvibfibal1iagj7SJJbZsgR5mx6ojHUeMkic7iccTrQ/640?wx_fmt=png)

测试没有问题。



那么重要的就来了：



如果我们能够将PQ返回的表按行转换为一条条的record记录，再逐条导入SQL Server，那么我们的需求就得到了解决。



第一步：使用Table.ToRecords函数将table转为record list：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvactq4wmsgsDqf0QVPuAibZ7AWiay9EbmeibEPsBc24sqH4SibuVdydLG2SeQ/640?wx_fmt=gif)



第二步：我们再做一个循环，逐行读取这些records，并用Value.NativeQuery函数套在这些records上：

insert=
  List.Transform(records,(x)=>
    Value.NativeQuery
            (            
            Sql.Database("DESKTOP-NLIOB2L\MSSQLSERVER1", "test1"),            
            "INSERT INTO Sheet1 VALUES(@KeyValue,@NumberValue,@DateValue)",            
            x        
            )
  )



就得到结果了：

![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvace5mhib0ElicG1kLeOQfFTicZZeoXRaqNqLc8IZOzugowbDYfia8FskaPAA/640?wx_fmt=gif)



还是那句感叹：

![img](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOhFSYQRT6gCntGhzicVysOxen5x5xnzO5Zib2piclRHX7aoYrSZBkHsLVtSsvaOp1bgqMbvUlibP51XSQ/640?wx_fmt=jpeg)



只不过，日期格式跟之前的并不太一致：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvac7yGJ66iaWcEC3hfgcwscDxKMAZf8HJ2Zs6HibaRJjtpt52cL4dOokTnw/640?wx_fmt=png)



好在这并不是什么大问题，在SQL中设置一下datevalue字段的格式为date就可以搞定：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvacjneLp8f1GibibbILN5WMcaib26KNdN79TsibFiaNzS0Sg6M7DdgQdf0m34Q/640?wx_fmt=png)



![img](https://mmbiz.qpic.cn/mmbiz_gif/OyXiackVTfOgfUZfjnTP7I2MY4D4f0G78t3M7lpbYGCM81Z4GORa6QoGvrroWjE8vqdVQictms7hwlibicG6DxicYQQ/640?wx_fmt=gif)



至于刷新时重复导入或者每日刷新多次的问题，大家结合上一篇文章自己就可以解决，无非就是用DELETE函数，这里就不再赘述了。



说到这里，我们再回过头来探讨一下Power BI和MySQL有没有可能也跳过Python这个“中间商”直接交易呢？



看图：

![img](https://mmbiz.qpic.cn/mmbiz_png/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvacyrdfAbBHicl6tEUEapSvgx5Cee5ttSLVO8MRJPPFycCNJfvzwiciaMDdQ/640?wx_fmt=png)



你说呢？

![img](https://mmbiz.qpic.cn/mmbiz_jpg/OyXiackVTfOiaYFMzgSvXEHcsLicyWQtvacqqh0HicqdeX8NsXiaibvhmrKscg6sQNd6tGicic9K6v63h7KQogcBiab9Zicw/640?wx_fmt=jpeg)







------



以下，后续文章预告：



今天我们讲的是PQ生成record列表，再逐个导入SQL中，那有没有办法将PQ中的table作为一个整体导入SQL中呢？



PowerQuery还为我们提供了其他方式，比如调用存储过程。



由于存储过程是SQL语言中很重要的一个内容，我们将用一整篇文章来详细说明，敬请期待。

