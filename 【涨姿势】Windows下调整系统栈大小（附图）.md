我们知道，递归以及开局部变量都是要占用栈空间的
而Windows默认给每个线程仅仅分配1M内存（大神说是这样的）
这时就需要手动调整系统栈大小了。

以下转自[Lynstery](http://blog.csdn.net/chhnz/article/details/70544399)：
在用gcc/g++编译时指定参数 
-Wl,--stack=size 
size是栈的大小，单位为字节。 
比如我现在要编译一个名为hh的c++程序，栈的大小要16M，就这样

![这里写图片描述](http://img.blog.csdn.net/20170423200452307?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQ0hITlo=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如果是像本蒟蒻一样用dev-c++的，那么可以 

![这里写图片描述](http://img.blog.csdn.net/20170423201619108?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQ0hITlo=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

点编译选项，然后

![这里写图片描述](http://img.blog.csdn.net/20170423201732047?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQ0hITlo=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

就可以了