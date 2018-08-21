# android-
Android基础学习笔记
1.Log调试
所有的输入：String TAG，String Message；
TAG一般为类名或方法名，标记该日志输出来源，Message为输出的具体信息。
1.	Log.v() 
这个方法用于打印那些最为琐碎的，意义最小的日志信息。对应级别verbose，是Android 日志里面级别最低的一种。
2.	Log.d() 
这个方法用于打印一些调试信息，这些信息对调试程序和分析问题应该是有帮助的。对应级别debug，比verbose 高一级。
3.	Log.i() 
这个方法用于打印一些比较重要的数据，这些数据应该是非常想看到的，可以帮助分析用户行为的那种。对应级别info，比debug 高一级。
4.	Log.w() 
这个方法用于打印一些警告信息，提示程序在这个地方可能会有潜在的风险，最好去修复一下这些出现警告的地方。对应级别warn，比info 高一级。
5.	Log.e() 
这个方法用于打印程序中的错误信息，比如程序进入到了catch 语句当中。当有错误信息打印出来的时候，一般都代表程序出现严重问题了，必须尽快修复。对应级别error，比warn 高一级。
2.Activity
2.1 Activity生命周期
Activity是由Activity栈进管理，当来到一个新的Activity后，此Activity将被加入到Activity栈顶，之前的Activity位于此Activity底部。如下图所示，Acitivity一般意义上有四种状态：
1.当Activity位于栈顶时，此时正好处于屏幕最前方，此时处于运行状态；
2.当Activity失去了焦点但仍然对用于可见（如栈顶的Activity是透明的或者栈顶Activity并不是铺满整个手机屏幕），此时处于暂停状态；
3.当Activity被其他Activity完全遮挡，此时此Activity对用户不可见，此时处于停止状态；
4.当Activity由于人为或系统原因（如低内存等）被销毁，此时处于销毁状态；
在每个不同的状态阶段，Adnroid系统对Activity内相应的方法进行了回调。因此，在程序中写Activity时，一般都是继承Activity类并重写相应的回调方法。
 
Activity实例是由系统自动创建，并在不同的状态期间回调相应的方法。一个最简单的完整的Activity生命周期会按照如下顺序回调：onCreate -> onStart -> onResume -> onPause -> onStop -> onDestroy。称之为entire lifetime。
当执行onStart回调方法时，Activity开始被用户所见，一直到onStop之前，此阶段Activity都是被用户可见，称之为visible lifetime。
当执行到onResume回调方法时，Activity可以响应用户交互，一直到onPause方法之前，此阶段Activity称之为foreground lifetime。
可通过重写Activity的上述方式，使用Log跟踪Activity的生命周期，形如：
protected void onStart(){
    super.onStart();
    Log.d("First Activity","First onStart");
    }
