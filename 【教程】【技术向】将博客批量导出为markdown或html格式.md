前言
--
最近突然想要备份一下自己的blog
但是上百篇博客，手动导出肯定要累死
所以LZ就上网搜索，自己捣鼓了一下
于是就有了这篇教程

注：本文的读者定向是小白，各位dalao如果觉得步骤太罗嗦可以跳过
本教程中所有用到的资源已经打包上传网盘
连接在最后给出，dalao们可以下载食用

教程
--
LZ首先在网上搜到了[这篇文章](http://www.jianshu.com/p/02ef1a98bf56)
果断下载脚本
但是LZ没有用过Python啊
于是又到官网上下载来Python的安装包：
![这里写图片描述](http://img.blog.csdn.net/20170802215041953?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

安装一路确定就好了
然后添加环境变量：
![这里写图片描述](http://img.blog.csdn.net/20170802215441255?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

然后满心欢喜地运行脚本：
![这里写图片描述](http://img.blog.csdn.net/20170802220043857?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
然后……然后就炸了！！
原来是缺少一个叫"BeautifulSoup"的库
又去下载，如下安装：
将压缩包解压到Python的安装目录下：
![这里写图片描述](http://img.blog.csdn.net/20170802221301924?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
然后打开cmd
按照下面输入：
![这里写图片描述](http://img.blog.csdn.net/20170802221456520?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
就成功安装了BeautifulSoup这个库

然后再回到导出工具的目录，输入如下命令：
![这里写图片描述](http://img.blog.csdn.net/20170802221646584?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
然后就成功导出啦！！
![这里写图片描述](http://img.blog.csdn.net/20170802221802842?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

看一看效果：
![这里写图片描述](http://img.blog.csdn.net/20170802221934255?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
心中真是一阵舒爽啊

资源
--
用的是度盘，为了防吞加了点奇怪的字符
链接: pan点baidu点com/s/1dFtinK1 密码: 9t3p