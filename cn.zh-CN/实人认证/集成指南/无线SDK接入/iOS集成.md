# iOS集成 {#task_1426491 .task}

下载无线认证SDK后，您可参考以下步骤将认证SDK集成到您的iOS应用中。

[单击查看老版本接入文档说明。](../../../../cn.zh-CN/老系统文档说明/老版本接入文档说明.md#)

**说明：** iOS SDK与BundleID绑定，不同BundleID需要在[管理控制台](https://yundun.console.aliyun.com/?p=cloudauth)上重新下载SDK。

## 在工程中导入 SDK {#section_1d0_tas_sxy .section}

1.  解压无线认证SDK后，将以下iOS的依赖包引入到您的应用工程中： 
    -   RPSDK.framework
    -   FaceLivenessOpen.framework
    -   SecurityGuardSDK.framework
    -   SGMain.framework
    -   SGNoCaptcha.framework
    -   SGSecurityBody.framework
    -   AliyunOSSiOS.framework
    -   WindVane.framework
    -   WindVaneBasic.framework
    -   WindVaneBridge.framework
    -   WindVaneCore.framework
    -   ZipArchive.framework
    -   AliReachability.framework
2.  确认您的工程中已引入以下实人认证服务需要的系统依赖： 
    -   CoreMedia.framework
    -   CoreMotion.framework
    -   CoreTelephony.framework
    -   AVFoundation.framework
    -   ImageIO.framework
    -   MobileCoreServices.framework
    -   MediaPlayer.framework
    -   CoreLocation.framework
    -   AddressBook.framework
    -   AddressBookUI.framework
    -   SystemConfiguration.framework
    -   AudioToolbox.framework
    -   AssetsLibrary.framework
    -   Libresolv.tbd
    -   WebKit.framework
    -   Libiconv.tbd
    -   Libc++.tbd
    -   Libz.tbd
3.  在您的工程资源中（Copy Bundle Resources），引入无线认证SDK 包中的yw\_1222\_\*.jpg签名图片文件，以及resource目录下的FaceLivenessSDK.bundle和RPSDK.bundle文件。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1135432/156574651453978_zh-CN.png)


4.  编译选项：在工程的Other Linker Flags选项中添加-ObjC。

## 使用SDK {#section_hc8_cxz_aok .section}

1.  初始化。 

    一般在应用启动时进行。

    ``` {#codeblock_6p9_xh3_81w}
    #import <RPSDK/RPSDK.h>
     [RPSDK initialize:RPSDKEnvOnline] //必须为RPSDKEnvOnline
    ```

2.  进入认证。 

    ``` {#codeblock_xiw_e0k_2eg}
    [RPSDK start:verifyToken rpCompleted:^(RPVerifyState verifyState, NSString *code) {
         NSLog(@"verifyResult = %ld",(unsigned long)verifyState);
         if(verifyState == RPVerifyStatePass) { //认证通过。
         }
         else if(verifyState == RPVerifyStateFail) { //认证不通过。
         }
         else if(verifyState == RPVerifyStateNotVerify) { //未认证，用户主动退出，或者姓名身份证号实名校验不匹配等原因，导致未完成认证流程。
         }
    
     }withVC:self.navigationController];
    ```

    **说明：** 

    -   verifyToken参数由接入方的服务端调用实人认证服务的DescribeVerifyToken接口获得。
    -   在RPSDK.start接口调用的回调会返回用户认证的各种状态，具体信息体现在verifyState和code参数取值，详见下表说明。
    |verifyState|code|code释义|
    |-----------|----|------|
    |RPVerifyStatePass|1|认证通过|
    |RPVerifyStateFail|取值2~12|表示认证不通过，具体的不通过原因可以查看服务端的[查询认证结果](cn.zh-CN/实人认证/集成指南/服务端接入/查询认证结果.md#)（DescribeVerifyResult）接口文档中认证状态的表格说明|
    |RPVerifyStateNotVerify|-1|未完成认证，原因是：用户在认证过程中，主动退出|
    |RPVerifyStateNotVerify|3001|未完成认证，原因是：认证token无效或已过期|
    |RPVerifyStateNotVerify|3101|未完成认证，原因是：用户姓名身份证实名校验不匹配|
    |RPVerifyStateNotVerify|3102|未完成认证，原因是：实名校验身份证号不存在|
    |RPVerifyStateNotVerify|3103|未完成认证，原因是：实名校验身份证号不合法|
    |RPVerifyStateNotVerify|3104|未完成认证，原因是：认证已通过，重复提交|
    |RPVerifyStateNotVerify|3204|未完成认证，原因是：操作太频繁|


## 常见问题 {#section_jaw_7io_ww2 .section}

-   调起无线认证SDK，进入认证页面展示“网络异常，请检查网络”/“UNKNOWN\_ERROR”/空白。

    处理建议：查看Xcode console。

    -   若SG ERROR= 1208、1215、1411，说明当前开发包与在管理控制台下载 SDK 时上传包的BundleID不一致。请在管理控制台重新上传当前开发包，用新下载 SDK 中的yw\_1222\_\*.jpg签名图片文件替换开发包中原有的文件。
    -   若SG ERROR= 1412、1225，说明图片不存在。请确保SDK 中的yw\_1222\_\*.jpg签名图片正确地引入到了Copy Bundle Resource 下。
-   项目中之前引入的组件与无线认证 SDK 中的组件有重复，例如 SecurityGuardSDK、AliyunOSSiOS 等。

    处理建议：若SecurityGuardSDK组件有重复，删除低版本，但保留两个版本的yw\_1222\_\*.jpg签名图片文件；若 AliyunOSSiOS等其他组件有重复，删除低版本即可。

-   项目中之前引入的SecurityEnvSDK组件与无线认证SDK中的SGMain组件报duplicate symbol冲突。

    处理建议：SecurityEnvSDK组件是SGMain组件的早先版本，删除SecurityEnvSDK，但保留两个组件的yw\_1222\_\*.jpg签名图片文件。