示例：创建两个活动FirstActivity，SecondActivity，FirstActivity加载Button，点击即跳入SecondActivity，调试结果如下：
1.	当app运行时FirstActivity：onCreate->onStart->onResume；
2.	点击Button，FirstActivity：onPause->onStop；
SecondActivity： onCreate->onStart->onResume；
3.	在SecondActivity点击back键，SecondActivity：onPause->onStop->onDestroy；
FirstActivity：onStart->onResume；
4.	回到FirstActivity，点击home，FirstActivity：onPause->onStop；
重新打开app，FirstActivity：onStart->onResume；
点击back：FirstActivity：onPause->onStop->onDestroy；
2.2 Activity启动模式
Activity的启动模式有4种，分别是standard.singleTop. SingleTask. singleInstance，可以在AndroidMainifest.xml文件中指定每一个Activity的启动模式。一个Android应用一般都会有多个Activity，系统会通过任务栈来管理这些Activity，栈是一种后进先出的集合，当前的Activity就在栈顶，按返回键，栈顶Activity就会退出。Activity启动模式不同，系统通过任务栈管理Activity的方式也会不同，以下将分别介绍。
1.	Standard模式
Standard模式是Android的默认启动模式，不在配置文件中做任何设置，那么这个Activity就是standard模式，这种模式下，Activity可以有多个实例，每次启动Activity，无论任务栈中是否已经有这个Activity的实例，系统都会创建一个新的Activity实例。
2.	SingleTop模式
SingleTop模式和standard模式非常相似，主要区别就是当一个singleTop模式的Activity已经位于任务栈的栈顶，再去启动它时，不会再创建新的实例,如果不位于栈顶，就会创建新的实例。
当一个Activity已经在栈顶，但依然有可能启动它，而又不想产生新的Activity实例，此时就可以用singleTop模式。例如，一个搜索Activity，可以输入搜索内容，也可以产生搜索结果，此时就可以用singleTop模式，不会用户每次搜索都会产生一个实例。
3.	SingleTask模式
SingleTask模式的Activity在同一个Task内只有一个实例，如果Activity已经位于栈顶，系统不会创建新的Activity实例，和singleTop模式一样。但Activity已经存在但不位于栈顶时，系统就会把该Activity移到栈顶，并把它上面的activity出栈。
singleTask模式和前面两种模式的最大区别就是singleTask模式是任务内单例的，所以是否设定Activity为singleTask模式，主要 activity是否需要单例，例如某个Activity里面有一个列表，如果有多个实例，有可能导致用户看到的列表不一致，有的Activity需要经常启动，如果每次都创建实例，会导致占用资源过多，这些情况都可以使用singleTask模式，但启动singleTask模式的Activity会导致任务栈内它上面的Activity被销毁，使用时要注意。
4.	singleInstance模式
singleInstance模式也是单例的，但和singleTask不同，singleTask只是任务栈内单例，系统里是可以有多个singleTask Activity实例的，而singleInstance Activity在整个系统里只有一个实例，启动一singleInstanceActivity时，系统会创建一个新的任务栈，并且这个任务栈只有他一个Activity。
它往往用于多个应用之间，例如一个电视launcher里的Activity，通过遥控器某个键在任何情况可以启动，这个Activity就可以设置为singleInstance模式，当在某应用中按键启动这个Activity，处理完后按返回键，就会回到之前启动它的应用，不影响用户体验。
3.Intent
Android中提供了Intent机制来协助应用间的交互与通讯，或者采用更准确的说法是，Intent不仅可用于应用程序之间，也可用于应用程序内部的activity, service和broadcast receiver之间的交互。
Intent是一种运行时绑定（runtime binding)机制，它能在程序运行的过程中连接两个不同的组件。通过Intent，程序可以向Android表达某种请求或者意愿，Android会根据Intent的内容选择适当的组件来响应。activity、service和broadcast receiver之间是通过Intent进行通信的
为了确保应用的安全性，启动 Service 时，始终使用显式 Intent。
3.1 Intent属性
1.	component(组件)：
Component属性明确指定Intent的目标组件的类名称。如果 component这个属性有指定的话，将直接使用它指定的组件。指定了这个属性以后，Intent的其它所有属性都是可选的。
例如：
Intent intent = new Intent();//创建组件，通过组件来响应
ComponentName component = new ComponentName
(MainActivity.this, SecondActivity.class);
intent.setComponent(component);    
2.	Action（动作）：用来表现意图的行动
在Intent中，Action就是描述做、写等动作的，当指明了一个Action，执行者就会依照这个动作的指示，接受相关输入，表现对应行为，产生符合的输出。在Intent类中，定义了一批量的动作，比如ACTION_VIEW，ACTION_PICK等， 基本涵盖了常用动作。加的动作越多，越精确。
Action 是一个用户定义的字符串，用于描述一个 Android 应用程序组件，一个 Intent Filter 可以包含多个 Action。在 AndroidManifest.xml 的Activity 定义时，可以在其 <intent-filter >节点指定一个 Action列表用于标识 Activity 所能接受的“动作”。
常用Action：
ACTION_MAIN	表示程序入口
ACTION_VIEW	自动以最合适的方式显示Data
ACTION_EDIT	提供可以编辑的
ACTION_PICK	选择一个一条Data，并且返回它
ACTION_DAIL	显示Data指向的号码在拨号界面Dailer上
ACTION_CALL	拨打Data指向的号码
ACTION_SEND	发送Data到指定的地方
ACTION_SENDTO	发送多组Data到指定的地方
ACTION_RUN	运行Data，不管Data是什么
ACTION_SEARCH	执行搜索
ACTION_WEB_SEARCH	执行网上搜索
ACRION_SYNC	执同步一个Data
ACTION_INSERT	添加一个空的项到容器中
ACTION_TIME_TICK	当前时间改变，并即时发送时间，只能通过系统发送。调用格式"android.intent.action.TIME_TICK"
ACTION_TIME_CHENGED	设置时间。调用格式"android.intent.action.TIME_SET"

