#Android：
---
**五种布局： FrameLayout 、 LinearLayout 、 AbsoluteLayout 、 RelativeLayout 、 TableLayout 全都继承自ViewGroup，各自特点及绘制效率对比。**

* FrameLayout(框架布局)

    此布局是五种布局中最简单的布局，Android中并没有对child view的摆布进行控制，这个布局中所有的控件都会默认出现在视图的左上角，我们可以使用``android:layout_margin``，``android:layout_gravity``等属性去控制子控件相对布局的位置。

* LinearLayout(线性布局)

    一行（或一列）只控制一个控件的线性布局，所以当有很多控件需要在一个界面中列出时，可以用LinearLayout布局。
    此布局有一个需要格外注意的属性:``android:orientation=“horizontal|vertical``。
    
    * 当`android:orientation="horizontal`时，*说明你希望将水平方向的布局交给**LinearLayout** *，其子元素的`android:layout_gravity="right|left"` 等控制水平方向的gravity值都是被忽略的，*此时**LinearLayout**中的子元素都是默认的按照水平从左向右来排*，我们可以用`android:layout_gravity="top|bottom"`等gravity值来控制垂直展示。
    * 反之，可以知道 当`android:orientation="vertical`时，**LinearLayout**对其子元素展示上的的处理方式。

* AbsoluteLayout(绝对布局)

    可以放置多个控件，并且可以自己定义控件的x,y位置

* RelativeLayout(相对布局)

    这个布局也是相对自由的布局，Android 对该布局的child view的 水平layout& 垂直layout做了解析，由此我们可以FrameLayout的基础上使用标签或者Java代码对垂直方向 以及 水平方向 布局中的views进行任意的控制.
    
    * 相关属性：
    
    ```
    
    　　android:layout_centerInParent="true|false"
	　　android:layout_centerHorizontal="true|false"
	　　android:layout_alignParentRight="true|false"
	　　
	```

* TableLayout(表格布局)

    将子元素的位置分配到行或列中，一个TableLayout由许多的TableRow组成
    

---


**Activity生命周期。**

* 启动Activity:
  onCreate()—>onStart()—>onResume()，Activity进入运行状态。
* Activity退居后台:
  当前Activity转到新的Activity界面或按Home键回到主屏：
  onPause()—>onStop()，进入停滞状态。
* Activity返回前台:
  onRestart()—>**onStart()**—>onResume()，再次回到运行状态。
* Activity退居后台，且系统内存不足，
  系统会杀死这个后台状态的Activity（此时这个Activity引用仍然处在任务栈中，只是这个时候引用指向的对象已经为null），若再次回到这个Activity,则会走onCreate()–>onStart()—>onResume()(将重新走一次Activity的初始化生命周期)
* 锁屏：`onPause()->onStop()`
* 解锁：`onStart()->onResume()`
  
