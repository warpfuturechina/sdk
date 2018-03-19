
UERG项目简介
--------
　　UERG(User Entity Risk Guard) SDK 是曲速未来人工智能技术（广州）有限公司推出的一套信息安全产品。

　　开发者可以根据需求采集用户信息，交由后台进行数据分析。判断当前用户是否是有效用户。


系统要求
----

* Android 4.1 + (Jelly Bean)


添加SDK到项目中
---------

1. 下载UERG SDK

2. 复制SDK到项目"libs"目录中

3. 编辑"build.gradle"文件，添加以下代码：

    ```java
    repositories {
        flatDir {
            dirs 'libs'
        }
    }
    ```

4. 添加 UERG SDK
    ```java
    compile(name:'uerg-x.x.x',ext:'aar')
    ```

5. 添加其他依赖包

    项目中需要依赖OkHttp3和fastJson包：
    ```java
    compile "com.squareup.okhttp3:okhttp:3.7.0"
    compile "com.alibaba:fastjson:1.2.39"
    ```

UERG SDK使用方法
------------

* SDK初始化

    ```
    WFUerg.register("Your App id");
    ```

    初始化时将会进行网络访问，所以必须在线程中调用。例如：
    ```java
    doAsync {
        try {
            WFUerg.regist(mAppId);
        }catch (e: UergException){
            uiThread{
                toast(e.message.toString());
            }
        }
    }
    ```

* SDK销毁:

    开发者不再需要SDK时，调用改接口销毁SDK，释放SDK占用的资源。
    ```java
    WFUerg.destory();
    ```

* SDK暂停:

    开发者根据需求需要暂停数据收集以节约资源消耗时，调用改接口可以暂停SDK。
    ```java
    WFUerg.pause();
    ```

* SDK恢复:

    开发者暂停SDK后需要恢复数据采集，调用改接口可以恢复SDK数据采集。
    ```java
    WFUerg.resume();
    ```

* SDK重置:

    开发者根据业务需求需要重置SDK状态时，调用改接口可以重置SDK。
   ```java
    WFUerg.reset(false);
    ```

    重置SDK时将会进行网络访问，所以必须在线程中调用。例如：
    ```java
    doAsync {
        try {
            WFUerg.reset(false);
        }catch (e: UergException){
            uiThread{
                toast(e.message.toString());
            }
        }
    }
    ```

* SDK重新加载:

    开发者在初始化SDK后，根据业务需求需要切换场景是调用。调用改接口时，可以传入参数控制是否开启一个新的场景。
    ```java
    WFUerg.reload("Your App id");
    ```

    SDK重新加载时将会进行网络访问，所以必须在线程中调用。例如：
    ```java
    doAsync {
        try {
            WFUerg.reload("Your App id");
        }catch (e: UergException){
            uiThread{
                toast(e.message.toString());
            }
        }
    }
    ```

* 获取当前未上传的数据块:

    业务开发者提交业务请求时调⽤该接⼝获取数据包与业务请求⼀起提交。
    ```java
    String package = WFUerg.getPackage();
    ```
    获取到SDK提供的数据包后，开发者可以主动将数据包发送到后台verify接口。
    ```
    public static boolean verify(String data){
        /*
         * APP ID， 从用户中心获取。
         */
        String appId = "{Your app id}";
        /*
         * 签名密钥 ， 从用户中心获取。
         */
        String appSecret = "{Your sign key}";
        /*
         * verify 接口 URL 地址。
         */
        String url = "http://{Server ip address}/uerg/"+appId+"/verify";
        /*
         * 从SDK中获取的数据块。
         */
        String blockData = data;
        long requestTime = System.currentTimeMillis();
        //计算签名
        String sign = SignCalculator.calSignature(appId,appSecret,blockData,requestTime);

        String resposeData = postToServer(url,appId,blockData,requestTime,sign);
        if(resposeData != null) {
            JSONObject jsonObject = JSON.parseObject(resposeData.toString());
            return (jsonObject.getInteger("cd") == 1)?true:false;
        }
        return false;
    }
    ```
    附：后台调用verify接口参考代码 [参考代码](www.baidu.com)


UERG SDK 权限列表
-------------
SDK在收集用户数据时，各个模块可根据业务需求动态开启关闭。所需权限需要开发者在项目中申请。

权限列表：

|名称|所需权限|
|:---|:----|
|设备基本信息采集|无|
|屏幕信息采集|无|
|摄像头采集模块|android.permission.CAMERA|
|SIM卡信息采集|android.permission.READ_PHONE_STATE|
|用户触摸事件采集|无|
|页面留存时间信息|无|
|用户输入数据采集|无|
|耳机状态变化数据采集|无|
|屏幕亮度信息采集|无|
|距离传感器信息采集|无|
|磁力计传感器信息采集|无|
|电池信息采集|无|
|加速度传感器信息采集|无|
|陀螺仪信息采集|无|
|通信录采集|android.permission.READ_CONTACTS|
|位置信息采集|android.permission.ACCESS_FINE_LOCATION    <br/>android.permission.ACCESS_COARSE_LOCATION|
|通话记录采集|android.permission.READ_CALL_LOG    <br/>android.permission.READ_CONTACTS|
|短信采集|android.permission.READ_SMS    <br/>android.permission.RECEIVE_SMS|
|应用安装列表信息采集|无|
|运行进程信息采集|无|
|WIFI信息采集|android.permission.ACCESS_WIFI_STATE|
|蓝牙信息采集|android.permission.BLUETOOTH|
|NFC信息采集|android.permission.NFC|
|CPU信息采集|无|
|内存信息采集|无|
|硬盘信息采集|无|
|网络环境信息采集|无|
|HTTP数据采集|无|