3.	category（category）：Action的附加信息
CATEGORY_DEFAULT	把一个组件Component设为可被implicit启动的。
CATEGORY_LAUNCHER	把一个action设置为在顶级执行。并且包含这个属性的Activity所定义的icon将取代application中定义的icon。
CATEGORY_BROWSABLE	当Intent指向网络相关时，必须要添加这个category
4.	data：Action操作的数据
使用URI表示，一条URI的结构是这样的：<scheme>://<host>:<port>/<path>，要仅设置数据 URI，调用 setData()。 要仅设置 MIME 类型，调用 setType()。如有必要，可以使用 setDataAndType() 同时显式设置二者。
注意：若要同时设置 URI 和 MIME 类型，请勿调用 setData() 和 setType()，因为它们会互相抵消彼此的值。始终使用 setDataAndType() 同时设置 URI 和 MIME 类型。
5.	Type：指定数据MIME类型
示例：<data Android:mimeType="video/mpeg" Android:scheme="http" ... />
      <data Android:mimeType="text/plain"/>
6.	Extra:组件间传递的附加信息或数据
可以使用各种 putExtra() 方法添加 extra 数据，每种方法均接受两个参数：键名和值。您还可以创建一个包含所有 extra 数据的 Bundle 对象，然后使用 putExtras() 将Bundle 插入 Intent 中。
例如：
intent.putExtra("sms_body", "具体短信内容"); //传递短信中的具体内容
7.	Flag：指定启动模式
3.2 Intent构建方式
1.	显示构建Intent
显式 Intent 是指用于启动某个特定component（例如，应用中的某个特定 Activity 或服务）的 Intent。 创建显式 Intent，为 Intent 对象定义组件名称 — Intent 的所有其他属性均为可选属性。
示例：
Intent downloadIntent = new Intent(this, DownloadService.class);
downloadIntent.setData(Uri.parse(fileUrl));
startService(downloadIntent);
2.	隐示构建Intent
隐式 Intent 指定能够在可以执行相应操作的设备上调用任何应用的操作，系统将检查已安装的所有应用，确定哪些应用能够处理这种 Intent。 如果只有一个应用能够处理，则该应用将立即打开并为其提供 Intent。 如果多个 Activity 接受 Intent，则系统将显示一个对话框，使用户能够选取要使用的应用。
在AndroidManifest.xml文件<activity></activituy>中添加<intent-fileter></intent-filter>，指明该活动可响应的action、data、与category，当且仅当三项均满足时，则该活动可被相应的intent启动，流程如下图所示。
 
Action： Intent 中指定的action必须与filter中列出的某一action匹配。如果该filter未列出任何action，则 Intent 没有任何匹配项，因此所有 Intent 均无法通过测试。 但是，如果 Intent 未指定action，则会通过测试（只要filter至少包含一个action）。
Category：若要使 Intent 通过category测试，则 Intent 中的每个category均必须与filter中的category匹配。Intent filter声明的category可以超出 Intent 中指定的数量，且 Intent 仍会通过测试。 因此，不含category的 Intent 应当始终会匹配。如需 Activity 接收隐式 Intent，则必须将  "android.intent.category.DEFAULT" 的类别包括在其 Intent filter中。
Data：<scheme>://<host>:<port>/<path>，若只指定schema，则所有schema下的URI均匹配，以此逐级匹配。若filter未指定URI或MIME类型，则只有不带URI或MIME的intent可匹配该filter。

