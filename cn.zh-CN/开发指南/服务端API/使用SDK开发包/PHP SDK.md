# PHP SDK {#reference_jy2_fss_gfb .reference}

## 获取地址 {#section_aq3_hss_gfb .section}

[GitHub](https://github.com/aliyun/aliyun-openapi-php-sdk)，引入`aliyun-php-sdk-core`和`aliyun-php-sdk-cloudauth`。

**说明：** 两个 SDK 都必须引入，其中`aliyun-php-sdk-core`为阿里云的核心 SDK，`aliyun-php-sdk-cloudauth`为实人认证的 SDK。

## 使用说明 {#section_cz4_l5s_gfb .section}

参看 GitHub 中的说明，或者 [阿里云 SDK 开发指南 - PHP SDK](https://help.aliyun.com/document_detail/53111.html) 目录下的章节。

## 实人认证示例 {#section_gwq_m5s_gfb .section}

**RPBasic认证方案**

```
<?php
include_once './aliyun-php-sdk-core/Config.php';
include_once 'Guid.php'; //参见https://help.aliyun.com/document_detail/64081.html#guid
use Cloudauth\Request\V20180504 as cloudauth; //请以实际目录为准
// 创建DefaultAcsClient实例并初始化
$iClientProfile = DefaultProfile::getProfile(
    "cn-hangzhou",            //默认
    "YourAccessKeyID",        //您的Access Key ID
    "YourAccessKeySecret");    //您的Access Key Secret
$iClientProfile::addEndpoint("cn-hangzhou", "cn-hangzhou", "Cloudauth", "cloudauth.aliyuncs.com");
$client = new DefaultAcsClient($iClientProfile);
$biz = "YourRPBasicBiz"; //您在控制台上创建的、采用RPBasic认证方案的认证场景标识, 创建方法：https://help.aliyun.com/document_detail/59975.html
$ticketId = guid(); //认证ID, 由使用方指定, 发起不同的认证任务需要更换不同的认证ID
$token = null; //认证token, 表达一次认证会话
$statusCode = -1; //-1 未认证, 0 认证中, 1 认证通过, 2 认证不通过
//1. 服务端发起认证请求, 获取到token
//GetVerifyToken接口文档：https://help.aliyun.com/document_detail/57050.html
$getVerifyTokenRequest = new cloudauth\GetVerifyTokenRequest();
$getVerifyTokenRequest->setBiz($biz);
$getVerifyTokenRequest->setTicketId($ticketId);
try {
    $response = $client->getAcsResponse($getVerifyTokenRequest);
    $token = $response->Data->VerifyToken->Token; //token默认30分钟时效，每次发起认证时都必须实时获取
} catch (Exception $e) {
    print $e->getTrace();
}
//2. 服务端将token传递给无线客户端
//3. 无线客户端用token调起无线认证SDK
//4. 用户按照由无线认证SDK组织的认证流程页面的指引，提交认证资料
//5. 认证流程结束退出无线认证SDK，进入客户端回调函数
//6. 服务端查询认证状态(客户端回调中也会携带认证状态, 但建议以服务端调接口确认的为准)
//GetStatus接口文档：https://help.aliyun.com/document_detail/57049.html
$getStatusRequest = new cloudauth\GetStatusRequest();
$getStatusRequest->setBiz($biz);
$getStatusRequest->setTicketId($ticketId);
try {
    $response = $client->getAcsResponse($getStatusRequest);
    $statusCode = $response->Data->StatusCode;
} catch (ServerException $e) {
    print $e->getMessage();
} catch (ClientException $e) {
    print $e->getMessage();
}
//7. 服务端获取认证资料
//GetMaterials接口文档：https://help.aliyun.com/document_detail/57641.html
$getMaterialsRequest = new cloudauth\GetMaterialsRequest();
$getMaterialsRequest->setBiz($biz);
$getMaterialsRequest->setTicketId($ticketId);
if (1 == $statusCode or 2 == $statusCode) { //认证通过or认证不通过
    try {
        $response = $client->getAcsResponse($getMaterialsRequest);
    } catch (ServerException $e) {
        print $e->getMessage();
    } catch (ClientException $e) {
        print $e->getMessage();
    }
}
//常见问题：https://help.aliyun.com/document_detail/57640.html
```

**RPManual认证方案**

代码示例同 RPBasic，其中`biz`须传入您在控制台上创建的、采用 RPManual 认证方案的 **场景标识**。

**RPBioID认证方案**

```
<?php
include_once './aliyun-php-sdk-core/Config.php';
include_once 'Guid.php'; //参见https://help.aliyun.com/document_detail/64081.html#guid
use Cloudauth\Request\V20180504 as cloudauth; //请以实际目录为准
// 创建DefaultAcsClient实例并初始化
$iClientProfile = DefaultProfile::getProfile(
    "cn-hangzhou",            //默认
    "YourAccessKeyID",        //您的Access Key ID
    "YourAccessKeySecret");    //您的Access Key Secret
$iClientProfile::addEndpoint("cn-hangzhou", "cn-hangzhou", "Cloudauth", "cloudauth.aliyuncs.com");
$client = new DefaultAcsClient($iClientProfile);
$biz = "YourRPBioIDBiz"; //您在控制台上创建的、采用RPBioID认证方案的认证场景标识, 创建方法：https://help.aliyun.com/document_detail/59975.html
$ticketId = guid();  //认证ID, 由使用方指定, 发起不同的认证任务需要更换不同的认证ID
$token = null; //认证token, 表达一次认证会话
$statusCode = -1; //-1 未认证, 0 认证中, 1 认证通过, 2 认证不通过
//1. 服务端发起认证请求, 获取到token
//GetVerifyToken接口文档：https://help.aliyun.com/document_detail/57050.html
$getVerifyTokenRequest = new cloudauth\GetVerifyTokenRequest();
$getVerifyTokenRequest->setBiz($biz);
$getVerifyTokenRequest->setTicketId($ticketId);
//若需要binding图片(如身份证正反面等), 且使用base64上传, 需要设置请求方法为POST
//$getVerifyTokenRequest->setMethod("POST");
//通过binding参数传入业务已经采集的认证资料，其中姓名、身份证号为必要字段
//若需要binding图片资料，请控制单张图片大小在 2M 内，避免拉取超时
$getVerifyTokenRequest->setBinding("{\"Name\": \"张三\",\"IdentificationNumber\": \"330110201711110101\"}");
try {
    $response = $client->getAcsResponse($getVerifyTokenRequest);
    $token = $response->Data->VerifyToken->Token; //token默认30分钟时效，每次发起认证时都必须实时获取
} catch (Exception $e) {
    print $e->getTrace();
}
//2. 服务端将token传递给无线客户端
//3. 无线客户端用token调起无线认证SDK
//4. 用户按照由无线认证SDK组织的认证流程页面的指引，提交认证资料
//5. 认证流程结束退出无线认证SDK，进入客户端回调函数
//6. 服务端查询认证状态(客户端回调中也会携带认证状态, 但建议以服务端调接口确认的为准)
//GetStatus接口文档：https://help.aliyun.com/document_detail/57049.html
$getStatusRequest = new cloudauth\GetStatusRequest();
$getStatusRequest->setBiz($biz);
$getStatusRequest->setTicketId($ticketId);
try {
    $response = $client->getAcsResponse($getStatusRequest);
    $statusCode = $response->Data->StatusCode;
} catch (ServerException $e) {
    print $e->getMessage();
} catch (ClientException $e) {
    print $e->getMessage();
}
//7. 服务端获取认证资料
//GetMaterials接口文档：https://help.aliyun.com/document_detail/57641.html
$getMaterialsRequest = new cloudauth\GetMaterialsRequest();
$getMaterialsRequest->setBiz($biz);
$getMaterialsRequest->setTicketId($ticketId);
if (1 == $statusCode or 2 == $statusCode) { //认证通过or认证不通过
    try {
        $response = $client->getAcsResponse($getMaterialsRequest);
    } catch (ServerException $e) {
        print $e->getMessage();
    } catch (ClientException $e) {
        print $e->getMessage();
    }
}
//常见问题：https://help.aliyun.com/document_detail/57640.html
```

**RPBioOnlyPro认证方案**

代码示例同RPBioID，其中`biz`须传入您在控制台上创建的、采用 RPBioOnlyPro 认证方案的 **场景标识**。

**RPBioOnly认证方案**

代码示例同 [RPBioID](https://help.aliyun.com/document_detail/64081.html?spm=a2c4g.11186623.6.565.5ae67463X5zC21#RPBioID)，其中`biz`须传入您在控制台上创建的、采用 RPBioOnly 认证方案的 **场景标识**。

**RPH5BioOnly认证方案**

```
<?php
include_once './aliyun-php-sdk-core/Config.php';
include_once 'Guid.php'; //参见https://help.aliyun.com/document_detail/64081.html#guid
use Cloudauth\Request\V20180504 as cloudauth; //请以实际目录为准
// 创建DefaultAcsClient实例并初始化
$iClientProfile = DefaultProfile::getProfile(
    "cn-hangzhou",            //默认
    "YourAccessKeyID",        //您的Access Key ID
    "YourAccessKeySecret");    //您的Access Key Secret
$iClientProfile::addEndpoint("cn-hangzhou", "cn-hangzhou", "Cloudauth", "cloudauth.aliyuncs.com");
$client = new DefaultAcsClient($iClientProfile);
$biz = "YourRPH5BioOnlyBiz"; //您在控制台上创建的、采用RPH5BioOnly认证方案的认证场景标识, 创建方法：https://help.aliyun.com/document_detail/59975.html
$ticketId = guid();  //认证ID, 由使用方指定, 发起不同的认证任务需要更换不同的认证ID
$token = null; //认证token, 表达一次认证会话
$statusCode = -1; //-1 未认证, 0 认证中, 1 认证通过, 2 认证不通过
//1. 服务端发起认证请求, 获取到token
//GetVerifyToken接口文档：https://help.aliyun.com/document_detail/57050.html
$getVerifyTokenRequest = new cloudauth\GetVerifyTokenRequest();
$getVerifyTokenRequest->setBiz($biz);
$getVerifyTokenRequest->setTicketId($ticketId);
//若需要binding图片(如身份证正反面等), 且使用base64上传, 需要设置请求方法为POST
//$getVerifyTokenRequest->setMethod("POST");
//通过binding参数传入业务已经采集的认证资料，其中姓名、身份证号为必要字段
//若需要binding图片资料，请控制单张图片大小在 2M 内，避免拉取超时
$getVerifyTokenRequest->setBinding("{\"Name\": \"张三\",\"IdentificationNumber\": \"330110201711110101\"}");
try {
    $response = $client->getAcsResponse($getVerifyTokenRequest);
    $token = $response->Data->VerifyToken->Token; //token默认30分钟时效，每次发起认证时都必须实时获取
} catch (Exception $e) {
    print $e->getTrace();
}
//2. 服务端将认证URL(带token)传递给H5前端
//3. H5前端跳转认证URL
//4. 用户按照认证H5流程页面的指引，提交认证资料
//5. 认证流程结束跳转指定的重定向URL
//6. 服务端查询认证状态(建议以服务端调接口确认的为准)
//GetStatus接口文档：https://help.aliyun.com/document_detail/57049.html
$getStatusRequest = new cloudauth\GetStatusRequest();
$getStatusRequest->setBiz($biz);
$getStatusRequest->setTicketId($ticketId);
try {
    $response = $client->getAcsResponse($getStatusRequest);
    $statusCode = $response->Data->StatusCode;
} catch (ServerException $e) {
    print $e->getMessage();
} catch (ClientException $e) {
    print $e->getMessage();
}
//7. 服务端获取认证资料
//GetMaterials接口文档：https://help.aliyun.com/document_detail/57641.html
$getMaterialsRequest = new cloudauth\GetMaterialsRequest();
$getMaterialsRequest->setBiz($biz);
$getMaterialsRequest->setTicketId($ticketId);
if (1 == $statusCode or 2 == $statusCode) { //认证通过or认证不通过
    try {
        $response = $client->getAcsResponse($getMaterialsRequest);
    } catch (ServerException $e) {
        print $e->getMessage();
    } catch (ClientException $e) {
        print $e->getMessage();
    }
}
//常见问题：https://help.aliyun.com/document_detail/57640.html
```

**RPMin认证方案**

```
<?php
include_once './aliyun-php-sdk-core/Config.php';
include_once 'Guid.php'; //参见https://help.aliyun.com/document_detail/64081.html#guid
use Cloudauth\Request\V20180504 as cloudauth; //请以实际目录为准
//创建DefaultAcsClient实例并初始化
$iClientProfile = DefaultProfile::getProfile(
    "cn-hangzhou",            //默认
    "YourAccessKeyID",        //您的Access Key ID
    "YourAccessKeySecret");    //您的Access Key Secret
$iClientProfile::addEndpoint("cn-hangzhou", "cn-hangzhou", "Cloudauth", "cloudauth.aliyuncs.com");
$client = new DefaultAcsClient($iClientProfile);
$biz = "YourRPMinBiz"; //您在控制台上创建的、采用RPMin认证方案的认证场景标识, 创建方法：https://help.aliyun.com/document_detail/59975.html
$ticketId = guid();  //认证ID, 由使用方指定, 发起不同的认证任务需要更换不同的认证ID
$token = null; //认证token, 表达一次认证会话
//1. 服务端发起认证请求, 获取到token
//GetVerifyToken接口文档：https://help.aliyun.com/document_detail/57050.html
$getVerifyTokenRequest = new cloudauth\GetVerifyTokenRequest();
$getVerifyTokenRequest->setBiz($biz);
$getVerifyTokenRequest->setTicketId($ticketId);
try {
    $response = $client->getAcsResponse($getVerifyTokenRequest);
    $token = $response->Data->VerifyToken->Token; //token默认30分钟时效，每次发起认证时都必须实时获取
} catch (Exception $e) {
    print $e->getTrace();
}
//2. 用token提交认证材料
//SubmitMaterials接口文档：https://help.aliyun.com/document_detail/58176.html
$submitRequest = new cloudauth\SubmitMaterialsRequest();
$submitRequest->setVerifyToken($token);
//若使用base64上传图片, 需要设置请求方法为POST
$submitRequest->setMethod("POST");
$identificationNumber = array("MaterialType" => "IdentificationNumber", "Value" => "330110201711110101");
$name = array("MaterialType" => "Name", "Value" => "张三");
//传入图片资料，请控制单张图片大小在 2M 内，避免拉取超时
$facePic = array("MaterialType" => "FacePic", "Value" => "base64://iVBORw0KGgoA..."); //base64方式上传图片, 格式为"base64://图片base64字符串", 以"base64://"开头且图片base64字符串去掉头部描述(如"data:image/png;base64,"), 并注意控制接口请求的Body在8M以内
$idCardFrontPic = array("MaterialType" => "IdCardFrontPic", "Value" => "http://image-demo.img-cn-hangzhou.aliyuncs.com/example.jpg"); //http方式上传图片, 此http地址须可公网访问
$idCardBackPic = array("MaterialType" => "IdCardBackPic", "Value" => "oss://verify-img:715559d76a40774OSS.JPG"); //oss方式上传图片, 此oss文件地址须可公开访问
$verifyMaterials = array($identificationNumber, $name, $facePic, $idCardFrontPic, $idCardBackPic);
$submitRequest->setMaterials($verifyMaterials);
try {
    $SubmitMaterialsResponse = $client->getAcsResponse($submitRequest);
    //由于审核需要时间，SubmitMaterials接口并不保证同步返回认证结果，可能会返回认证中状态, 此时需要使用GetStatus接口轮询认证结果。
    //GetStatus接口文档：https://help.aliyun.com/document_detail/57049.html
    //$getStatusRequest = new cloudauth\GetStatusRequest();
    //$getStatusRequest->setBiz($biz);
    //$getStatusRequest->setTicketId($ticketId);
    //$response = $client->getAcsResponse($getStatusRequest);
    //$statusCode = $response->Data->StatusCode;
    //后续业务处理
} catch (ServerException $e) {
    print $e->getMessage();
} catch (ClientException $e) {
    print $e->getMessage();
}
//常见问题：https://help.aliyun.com/document_detail/57640.html
```

**FVBioOnly认证方案**

```
<?php
include_once './aliyun-php-sdk-core/Config.php';
include_once 'Guid.php'; //参见https://help.aliyun.com/document_detail/64081.html#guid
use Cloudauth\Request\V20180504 as cloudauth; //请以实际目录为准
// 创建DefaultAcsClient实例并初始化
$iClientProfile = DefaultProfile::getProfile(
    "cn-hangzhou",            //默认
    "YourAccessKeyID",        //您的Access Key ID
    "YourAccessKeySecret");    //您的Access Key Secret
$iClientProfile::addEndpoint("cn-hangzhou", "cn-hangzhou", "Cloudauth", "cloudauth.aliyuncs.com");
$client = new DefaultAcsClient($iClientProfile);
$biz = "YourFVBioOnly"; //您在控制台上创建的、采用FVBioOnly认证方案的认证场景标识, 创建方法：https://help.aliyun.com/document_detail/59975.html
$ticketId = guid();  //认证ID, 由使用方指定, 发起不同的认证任务需要更换不同的认证ID
$token = null; //认证token, 表达一次认证会话
$statusCode = -1; //-1 未认证, 0 认证中, 1 认证通过, 2 认证不通过
//1. 服务端发起认证请求, 获取到token
//GetVerifyToken接口文档：https://help.aliyun.com/document_detail/57050.html
$getVerifyTokenRequest = new cloudauth\GetVerifyTokenRequest();
$getVerifyTokenRequest->setBiz($biz);
$getVerifyTokenRequest->setTicketId($ticketId);
$getVerifyTokenRequest->setMethod("POST");
//通过binding参数传入业务已经采集的认证资料，其中人像留底照为必要字段
//若需要binding图片资料，请控制单张图片大小在 2M 内，避免拉取超时
$getVerifyTokenRequest->setBinding("{\"FaceRetainedPic\": \"http://...\"}");
try {
    $response = $client->getAcsResponse($getVerifyTokenRequest);
    $token = $response->Data->VerifyToken->Token; //token默认30分钟时效，每次发起认证时都必须实时获取
} catch (Exception $e) {
    print $e->getTrace();
}
//2. 服务端将token传递给无线客户端
//3. 无线客户端用token调起无线认证SDK
//4. 用户按照由无线认证SDK组织的认证流程页面的指引，提交认证资料
//5. 认证流程结束退出无线认证SDK，进入客户端回调函数
//6. 服务端查询认证状态(客户端回调中也会携带认证状态, 但建议以服务端调接口确认的为准)
//GetStatus接口文档：https://help.aliyun.com/document_detail/57049.html
$getStatusRequest = new cloudauth\GetStatusRequest();
$getStatusRequest->setBiz($biz);
$getStatusRequest->setTicketId($ticketId);
try {
    $response = $client->getAcsResponse($getStatusRequest);
    $statusCode = $response->Data->StatusCode;
} catch (ServerException $e) {
    print $e->getMessage();
} catch (ClientException $e) {
    print $e->getMessage();
}
//7. 服务端获取认证资料
//GetMaterials接口文档：https://help.aliyun.com/document_detail/57641.html
$getMaterialsRequest = new cloudauth\GetMaterialsRequest();
$getMaterialsRequest->setBiz($biz);
$getMaterialsRequest->setTicketId($ticketId);
if (1 == $statusCode or 2 == $statusCode) { //认证通过or认证不通过
    try {
        $response = $client->getAcsResponse($getMaterialsRequest);
    } catch (ServerException $e) {
        print $e->getMessage();
    } catch (ClientException $e) {
        print $e->getMessage();
    }
}
//常见问题：https://help.aliyun.com/document_detail/57640.html
```

## 人脸验证示例 {#section_bzm_bvs_gfb .section}

**人脸比对验证**

```
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

## 附录 {#section_bmw_dvs_gfb .section}

**生成GUID**

```
<?php
function guid(){
    if (function_exists('com_create_guid')){
        return com_create_guid();
    }else{
        mt_srand((double)microtime()*10000);//optional for php 4.2.0 and up.
        $charid = strtoupper(md5(uniqid(rand(), true)));
        $hyphen = chr(45);// "-"
        $uuid = chr(123)// "{"
            .substr($charid, 0, 8).$hyphen
            .substr($charid, 8, 4).$hyphen
            .substr($charid,12, 4).$hyphen
            .substr($charid,16, 4).$hyphen
            .substr($charid,20,12)
            .chr(125);// "}"
        return $uuid;
    }
}
```

