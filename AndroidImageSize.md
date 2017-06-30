
创建一个Bitmap时，其单位像素占用的字节数由其参数BitmapFactory.Options的inPreferredConfig变量决定。inPreferredConfig为Bitmap.Config类型，它可以为以下值：
``` markdown
图片格式（Bitmap.Config）  | 占用内存的计算方式  | 一张100*100的图片占用内存的大小
:----|:-----:|-----:
ALPHA_8代表8位Alpha位图  | 图片长度*图片宽度  | 100 * 100=10000字节
ARGB_4444代表16位ARGB位图  | 图片长度*图片宽度 * 2  | 100 * 100 * 2=20000字节
ARGB_8888代表32位ARGB位图  | 	图片长度*图片宽度 * 4  | 	100 * 100 * 4=40000字节
RGB_565代表8位RGB位图  | 图片长度*图片宽度 * 2  | 100 * 100 * 2=20000字节
 ``` 
 另外，需要注意这里的图片占用内存是指在Navtive中占用的内存，当然Bitmap使用的绝大多数内存就是该内存。
因此，我们可以简单的认为它就是Bitmap所占用的内存。
Bitmap对象在不使用时,我们应该先调用recycle()，然后才它设置为null.
虽然Bitmap在被回收时可以通过BitmapFinalizer来回收内存。但是调用recycle()是一个良好的习惯
在Android4.0之前，Bitmap的内存是分配在Native堆中，调用recycle()可以立即释放Native内存。
从Android4.0开始，Bitmap的内存就是分配在dalvik堆中，即Java堆中的，调用recycle()并不能立即释放Native内存。但是调用recycle()也是一个良好的习惯。

 density | densityDpi | drawable
 :----:|:-----:|:-----:
 0.75 | 120 | l 
 1	| 160|m
 1.5|240|h
 2	|320|xh
 3	|480|xxh
 3.5|560 | xxxh
 4|640|xxxxh
 
 图片大小计算公式：
 假设图片宽为Xpx，高为Ypx，把图片放到dpi为Z dpi的drawable目录下(h,xxh,xxxh)，运行时手机的dpi为M dpi，图片的Bitmap.Config，为ARGB_8888的32位图，则图片在运行的手机上所占用的内存为：
 
**在手机上图片大小 = ( X*M/Z +0.5)*(Y*M/Z+0.5)*4**
 
 
 http://bugly.qq.com/bbs/forum.php?mod=viewthread&tid=498
 http://www.cnblogs.com/jiangz/p/3428186.html
 http://bugly.qq.com/bbs/forum.php?mod=viewthread&tid=498
 http://blog.csdn.net/zhaokaiqiang1992/article/details/49787117
 http://sodino.com/2015/05/25/check-bitmap-size/
 http://mp.weixin.qq.com/s?__biz=MzI3MDE0NzYwNA==&mid=2651433713&idx=1&sn=d152b053221c4c0bf1baa684b2a51e9c&scene=23&srcid=0805ti3zGykdgDuw65pZt6Wr#rd
 https://github.com/bingoogolapple/bingoogolapple.github.io/issues/109