1.	强制使用应用选择器
如果多个应用可以响应 Intent，且用户可能希望每次使用不同的应用，则应采用显式方式显示选择器对话框。 选择器对话框每次都会要求用户选择用于操作的应用（用户无法为该操作选择默认应用）。
示例：
Intent sendIntent = new Intent(Intent.ACTION_SEND);
...
String title = getResources().getString(R.string.chooser_title);
Intent chooser = Intent.createChooser(sendIntent, title);
if (sendIntent.resolveActivity(getPackageManager()) != null) {
 startActivity(chooser);
}
4.  Notification
Notification用于给用户一些提示信息，在状态栏显示想要展示的信息，能够在Service、BroadcastReceiver以及Activity中创建。
4.1 显示通知
1.	首先创建一个NotificationManager用于负责发送通知，清楚通知，如下所NotificationManager manager = (NotificationManager)
getSystemService(Contex.NOTIFICATION_SERVICE):
2.	然后通过NotificationCompat.Builder（出于兼容性使用NotificationCompat）对象为通知指定UI信息以及操作，调用NotificationCompat.Builder.build()创建包含具体信息与动作的Notification 对象，如下所示：
NotificationCompat.Builder mBuilder =new NotificationCompat.Builder(this)
        .setSmallIcon(R.drawable.notification_icon)
        .setContentTitle("My notification")
        .setContentText("Hello World!");
Notification notification = mBuilder.build();
注：Notification对象必须包含小图标（由setSmallIcon()设置），标题（setContentTitle()设置）以及详细文本（setContentText()设置），否则无法正常显示。
3.	使用通过NotificationManager  的 notify(int, Notification) 方法来启动Notification。第一个参数唯一的标识该Notification，第二个参数就是Notification对象，通过cancel(int)方法，来清除某个通知。其中参数就是Notification的唯一标识ID。也可以通过  cancelAll() 来清除状态栏所有的通知，如下：
manager.notify(1,notification);
manager.cancel(1);
4.2 适配Android O版本
Android O版本引入了NotificationChannel类来设置通知的主题，以下设置不再由上述的NotificationCompat.Builder来设置：
1.	是否锁屏可见（setLockscreenVisibility(int lockscreenVisibility)）；
2.	重要级别（setImportance(int importance)）。
3.	是否可以绕过请勿打打扰（setBypassDnd()设置）；
4.	通知led信号灯（enableLights(boolean lights)设置是否允许，setLightColor(int argb)设置颜色）；
5.	震动（enableVibration(boolean vibration)设置是否应该震动，setVibrationPattern(long[] vibrationPattern)设置震动模式）；
6.	声音（setSound(Uri sound, AudioAttributes audioAttributes)通知声音）；
7.	如下所示：
String id = "my_channel_01"; //id,必须唯一
CharSequence name = getString(R.string.channel_name); // 用户可以看到的通知渠道的名字.
int importance = NotificationManager.IMPORTANCE_HIGH; 
NotificationChannel mChannel = new NotificationChannel(id, name, importance); 
mChannel.enableLights(true); // 设置通知出现时的闪光灯
mChannel.setLightColor(Color.RED);
mChannel.enableVibration(true); // 设置通知出现时的震动
mChannel.setVibrationPattern(new long[]{100, 200, 300, 400, 500, 400, 300, 200, 400});
 mNotificationManager.createNotificationChannel(mChannel); //在notificationmanager中创建该通知渠道 
