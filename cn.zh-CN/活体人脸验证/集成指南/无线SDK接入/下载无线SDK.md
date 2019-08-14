# 下载无线SDK {#task_1443363 .task}

本文介绍了下载活体人脸验证无线SDK的具体步骤。

1.  使用阿里云账号或具有AliyunYundunCloudAuthFullAccess授权 的RAM子用户，登录 [实人认证管理控制台](https://yundunnext.console.aliyun.com/?p=cloudauth#/settings)。 

    **说明：** 关于创建RAM子用户、给RAM用户授权、RAM用户登录控制台的方法，请参见[RAM子账号接入](https://help.aliyun.com/document_detail/128360.htm)。

2.  在左侧导航栏，单击**接入及设置**。
3.  打开活体人脸验证页签，选择要操作的认证场景，并单击**获取认证SDK**。![活体人脸验证](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1148230/156569000553816_zh-CN.png)


4.  在认证SDK生成对话框中，选择应用类型（**iOS**/**Android**），并单击**上传应用**。![认证SDK生成](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1148230/156569000553817_zh-CN.png)


5.  从本地选择需要集成认证SDK的应用，并上传其ipa文件（iOS应用）或apk文件（Android应用）。 

    **说明：** 

    -   无线认证SDK是绑定应用的，因此需要先上传您的应用以便系统为您生成该应用专属的SDK。如果应用版本迭代过程中，包名和签名（或bundleid）未变化，则无需重新生成SDK，如果包名或签名有变化，则需要重新上传应用，生成SDK再集成使用。
    -   在流程配置中，修改了引导页、用户授权声明页、结果页，不需要重新下载更新认证SDK。
6.  应用上传完成后，单击**下载认证SDK**完成下载。
7.  参见以下文档完成无线认证SDK的集成： 
    -   [Android SDK集成](https://help.aliyun.com/document_detail/128362.htm)
    -   [iOS SDK集成](https://help.aliyun.com/document_detail/128363.htm)

