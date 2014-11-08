## 关于R中的一些报错总结 ##
### 1、R： 在读入csv数据时老是出错 ###

    > ALL1997<-read.csv(ALL1997)
    Error in read.table(file = file, header = header, sep = sep, quote = quote,  :
    duplicate 'row.names' are not allowed
这是由于文件中第一列存在重复，导致转换成行名时，考虑加上header=True参数，强制把第一行和第一列都当数据读入，然后再做处理

### 2、处理数据时显示错误subscript too large for 32-bit R，怎么解决，求大神支招，ps我电脑内存4g，32位Win7系统 ###

大神回答：换64位系统，哈哈

### 3、Rstudio启动出现错误 ###

本人按照网上的步骤安装好Rstudio和R（都是最新版本的），但是启动Rstudio时出现错误，如下
Error installing package: 'E:\Program' �����ڲ����ⲿ���Ҳ���ǿ����еĳ���
���������ļ���

Error installing package: 'E:\Program' �����ڲ����ⲿ���Ҳ���ǿ����еĳ���
���������ļ���
我该怎么解决呢？求大神大侠靓仔美女们帮帮忙吧？

回答：不要使用中文和带空格路径。

### 4、ggplot2中设置字体 ###
在《R语言实战》里面有，48页，用windowsFonts()函数，宋体啊、华文行楷什么的随便设置。

###5、R 中日期格式转换时遇到中文月份的问题###

    format(Sys.time(), "%Y-%b-%d")
    #"2014-10月-03"
要把10月改成Oct

    Sys.setlocale("LC_TIME", "English")
    format(Sys.time(), "%Y-%b-%d")
    #"2014-Oct-03"
### 6、请问如何解决Rwordseg无法安装词典的问题 ###
    > installDict("E:/travel.scel")
    Error in paste(dictname, "dic", sep = ".") :
    argument "dictname" is missing, with no default
    > installDict("E:/dic22.dic")
    Error in paste(dictname, "dic", sep = ".") :
    argument "dictname" is missing, with no default

楼主自己回复：自己找到了解决办法。哈哈哈，真像个逗比一样。

主要的问题其实warning中已经说明了，缺少dicname，而installDict中是有这个字段的。补上即可。
完整的语句如下：
    installDict(dictpath='E:/travel.scel',dictname="all1",dicttype="scel")
    installDict(dictpath='E:/dic22.dic',dictname="all",dicttype="text")

### 6、代码改进 ###

    x = c(1:20)
    N=NULL
    for(i in (1:length(x)))
    {
    N[i]=i^2
    }
我现在生成了一个N
    
    > N
    [1] 1 4 9 16 25 36 49 64 81 100 121 144 169 196 225 256 289 324 361 400

那么，我现在想要做的是，找到N[i]最小于80的那个，我可以用这个命令实现
   
    > which(N<=80)
    [1] 1 2 3 4 5 6 7 8

但是我的实现方法还是比较丑陋啊，用什么样的代码，能够直接把这个数字8给我导出来？

    N <- (x <- 1:20) ^ 2
    sum(N<=80)
    [1] 8