* 更多流程分支，请参照以下生命周期流程图
	![](http://img.blog.csdn.net/20130828141902812)


---


**通过Acitivty的xml标签来改变任务栈的默认行为**

* 使用``android:launchMode="standard|singleInstance|singleTask|singleTop"``来控制Acivity任务栈。

    **任务栈**是一种后进先出的结构。位于栈顶的Activity处于焦点状态,当按下back按钮的时候,栈内的Activity会一个一个的出栈,并且调用其``onDestory()``方法。如果栈内没有Activity,那么系统就会回收这个栈,每个APP默认只有一个栈,以APP的包名来命名.

    * standard : 标准模式,每次启动Activity都会创建一个新的Activity实例,并且将其压入任务栈栈顶,而不管这个Activity是否已经存在。Activity的启动三回调(*onCreate()->onStart()->onResume()*)都会执行。
    - singleTop : 栈顶复用模式.这种模式下,如果新Activity已经位于任务栈的栈顶,那么此Activity不会被重新创建,所以它的启动三回调就不会执行,同时Activity的``onNewIntent()``方法会被回调.如果Activity已经存在但是不在栈顶,那么作用与*standard模式*一样.
    - singleTask: 栈内复用模式.创建这样的Activity的时候,系统会先确认它所需任务栈已经创建,否则先创建任务栈.然后放入Activity,如果栈中已经有一个Activity实例,那么这个Activity就会被调到栈顶,``onNewIntent()``,并且singleTask会清理在当前Activity上面的所有Activity.(clear top)
    - singleInstance : 加强版的singleTask模式,这种模式的Activity只能单独位于一个任务栈内,由于栈内复用的特性,后续请求均不会创建新的Activity,除非这个独特的任务栈被系统销毁了

Activity的堆栈管理以ActivityRecord为单位,所有的ActivityRecord都放在一个List里面.可以认为一个ActivityRecord就是一个Activity栈

---

**Activity缓存方法。**

有a、b两个Activity，当从a进入b之后一段时间，可能系统会把a回收，这时候按back，执行的不是a的onRestart而是onCreate方法，a被重新创建一次，这是a中的临时数据和状态可能就丢失了。

可以用Activity中的onSaveInstanceState()回调方法保存临时数据和状态，这个方法一定会在活动被回收之前调用。方法中有一个Bundle参数，putString()、putInt()等方法需要传入两个参数，一个键一个值。数据保存之后会在onCreate中恢复，onCreate也有一个Bundle类型的参数。

示例代码：

```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //这里，当Acivity第一次被创建的时候为空
        //所以我们需要判断一下
        if( savedInstanceState != null ){
            savedInstanceState.getString("anAnt");
        }
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);

        outState.putString("anAnt","Android");

    }

```

一、onSaveInstanceState (Bundle outState)

当某个activity变得“容易”被系统销毁时，该activity的onSaveInstanceState就会被执行，除非该activity是被用户主动销毁的，例如当用户按BACK键的时候。

注意上面的双引号，何为“容易”？言下之意就是该activity还没有被销毁，而仅仅是一种可能性。这种可能性有哪些？通过重写一个activity的所有生命周期的onXXX方法，包括onSaveInstanceState和onRestoreInstanceState方法，我们可以清楚地知道当某个activity（假定为activity A）显示在当前task的最上层时，其onSaveInstanceState方法会在什么时候被执行，有这么几种情况：

1、当用户按下HOME键时。

这是显而易见的，系统不知道你按下HOME后要运行多少其他的程序，自然也不知道activity A是否会被销毁，故系统会调用onSaveInstanceState，让用户有机会保存某些非永久性的数据。以下几种情况的分析都遵循该原则

2、长按HOME键，选择运行其他的程序时。

3、按下电源按键（关闭屏幕显示）时。

4、从activity A中启动一个新的activity时。

5、屏幕方向切换时，例如从竖屏切换到横屏时。（如果不指定configchange属性）
在屏幕切换之前，系统会销毁activity A，在屏幕切换之后系统又会自动地创建activity A，所以onSaveInstanceState一定会被执行
 
总而言之，onSaveInstanceState的调用遵循一个重要原则，即当系统“未经你许可”时销毁了你的activity，则onSaveInstanceState会被系统调用，这是系统的责任，因为它必须要提供一个机会让你保存你的数据（当然你不保存那就随便你了）。另外，需要注意的几点：
 
1.布局中的每一个View默认实现了onSaveInstanceState()方法，这样的话，这个UI的任何改变都会自动地存储和在activity重新创建的时候自动地恢复。但是这种情况只有在你为这个UI提供了唯一的ID之后才起作用，如果没有提供ID，app将不会存储它的状态。
 
2.由于默认的onSaveInstanceState()方法的实现帮助UI存储它的状态，所以如果你需要覆盖这个方法去存储额外的状态信息，你应该在执行任何代码之前都调用父类的onSaveInstanceState()方法（super.onSaveInstanceState()）。
既然有现成的可用，那么我们到底还要不要自己实现onSaveInstanceState()?这得看情况了，如果你自己的派生类中有变量影响到UI，或你程序的行为，当然就要把这个变量也保存了，那么就需要自己实现，否则就不需要。

3.由于onSaveInstanceState()方法调用的不确定性，你应该只使用这个方法去记录activity的瞬间状态（UI的状态）。不应该用这个方法去存储持久化数据。当用户离开这个activity的时候应该在onPause()方法中存储持久化数据（例如应该被存储到数据库中的数据）。
 
4.onSaveInstanceState()如果被调用，这个方法会在onStop()前被触发，但系统并不保证是否在onPause()之前或者之后触发。
 
 
二、onRestoreInstanceState (Bundle outState)

至于onRestoreInstanceState方法，需要注意的是，onSaveInstanceState方法和onRestoreInstanceState方法“不一定”是成对的被调用的，（本人注：我昨晚调试时就发现原来不一定成对被调用的！）
 
onRestoreInstanceState被调用的前提是，activity A“确实”被系统销毁了，而如果仅仅是停留在有这种可能性的情况下，则该方法不会被调用，例如，当正在显示activity A的时候，用户按下HOME键回到主界面，然后用户紧接着又返回到activity A，这种情况下activity A一般不会因为内存的原因被系统销毁，故activity A的onRestoreInstanceState方法不会被执行

另外，onRestoreInstanceState的bundle参数也会传递到onCreate方法中，你也可以选择在onCreate方法中做数据还原。
还有onRestoreInstanceState在onstart之后执行。
至于这两个函数的使用，给出示范代码（留意自定义代码在调用super的前或后）：

```
@Override
public void onSaveInstanceState(Bundle savedInstanceState) {
        savedInstanceState.putBoolean("MyBoolean", true);
        savedInstanceState.putDouble("myDouble", 1.9);
        savedInstanceState.putInt("MyInt", 1);
        savedInstanceState.putString("MyString", "Welcome back to Android");
        // etc.
        super.onSaveInstanceState(savedInstanceState);
}

@Override
public void onRestoreInstanceState(Bundle savedInstanceState) {
        super.onRestoreInstanceState(savedInstanceState);

        boolean myBoolean = savedInstanceState.getBoolean("MyBoolean");
        double myDouble = savedInstanceState.getDouble("myDouble");
        int myInt = savedInstanceState.getInt("MyInt");
        String myString = savedInstanceState.getString("MyString");
}

```

---



**Fragment的生命周期和activity如何的一个关系**

这我们引用本知识库里的一张图片：
![Mou icon](https://github.com/GeniusVJR/LearningNotes/blob/master/Part1/Android/FlowchartDiagram.jpg?raw=true)


**为什么在Service中创建子线程而不是Activity中**

这是因为Activity很难对Thread进行控制，当Activity被销毁之后，就没有任何其它的办法可以再重新获取到之前创建的子线程的实例。而且在一个Activity中创建的子线程，另一个Activity无法对其进行操作。但是Service就不同了，所有的Activity都可以与Service进行关联，然后可以很方便地操作其中的方法，即使Activity被销毁了，之后只要重新与Service建立关联，就又能够获取到原有的Service中Binder的实例。因此，使用Service来处理后台任务，Activity就可以放心地finish，完全不需要担心无法对后台任务进行控制的情况。
还有可能造成内存泄漏！！

**Intent的使用方法，可以传递哪些数据类型。**

通过查询Intent/Bundle的API文档，我们可以获知，Intent/Bundle支持传递基本类型的数据和基本类型的数组数据，以及String/CharSequence类型的数据和String/CharSequence类型的数组数据。而对于其它类型的数据貌似无能为力，其实不然，我们可以在Intent/Bundle的API中看到Intent/Bundle还可以传递Parcelable（包裹化，邮包）和Serializable（序列化）类型的数据，以及它们的数组/列表数据。

所以要让非基本类型和非String/CharSequence类型的数据通过Intent/Bundle来进行传输，我们就需要在数据类型中实现Parcelable接口或是Serializable接口。
Parcelable要实现：writeToParcel、CREATOR 、describeContents。
[http://blog.csdn.net/kkk0526/article/details/7214247](http://blog.csdn.net/kkk0526/article/details/7214247)

**Fragment生命周期**

![](http://7xntdm.com1.z0.glb.clouddn.com/fragment_lifecycle.png)

注意和Activity的相比的区别,按照执行顺序

* onAttach(),onDetach()
* onCreateView(),onDestroyView()

---

**Service的两种启动方法，有什么区别**
1.在Context中通过``public boolean bindService(Intent service,ServiceConnection conn,int flags)`` 方法来进行Service与Context的关联并启动，并且Service的生命周期依附于Context(**不求同时同分同秒生！但求同时同分同秒屎！！**)。

2.通过`` public ComponentName startService(Intent service)``方法去启动一个Service，此时Service的生命周期与启动它的Context无关。

3.要注意的是，whatever，**都需要在xml里注册你的Service**，就像这样:
```
<service
        android:name=".packnameName.youServiceName"
        android:enabled="true" />
```

**广播(Broadcast Receiver)的两种动态注册和静态注册有什么区别。**

* 静态注册：在AndroidManifest.xml文件中进行注册，当App退出后，Receiver仍然可以接收到广播并且进行相应的处理
* 动态注册：在代码中动态注册，当App退出后，也就没办法再接受广播了

---


**ContentProvider使用方法**
继承ContentProvider，重写insert、delete、update、query等。

[http://blog.csdn.net/juetion/article/details/17481039](http://blog.csdn.net/juetion/article/details/17481039)

---

**目前能否保证service不被杀死**

**Service设置成START_STICKY**

* kill 后会被重启（等待5秒左右），重传Intent，保持与重启前一样

**提升service优先级**
 * 在AndroidManifest.xml文件中对于intent-filter可以通过``android:priority = "1000"``这个属性设置最高优先级，1000是最高值，如果数字越小则优先级越低，**同时适用于广播**。
 * 【结论】目前看来，priority这个属性貌似只适用于broadcast，
 
**设置成系统服务**
*在Manifest.xml文件中设置persistent属性为true，则可使该服务免受out-of-memory killer的影响。但是这种做法一定要谨慎，系统服务太多将严重影响系统的整体运行效率。

**提升service进程优先级**
 * Android中的进程是托管的，当系统进程空间紧张的时候，会依照优先级自动进行进程的回收
 * 当service运行在低内存的环境时，将会kill掉一些存在的进程。因此进程的优先级将会很重要，可以在startForeground()使用startForeground()将service放到前台状态。这样在低内存时被kill的几率会低一些。
 * 【结论】如果在极度极度低内存的压力下，该service还是会被kill掉，并且不一定会restart()

**onDestroy方法里重启service**
 * service +broadcast  方式，就是当service走onDestory()的时候，发送一个自定义的广播，当收到广播的时候，重新启动service
 * 也可以直接在onDestroy()里startService
 * 【结论】当使用类似口口管家等第三方应用或是在setting里-应用-强制停止时，APP进程可能就直接被干掉了，onDestroy方法都进不来，所以还是无法保证

**监听系统广播判断Service状态**
 * 通过系统的一些广播，比如：时钟改变（Intent.ACTION_TIME_TICK）、手机重启(BOOT_COMPLETED，貌似4.0以上手机接收不到)、界面唤醒（Intent.ACTION_SCREEN_ON，貌似需要动态注册才行）、应用状态改变等等监听并捕获到，然后判断我们的Service是否还存活，别忘记加权限
 * 【结论】这也能算是一种措施，不过感觉监听多了会导致Service很混乱，带来诸多不便

**在JNI层,用C代码fork一个进程出来**

* 这样产生的进程,会被系统认为是两个不同的进程.但是Android5.0之后可能不行

**root之后放到system/app变成系统级应用**

**大招: 放一个像素在前台(手机QQ)**

其他：http://blog.csdn.net/lonely_fireworks/article/details/18005971

---


**动画有哪两类，各有什么特点？三种动画的区别**

* Tween 补间动画。通过指定View的初末状态和变化时间、方式，对View的内容完成一系列的图形变换来实现动画效果。
Alpha        渐变
Scale        伸缩
Translate  移动
Rotate       旋转
Tween Animation只能应用于View对象，而且只支持一部分属性，如支持缩放旋转而不支持背景颜色的改变。而且对于Tween Animation，并不改变属性的值，它只是改变了View对象绘制的位置，而没有改变View对象本身，比如，你有一个Button，坐标（100,100），Width:100,Height:100，而你有一个动画使其移动（200，200），你会发现动画过程中触发按钮点击的区域仍是(100,100)-(200,200)。

* Frame 帧动画
AnimationDrawable 控制
animation-list xml布局
帧动画是一系列的图片按顺序显示。
使用Frame Animation注意一下问题：
1. 要在代码中调用Imageview的setBackgroundResource方法，如果直接在XML布局文件中设置其src属性当触发动画时会FC。
2. 在动画start()之前要先stop()，不然在第一次动画之后会停在最后一帧，这样动画就只会触发一次。
3. 最后一点是SDK中提到的，不要在onCreate中调用start，因为AnimationDrawable还没有完全跟Window相关联，如果想要界面显示时就开始动画的话，可以在onWindowFoucsChanged()中调用start()。

* PropertyAnimation 属性动画
Property Animation可以定义在xml文件中，它用来在设定的时间内修改对象的属性。例如背景颜色和alpha的值。
http://blog.csdn.net/xyz_lmn/article/details/38667899

---

**Android的数据存储形式。**



* SQLite：SQLite是一个轻量级的数据库，支持基本的SQL语法，是常被采用的一种数据存储方式。
Android为此数据库提供了一个名为SQLiteDatabase的类，封装了一些操作数据库的api

* SharedPreference： 除SQLite数据库外，另一种常用的数据存储方式，其本质就是一个xml文件，常用于存储较简单的参数设置。

* File： 即常说的文件（I/O）存储方法，常用语存储大数量的数据，但是缺点是更新数据将是一件困难的事情。

* ContentProvider: Android系统中能实现所有应用程序共享的一种数据存储方式，由于数据通常在各应用间的是互相私密的，所以此存储方式较少使用，但是其又是必不可少的一种存储方式。例如音频，视频，图片和通讯录，一般都可以采用此种方式进行存储。每个Content Provider都会对外提供一个公共的URI（包装成Uri对象），如果应用程序有数据需要共享时，就需要使用Content Provider为这些数据定义一个URI，然后其他的应用程序就通过Content Provider传入这个URI来对数据进行操作。

---

**Sqlite的基本操作。**


[http://blog.csdn.net/zgljl2012/article/details/44769043](http://blog.csdn.net/zgljl2012/article/details/44769043)
db.beginTransaction();          //事务开始
//建表、增、删、改、查
public void insert(String table, String nullColumnHack, ContentValues values)；
public void delete(String table , String whereClause , String[] whereArgs）；
public void update(String table, ContentValues values, String whereClause, String[] whereArgs);
public Cursor read(String sql ,String[] args);
db.setTransactionSuccessful();      //事务成功
db.endTransaction();                //提交事务

---

**如何判断应用被强杀**

在Application中定义一个**static常量**，赋值为－1，在欢迎界面改为0，如果被强杀，application重新初始化，在父类Activity判断该常量的值。

**应用被强杀如何解决**

如果在每一个Activity的onCreate里判断是否被强杀，冗余了，封装到Activity的父类中，如果被强杀，跳转回主界面，如果没有被强杀，执行Activity的初始化操作，给主界面传递intent参数，主界面会调用onNewIntent方法，在onNewIntent跳转到欢迎页面，重新来一遍流程。

**Json有什么优劣势。**
　　对于JSON，首先要明白JSON和XML一样也是一种简单文本格式。相对于XML，它更加易读、更便于肉眼检查。在语法的层面上，JSON与其他格式的区别是在于分隔数据的字符，JSON中的分隔符限于单引号、小括号、中括号、大括号、冒号和逗号。下图是一个JSON有效负载：

Java代码 复制代码
{"addressbook": {"name": "Mary Lebow",   
    "address": {   
        "street": "5 Main Street"  
        "city": "San Diego, CA",   
        "zip": 91912,   
    },   
    "phoneNumbers": [   
        "619 332-3452",   
        "664 223-4667"  
    ]   
 }   
}  
[java] view plain copy
{"addressbook": {"name": "Mary Lebow",  
    "address": {  
        "street": "5 Main Street"  
        "city": "San Diego, CA",  
        "zip": 91912,  
    },  
    "phoneNumbers": [  
        "619 332-3452",  
        "664 223-4667"  
    ]  
 }  
}  
　　将上面的JSON有效负载用XML改写，如下： 

Xml代码 复制代码
<addressbook>  
 <name>Mary Lebow</name>  
 <address>    
    <street>5 Main Street</street>  
    <city zip="91912"> San Diego, CA </city>  
    <phoneNumbers>  
      <phone>619 332-3452</phone>  
      <phone>664 223-4667</phone>  
    </phoneNumbers>  
 </address>  
</addressbook>  
[xml] view plain copy
<addressbook>  
 <name>Mary Lebow</name>  
 <address>   
    <street>5 Main Street</street>  
    <city zip="91912"> San Diego, CA </city>  
    <phoneNumbers>  
      <phone>619 332-3452</phone>  
      <phone>664 223-4667</phone>  
    </phoneNumbers>  
 </address>  
</addressbook>  
　　是不是很相似？但它们并不相同。下面将详细阐述采用JSON句法的优点和不足。 

优点 
　　乍看上去，使用JSON的数据分隔符的优点可能并不那么明显，但存在一个根本性的缘由：它们**简化了数据访问**。使用这些数据分隔符时， JavaScript引擎对数据结构（如字符串、数组、对象）的内部表示恰好与这些符号相同。 

　　这将开创一条比DOM技术更为便捷的数据访问途径。下面列举几个JavaScript代码片段来说明这一过程，这些代码片段会访问先前的JSON代码片段中的信息： 

访问JSON中的名称： addressbook.name 
访问JSON中的地址： addressbook.address.street 
访问JSON中的电话号码第一位：addressbook.address.phoneNumbers[0] 
　　如果您具备DOM编程经验，就能很快地看出区别；新手可以参看 Document Object Model 的这一外部资源，这里提供了关于数据导航的实例。 

　　JSON的另一个优点是它的**非冗长性**。在XML中，打开和关闭标记是必需的，这样才能满足标记的依从性；而在JSON中，所有这些要求只需通过一个简单的括号即可满足。在包含有数以百计字段的数据交换中，传统的XML标记将会延长数据交换时间。目前还没有正式的研究表明JSON比XML有更高的线上传输效率；人们只是通过简单的字节数比较发现，对于等效的JSON和XML有效负载，前者总是小于后者。至于它们之间的差距有多大，特别是在新的XML压缩格式下它们的差距有多大，有待进一步的研究。 

　　此外，JSON受到了擅长不同编程语言的开发人员的青睐。这是因为无论在Haskell中或 Lisp中，还是在更为主流的C#和PHP中，开发都可以**方便地生成JSON**（详见 参考资料）。 

不足 
　　和许多好东西都具有两面性一样，JSON的非冗长性也不例外，为此JSON丢失了XML具有的一些特性。命名空间允许不同上下文中的相同的信息段彼此混合，然而，显然在JSON中已经找不到了命名空间。JSON与XML的另一个差别是属性的差异，由于JSON采用冒号赋值，这将导致当XML转化为JSON时，在标识符（XML CDATA）与实际属性值之间很难区分谁应该被当作文本考虑。 

　　另外，JSON片段的创建和验证过程比一般的XML稍显复杂。从这一点来看，XML在开发工具方面领先于JSON。尽管如此，为了消除您对这一领域可能存在的困惑，
**怎样退出终止App**
1. Dalvik VM的本地方法
  Android.os.Process.killProcess(android.os.Process.myPid())    //获取PID 
  System.exit(0);   //常规java、c#的标准退出法，返回值为0代表正常退出
 
2. 任务管理器方法
  首先要说明该方法运行在Android 1.5 API Level为3以上才可以，同时需要权限。
  ActivityManager am = (ActivityManager)getSystemService (Context.ACTIVITY_SERVICE);
  am.**restartPackage**(getPackageName()); 
  系统会将，该包下的 ，所有进程，服务，全部杀掉，就可以杀干净了，要注意加上 
  <uses-permission android:name=\"android.permission.RESTART_PACKAGES\"></uses-permission>
3. 根据Activity的生命周期
我们知道Android的窗口类提供了历史栈，我们可以通过stack的原理来巧妙的实现，这里我们在A窗口打开B窗口时在Intent中直接加入标 志Intent.FLAG_ACTIVITY_CLEAR_TOP，这样开启B时将会清除该进程空间的所有Activity。
在A窗口中使用下面的代码调用B窗口：
Intent intent = new Intent(); 
intent.setClass(A.this, B.class); 
intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);  //注意本行的FLAG设置 
startActivity(intent);
接下来在B窗口中需要退出时直接使用finish方法即可全部退出。
4.另类根据Activity的生命周期的方法
建立BaseActivity类，新建的其他activity全部继承BaseActivity这个类，同时在BaseActivity中记录有哪个Activity继承了该类，并放到一个数组中，等系统需要全部退出的时候调用BaseActivity的退出方法，从数组中取出所有的Activity并以此做finish的方法，即可全部退出。

**Asset目录与res目录的区别。**
res 目录下面有很多文件，例如 drawable,mipmap,raw 等。res 下面除了 raw 文件不会被压缩外，其余文件都会被压缩。同时 res目录下的文件可以通过R 文件访问。Asset 也是用来存储资源，但是 asset 文件内容只能通过路径或者 AssetManager 读取。 [官方文档](https://developer.android.com/studio/projects/index.html)

**Android怎么加速启动Activity。**
分两种情况，启动应用 和 普通Activity
启动应用 ：Application 的构造方法，onCreate 方法中不要进行耗时操作，数据预读取(例如 init 数据) 放在异步中操作
启动普通的Activity：A 启动B 时不要在 A 的 onPause 中执行耗时操作。因为 B 的 onResume 方法必须等待 A 的 onPause 。

**Android内存优化方法**
ListView优化，及时关闭资源，图片缓存等等。
http://blog.csdn.net/tuke_tuke/article/details/52316285

**Android中弱引用与软引用的应用场景。**
分析：为什么Handler可能造成内存泄露。
当使用内部类（包括匿名类）来创建Handler的时候，Handler对象会隐式地持有一个外部类对象（通常是一个Activity）的引用，不然你怎么可能通过Handler来操作Activity中的View。而Handler通常会伴随着一个耗时的后台线程（例如从网络拉取图片）一起出现，这个后台线程在任务执行完毕（例如图片下载完毕）之后，通过消息机制通知Handler，然后Handler把图片更新到界面。
然而，如果用户在网络请求过程中关闭了Activity，正常情况下，Activity不再被使用，它就有可能在GC检查时被回收掉，但由于这时线程尚未执行完，而该线程持有Handler的引用（不然它怎么发消息给Handler？），这个Handler又持有Activity的引用，就导致该Activity无法被回收（即内存泄露），直到网络请求结束（例如图片下载完毕）。
解决办法：使用弱引用来解决，模板性代码如下：

package com.crs.demo.ui.widget;  
  
import android.app.Activity;  
import android.graphics.Bitmap;  
import android.os.Bundle;  
import android.os.Handler;  
import android.os.Message;  
import android.support.v4.util.LruCache;  
  
import com.crs.demo.R;  
import com.crs.demo.base.BaseActivity;  
  
import java.lang.ref.WeakReference;  
 
 // Description:软引用和弱引用在android中的使用 
public class TestReferenceActivity extends BaseActivity {  
    static class MyHandler extends Handler {  
        private WeakReference<TestReferenceActivity> reference;  
  
        public MyHandler(Activity activity) {  
            //使用弱引用包裹当前的activity  
            reference = new WeakReference(activity);  
        }  
  
        @Override  
        public void handleMessage(Message msg) {  
            TestReferenceActivity activity = reference.get();  
            //当前Activity不存在或者页面正在关闭的时候，不在进行消息处理  
            if (activity != null && !activity.isFinishing()) {  
                switch (msg.what) {  
                    //此时再进行消息的处理  
                }  
            }  
        }  
    }  
  
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_test_reference);  
  
        //案例一,解决Handler可能造成的内存泄露  
        //通过弱引用实现，如果当前activity需要被回收了,而且Handle持有的activity是被弱引用包装的，则垃圾回收器可以释放掉此activity。  
        MyHandler myHandler = new MyHandler(this);  
    }  
}  

用处二：解决图片存储时，可能造成的内存不足问题
[java] view plain copy 在CODE上查看代码片派生到我的代码片
package com.crs.demo.ui.widget;  
  
import android.graphics.Bitmap;  
import android.os.Bundle;  
import android.support.v4.util.LruCache;  
  
import com.crs.demo.R;  
import com.crs.demo.base.BaseActivity;  
  
import java.lang.ref.SoftReference;  
  
//Description:软引用和弱引用在android中的使用 
public class TestReferenceActivity extends BaseActivity {  
  
    //使用LruCache进行替换,参数为使用多大的内存去存储图片  
    //花费虚拟机八分之一的空间去存储图片  
    //使用LruCache比软引用更好，因为里面有一套算法，根据图片的使用频率去销毁图片。  
    int maxSize = (int) (Runtime.getRuntime().maxMemory() / 8);  
    private LruCache<String, Bitmap> lruCacheMap = new LruCache<String, Bitmap>(maxSize) {  
        @Override  
        protected int sizeOf(String key, Bitmap value) {  
            return value.getRowBytes() * value.getHeight();  
        }  
    };  
  
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_test_reference);  
  
        String imageUrl=null;  
        Bitmap bitmap = null;  
        //使用软引用包裹获取到的bitmap  
        SoftReference<Bitmap> softReference = new SoftReference<>(bitmap);  
        lruCacheMap.put(imageUrl, bitmap);  
  
        //通过url获取缓存  
        Bitmap bit= lruCacheMap.get(imageUrl);  
    }  
}  

用处三：java中引用类型的原理
Java语言的一个重要特性是引入了自动的内存管理机制，使得开发人员不用自己来管理应用中的内存。自动的内存管理则是把管理内存的任务交给编程语言的运行环境来完成。开发人员并不需要关心内存的分配和回收的底层细节。Java平台通过垃圾回收器来进行自动的内存管理。
 
Java垃圾回收机制:
Java的垃圾回收器要负责完成3件任务：分配内存、确保被引用的对象的内存不被错误回收以及回收不再被引用的对象的内存空间。
垃圾回收是一个复杂而且耗时的操作。如果JVM花费过多的时间在垃圾回收上，则势必会影响应用的运行性能。一般情况下，当垃圾回收器在进行回收操作的时候，整个应用的执行是被暂时中止（stop-the-world）的。对于与用户交互的应用来说，则可能希望所垃圾回收所带来的应用停顿的时间间隔越小越好。对于这种情况，JVM中提供了多种垃圾回收方法以及对应的性能调优参数，应用可以根据需要来进行定制。
Java引用类型:
如果一个内存中的对象没有任何引用的话，就说明这个对象已经不再被使用了，从而可以成为被垃圾回收的候选。不过由于垃圾回收器的运行时间不确定，可被垃圾回收的对象的实际被回收时间是不确定的。对于一个对象来说，只要有引用的存在，它就会一直存在于内存中。如果这样的对象越来越多，超出了JVM中的内存总数，JVM就会抛出OutOfMemory错误。虽然垃圾回收的具体运行是由JVM来控制的，但是开发人员仍然可以在一定程度上与垃圾回收器进行交互，其目的在于更好的帮助垃圾回收器管理好应用的内存。

强引用（一般垃圾回收器不回收被强引用的对象）
在一般的Java程序中，见到最多的就是强引用（strong reference）。如Date date = new Date()，date就是一个对象的强引用。对象的强引用可以在程序中到处传递。很多情况下，会同时有多个引用指向同一个对象。强引用的存在限制了对象在内存中的存活时间。假如对象A中包含了一个对象B的强引用，那么一般情况下，对象B的存活时间就不会短于对象A。如果对象A没有显式的把对象B的引用设为null的话，就只有当对象A被垃圾回收之后，对象B才不再有引用指向它，才可能获得被垃圾回收的机会。

软引用 (内存不足的时候，对象被回收)
软引用（soft reference）在强度上弱于强引用，通过类**SoftReference**来表示。它的作用是告诉垃圾回收器，程序中的哪些对象是不那么重要，当内存不足的时候是可以被暂时回收的。当JVM中的内存不足的时候，垃圾回收器会释放那些只被软引用所指向的对象。如果全部释放完这些对象之后，内存还不足，才会抛出OutOfMemory错误。软引用非常适合于创建缓存。当系统内存不足的时候，缓存中的内容是可以被释放的。

弱引用  (回收器只要发现一个对象的引用是全部是弱引用，就会回收此对象、释放内存)
弱引用（weak reference）在强度上弱于软引用，通过类**WeakReference**来表示。它的作用是引用一个对象，但是并不阻止该对象被回收。如果使用一个强引用的话，只要该引用存在，那么被引用的对象是不能被回收的。弱引用则没有这个问题。在垃圾回收器运行的时候，如果一个对象的所有引用都是弱引用的话，该对象会被回收。

**Bitmap的四种属性，与每种属性队形的大小。**


**View与View Group分类。自定义View过程：onMeasure()、onLayout()、onDraw()。**
Android的UI界面都是由View和ViewGroup及其派生类组合而成的。
　　其中，View是所有UI组件的基类，而 ViewGroup是容纳这些组件的容器，其本身也是从View派生出来的.
　　View对象是Android平台中用户界面体现的基础单位。
　　View类是它称为“widgets(工具)”的子类的基础，它们提供了诸如文本输入框和按钮之类的UI对象的完整实现。
　　ViewGroup类同样为其被称为“Layouts(布局)”的子类奠定了基础，它们提供了象流式布局、表格布局以及相对布局之类的布局架构。
　　一般来说，开发Android应用程序的UI界面都不会直接使用View和ViewGroup，而是使用这两大基类的派生类。
  
如何自定义控件

1. 自定义属性的声明和获取
    * 分析需要的自定义属性
    * 在res/values/attrs.xml定义声明
    * 在layout文件中进行使用
    * 在View的构造方法中进行获取       
2. 测量onMeasure
3. 布局onLayout(ViewGroup)
4. 绘制onDraw
5. onTouchEvent
6. onInterceptTouchEvent(ViewGroup)
7. 状态的恢复与保存

View类一般用于绘图操作，重写它的onDraw方法，但它不可以包含其他组件，没有addView(View view)方法。
ViewGroup是一个组件容器，它可以包含任何组件，但必须重写onLayout(boolean changed,int l,int t,int r,int b)和onMesure(int widthMesureSpec,int heightMesureSpec)方法. 否则ViewGroup中添加组件是不会显示的。
http://www.ixueyi.com/jingyan/1846454.html

**Android长连接，怎么处理心跳机制。**
http://blog.csdn.net/sunmenggmail/article/details/12008075

---

**View树绘制流程**

---

**下拉刷新实现原理**

---

**你用过什么框架，是否看过源码，是否知道底层原理。**

Retrofit

EventBus

glide

---

**Android5.0、6.0、7.0新特性。**

Android5.0新特性：

* MaterialDesign设计风格
* 支持多种设备
* 支持64位ART虚拟机

Android6.0新特性

* 大量漂亮流畅的动画
* 支持快速充电的切换
* 支持文件夹拖拽应用
* 相机新增专业模式

Android7.0新特性

* 分屏多任务
* 增强的Java8语言模式
* 夜间模式

---


**Context区别**

* Activity和Service以及Application的Context是不一样的,`Activity继承自ContextThemeWraper.其他的继承自ContextWrapper`
* 每一个Activity和Service以及Application的Context都是一个新的ContextImpl对象
* getApplication()用来获取Application实例的，但是这个方法`只有在Activity和Service中才能调用的到`。那么也许在绝大多数情况下我们都是在Activity或者Service中使用Application的，但是如果在一些其它的场景，比如BroadcastReceiver中也想获得Application的实例，这时就可以借助getApplicationContext()方法，getApplicationContext()比getApplication()方法的作用域会更广一些，任何一个Context的实例，只要调用getApplicationContext()方法都可以拿到我们的Application对象。
* Activity在创建的时候会new一个ContextImpl对象并在attach方法中关联它，Application和Service也差不多。ContextWrapper的方法内部都是转调ContextImpl的方法
* 创建对话框传入Application的Context是不可以的
* 尽管Application、Activity、Service都有自己的ContextImpl，并且每个ContextImpl都有自己的mResources成员，但是由于它们的mResources成员都来自于唯一的ResourcesManager实例，所以它们看似不同的mResources其实都指向的是同一块内存
* Context的数量等于Activity的个数 + Service的个数 + 1，这个1为Application

---

**IntentService的使用场景与特点。**

>IntentService是Service的子类，是一个*异步*的，会*自动停止*的服务，很好解决了传统的Service中处理完耗时操作忘记停止并销毁Service的问题

优点：

* 一方面不需要自己去new Thread
* 另一方面不需要考虑在什么时候关闭该Service

onStartCommand中回调了onStart，onStart中通过mServiceHandler发送消息到该handler的handleMessage中去。最后handleMessage中回调onHandleIntent(intent)。onHandleIntent(Intent)是抽象函数，由子类实现。

哪里体现了不用手动创建工作子线程？
在onCreate里：
  HandlerThread thread = new HandlerThread("IntentService[" + mName + "]");
  thread.start();
  mServiceLooper = thread.getLooper();
  mServiceHandler = new ServiceHandler(mServiceLooper);


---


**图片缓存**

查看每个应用程序最高可用内存：

```
    int maxMemory = (int) (Runtime.getRuntime().maxMemory() / 1024);  
    Log.d("TAG", "Max memory is " + maxMemory + "KB");  
```

---

**Gradle**

构建工具、Groovy语法、Java

Jar包里面只有代码，aar里面不光有代码还包括代码还包括资源文件，比如 drawable 文件，xml 资源文件。对于一些不常变动的 Android Library，我们可以直接引用 aar，加快编译速度

---

**你是如何自学Android**

首先是看书和看视频敲代码，然后看大牛的博客，做一些项目，向github提交代码，觉得自己API掌握的不错之后，开始看进阶的书，以及看源码，看完源码学习到一些思想，开始自己造轮子，开始想代码的提升，比如设计模式，架构，重构等。

---



