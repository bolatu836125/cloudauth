# PHP SDK {#reference_jy2_fss_gfb .reference}

本文介绍了如何使用阿里云实人认证服务的PHP SDK。

## 获取地址 {#section_aq3_hss_gfb .section}

单击前往[GitHub](https://github.com/aliyun/openapi-sdk-php)

## 使用说明 {#section_cz4_l5s_gfb .section}

参见GitHub中的说明。

## 实人认证和活体人脸验证示例 {#section_67t_8au_0u1 .section}

完整示例正在准备中，请先参见Java SDK示例。

## 人脸比对验证示例 {#section_as9_tb8_99u .section}

``` {#codeblock_s0l_r4e_cjb}
<?php
include_once './aliyun-php-sdk-core/Config.php';
use Cloudauth\Request\V20180504 as cloudauth; //请以实际目录为准
//创建DefaultAcsClient实例并初始化
$iClientProfile = DefaultProfile::getProfile(
    "cn-hangzhou",            //默认
    "YourAccessKeyID",        //您的Access Key ID
    "YourAccessKeySecret");    //您的Access Key Secret
$iClientProfile::addEndpoint("cn-hangzhou", "cn-hangzhou", "Cloudauth", "cloudauth.aliyuncs.com");
$client = new DefaultAcsClient($iClientProfile);
//创建API请求并设置参数
//CompareFaces接口文档：https://help.aliyun.com/document_detail/59317.html
$request = new cloudauth\CompareFacesRequest();
//若使用base64上传图片, 需要设置请求方法为POST
$request->setMethod("POST");
//传入图片资料，请控制单张图片大小在 2M 内，避免拉取超时
$request->setSourceImageType("FacePic");
$request->setSourceImageValue("base64://iVBORw0KGgoA..."); //base64方式上传图片, 格式为"base64://图片base64字符串", 以"base64://"开头且图片base64字符串去掉头部描述(如"data:image/png;base64,"), 并注意控制接口请求的Body在8M以内
$request->setTargetImageType("FacePic"); //若为身份证芯片照则传"IDPic"
$request->setTargetImageValue("http://image-demo.img-cn-hangzhou.aliyuncs.com/example.jpg"); //http方式上传图片, 此http地址须可公网访问
//发起请求并处理异常
try {
    $response = $client->getAcsResponse($request);
    // 后续业务处理
} catch (ServerException $e) {
    print $e->getMessage();
} catch (ClientException $e) {
    print $e->getMessage();
}
//常见问题：https://help.aliyun.com/document_detail/57640.html
```

