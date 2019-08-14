# Android客户端接入 {#concept_msx_z24_1gb .concept}

实人认证提供客户端 SDK 帮助接入方在 APP 中实现刷脸认证功能。接入方可通过服务端调用接口发起认证请求获取刷脸认证唯一标识 queryId，并使用 queryId 唤起无线认证 SDK 进行刷脸认证。

## 操作步骤 {#section_cbs_bf4_1gb .section}

1.  下载 Android SDK 及 Demo 工程。
    -   Android SDK：[FaceVerifySDK\_Android-20190418](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/115364/cn_zh/1555637423613/FaceVerifySDK_Android-20190418.zip)
    -   Android Demo：[FaceDetectDemo-Android-master-20181121.zip](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/97845/cn_zh/1542875137254/FaceDetectDemo-Android-master-20181121.zip) 

        **说明：** 此 Android Demo 采用的是通过 gradle 拉取 maven 仓库下载 SDK，您可以修改为直接引入 SDK 的方式。

2.  添加 SDK。

    将下载的 SDK 解压后复制到项目下，并在build.gradle文件中增加以下依赖：

    ```
    dependencies {
        implementation 'com.android.support:appcompat-v7:25.3.1'
        implementation "com.alibaba:fastjson:1.2.2@jar"
        implementation "com.alipay.android.phone.zoloz:bio:7.1.0.00000001@aar"
        implementation "com.alipay.android.phone.zoloz:hardware:7.1.0.00000001@aar"
        implementation "com.alipay.android.phone.zoloz:toyger:7.1.0.00000001@aar"
        implementation "com.alipay.android.phone.zoloz:zface:7.1.0.00000001@aar"
        implementation "com.alipay.android.phone.zoloz:zim:7.1.0.00000001@aar"
        implementation "com.alipay.android.phone.zoloz:releaseLoging:4.0.0.00000003@aar"
        implementation "com.alipay.android.phone.zoloz:rpc:2.1.0.180302121215-ZOLOZ@aar"
        implementation "com.alipay.android.phone.zoloz:zolozsdk:7.1.0.00000003@aar"
        implementation "com.alipay.android.phone:deviceid-release:6.0.2.20171228@aar"
    }
    ```

3.  调用 SDK。
    -   初始化 SDK。

        ```
        ZIMFacade.install(context);
        ```

    -   获取 metainfo。

        ```
        String metaInfos = ZIMFacade.getMetaInfos(context);
        ```

        接入方服务端在调用实人认证服务端 发起认证请求时需要传入 metainfo。

    -   开始刷脸认证。

        ```
        ZIMFacade zimFacade = ZIMFacadeBuilder.create(MainActivity.this);
        zimFacade.verify(queryId, null, new ZIMCallback() {
         @Override
         public boolean response(ZIMResponse response) {
             // TODO: 接入方在这里实现自身业务
             if (null != response && 1000 == response.code) {
                 // 认证成功
             } else {
                 // 认证失败
             }
             return true；
         }
        }
        ```

        其中，

        -   入参第一个是 queryId，通过服务端发起认证请求 获取（在 SDK 接口声明和 Demo 工程中此参数名为 zimId，含义与 queryId 相同）
        -   response.code 类型是整型，有以下五种返回值：

            |Code|说明|
            |----|--|
            |1000|刷脸成功，该结果仅供参考，可通过服务端人脸结果查询获取最终认证结果。|
            |1001|系统错误。|
            |1003|验证中断。|
            |2002|网络错误。|
            |2006|刷脸失败，如需获取更详细的失败原因，需调用服务端人脸结果查询接口。|


## **接口混淆配置属性** {#section_hzv_nzc_wgb .section}

为避免接口混淆，您可以参考以下代码来保留类名：

```
-keep public class com.alipay.mobile.security.zim.api.**{
    public <fields>;
    public <methods>;
}
 
-keep class com.alipay.mobile.security.zim.biz.ZIMFacadeBuilder {
  !private <fields>;
   !private <methods>;
}
 
-keep class com.alipay.android.phone.mobilecommon.logger.AlipayMonitorLogService {
    !private <fields>;
    !private <methods>;
}
 
-keep class com.alipay.android.phone.mobilecommon.rpc.AlipayRpcService {
    !private <fields>;
    !private <methods>;
}
 
-keep class com.alipay.android.phone.mobilecommon.apsecurity.AlipayApSecurityService {
    !private <fields>;
    !private <methods>;
}
 
-keep class com.alipay.zoloz.toyger.bean.ToygerMetaInfo {
    !private <fields>;
    !private <methods>;
}
 
-keep class com.alipay.zoloz.toyger.algorithm.** { *; }
 
-keep class com.alipay.zoloz.toyger.blob.** {
    !private <fields>;
    !private <methods>;
}
 
-keep class com.alipay.zoloz.toyger.face.** {
    !private <fields>;
    !private <methods>;
}
 
-keep class com.alipay.zoloz.hardware.camera.impl.** {
    !private <fields>;
    !private <methods>;
}
 
 
-keep public class com.alipay.mobile.security.zim.plugin.**{
    public <fields>;
    public <methods>;
}
 
-keep class * extends com.alipay.mobile.security.zim.gw.BaseGwService{
    !private <fields>;
    !private <methods>;
}
 
-keep class * extends com.alipay.mobile.security.bio.service.BioMetaInfo{
    !private <fields>;
    !private <methods>;
}
 
-keep class com.alipay.zoloz.toyger.workspace.FaceRemoteConfig{
    *;
}
 
-keep public class com.alipay.zoloz.toyger.**{
    *;
}
-keep public class com.alipay.mobile.security.zim.gw.**{
    *;
}
			
```

