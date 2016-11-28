Create Different Bitmaps
You should always provide bitmap resources that are properly scaled to each of the generalized density buckets: low, medium, high and extra-high density. This helps you achieve good graphical quality and performance on all screen densities.

To generate these images, you should start with your raw resource in vector format and generate the images for each density using the following size scale:

xhdpi: 2.0
hdpi: 1.5
mdpi: 1.0 (baseline)
ldpi: 0.75
This means that if you generate a 200x200 image for xhdpi devices, you should generate the same resource in 150x150 for hdpi, 100x100 for mdpi, and 75x75 for ldpi devices.

Then, place the files in the appropriate drawable resource directory:

MyProject/
    res/
        drawable-xhdpi/
            awesomeimage.png
        drawable-hdpi/
            awesomeimage.png
        drawable-mdpi/
            awesomeimage.png
        drawable-ldpi/
            awesomeimage.png
Any time you reference @drawable/awesomeimage, the system selects the appropriate bitmap based on the screen's density.

Note: Low-density (ldpi) resources aren’t always necessary. When you provide hdpi assets, the system scales them down by one half to properly fit ldpi screens.

For more tips and guidelines about creating icon assets for your app, see the Iconography design guide.



为什么要屏幕适配  http://www.cocoachina.com/android/20151030/13971.html
http://www.jianshu.com/p/ec5a1a30694b

http://www.zcool.com.cn/article/ZNjI3NDQ=.html

http://www.jianshu.com/p/ec5a1a30694b

百分布局  http://blog.csdn.net/lmj623565791/article/details/46695347

Android是如何在不同屏幕上适配图片的 -- 或控件大小
http://blog.csdn.net/chinalwb/article/details/18843101

android多分辨率多屏幕密度下UI适配方案
http://www.cnblogs.com/maxinliang/p/3170711.html

Android 应用设计开发有没有更好的 UI 适配方法？
https://www.zhihu.com/question/22181844

https://developer.android.com/training/best-ui.html
