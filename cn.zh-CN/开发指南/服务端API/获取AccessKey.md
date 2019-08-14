# 获取AccessKey {#concept_63821_zh .concept}

介绍如何创建RAM子用户，并授权其访问API和进行控制台操作。

阿里云会对每个访问请求进行身份验证，采用Access Key ID和Access Key Secret对称加密的方法来验证请求的发送者身份。其中，Access Key ID和Access Key Secret统称为AccessKey，是阿里云颁发给用户的访问服务所用的密钥。

实人认证支持阿里云账号或其 RAM 子用户的 AccessKey，但因为阿里云账号的 AccessKey 具有所有云产品 API 的访问权限，一旦泄露将导致极大的安全风险，所以强烈建议您根据最小权限原则创建并使用 RAM 子用户来进行 API 访问和控制台操作，具体操作步骤如下：

1.  创建 RAM 子用户，具体请参考[创建 RAM 用户](https://help.aliyun.com/document_detail/28637.html)。
2.  给 RAM 子用户授权系统策略`AliyunYundunCloudAuthFullAccess`，具体请参考[给 RAM 用户授权](https://help.aliyun.com/document_detail/28639.html)。
3.  给 RAM 子用户创建 AccessKey，具体请参考[创建 AccessKey](https://help.aliyun.com/document_detail/53045.html?parentId=57038)。

