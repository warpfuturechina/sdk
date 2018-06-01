
UERG项目简介
--------
　　曲速 SDK 是曲速未来人工智能技术（广州）有限公司推出的一套信息安全产品。

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

4. 添加 曲速 SDK
    ```java
    compile(name:'uerg-x.x.x',ext:'aar')
    ```

5. 添加其他依赖包

    项目中需要依赖OkHttp3，fastJson和j2v8包：
    ```java
    implementation "com.squareup.okhttp3:okhttp:3.7.0"
    implementation "com.alibaba:fastjson:1.1.68.android"
    implementation 'com.eclipsesource.j2v8:j2v8:4.8.2@aar'
    ```

曲速SDK 公共方法
----------

* SDK初始化

	初始化接口用于SDK初始化。建议将初始化接口放在Application，或者应用Protal页面中调用。
    ```
    WFSDK.register("Your App id",WFCallback.Callback<SDKResult> callback);
    ```

    默认采用异步的方式初始化，初始化结果在回调中返回。示例：
    ```java
    WFSDK.register(mAppId){ it ->
        if(it.code != WFSDK.SUCCESS){
            runOnUiThread {
                toast(it.message)
            }
        }
    }
    ```

* SDK启动:
	
	当开发者需要用到SDK时，调用此接口启动SDK。
    ```java
    WFSDK.start(WFCallback.Callback<SDKResult> callback);
    ```
	
	同样是采用异步的形式，回调结果。示例：
	```java
	WFSDK.start{ it ->
        if(it.code != WFSDK.SUCCESS){
            runOnUiThread {
                toast(it.message)
            }
        }
    }
	```

* SDK暂停:

    开发者根据需求需要暂停数据收集以节约资源消耗时，调用该接口可以暂停SDK。
    ```java
    WFSDK.pause(WFCallback.Callback<SDKResult> callback);
    ```
	
	示例：
	```java
	WFSDK.pause{ it ->
        if(it.code != WFSDK.SUCCESS){
            runOnUiThread {
                toast(it.message)
            }
        }
    }
	```

* SDK恢复:

    开发者暂停SDK后需要恢复数据采集，调用该接口可以恢复SDK数据采集。
    ```java
    WFSDK.resume(WFCallback.Callback<SDKResult> callback);
    ```

	示例：
	```java
	WFSDK.resume{ it ->
        if(it.code != WFSDK.SUCCESS){
            runOnUiThread {
                toast(it.message)
            }
        }
    }
	```

* SDK重置:

    开发者根据业务需求需要重置SDK状态时，调用改接口可以重置SDK。
    ```java
    WFSDK.reset(WFCallback.Callback<SDKResult> callback);
    ```
	
	示例：
	```java
	WFSDK.reset{ it ->
        if(it.code != WFSDK.SUCCESS){
            runOnUiThread {
                toast(it.message)
            }
        }
    }
	```

* SDK停止:

	开发者不需要用到SDK时，可以调用此接口停止SDK，同时释放SDK占用的资源。
	```java
    WFSDK.stop(WFCallback.Callback<SDKResult> callback);
    ```
	
	示例：
	```java
	WFSDK.stop{ it ->
        if(it.code != WFSDK.SUCCESS){
            runOnUiThread {
                toast(it.message)
            }
        }
    }
	```

* SDK销毁:

    开发者不再需要SDK时，调用该接口销毁SDK，释放SDK占用的资源。
    ```java
    WFSDK.destory(WFCallback.Callback<SDKResult> callback);
    ```

	示例：
	```java
	WFSDK.destory{ it ->
        if(it.code != WFSDK.SUCCESS){
            runOnUiThread {
                toast(it.message)
            }
        }
    }
	```


UERG SDK使用方法
------------
UERG是WFSDK其中的一款产品。在使用UERG时，同时需要调用WFSDK的接口初始化，并启动SDK。

使用UERG产品时，需要在用户中心购买并开通功能。

* 获取当前未上传的数据块:

    业务开发者提交业务请求时调用该接口获取数据包与业务请求一起提交。
	```java
	UERG.getPackage(WFCallback.Callback<SDKResult> callback);
	```

	示例:
    ```java
    UERG.getPackage{ it ->
        if(it.code == WFSDK.SUCCESS){
            //调用成功
			val data = it.message;
            // TODO 验证数据包data
			// ...
			
        }else{
            //调用失败
            runOnUiThread {
                toast(it.message)
            }
        }
    }
    ```
	
    获取到SDK提供的数据包后，开发者需要将数据包发送到应用的后台，后台服务再通过调用曲速大脑的vertify接口进行验证。
	
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


设备指纹 SDK使用方法
------------

设备指纹是WFSDK其中的一款产品。在使用设备指纹SDK时，同时需要调用WFSDK的接口初始化，并启动SDK。

使用设备指纹产品时，需要在用户中心购买并开通功能。

* 检查设备是否有风险
    
	业务开发者可以调用以下方法，检查当前设备是否有风险。
	```java
	DVID.check(String ext,WFCallback.DVIDCallback callback);
	```

	示例：
    ```
    val extInfo:HashMap<String,String> = HashMap()
    extInfo.put("username","xxxxxxx")
    extInfo.put("mobile","1234567890")
    extInfo.put("email","helloworld@warpfurue.com")
    DVID.check(extInfo){ code, risk, grade ->
        if(code == WFSDK.SUCCESS){
            // code 错误码。0：调用成功
			// risk 是否有风险。 0：没有风险，1:有风险
			// grade 风险等级。 1~10, 越大风险越高（需开通风险等级获取权限）
        }else {
			// code 错误码。
		}
	}
	```


知识图谱验证码 SDK使用方法
------------

使用“知识图谱验证码”产品时，需要在用户中心购买并开通功能。

* 获取验证码并显示
	
	开发者需要在UI定义添加一个surfaceview控件用于显示验证码。在调用时需要传入该控件。
	```java
	KLCA.startup(String appId,SurfaceView view,WFCallback.Callback<SDKResult> callback);
	```
	
	当前页面（Activity）暂停后，SDK会自动回收验证码占用的资源。所以建议在onResume()方法中，调用startup()方法获取验证码。
	
	示例：
	```java
	override fun onResume() {
        super.onResume()
		val surfaceview = find(R.id.surfaceview) as SurfaceView
        KLCA.startup("<Your App Id>",surfaceview){
            if(it.code != WFSDK.SUCCESS) {
                toast(it.message)
            }
        }
    }
	```

* 验证结果
    
	业务开发者可以调用以下方法，验证用户点击结果。
	
	当返回token不为空时，表示用户已经验证成功。
	
	验证失败时，SDK会自动重新获取一张验证码。
	```java
	KLCA.check(WFCallback.Callback<KLCAResult> callback);
	```

	示例：
    ```
    KLCA.check { `object` ->
        Log.v("Login", "KLCAResult:" + `object`.token)
		// token：验证结果token。当token不为空时，表示用户已经验证成功。
        if (`object`.token != null) {
            // TODO 验证成功,可以继续后续登陆操作。
        }
    }
    ```


