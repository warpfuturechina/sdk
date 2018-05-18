
UERG项目简介
--------
WFSDK(Warpfuture SDK)是曲速未来人工智能技术（广州）有限公司推出的一套信息安全产品，开发者可以根据业务需求启用不同的产品。

* WFSDK，曲速未来SDK简称
* UERG，风险识别
* DVID，设备指纹
* KLCA，知识图谱验证码

系统要求
----

* iOS8.0 及 以上


WFSDK 文件说明
----

* `libWFSDK.a`
* `WFSDK.h`
* `UERG.h`
* `DVID.h`
* `KLCA.h`
* `WFHeader.h`
* `route.h`
* `if_arp.h`
* `if_dl.h`
* `if_ether.h`
* `if_types.h`
* `route.h`
* `WFSDK.bundle`

* Note: `WFSDK.bundle`包含`WFSDK`所依赖的`js`文件，`WFSDK`加载依赖`js`前会对`js`文件进行哈希校验，校验文件是否有被修改，一旦被修改可能会导致`WFSDK`加载失败，因此，在获得`SDK`之后切记不要对`WFSDK.bundle`中的文件做任何修改。

![文件](https://raw.githubusercontent.com/warpfuturechina/sdk/master/uerg/ios/IMG/file.png)

集成WFSDK
----

* 下载`WFSDK`后解压压缩文件，得到文件夹:`WFSDK`，文件夹下包含所有文件。
* 将`SDK`所依赖的全部文件含`WFSDK`文件夹拖到工程中，注意勾选：`Copy items if needed` 和 `Add to targets`。

![集成](https://raw.githubusercontent.com/warpfuturechina/sdk/master/uerg/ios/IMG/integration.png)

![目录结构](https://raw.githubusercontent.com/warpfuturechina/sdk/master/uerg/ios/IMG/files.png)

UERG环境配置
----

* 添加`libWFSDK.a`静态库引用路径，`TARGETS -> Build Settings -> Library Search Paths`，添加配置：`$(PROJECT_DIR)/WFSDK_DEMO/WFSDK`，其中 `$(PROJECT_DIR)/xxx/WFSDK`即`libWFSDK.a`所在路径

![静态库引用路径](https://raw.githubusercontent.com/warpfuturechina/sdk/master/uerg/ios/IMG/library_search_paths.png)

* 添加`Category`文件引用配置：` Other Linker Flags -> -ObjC`。

![Category](https://raw.githubusercontent.com/warpfuturechina/sdk/master/uerg/ios/IMG/category.png)

* 添加`WFSDK`动态库依赖：`TARGETS -> Build Phases -> Link Binary With Libraries -> rebresolv.tbd`
* IDFA标识符：</br>如果您的项目已经使用了获取广告标识符API，请忽略并跳过以下内容。</br>如`WFSDK项目简介`中所描述，`UERG`用于进行人机识别，获取IDFA标识符能够提高人机识别速度及准确率，若您的项目之前未使用过获取IDFA标识符API，并决定授权`UERG`使用IDFA标识符，请按如下操作：</br>`TARGETS -> Build Phases` -> `Link Binary With Libraries` 添加 `AdSupport.framework`</br>将应用提交至`AppStore`时按如下方式配置：避免审核时被苹果以“应用不含广告功能，但获取了广告标识符IDFA”而拒绝上架。

![idfa](https://raw.githubusercontent.com/warpfuturechina/sdk/master/uerg/ios/IMG/idfa.png)


快速开始（QUICK START）
----

推荐在`- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions;`中注册并启动`SDK`。

```objc
#import "UERG.h"

@interface AppDelegate ()<UERGDelegate>

@end

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    [[UERG shareInstance] registerWithAppId:@"your appId" delegate:self];

    return YES;
}

#pragma mark - UERG delegate

- (void)didRegisteredFinishing:(NSInteger)code message:(NSString *)message {
    NSLog(@"UERG: register/reload callback: %ld %@", code, message ?: @"");
}

- (void)didReceivedError:(NSInteger)errorCode message:(NSString *)message {
    NSLog(@"UERG: Error callback: %ld %@", errorCode, message);
}

- (void)didReceivedStatusChangeCallback:(NSInteger)code {
    NSLog(@"UERG: status change callback: %ld", code);
}

@end
```
