# 下载无线SDK {#task_1426508 .task}

本文介绍了获取实人认证无线SDK的具体操作。

1.  使用阿里云账号或具有AliyunYundunCloudAuthFullAccess授权的RAM用户账号，登录 [实人认证管理控制台](https://yundunnext.console.aliyun.com/?p=cloudauth#/settings)。 

    **说明：** 关于创建RAM子用户、给RAM用户授权、RAM用户登录控制台的方法，请参见[RAM子账号接入](cn.zh-CN/实人认证/集成指南/服务端接入/RAM子账号接入.md#)。

2.  在左侧导航栏，单击**接入及设置**。
3.  打开实人认证页签，选择要操作的认证场景，并单击**获取认证SDK**。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1135434/156568993353975_zh-CN.png)


4.  在认证SDK生成对话框中，选择应用类型（**iOS**/**Android**），并单击**上传应用**。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1135434/156568993353976_zh-CN.png)


5.  从本地选择需要集成认证SDK的应用，并上传其ipa文件（iOS应用）或apk文件（Android应用）。 

    **说明：** 

    -   无线认证SDK是绑定应用的，因此需要先上传您的应用以便系统为您生成该应用专属的SDK。如果应用版本迭代过程中，包名和签名（或bundleid）未变化，则无需重新生成SDK，如果包名或签名有变化，则需要重新上传应用，生成SDK再集成使用。
    -   在流程配置中，修改了引导页、用户授权声明页、结果页，不需要重新下载更新认证SDK。
6.  应用上传完成后，单击下载认证SDK完成下载。
7.  参见以下文档完成无线认证SDK的集成： 
    -   [Android应用集成SDK](cn.zh-CN/实人认证/集成指南/无线SDK接入/Android集成.md#)
    -   [iOS应用集成SDK](cn.zh-CN/实人认证/集成指南/无线SDK接入/iOS集成.md#)