notification的创建方式修改为：
NotificationCompat.Builder mBuilder =
new NotificationCompat.Builder(this，mChannel);
4.3 其他属性设置
1.	点击通过Intent跳转：
用户可以通过点击通知跳转到Activity、BroadcastReceiver以及Service，首先通过PendingIntent类的getActivity()，getBroadcast()以及getService()方法来构建一个PendingIntent对象，这些方法都接受四个参数：
Context context	启动环境
int requesetCode	请求码，发件人的私人请求代码（当前未使用）。
Intent intent	定义的Intent
int flags	FLAG_CANCEL_CURRENT:如果当前系统中已经存在一个相同的PendingIntent对象，那么就将先将已有的PendingIntent取消，然后重新生成一个PendingIntent对象。
FLAG_NO_CREATE:如果当前系统中不存在相同的PendingIntent对象，系统将不会创建该PendingIntent对象而是直接返回null。
FLAG_ONE_SHOT:该PendingIntent只作用一次。在该PendingIntent对象通过send()方法触发过后，PendingIntent将自动调用cancel()进行销毁，那么如果再调用send()方法的话，系统将会返回一个SendIntentException。
FLAG_UPDATE_CURRENT:如果系统中有对等的PendingInent，那么系统将使用该PendingIntent对象，但是会使用新的Intent来更新之前PendingIntent中的Intent对象数据，例如更新Intent中的Extras。
如下所示：
Intent intent = new Intent（…）;
PendingIntent pi = PendingIntent.setActivity(this,0,intent,0);
…Builder().
setContentIntent(pi);
2.	setPriority()定义优先级
3.	setStyle()定义通知样式
5. BroadcastReceiver
为了便于系统级别的消息通知，Android引入了广播机制，用于广播一些系统发生的变化，例如当网络连接改变，电池电量低时，系统将发送广播，用户可在程序中注册BroadcastReceiver用于监听广播，当收到广播后做出对应的操作。BroadcastReceiver的注册有两种方式：动态注册与静态注册。
5.1 动态注册
1.	继承BroadcastReceiver的子类，并重写onReceive()方法，onReceive()方法中用于执行接受到广播后需要进行的操作。如下所示：
class MyBroadcastReceiver extends BroadcastReceiver{
public void onReceive(){
…//接受广播需要进行的操作
}
}
注： onReceive()方法不应添加过多的逻辑或者耗时的操作，因为BroadcastReceiver不允许开启多线程，当onReceive()方法过久没有结束，程序将报错。因此一般仅进行打开其他组件，启动一个服务，创建一个通知栏等操作。
2.	在需要接受广播的Service或Activity中注册广播，建立一个对应的BroadcastReceiver对象，建立对应action或category的IntentFilter对象，调用方法registerReceiver(Broadcast …，IntentFilter …)对广播进行注册，调用unregisterReceiver(Broadcast …)取消注册。如下所示：
public class MyService extends Service{
private IntentFilter intentFilter;
MyBroadcastReceiver receiver;
public void onCreate(){
       …
       receiver = new MyBroadcastReceiver();
       intentFilter = new IntentFilter(“action”);//指定对应的action
       registerReceiver(receiver,intentFilter);
}
public void onDestroy{
       …
       unregisterReceriver(receriver);
}
}
3.	在需要发送广播的地方，定义对应的Intent，调用sendBroadcast(Intent …)或sendOrderedBroadcast(Intent  …)发送广播。例如：
Intent intent = new Intent(“action”);
sendBroadcast(intent);
	标准广播：调用sendBroadcast()所有的BroadcastReceiver同时接受广播；
	有序广播：调用sendOrderdBroadcast()，按照BroadcastReiver的优先级(ActivityMainifest.xml中定义)先后发送广播，先收到广播的BroadcastReceiver可以通过abortBroadcast()截断广播，或者通过setResultCode(int code)修改传递的结果值。
5.2 静态注册
静态注册的BroadcastReceiver不依赖于注册的组件生存，尽管程序还未打开，只要广播发送，即可作出相应操作。
静态注册BroadcastReceiver的方式与隐式构建Intent的方式相同，仅需在ActivityManifest.xml文件<receiver>中注册对应的intent-filter，如下所示：
<receiver android:name=".StaticReceiver">
            <intent-filter>
                <action android:name="BOOT_COMPLETED" />
            </intent-filter>
        </receiver>
