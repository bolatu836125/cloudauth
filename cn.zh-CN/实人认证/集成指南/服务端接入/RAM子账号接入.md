# RAM子账号接入 {#reference_1426119 .reference}

实人认证服务端API支持您通过RAM子账号的方式进行调用。要使用RAM子账号调用实人认证API，您需要创建子账号并完成授权。

## 操作步骤 {#section_aaq_tvv_m75 .section}

1.  登录[RAM控制台](https://ram.console.aliyun.com/#/user/list)，创建子账号， 并选择生成AccessKeyID和AccessKeySecret。创建后请妥善保存该AccessKeyID和AccessKeySecret，后续使用SDK时需要提供。

    **说明：** 更多关于创建RAM子账号的操作，请参见[创建RAM用户](../../../../cn.zh-CN/用户指南/用户/创建 RAM 用户.md#)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1135168/156568997053723_zh-CN.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1135168/156568997053724_zh-CN.png)

2.  完成子账号授权。只有完成子账号授权，您的子账号才能够调用相关API。您需要向子账号授权以下系统策略权限：AliyunYundunCloudAuthFullAccess。

    **说明：** 更多关于子账号授权的操作，请参见[为RAM用户授权](../../../../cn.zh-CN/用户指南/用户/为 RAM 用户授权.md#)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1135168/156568997053725_zh-CN.png)

3.  给 RAM 子用户创建 AccessKey，具体请参见[创建AccessKey](../../../../cn.zh-CN/通用参考/创建AccessKey.md#)。

