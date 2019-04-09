# iOS客户端接入 {#concept_gqk_v24_1gb .concept}

实人认证提供客户端 SDK 帮助接入方在 APP 中实现刷脸认证功能。接入方可通过服务端调用接口发起认证请求来获取刷脸认证唯一标识 queryId，并使用 queryId 唤起无线认证 SDK 进行刷脸认证。

## 操作步骤 {#section_y4z_x24_1gb .section}

1.  下载 iOS SDK 及 Demo 工程。
    -   iOS SDK：[FaceVerifySDK\_iOS-20190327.zip\(25.2 MB\)](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/113697/cn_zh/1554787504920/FaceVerifySDK_iOS-20190327.zip)
    -   iOS Demo：[FaceDetectDemo-iOS-master-20181121.zip](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/97845/cn_zh/1542875168174/FaceDetectDemo-iOS-master-20181121.zip)
2.  添加 SDK。

    在 **Apple Xcode** 中，选择 **TARGETS**，单击 **General** 标签页， 展开 **Linked Frameworks and Libraries** 列表，添加以下 Framework：

    -   APBToygerFacade.framework
    -   APPSecuritySDK.framework
    -   BioAuthAPI.framework
    -   BioAuthEngine.framework
    -   MPRemoteLogging.framework
    -   ToygerService.framework
    -   ZolozIdentityManager.framework
    -   ZolozMobileRPC.framework
    -   ZolozOpenPlatformBuild.framework
    -   ZolozSensorServices.framework
    -   ZolozUtility.framework
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/77489/155478903138885_zh-CN.png)

3.  添加系统库。

    在 **Linked Framweworks and Libraries** 中添加如下 iOS 系统库：

    -   CoreGraphics.framework
    -   Accelerate.framework
    -   SystemConfiguration.framework
    -   AssetsLibrary.framework
    -   CoreTelephony.framework
    -   QuartzCore.framework
    -   CoreFoundation.framework
    -   CoreLocation.framework
    -   ImageIO.framework
    -   CoreMedia.framework
    -   CoreMotion.framework
    -   AVFoundation.framework
    -   libz.tbd
    -   libc++.1.tbd
    -   libstdc++.6.0.9.tbd
    -   libc++abi.tbd
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/77489/155478903138886_zh-CN.png)

4.  拷贝资源文件。

    选择 **TARGETS**，单击 **Build Phases** 标签页，在 **Copy Bundle Resources** 中添加如下三个 bundle：

    -   APBToygerFacade.bundle：位于 APBToygerFacade.framework 中
    -   ToygerService.bundle：位于 ToygerService.framework 中
    -   BioAuthEngine.bundle：位于 BioAuthEngine.framework 中
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/77489/155478903138887_zh-CN.png)

5.  链接器参数设置。

    选择 **TARGETS**，单击 **Build Settings** 标签页，在 **Linking** 中设置 **Other linker flags** 值为 **-ObjC**。 ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/77489/155478903138888_zh-CN.png)

6.  调用 SDK。
    -   引入头文件。

        ```
        #import <ZolozIdentityManager/ZolozIdentityPublicApi.h>
        ```

    -   初始化 SDK。

        ```
        [ZolozSdk init]
        ```

        初始化接口是异步执行，不会影响主线程 UI 渲染，建议接入方将初始化接口放在 appdelegate 的如下函数中调用：

        ```
        (BOOL)application:(UIApplication)applicationdidFinishLaunchingWithOptions:(NSDictionary)launchOptions
        ```

    -   获取 metainfo。

        ```
        [ZolozIdentityManagergetMetaInfo]
        ```

        返回值是 NSDictionary，接入方服务端需要将其转换为 JSON 字符串，调用实人认证服务端发起认证请求时需要传入 metainfo。

    -   开始刷脸认证。

        ```
        [[ZolozIdentityManager sharedInstance] verifyWith:_queryId extParams:extParams onCompletion:^(ZIMResponse *response) {
                     NSString *title = @"刷脸成功";
                     switch (response.code) {
                         case 1000:
                             break;
                         case 1001:
                             title = @"系统错误";
                             break;
                         case 1003:
                             title = @"验证中断";
                             break;
                         case 2002:
                             title = @"网络错误";
                             break;
                         case 2006:
                             title = @"刷脸失败";
                             break;
                         default:
                             break;
                     }
        ```

        其中，

        -   入参第一个是 queryId，通过服务端发起认证请求 获取（在 SDK 接口声明和 Demo 工程中此参数名为 zimId，含义与 queryId 相同）
        -   入参第二个是 extParams，具体为：

            |名称|类型|是否必需|描述|
            |--|--|----|--|
            |currentCtr|对象|是|当前页面的controller。|

        -   response.code类型是整型，有以下五种返回值，：

            |Code|说明|
            |----|--|
            |1000|刷脸成功，该结果仅供参考，可通过服务端人脸结果查询获取最终认证结果。|
            |1001|系统错误。|
            |1003|验证中断。|
            |2002|网络错误。|
            |2006|刷脸失败，如需获取更详细的失败原因，需调用服务端人脸结果查询接口。|