5.3 适配Android O版本
除例外情况，静态注册的BroadcastReceiver不再适用，仅有一下几种系统广播可以通过静态注册监听：
1.	ACTION_LOCKED_BOOT_COMPLETED、ACTION_BOOT_COMPLETED：原因：这些广播只在首次启动时发送一次，并且许多应用都需要接收此广播以便进行作业、闹铃等事项的安排。
2.	 ACTION_USER_INITIALIZE、”android.intent.action.USER_ADDED”、”android.intent.action.USER_REMOVED”：原因：这些广播受特权保护，因此大多数正常应用无论如何都无法接收它们。 
3.	“android.intent.action.TIME_SET”、ACTION_TIMEZONE_CHANGED：原因：时钟应用可能需要接收这些广播，以便在时间或时区变化时更新闹铃。 
4.	ACTION_LOCALE_CHANGED：原因：只在语言区域发生变化时发送，并不频繁。 应用可能需要在语言区域发生变化时更新其数据。
5.	ACTION_USB_ACCESSORY_ATTACHED、ACTION_USB_ACCESSORY_DETACHED、ACTION_USB_DEVICE_ATTACHED、ACTION_USB_DEVICE_DETACHED：原因：如果应用需要了解这些 USB 相关事件的信息，目前尚未找到能够替代注册广播的可行方案。 
6.	ACTION_HEADSET_PLUG：原因：由于此广播只在用户进行插头的物理连接或拔出时发送，因此不太可能会在应用响应此广播时影响用户体验。
7.	 ACTION_CONNECTION_STATE_CHANGED、ACTION_CONNECTION_STATE_CHANGED：原因：与 ACTION_HEADSET_PLUG 类似，应用接收这些蓝牙事件的广播时不太可能会影响用户体验。 
8.	 ACTION_CARRIER_CONFIG_CHANGED, TelephonyIntents.ACTION_*_SUBSCRIPTION_CHANGED、”TelephonyIntents.SECRET_CODE_ACTION”：原因：原始设备制造商 (OEM) 电话应用可能需要接收这些广播。 
9.	 LOGIN_ACCOUNTS_CHANGED_ACTION：原因：一些应用需要了解登录帐号的变化，以便为新帐号和变化的帐号设置计划操作。 
10.	 ACTION_PACKAGE_DATA_CLEARED：原因：只在用户显式地从 Settings 清除其数据时发送，因此广播接收器不太可能严重影响用户体验。 
11.	ACTION_PACKAGE_FULLY_REMOVED：原因：一些应用可能需要在另一软件包被移除时更新其存储的数据；对于这些应用，尚未找到能够替代注册此广播的可行方案。 
12.	 ACTION_NEW_OUTGOING_CALL：原因：执行操作来响应用户打电话行为的应用需要接收此广播。 
13.	ACTION_DEVICE_OWNER_CHANGED：原因：此广播发送得不是很频繁；一些应用需要接收它，以便知晓设备的安装状态发生了变化。 
14.	ACTION_EVENT_REMINDER：原因：由日历提供程序发送，用于向日历应用发布事件提醒。因为日历提供程序不清楚日历应用是什么，所以此广播必须是隐式广播。 
15.	ACTION_MEDIA_MOUNTED、ACTION_MEDIA_CHECKING、ACTION_MEDIA_UNMOUNTED、ACTION_MEDIA_EJECT、 ACTION_MEDIA_UNMOUNTABLE、ACTION_MEDIA_REMOVED、ACTION_MEDIA_BAD_REMOVAL：原因：这些广播是作为用户与设备进行物理交互的结果（安装或移除存储卷）或启动初始化（作为已装载的可用卷）的一部分发送的，因此它们不是很常见，并且通常是在用户的掌控下。
16.	 SMS_RECEIVED_ACTION、WAP_PUSH_RECEIVED_ACTION：原因：这些广播依赖于短信接收应用。
