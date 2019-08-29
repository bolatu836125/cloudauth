# Android集成 {#task_1426480 .task}

下载无线认证 SDK 后，您可参考以下步骤将认证 SDK 集成到您的 Android 应用中。

[单击查看老版本接入文档说明。](../../../../cn.zh-CN/老系统文档说明/老版本接入文档说明.md#)

Android SDK 与包名（package name）+签名（keystore）绑定，修改package name或keystore都需要在[管理控制台](https://yundun.console.aliyun.com/?p=cloudauth)上重新下载SDK，debug和release包在使用不同签名时不能混用。

**说明：** 若您需要用到 V2 方式的签名，打包时请同时勾选 V1、V2 签名（如果只选择V2签名，apk将无法在Android 7.0以下安装）。

## 在工程中导入SDK {#section_hh6_fsh_a4b .section}

**说明：** 对于已经接入过实人认证无线SDK的接入方（rpsdk版本号是2.1.x.x），在控制台下载到新版本的无线SDK后（rpsdk 版本号3.1.x.x），需要将新下载SDK包中的所有的文件覆盖到原有开发包，并按下述步骤引入到应用工程中。

解压无线认证SDK包中的client.zip文件，将以下Android依赖包引入到您的应用工程中：

-   oss-android-sdk-x.x.x.x.aar
-   FaceLivenessOpen-x.x.x.x.aar
-   NoCaptchaSDK-external-release.aar（通过解压Android.NoCaptchaSDK.xxx.tar获得该依赖包）
-   SecurityBodySDK-external-release.aar（通过解压Android.SecurityBodySDK.xxx.tar获得该依赖包）
-   SecurityGuardSDK-external-release.aar（通过解压Android.SecurityGuardSDK.xxx.tar获得该依赖包）
-   Okhttp.jar Okio.jar
-   logging-interceptor.jar
-   Rpsdk.aar
-   Windvane-mini.jar

例如，如果您使用的AndroidStudio，可参考以下步骤：

1.  设定依赖包所在的路径。 

    ``` {#codeblock_hrd_fh1_i38}
    apply plugin: 'com.android.application'
     repositories {
         flatDir {
             dirs '../libs'
         }
     }
    ```

2.  设定引入的本地库所在路径，将需要引入的依赖包都放在../libs目录，包含所有需要的库。
3.  在gradle文件中引入以下需要的库依赖。 

    ``` {#codeblock_u34_286_71o}
     compile fileTree(dir: '../libs', include: ['*.jar'])
     compile (name:'aliyun-oss-sdk-android-2.9.2',ext:'aar')
     compile (name:'FaceLivenessOpen-2.1.6.10',ext:'aar')
     compile (name:'rpsdk-2.4.0.3',ext:'aar')
     compile (name:'SecurityGuardSDK-external-release-5.4.94',ext:'aar')
     compile (name:'SecurityBodySDK-external-release-5.4.66',ext:'aar')
     compile (name:'NoCaptchaSDK-external-release-5.4.26',ext:'aar')
    ```

    关于签名图片

    -   解压已下载的无线认证SDK包，获得yw\_1222\_\*.jpg签名图片文件，该文件用于无线认证SDK的授权。
    -   把该图片文件导入到工程中res\\drawable\\目录，如果没有这个文件夹，请先在工程中创建，否则将无法正常工作。
    -   如果在安卓工程打包时启用了shrinkResources true，还需要在keep.xml文件中添加以下内容：

        ``` {#codeblock_ez5_ide_dkd}
        <resources xmlns:tools="http://schemas.android.com/tools" tools:keep="@drawable/yw_1222_*" />
        ```

    关于 CPU 类型

    无线认证 SDK 目前支持 armeabi、armeabi-v7a、arm64-v8a，请接入方按需在build.gradle中增加abifilters配置。例如，接入方仅需要支持其中 armeabi 和 arm64-v8a，则配置如下：

    ``` {#codeblock_jem_nw0_ef7}
    defaultConfig {
        ndk {
            abiFilters "armeabi", "arm64-v8a"
        }
    }
    ```

    关于权限

    无线认证SDK的使用需要添加以下权限：

    ``` {#codeblock_bg9_pcr_hpk}
    <uses-permission
    android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission
    android:name="android.permission.READ_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.CAMERA"/>
    ```

    关于应用主题配置

    ``` {#codeblock_2fe_8z7_gnl}
    <item name="android:windowIsTranslucent">false</item>
    ```


## 添加ProGuard 配置 {#section_5mo_y8h_nja .section}

如果您的应用使用了ProGuard 进行代码混淆，为了保证实人认证服务需要的一些类不被混淆，需要在 ProGuard 配置文件中添加相关指令。

1.  根据您接入方式的情况，判断是否使用了 ProGuard 进行代码混淆。 
    -   Eclipse

        如果在 project.properties中指定了ProGuard配置（例如，在`project.properties`中包含`proguard.config=proguard.cfg`语句），则表明已使用 ProGuard 进行代码混淆，混淆配置在 proguard.cfg 文件中。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1135430/156707064453977_zh-CN.png)

    -   Android Studio

        如果在build.gradle 中配置了 proguardFiles，并且启用了 minifyEnabled 方法，则表明已使用 proguard-rules.pro 这个配置文件进行代码混淆。例如：

        ``` {#codeblock_t9h_kbj_ua4}
        buildTypes {
          release {
              minifyEnabled true
              proguardFiles getDefaultProguardFile('proguard-android.txt'),     'proguard-rules.pro'
          }
        }
        ```

2.  在相应的ProGuard 配置文件中添加以下配置信息，保证实人认证服务需要的类不被混淆。 

    ``` {#codeblock_e75_oej_wlq}
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


## 使用SDK {#section_mza_h57_5pq .section}

1.  初始化。 

    一般在应用启动时进行。

    ``` {#codeblock_v28_q8v_g4x}
    import com.alibaba.security.rp.RPSDK;
     RPSDK.initialize(appContext);
    ```

2.  进入认证。 

    ``` {#codeblock_wft_012_8y7}
    RPSDK.start(verifyToken, ParametersActivity.this, 
         new RPSDK.RPCompletedListener() {
         @Override
         public void onAuditResult(RPSDK.AUDIT audit, String code) {
             Toast.makeText(ParametersActivity.this, audit + "",
    Toast.LENGTH_SHORT).show();
             if(audit == RPSDK.AUDIT.AUDIT_PASS) { //认证通过
             }
             else if(audit ==
    RPSDK.AUDIT.AUDIT_FAIL) { //认证不通过
             }
             else if(audit == RPSDK.AUDIT.AUDIT_NOT) { //未认证，具体原因可通过code来区分（code取值参见下方表格），通常是用户主动退出或者姓名身份证号实名校验不匹配等原因，导致未完成认证流程
             }
         }
     });
    ```

    **说明：** 

    -   verifyToken参数由接入方的服务端调用实人认证服务的DescribeVerifyToken接口获得。
    -   在RPSDK.start接口调用的回调会返回用户认证的各种状态，onAuditResult函数的的audit和code参数取值，可参见下表说明。
    |audit|code|code释义|
    |-----|----|------|
    |RPSDK.AUDIT.AUDIT\_PASS|1|认证通过|
    |RPSDK.AUDIT.AUDIT\_FAIL|取值3~12|表示认证不通过，具体的不通过原因可以查看服务端的查询认证结果（DescribeVerifyResult）接口文档中认证状态的表格说明|
    |RPSDK.AUDIT.AUDIT\_NOT|-1|未完成认证，原因是：用户在认证过程中，主动退出|
    |RPSDK.AUDIT.AUDIT\_NOT|3001|未完成认证，原因是：认证token无效或已过期|
    |RPSDK.AUDIT.AUDIT\_NOT|3101|未完成认证，原因是：用户姓名身份证实名校验不匹配|
    |RPSDK.AUDIT.AUDIT\_NOT|3102|未完成认证，原因是：实名校验身份证号不存在|
    |RPSDK.AUDIT.AUDIT\_NOT|3103|未完成认证，原因是：实名校验身份证号不合法|
    |RPSDK.AUDIT.AUDIT\_NOT|3104|未完成认证，原因是：认证已通过，重复提交|
    |RPSDK.AUDIT.AUDIT\_NOT|3204|未完成认证，原因是：操作太频繁|


## 常见问题 {#section_zt8_umt_4m6 .section}

-   调起无线认证SDK进入认证页面时，遇到**UNKNOWN\_ERROR**、**网络异常，请检查网络**报错提示。

    处理建议：查看logcat。

    -   若ErrorCode = 103，需要将abiFilters配置为`armeabi-v7a`。若配置后问题仍然存在，请检查下gradle版本。如果gradle的版本是5.4.1及以上，请将其降回到5.1或4.x系列版本后，再试一遍。
    -   若ErrorCode = 202，说明签名图片文件（yw\_1222\_0670.jpg）有问题。一般是获取签名图片文件时APK的签名和当前正在运行的APK签名不一致。请使用当前正在运行的APK重新获取签名图片文件。
    -   若ErrorCode = 203，则请考虑下述三种情况：
        -   签名图片文件（yw\_1222\_0670.jpg）不存在，请确保签名图片文件在res/drawable目录下。
        -   安卓环境下可能是因为资源优化被优化成了0KB，请检查APK中的签名图片文件（res/drawable/yw\_1222\_0670.jpg），具体请参见shrinkResources解决方案。
        -   如果是在Android Studio下调试发现找不到图片，但是确认图片是正常的，请关闭Android Studio的Instant Run功能。在Instant Run下，APK并非一个完整的bundle，其资源文件被拆分到特定bundle中，图片路径发生改变会导致找不到签名图片文件。
    **说明：** 工程上，通常对 IDE 中直接运行（debug）和正式打包（release）会配置不同的签名（keystore），在 IDE 中直接编译运行的是 debug 包，其签名图片文件和 release 包是不同的。

-   项目中之前引入的组件与无线认证 SDK 中的组件有重复，例如 SecurityGuardSDK、oss-android-sdk 等。

    处理建议：若 SecurityGuardSDK 组件有重复，删除低版本，但保留两个版本的yw\_1222\_\*.jpg签名图片文件；若 oss-android-sdk 等其他组件有重复，删除低版本即可。

-   项目中之前引入的SecurityEnvSDK 组件与无线认证 SDK 中的SGMain 组件报 duplicate symbol 冲突。

    处理建议：SecurityEnvSDK 组件是 SGMain 组件的早先版本，删除 SecurityEnvSDK，但保留两个组件的yw\_1222\_\*.jpg签名图片文件。

-   APP 采用了插件机制，集成无线认证 SDK要注意什么？

    处理建议：建议把无线认证 SDK 放到主 Bundle 下。


