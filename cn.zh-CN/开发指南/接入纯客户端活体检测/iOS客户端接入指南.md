# iOS客户端接入指南 {#concept_ut3_sf4_1gb .concept}

实人认证提供客户端 SDK 帮助接入方在 APP 中实现刷脸认证功能。

## 操作步骤 {#section_nl2_yf4_1gb .section}

1.  下载 iOS SDK。

    iOS SDK：[frameworks-blink-update.zip](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/99125/cn_zh/1543998821732/frameworks-blink-update.zip)

2.  添加 SDK。

    在 **Apple Xcode** 中，选择 **TARGETS**，单击 **General** 标签页， 展开 **Linked Frameworks and Libraries** 列表，添加以下 Framework：

    -   APBToygerFacade.framework
    -   ZolozMobileRPC.framework
    -   BioAuthAPI.framework
    -   BioAuthEngine.framework
    -   ToygerService.framework
    -   ZolozIdentityManager.framework
    -   ZolozSensorServices.framework
    -   ZolozOpenPlatformBuild.framework
    -   ZolozUtility.framework
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/77353/154409055733802_zh-CN.png)

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
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/77353/154409055733803_zh-CN.png)

4.  拷贝资源文件。

    选择 **TARGETS**，单击 **Build Phases** 标签页，在 **Copy Bundle Resources** 中添加如下三个 bundle：

    -   APBToygerFacade.bundle：位于 APBToygerFacade.framework 中
    -   ToygerService.bundle：位于 ToygerService.framework 中
    -   BioAuthEngine.bundle：位于 BioAuthEngine.framework 中
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/77353/154409055733804_zh-CN.png)

5.  链接器参数设置。

    选择 **TARGETS**，单击 **Build Settings** 标签页，在 **Linking** 中设置 **Other linker flags** 值为 **-ObjC**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/77353/154409055733805_zh-CN.png)

6.  调用 SDK。
    -   引入头文件。

        ```
        #import <ZolozIdentityManager/ZolozIdentityPublicApi.h>
        ```

    -   绑定回调函数。

        ```
        [ZolozIdentityManager sharedInstance].delegate = self;
        ```

    -   开始刷脸认证。

        ```
        [[ZolozIdentityManager sharedInstance] verifyWith:NULL extParams:extParams onCompletion:^(ZIMResponse *response) {
                     }
        ```

        其中，

        -   入参 extParams 具体为：

            |名称|类型|是否必需|描述|
            |--|--|----|--|
            |currentCtr|对象|是|当前页面的controller。|

        -   回调函数

            ```
            /**
            * msg代表最终的返回结果
            * msg里面的完整json串即为BLOB内容
            */
            - (void)didReceivedMsg:(NSDictionary *)msg withHandler:(ZIMMsgProxyHandler)handler {
                handler(nil);
            }
            ```


