
WFSDK项目简介
--------
WFSDK(Warpfuture SDK)是曲速未来人工智能技术（广州）有限公司推出的一套信息安全产品，开发者可以根据安全方面的需求使用不同的产品。

* WFSDK: 曲速未来SDK简称
* UERG: 风险识别组件
* DVID: 设备指纹组件
* SDKG: 知识图谱验证码组件

系统要求
----

* iOS8.0 或 以上


WFSDK 文件说明
----

* `libWFSDK.a`
* `WFSDK.h`
* `UERG.h`
* `DVID.h`
* `SDKG.h`
* `WFHeader.h`
* `route.h`
* `if_arp.h`
* `if_dl.h`
* `if_ether.h`
* `if_types.h`
* `route.h`
* `WFSDK.bundle`

* 注意: `WFSDK.bundle`包含`WFSDK`依赖的所有`js`文件，`WFSDK`加载`js`前会对`js`文件进行哈希校验，校验文件是否有被修改，一旦被修改可能会导致`WFSDK`加载失败，因此，在获得`SDK`之后切记不要对`WFSDK.bundle`中的文件做任何修改。

![文件](IMG/file.png)

集成WFSDK
----

* 下载`WFSDK`后解压压缩文件，得到文件夹:`WFSDK`，文件夹下包含所有文件。
* 将`SDK`所依赖的全部文件含`WFSDK`文件夹拖到工程中，注意勾选：`Copy items if needed` 和 `Add to targets`。

![集成](IMG/integration.png)

![目录结构](IMG/files.png)

环境配置
----

* 添加`libWFSDK.a`静态库引用路径，`TARGETS -> Build Settings -> Library Search Paths`，添加配置：`$(PROJECT_DIR)/WFSDKDemo/WFSDK`，其中 `$(PROJECT_DIR)/xxx/WFSDK`即`libWFSDK.a`所在路径

![静态库引用路径](IMG/library_search_paths.png)

* 添加`Category`文件配置：` Other Linker Flags -> -ObjC`。

![Category](IMG/category.png)

* 添加`WFSDK`动态库依赖：`TARGETS -> Build Phases -> Link Binary With Libraries -> rebresolv.tbd`
* IDFA标识符：</br>如果您的项目已经使用了获取广告标识符API，请忽略并跳过以下内容。</br>如`WFSDK项目简介`中所描述，`UERG`用于进行人机识别，获取IDFA标识符能够提高人机识别速度及准确率，若您的项目之前未使用过获取IDFA标识符API，并决定授权`UERG`使用IDFA标识符，请按如下操作：</br>`TARGETS -> Build Phases` -> `Link Binary With Libraries` 添加 `AdSupport.framework`</br>将应用提交至`AppStore`时按如下方式配置：避免审核时被苹果以“应用不含广告功能，但获取了广告标识符IDFA”而拒绝上架。

![idfa](IMG/idfa.png)


快速集成
----

注册SDK

`+ (void)registerWithAppId:(NSString *)appId delegate:(id<WFSDKDelegate>)delegate;`

`- (void)registerCallback:(NSInteger)code message:(id)message;`

推荐在应用启动入口中注册`SDK`。

```objc
#import "WFSDK.h"

@interface AppDelegate ()<WFSDKDelegate>

@end

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    // 注册App
    [WFSDK registerWithAppId:@"your appId" delegate:self];

    return YES;
}

#pragma mark - WFSDKDelegate

- (void)registerCallback:(NSInteger)code message:(id)message {
    NSLog(@"WFSDK: register callback: %ld %@", code, message ?: @"");
}

@end
```

WFSDK 生命周期控制示例
----

#### 暂停SDK

`+ (void)pause:(id<WFSDKDelegate>)delegate;`

`- (void)pauseCallback:(NSInteger)code message:(id)message;`

暂停SDK，SDK将停止运行

```objc

- (void)pauseSDK {
    [WFSDK pause:self];
}

#pragma mark - WFSDKDelegate

- (void)pauseCallback:(NSInteger)code message:(id)message {
    NSLog(@"pause:%@",message);
    NSLog(@"code:%ld",code);
}

```

