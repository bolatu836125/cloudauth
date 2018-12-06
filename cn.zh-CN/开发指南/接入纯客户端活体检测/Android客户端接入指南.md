# Android客户端接入指南 {#concept_jbm_tf4_1gb .concept}

实人认证提供客户端 SDK 帮助接入方在 APP 中实现刷脸认证功能。

## 操作步骤 {#section_wdj_zf4_1gb .section}

1.  下载 Android SDK。

    Android SDK：[FaceVerifySDK\_Android\_3.1.zip](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/99125/cn_zh/1543998791619/FaceVerifySDK_Android_3.1.zip)

2.  添加 SDK。

    将下载的 SDK 解压后复制到项目下的 libs 目录（如果不存在 libs目录，请自行创建），并在 build.gradle 文件中，增加以下依赖：

    ```
    dependencies {
        ...
        compile 'com.android.support:support-v4:26.0.0-alpha1'
        compile files('libs/deviceid-release-6.0.2.20181008.aar')
        compile files('libs/fastjson-1.1.45.jar')
        compile files('libs/sdk_antcloud-3.0.0.31.aar')
    }
    ```

3.  调用 SDK。

    开始刷脸认证。

    ```
    ZIMFacade zimFacade = ZIMFacadeBuilder.create(MainActivity.this);
    zimFacade.verify(queryId, null, new ZIMCallback() {
     @Override
     public boolean response(ZIMResponse response) {
         // TODO: 接入方在这里实现自身业务
    
         return true；
     }
    }
    ```


