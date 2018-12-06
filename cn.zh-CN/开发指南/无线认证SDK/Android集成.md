# Android集成 {#reference4597 .reference}

下载无线认证 SDK 后，您可参考以下步骤将认证 SDK 集成到您的 Android 应用中。

Android SDK 与包名\(package name\)+签名\(keystore\)绑定，package name和keystore两者修改任一，都需要在[管理控制台](https://yundun.console.aliyun.com/?p=cloudauth)上重新下载 SDK，debug 和 release 不能混用。

**说明：** 若您需要用到 V2 方式的签名，打包时请同时勾选 V1、V2 签名（只选 V2 签名，apk 在 Android 7.0 以下都无法安装）。

## 步骤一： 在工程中导入SDK { .section}

解压无线认证SDK包中的client.zip文件，将以下Android依赖包引入到您的应用工程中：

-   aliyun-oss-sdk-android.jar
-   FaceLivenessOpen-x.x.x.x.aar
-   NoCaptchaSDK-external-release.aar（通过解压Android.NoCaptchaSDK.xxx.tar获得该依赖包\)
-   SecurityBodySDK-external-release.aar（通过解压Android.SecurityBodySDK.xxx.tar获得该依赖包）
-   SecurityGuardSDK-external-release.aar（通过解压Android.SecurityGuardSDK.xxx.tar获得该依赖包）
-   Okhttp.jar
-   Okio.jar
-   Rpsdk.aar
-   Windvane-mini.jar

例如，如果您使用的 AndroidStudio，可参考以下步骤：

1.  设定依赖包所在的路径：

    ```
    apply plugin: 'com.android.application'
     repositories {
         flatDir {
             dirs '../libs'
         }
     }
    ```

2.  设定引入的本地库所在路径，将需要引入的依赖包都放在../libs目录，包含所有需要的库。
3.  在gradle文件中引入以下需要的库依赖：

    ```
    compile files('../libs/okhttp-3.2.0.jar')
     compile files('../libs/okio-1.6.0.jar')
     compile files('../libs/aliyun-oss-sdk-android-2.2.0.jar')
     compile files('../libs/windvane-min-8.0.3.2.3.jar')
     compile (name:'FaceLivenessOpen-2.1.6.10',ext:'aar')
     compile (name:'rpsdk-2.4.0.3',ext:'aar')
     compile (name:'SecurityGuardSDK-external-release-5.4.94',ext:'aar')
     compile (name:'SecurityBodySDK-external-release-5.4.66',ext:'aar')
     compile (name:'NoCaptchaSDK-external-release-5.4.26',ext:'aar')
    ```


 **关于签名图片** 

1.  解压已下载的无线认证SDK包，获得yw\_1222\_\*.jpg签名图片文件，该文件用于无线认证SDK的授权。
2.  把该图片文件导入到工程中res\\drawable\\目录，如果没有这个文件夹，请先在工程中创建，否则将无法正常工作。
3.  如果在安卓工程打包时启用了`shrinkResources true`，还需要在keep.xml文件中添加以下内容：

    ```
    <resources xmlns:tools="http://schemas.android.com/tools" tools:keep="@drawable/yw_1222_*" />
    ```


 **关于 CPU 类型** 

无线认证 SDK 目前支持 armeabi、armeabi-v7a、arm64-v8a 和 x86 四种，请接入方按需在build.gradle中增加abifilters配置。例如，接入方仅需要支持其中 armeabi、arm64-v8a 和 x86 三种，则配置如下：

```
defaultConfig {
    ndk {
        abiFilters "armeabi", "arm64-v8a", "x86"
    }
}
```

## 步骤二： 添加 ProGuard 配置 { .section}

如果您的应用使用了 ProGuard 进行代码混淆，为了保证实人认证服务需要的一些类不被混淆，需要在 ProGuard 配置文件中添加相关指令。