#### 恢复SDK

`+ (void)resume:(id<WFSDKDelegate>)delegate;`

`- (void)resumeCallback:(NSInteger)code message:(id)message;`

与pause方法配套使用，pause调用若想恢复SDK的运行，调用该方法

```objc

- (void)resumeSDK {
    [WFSDK resume:self];
}

#pragma mark - WFSDKDelegate

- (void)resumeCallback:(NSInteger)code message:(id)message {
    NSLog(@"resume:%@",message);
    NSLog(@"code:%ld",code);
}

```

#### 重启SDK

`+ (void)reset:(id<WFSDKDelegate>)delegate;`

`- (void)resetCallback:(NSInteger)code message:(id)message;`

SDK将使用上一次注册成功的配置重新启动并运行

```objc

- (void)resetSDK {
   [WFSDK reset:self];
}

#pragma mark - WFSDKDelegate

- (void)resetCallback:(NSInteger)code message:(id)message {
    NSLog(@"reset:%@",message);
    NSLog(@"code:%ld",code);
}

```

#### 销毁SDK

`+ (void)destory:(id<WFSDKDelegate>)delegate;`

`- (void)destoryCallback:(NSInteger)code message:(id)message;`

SDK一旦销毁，需要重新注册SDK才能使用

```objc
- (void)destorySDK {
    [WFSDK destory:self];
}

#pragma mark - WFSDKDelegate

- (void)destoryCallback:(NSInteger)code message:(id)message {
    NSLog(@"destory:%@",message);
    NSLog(@"code:%ld",code);
}

```
UERG 集成风险识别组件
----

```objc

#import "WFSDK.h"
#import "UERG.h"

@interface UERGTest ()<UERGDelegate>

@end

@implementation UERGTest

- (void)startUerg {
    // 启动风控组件包含两种方式，请确保App注册成功之后再启动

    // 方式一
    [WFSDK start:WFSDKProductTypeUERG delegate:self];

    // 方式二
    // [UERG start:self];
}

- (void)gegPackage {
    // 获取UERG数据包
    NSLog(@"package: %@", [UERG getPackage]);
}

#pragma mark - UERGDelegate

- (void)startUERGCallback:(NSInteger)code message:(id)message {
    // code = 1,  成功  
    // code = 0,  失败
    NSLog(@"UERG 回调码:%ld message:%@",code,message);
}

@end

```

DVID 集成设备指纹
----

```objc

#import "WFSDK.h"
#import "DVID.h"

@interface DVIDTest ()<DVIDDelegate>

@end

@implementation DVIDTest

- (void)startDVID {
    // 启动设备指纹包含两种方式，请确保App注册成功之后再启动

    // 方式一
    [WFSDK start:WFSDKProductTypeDVID delegate:self];

    // 方式二
    // [DVID start:self];
}

- (void)getDviceId {
    // 获取设备指纹
    [DVID get:self];
}

- (void)checkDviceId {
    // 1. 验证当前设备的指纹是否存在风险，不带拓展信息
    [DVID check:self extra:nil];

    // 2. 验证当前设备的指纹是否存在风险，携带拓展信息
    [DVID check:self extra:@{@"email":@"your email"}];
}

#pragma mark - DVIDDelegate

// 启动回调
- (void)startDeviceIdCallback:(NSInteger)code message:(id)message {
     NSLog(@"DVID 回调码:%ld 回调信息:%@",code,message);
}

// 获取设备指纹回调
- (void)getDeviceIdCallback:(NSString *)deviceId message:(id)message {
    NSLog(@"指纹:%@ 回调信息:%@", deviceId, message);
}

// 验证结果回调
- (void)checkDeviceIdCallback:(id)data message:(id)message {
    // risk: 0,没有风险; 1,有风险
    // grade: 风险等级, 1~10, 越大风险越高
    NSLog(@"指纹验证结果:%@ 提示信息:%@",data,message);
}

@end

```

SDKG 集成知识图谱验证码
----

