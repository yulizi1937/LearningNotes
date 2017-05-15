#Android关于OOM的解决方案
##OOM
* 内存溢出（Out Of Memory）
* 也就是说内存占有量超过了VM所分配的最大


##出现OOM的原因
1. 加载对象过大
2. 相应资源过多，来不及释放

##如何解决
1. 在内存引用上做些处理，常用的有软引用、强化引用、弱引用
2. 在内存中加载图片时直接在内存中作处理，如边界压缩。（主要是修改采样率inSampleSize）
``` processing
// 图片解析的配置
BitmapFactory.Options opts = new Options();
// 不去解析图片信息，只是得到图片的头部宽、高信息 
opts.inJustDecodeBounds = true;
BitmapFactory.decodeFile("/sdcard/a.jpg", opts);
int imageHeight = opts.outHeight;
int imagewidth = opts.outWidth;
System.out.println("图片宽：" + imagewidth);
System.out.println("图片高" + imageHeight);
// 计算缩放比例
int scaleX = imagewidth / windowWidth;
int scaleY = imageHeight / windowHeight;
int scale = 1;
if (scaleX > scaleY & scaleY >= 1) {
scale = scaleX;
}
if (scaleY > scaleX & scaleX >= 1) {
scale = scaleY;
}
//解析图片
opts.inJustDecodeBounds = false;
//采样率
opts.inSampleSize = scale;
Bitmap bitmap = BitmapFactory.decodeFile("/sdcard/a.jpg", opts);
```
3. 动态回收内存
4. 优化Dalvik虚拟机的堆内存分配
5. 自定义堆内存大小
对于Android平台来说，其托管层使用的Dalvik Java VM从目前的表现来看还有很多地方可以优化处理，比如我们在开发一些大型游戏或耗资源的应用中可能考虑手动干涉GC处理，使用dalvik.system.VMRuntime类提供的setTargetHeapUtilization方法可以增强程序堆内存的处理效率。当然具体原理我们可以参考开源工程，这里我们仅说下使用方法:private final static float TARGET_HEAP_UTILIZATION = 0.75f;在程序onCreate时就可以调用VMRuntime.getRuntime().**setTargetHeapUtilization**(TARGET_HEAP_UTILIZATION);即可。
Android堆内存也可自己定义大小
    对于一些Android项目，影响性能瓶颈的主要是Android自己内存管理机制问题，目前手机厂商对RAM都比较吝啬，对于软件的流畅性来说RAM对性能的影响十分敏感，除了 优化Dalvik虚拟机的堆内存分配外，我们还可以强制定义自己软件的堆内存大小，我们使用Dalvik提供的 dalvik.system.VMRuntime类来设置最小堆内存为例:
       private final static int CWJ_HEAP_SIZE = 6* 1024* 1024 ;
       VMRuntime.getRuntime().**setMinimumHeapSize**(CWJ_HEAP_SIZE); //设置最小heap内存为6MB大小。当然对于内存吃紧来说还可以通过手动干涉GC去处理