1.  根据您接入方式的情况，判断是否使用了 ProGuard 进行代码混淆：
    -   **Eclipse** 

        如果在`project.properties`中指定了ProGuard配置（例如，在`project.properties`中包含`proguard.config=proguard.cfg`语句），则表明已使用 ProGuard 进行代码混淆，混淆配置在 proguard.cfg 文件中：

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/13550/154045838414305_zh-CN.png)

    -   **Android Studio**

        如果在 build.gradle 中配置了 proguardFiles，并且启用了 minifyEnabled 方法，则表明已使用 proguard-rules.pro 这个配置文件进行代码混淆。例如：

        ```
        buildTypes {
          release {
              minifyEnabled true
              proguardFiles getDefaultProguardFile('proguard-android.txt'),     'proguard-rules.pro'
          }
        }
        ```

2.  在相应的 ProGuard 配置文件中添加以下配置信息，保证实人认证服务需要的类不被混淆：

    ```
    -keep class com.taobao.securityjni.**{*;}
     -keep class com.taobao.wireless.security.**{*;}
     -keep class com.ut.secbody.**{*;}
     -keep class com.taobao.dp.**{*;}
     -keep class com.alibaba.wireless.security.**{*;}
     -keep class com.alibaba.security.rp.**{*;}
     -keep class com.alibaba.sdk.android.**{*;}
     -keep class com.alibaba.security.biometrics.**{*;}
     -keep class android.taobao.windvane.**{*;}
    ```


## 步骤三： 使用 SDK { .section}

1.  **初始化** 

    一般在应用启动时进行。

    ```
    import com.alibaba.security.rp.RPSDK;
     RPSDK.initialize(appContext);
    ```

2.  **进入认证** 

    ```
    RPSDK.start(verifyToken, ParametersActivity.this, 
         new RPSDK.RPCompletedListener() {
         @Override
         public void onAuditResult(RPSDK.AUDIT audit) {
             Toast.makeText(ParametersActivity.this, audit + "", Toast.LENGTH_SHORT).show();
             if(audit == RPSDK.AUDIT.AUDIT_PASS) { //认证通过
             }
             else if(audit == RPSDK.AUDIT.AUDIT_FAIL) { //认证不通过
             }
             else if(audit == RPSDK.AUDIT.AUDIT_IN_AUDIT) { //认证中，通常不会出现，只有在认证审核系统内部出现超时，未在限定时间内返回认证结果时出现。此时提示用户系统处理中，稍后查看认证结果即可。
             }
             else if(audit == RPSDK.AUDIT.AUDIT_NOT) { //未认证，用户取消
             }
             else if(audit == RPSDK.AUDIT.AUDIT_EXCEPTION){ //系统异常
             }
         }
     });
    ```

    **说明：** 

    -   `verifyToken`参数由接入方的服务端调用实人认证服务的[GetVerifyToken](https://help.aliyun.com/document_detail/57050.html)接口获得。
    -   在RPSDK.start 接口调用的回调会返回用户认证完之后的各种状态。

## 常见问题 { .section}

-   调起无线认证SDK，进入认证页面展示“UNKNOWN\_ERROR”。

    **处理建议**：查看logcat，若 ErrorCode = 1208 或 1215 或 1411，说明当前开发包与在管理控制台下载 SDK 时上传包的`包名(package name)`或`签名(keystore)`不一致，请在管理控制台重新上传当前开发包，用新下载 SDK 中的yw\_1222\_\*.jpg签名图片文件替换开发包中原有的文件。

    **说明：** 工程上，通常对 IDE 中直接运行\(debug\)和正式打包\(release\)会配置不同的签名\(keystore\)，在 IDE 中直接编译运行的是 debug 包，其签名图片文件和 release 包是不同的。

-   项目中之前引入的组件与无线认证 SDK 中的组件有重复，例如 SecurityGuardSDK、aliyun-oss-sdk-android 等。

    **处理建议**：若 SecurityGuardSDK 组件有重复，删除低版本，但保留两个版本的yw\_1222\_\*.jpg签名图片文件；若 aliyun-oss-sdk-android 等其他组件有重复，删除低版本即可。

-   项目中之前引入的 SecurityEnvSDK 组件与无线认证 SDK 中的 SGMain 组件报 duplicate symbol 冲突。

    **处理建议**：SecurityEnvSDK 组件是 SGMain 组件的早先版本，删除 SecurityEnvSDK，但保留两个组件的`yw_1222_*.jpg`签名图片文件。

-   APP 采用了插件机制，集成无线认证 SDK要注意什么。

    **处理建议**：建议把无线认证 SDK 放到主 Bundle 下。


