#Alibaba
---

一面

* 说一下你怎么学习安卓的？
基础——自己实现小功能——看系统源码——论坛——github优秀开源项目
* 项目中遇到哪些问题，如何解决的？
DLNA
BT
Wifi
FileExplorer
Email
* Android事件分发机制？
![enter description here][1]
1事件分发的本质
答：将点击事件（MotionEvent）向某个View进行传递并最终得到处理，即当一个点击事件发生后，系统需要将这个事件**传递**给一个具体的View去处理。这个事件传递的过程就是分发过程。
2 事件分发过程由哪些方法协作完成？
答：dispatchTouchEvent() 、onInterceptTouchEvent()和onTouchEvent()
事件分发相关方法
![enter description here][2]
[enter description here][3]
![enter description here][4]
* 三级缓存底层实现？
* HashMap底层实现，hashCode如何对应bucket?
* Java的垃圾回收机制，引用计数法两个对象互相引用如何解决？
* 用过的开源框架的源码分析
* Acticity的生命周期，Activity异常退出该如何处理？
* tcp和udp的区别，tcp如何保证可靠的，丢包如何处理？

二面：

* 标号1-n的n个人首尾相接，1到3报数，报到3的退出，求最后一个人的标号
* 给定一个字符串，求第一个不重复的字符 abbcad -> c


面试者：陶程


  [1]: http://upload-images.jianshu.io/upload_images/944365-79b1e86793514e99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240
  [2]: http://upload-images.jianshu.io/upload_images/944365-a5eeeae6ee27682a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240
  [3]: http://blog.csdn.net/carson_ho/article/details/54136311
  [4]: http://upload-images.jianshu.io/upload_images/944365-aa8416fc6d2e5ecd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240