知识图谱验证码的使用包含两种方式，可以根据需要选择其中一种方式接入即可。

##### 知识图谱验证码第一种接入方式

```objc

#import "WFSDK.h"
#import "SDKG.h"

@interface SDKGViewController ()<SDKGDelegate>
@property (nonatomic, strong) UIView *vcodeView;
@end

@implementation SDKGViewController

- (void)start {
    // 步骤一：启动知识图谱包含两种方式，请确保在App注册成功之后再启动

    // 方式一：WFSDK类方法
    [WFSDK start:WFSDKProductTypeSDKG delegate:self];

    // 方式二：UERG类方法
    // [SDKG start:self];
}

- (void)get {
    // 步骤二：获取验证码
    [SDKG get:self];
}

- (void)check {
    // 步骤三：验证结果，用户点击注册或登录按钮时调用
    [SDKG check:self];
}

#pragma mark - SDKGDelegate

// 启动回调
- (void)startCaptchaCallback:(NSInteger)code message:(id)message  {
    NSLog(@"SDKG 回调码:%ld message:%@",code,message);
}

// 获取验证码回调
- (void)getCaptchaCallback:(UIView *)captchaView message:(id)message {
    if (captchaView) {
        NSLog(@"获取验证码成功:%@",message);

        [self.vcodeView removeFromSuperview];

        CGFloat widht = 300;
        CGFloat height = widht * 370 / 600;

        // captchaView的frame默认为CGSizeZero，必须重新设置frame
        captchaView.frame = CGRectMake(0, 0, widht, height);
        captchaView.center = self.view.center;

        self.vcodeView = captchaView;

        [self.view addSubview:self.vcodeView];

    } else {
        NSLog(@"获取验证码失败提示:%@",message);
    }
}

// 校验回调
- (void)checkCaptchaCallback:(NSString *)token message:(id)message {
    // 注意：不管验证通过还是失败，若需要重新验证需要重新调用获取验证码的方法（此时SDK内部对验证码视图组件的引用可能被释放调用，用户点击刷新按钮不能保证刷新成功）
    if (token) {
        // 将token传给您的后端服务器，服务器再调用曲速验证接口进行最终的校验
        NSLog(@"验证成功 token:%@ message:%@",token,message);
    } else {
        NSLog(@"验证失败 message:%@",message);
    }
}

```

##### 知识图谱验证码第二种接入方式

便捷方式

```objc
#import "SDKG.h"

@interface SDKGViewController ()<SDKGDelegate>
@property (nonatomic, strong) UIView *vcodeView;
@end

- (void)setup {
    // 注册并启动知识图谱及获取验证码
    [SDKG startup:@"your appId" delegate:self];
}

- (void)check {
    // 验证结果，用户点击注册或登录按钮时调用
    [SDKG check:self];
}

#pragma mark - SDKGDelegate

// 获取验证码回调
- (void)startupCaptchaCallback:(UIView *)captchaView message:(id)message {
    if (captchaView) {
        NSLog(@"获取验证码成功:%@",message);

        [self.vcodeView removeFromSuperview];

        CGFloat widht = 300;
        CGFloat height = widht * 370 / 600;

        // captchaView的frame默认为CGSizeZero，必须重新设置frame
        captchaView.frame = CGRectMake(0, 0, widht, height);
        captchaView.center = self.view.center;

        self.vcodeView = captchaView;

        [self.view addSubview:self.vcodeView];

    } else {
        NSLog(@"获取验证码失败提示:%@",message);
    }
}

// 验证结果回调
- (void)checkCaptchaCallback:(NSString *)token message:(id)message {
    // 注意：不管验证通过还是失败，若需要重新验证需要重新调用获取验证码的方法（此时SDK内部对验证码视图组件的引用可能被释放调用，用户点击刷新按钮不能保证刷新成功）
    if (token) {
        // 将token传给您的后端服务器，服务器再调用曲速验证接口进行最终的校验
        NSLog(@"验证成功 token:%@ 回调信息:%@",token,message);
    } else {
        NSLog(@"验证失败 回调信息:%@",message);
    }
}

```
