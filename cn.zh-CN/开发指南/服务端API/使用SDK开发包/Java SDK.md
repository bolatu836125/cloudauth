# Java SDK {#reference_jy2_fss_gfb .reference}

## 获取地址 {#section_aq3_hss_gfb .section}

-   Maven，引入 [Aliyun Java Sdk Core](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22aliyun-java-sdk-core%22) 和 [Aliyun Java Sdk Cloudauth](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22aliyun-java-sdk-cloudauth%22)。
-   [GitHub](https://github.com/aliyun/aliyun-openapi-java-sdk)，引入`aliyun-java-sdk-core`和`aliyun-java-sdk-cloudauth`。

**说明：** 两个 SDK 都必须引入，其中`aliyun-java-sdk-core`为阿里云的核心 SDK，`aliyun-java-sdk-cloudauth`为实人认证的 SDK。

若您使用的是 aliyun-java-sdk-core 4.0.0~4.0.2 版本，调用 https 接口时需要 profile 额外加一句：profile.getHttpClientConfig\(\).setIgnoreSSLCerts\(true\);

## 安装说明 {#section_nww_lss_gfb .section}

参看 GitHub 中的说明，或者 [阿里云 SDK 开发指南 - Java SDK](https://help.aliyun.com/document_detail/52740.html) 目录下的章节。

## 实人认证示例 {#section_lcv_mss_gfb .section}

****RPBasic认证方案****

```
//创建DefaultAcsClient实例并初始化
DefaultProfile profile = DefaultProfile.getProfile(
        "cn-hangzhou",             //默认
        "YourAccessKeyID",         //您的Access Key ID
        "YourAccessKeySecret");    //您的Access Key Secret
IAcsClient client = new DefaultAcsClient(profile);
String biz = "YourRPBasicBiz"; //您在控制台上创建的、采用RPBasic认证方案的认证场景标识, 创建方法：https://help.aliyun.com/document_detail/59975.html
String ticketId = UUID.randomUUID().toString(); //认证ID, 由使用方指定, 发起不同的认证任务需要更换不同的认证ID
String token = null; //认证token, 表达一次认证会话
int statusCode = -1; //-1 未认证, 0 认证中, 1 认证通过, 2 认证不通过
//1. 服务端发起认证请求, 获取到token
//GetVerifyToken接口文档：https://help.aliyun.com/document_detail/57050.html
GetVerifyTokenRequest getVerifyTokenRequest = new GetVerifyTokenRequest();
getVerifyTokenRequest.setBiz(biz);
getVerifyTokenRequest.setTicketId(ticketId);
try {
    GetVerifyTokenResponse response = client.getAcsResponse(getVerifyTokenRequest);
    token = response.getData().getVerifyToken().getToken(); //token默认30分钟时效，每次发起认证时都必须实时获取
} catch (ServerException e) {
    e.printStackTrace();
} catch (ClientException e) {
    e.printStackTrace();
}
//2. 服务端将token传递给无线客户端
//3. 无线客户端用token调起无线认证SDK
//4. 用户按照由无线认证SDK组织的认证流程页面的指引，提交认证资料
//5. 认证流程结束退出无线认证SDK，进入客户端回调函数
//6. 服务端查询认证状态(客户端回调中也会携带认证状态, 但建议以服务端调接口确认的为准)
//GetStatus接口文档：https://help.aliyun.com/document_detail/57049.html
GetStatusRequest getStatusRequest = new GetStatusRequest();
getStatusRequest.setBiz(biz);
getStatusRequest.setTicketId(ticketId);
try {
    GetStatusResponse response = client.getAcsResponse(getStatusRequest);
    statusCode = response.getData().getStatusCode();
} catch (ServerException e) {
    e.printStackTrace();
} catch (ClientException e) {
    e.printStackTrace();
}
//7. 服务端获取认证资料
//GetMaterials接口文档：https://help.aliyun.com/document_detail/57641.html
GetMaterialsRequest getMaterialsRequest = new GetMaterialsRequest();
getMaterialsRequest.setBiz(biz);
getMaterialsRequest.setTicketId(ticketId);
if( 1 == statusCode || 2 == statusCode ) { //认证通过or认证不通过
    try {
        GetMaterialsResponse response = client.getAcsResponse(getMaterialsRequest);
        //后续业务处理
    } catch (ServerException e) {
        e.printStackTrace();
    } catch (ClientException e) {
        e.printStackTrace();
    }
}
//常见问题：https://help.aliyun.com/document_detail/57640.html
```

**RPManual认证方案**

代码示例同RPBasic，其中`biz`须传入您在控制台上创建的、采用 RPManual 认证方案的 **场景标识**。

****RPBioID认证方案****

```
//创建DefaultAcsClient实例并初始化
DefaultProfile profile = DefaultProfile.getProfile(
        "cn-hangzhou",             //默认
        "YourAccessKeyID",         //您的Access Key ID
        "YourAccessKeySecret");    //您的Access Key Secret
IAcsClient client = new DefaultAcsClient(profile);
String biz = "YourRPBioIDBiz"; //您在控制台上创建的、采用RPBioID认证方案的认证场景标识, 创建方法：https://help.aliyun.com/document_detail/59975.html
String ticketId = UUID.randomUUID().toString(); //认证ID, 由使用方指定, 发起不同的认证任务需要更换不同的认证ID
String token = null; //认证token, 表达一次认证会话
int statusCode = -1; //-1 未认证, 0 认证中, 1 认证通过, 2 认证不通过
//1. 服务端发起认证请求, 获取到token
//GetVerifyToken接口文档：https://help.aliyun.com/document_detail/57050.html
GetVerifyTokenRequest getVerifyTokenRequest = new GetVerifyTokenRequest();
getVerifyTokenRequest.setBiz(biz);
getVerifyTokenRequest.setTicketId(ticketId);
//若需要binding图片(如身份证正反面等), 且使用base64上传, 需要设置请求方法为POST
//getVerifyTokenRequest.setMethod(MethodType.POST);
//通过binding参数传入业务已经采集的认证资料，其中姓名、身份证号为必要字段
//若需要binding图片资料，请控制单张图片大小在 2M 内，避免拉取超时
getVerifyTokenRequest.setBinding("{\"Name\": \"张三\",\"IdentificationNumber\": \"330110201711110101\"}");
try {
    GetVerifyTokenResponse response = client.getAcsResponse(getVerifyTokenRequest);
    token = response.getData().getVerifyToken().getToken(); //token默认30分钟时效，每次发起认证时都必须实时获取
} catch (ServerException e) {
    e.printStackTrace();
} catch (ClientException e) {
    e.printStackTrace();
}
//2. 服务端将token传递给无线客户端
//3. 无线客户端用token调起无线认证SDK
//4. 用户按照由无线认证SDK组织的认证流程页面的指引，提交认证资料
//5. 认证流程结束退出无线认证SDK，进入客户端回调函数
//6. 服务端查询认证状态(客户端回调中也会携带认证状态, 但建议以服务端调接口确认的为准)
//GetStatus接口文档：https://help.aliyun.com/document_detail/57049.html
GetStatusRequest getStatusRequest = new GetStatusRequest();
getStatusRequest.setBiz(biz);
getStatusRequest.setTicketId(ticketId);
try {
    GetStatusResponse response = client.getAcsResponse(getStatusRequest);
    statusCode = response.getData().getStatusCode();
} catch (ServerException e) {
    e.printStackTrace();
} catch (ClientException e) {
    e.printStackTrace();
}
//7. 服务端获取认证资料
//GetMaterials接口文档：https://help.aliyun.com/document_detail/57641.html
GetMaterialsRequest getMaterialsRequest = new GetMaterialsRequest();
getMaterialsRequest.setBiz(biz);
getMaterialsRequest.setTicketId(ticketId);
if( 1 == statusCode || 2 == statusCode ) { //认证通过or认证不通过
    try {
        GetMaterialsResponse response = client.getAcsResponse(getMaterialsRequest);
        //后续业务处理
    } catch (ServerException e) {
        e.printStackTrace();
    } catch (ClientException e) {
        e.printStackTrace();
    }
}
//常见问题：https://help.aliyun.com/document_detail/57640.html
```

**RPBioOnlyPro认证方案**

代码示例同RPBioID，其中`biz`须传入您在控制台上创建的、采用 RPBioOnlyPro 认证方案的 **场景标识**。

**RPBioOnly认证方案**

代码示例同RPBioID，其中`biz`须传入您在控制台上创建的、采用 RPBioOnly 认证方案的 **场景标识**。

**RPH5BioOnly认证方案**

```
//创建DefaultAcsClient实例并初始化
DefaultProfile profile = DefaultProfile.getProfile(
        "cn-hangzhou",             //默认
        "YourAccessKeyID",         //您的Access Key ID
        "YourAccessKeySecret");    //您的Access Key Secret
IAcsClient client = new DefaultAcsClient(profile);
String biz = "YourRPH5BioOnlyBiz"; //您在控制台上创建的、采用RPH5BioOnly认证方案的认证场景标识, 创建方法：https://help.aliyun.com/document_detail/59975.html
String ticketId = UUID.randomUUID().toString(); //认证ID, 由使用方指定, 发起不同的认证任务需要更换不同的认证ID
String token = null; //认证token, 表达一次认证会话
int statusCode = -1; //-1 未认证, 0 认证中, 1 认证通过, 2 认证不通过
//1. 服务端发起认证请求, 获取到token
//GetVerifyToken接口文档：https://help.aliyun.com/document_detail/57050.html
GetVerifyTokenRequest getVerifyTokenRequest = new GetVerifyTokenRequest();
getVerifyTokenRequest.setBiz(biz);
getVerifyTokenRequest.setTicketId(ticketId);
//若需要binding图片(如身份证正反面等), 且使用base64上传, 需要设置请求方法为POST
//getVerifyTokenRequest.setMethod(MethodType.POST);
//通过binding参数传入业务已经采集的认证资料，其中姓名、身份证号为必要字段
//若需要binding图片资料，请控制单张图片大小在 2M 内，避免拉取超时
getVerifyTokenRequest.setBinding("{\"Name\": \"张三\",\"IdentificationNumber\": \"330110201711110101\"}");
try {
    GetVerifyTokenResponse response = client.getAcsResponse(getVerifyTokenRequest);
    token = response.getData().getVerifyToken().getToken(); //token默认30分钟时效，每次发起认证时都必须实时获取
} catch (ServerException e) {
    e.printStackTrace();
} catch (ClientException e) {
    e.printStackTrace();
}
//2. 服务端将认证URL(带token)传递给H5前端
//3. H5前端跳转认证URL
//4. 用户按照认证H5流程页面的指引，提交认证资料
//5. 认证流程结束跳转指定的重定向URL
//6. 服务端查询认证状态(建议以服务端调接口确认的为准)
//GetStatus接口文档：https://help.aliyun.com/document_detail/57049.html
GetStatusRequest getStatusRequest = new GetStatusRequest();
getStatusRequest.setBiz(biz);
getStatusRequest.setTicketId(ticketId);
try {
    GetStatusResponse response = client.getAcsResponse(getStatusRequest);
    statusCode = response.getData().getStatusCode();
} catch (ServerException e) {
    e.printStackTrace();
} catch (ClientException e) {
    e.printStackTrace();
}
//7. 服务端获取认证资料
//GetMaterials接口文档：https://help.aliyun.com/document_detail/57641.html
GetMaterialsRequest getMaterialsRequest = new GetMaterialsRequest();
getMaterialsRequest.setBiz(biz);
getMaterialsRequest.setTicketId(ticketId);
if( 1 == statusCode || 2 == statusCode ) { //认证通过or认证不通过
    try {
        GetMaterialsResponse response = client.getAcsResponse(getMaterialsRequest);
        //后续业务处理
    } catch (ServerException e) {
        e.printStackTrace();
    } catch (ClientException e) {
        e.printStackTrace();
    }
}
//常见问题：https://help.aliyun.com/document_detail/57640.html
```

**RPMin认证方案**

```
//创建DefaultAcsClient实例并初始化
DefaultProfile profile = DefaultProfile.getProfile(
        "cn-hangzhou",             //默认
        "YourAccessKeyID",         //您的Access Key ID
        "YourAccessKeySecret");    //您的Access Key Secret
IAcsClient client = new DefaultAcsClient(profile);
String biz = "YourRPMinBiz"; //您在控制台上创建的、采用RPMin认证方案的认证场景标识, 创建方法：https://help.aliyun.com/document_detail/59975.html
String ticketId = UUID.randomUUID().toString(); //认证ID, 由使用方指定, 发起不同的认证任务需要更换不同的认证ID
String token = null; //认证token, 表达一次认证会话
//1. 发起认证请求, 获取到token
//GetVerifyToken接口文档：https://help.aliyun.com/document_detail/57050.html
GetVerifyTokenRequest getVerifyTokenRequest = new GetVerifyTokenRequest();
getVerifyTokenRequest.setBiz(biz); //传入采用RPMin认证方案的认证场景标识(biz)
getVerifyTokenRequest.setTicketId(ticketId);
try {
    GetVerifyTokenResponse response = client.getAcsResponse(getVerifyTokenRequest);
    token = response.getData().getVerifyToken().getToken(); //token默认30分钟时效，每次发起认证时都必须实时获取
} catch (Exception e) {
    e.printStackTrace();
}
//2. 用token提交认证材料
//SubmitMaterials接口文档：https://help.aliyun.com/document_detail/58176.html
SubmitMaterialsRequest submitRequest = new SubmitMaterialsRequest();
submitRequest.setVerifyToken(token);
//若使用base64提交图片, 需要设置请求方法为POST
submitRequest.setMethod(MethodType.POST);
//创建要提交的认证材料列表, 请根据 认证方案 中的说明传入相应字段
List<SubmitMaterialsRequest.Material> verifyMaterials = new ArrayList<SubmitMaterialsRequest.Material>();
SubmitMaterialsRequest.Material identificationNumber = new SubmitMaterialsRequest.Material();
identificationNumber.setMaterialType("IdentificationNumber");
identificationNumber.setValue("330110201711110101");
verifyMaterials.add(identificationNumber);
SubmitMaterialsRequest.Material name = new SubmitMaterialsRequest.Material();
name.setMaterialType("Name");
name.setValue("张三");
verifyMaterials.add(name);
//传入图片资料，请控制单张图片大小在 2M 内，避免拉取超时
SubmitMaterialsRequest.Material facePic = new SubmitMaterialsRequest.Material();
facePic.setMaterialType("FacePic");
facePic.setValue("base64://iVBORw0KGgoA..."); //base64方式上传图片, 格式为"base64://图片base64字符串", 以"base64://"开头且图片base64字符串去掉头部描述(如"data:image/png;base64,"), 并注意控制接口请求的Body在8M以内
verifyMaterials.add(facePic);
SubmitMaterialsRequest.Material idCardFrontPic = new SubmitMaterialsRequest.Material();
idCardFrontPic.setMaterialType("IdCardFrontPic");
idCardFrontPic.setValue("http://image-demo.img-cn-hangzhou.aliyuncs.com/example.jpg"); //http方式上传图片, 此http地址须可公网访问
verifyMaterials.add(idCardFrontPic);
SubmitMaterialsRequest.Material idCardBackPic = new SubmitMaterialsRequest.Material();
idCardBackPic.setMaterialType("IdCardBackPic");
idCardBackPic.setValue("oss://verify-img:715559d76a40774OSS.JPG"); //oss方式上传图片, 此oss文件地址须可公开访问
verifyMaterials.add(idCardBackPic);
submitRequest.setMaterials(verifyMaterials);
try {
    SubmitMaterialsResponse response = client.getAcsResponse(submitRequest);
    //由于审核需要时间，SubmitMaterials接口并不保证同步返回认证结果，可能会返回认证中状态, 此时需要使用GetStatus接口轮询认证结果。
    //GetStatus接口文档：https://help.aliyun.com/document_detail/57049.html
    //GetStatusRequest getStatusRequest = new GetStatusRequest();
    //getStatusRequest.setBiz(biz);
    //getStatusRequest.setTicketId(ticketId);
    //GetStatusResponse response = client.getAcsResponse(getStatusRequest);
    //int statusCode = response.getData().getStatusCode();
    //后续业务处理
} catch (Exception e) {
    e.printStackTrace();
}
//常见问题：https://help.aliyun.com/document_detail/57640.html
```

**FVBioOnly认证方案**

```
//创建DefaultAcsClient实例并初始化
DefaultProfile profile = DefaultProfile.getProfile(
        "cn-hangzhou",             //默认
        "YourAccessKeyID",         //您的Access Key ID
        "YourAccessKeySecret");    //您的Access Key Secret
IAcsClient client = new DefaultAcsClient(profile);
String biz = "YourFVBioOnly"; //您在控制台上创建的、采用FVBioOnly认证方案的认证场景标识, 创建方法：https://help.aliyun.com/document_detail/59975.html
String ticketId = UUID.randomUUID().toString(); //认证ID, 由使用方指定, 发起不同的认证任务需要更换不同的认证ID
String token = null; //认证token, 表达一次认证会话
int statusCode = -1; //-1 未认证, 0 认证中, 1 认证通过, 2 认证不通过
//1. 服务端发起认证请求, 获取到token
//GetVerifyToken接口文档：https://help.aliyun.com/document_detail/57050.html
GetVerifyTokenRequest getVerifyTokenRequest = new GetVerifyTokenRequest();
getVerifyTokenRequest.setBiz(biz);
getVerifyTokenRequest.setTicketId(ticketId);
getVerifyTokenRequest.setMethod(MethodType.POST);
//通过binding参数传入业务已经采集的认证资料，其中人像留底照为必要字段
//若需要binding图片资料，请控制单张图片大小在 2M 内，避免拉取超时
getVerifyTokenRequest.setBinding("{\"FaceRetainedPic\": \"http://...\"}");
try {
    GetVerifyTokenResponse response = client.getAcsResponse(getVerifyTokenRequest);
    token = response.getData().getVerifyToken().getToken(); //token默认30分钟时效，每次发起认证时都必须实时获取
} catch (ServerException e) {
    e.printStackTrace();
} catch (ClientException e) {
    e.printStackTrace();
}
//2. 服务端将token传递给无线客户端
//3. 无线客户端用token调起无线认证SDK
//4. 用户按照由无线认证SDK组织的认证流程页面的指引，提交认证资料
//5. 认证流程结束退出无线认证SDK，进入客户端回调函数
//6. 服务端查询认证状态(客户端回调中也会携带认证状态, 但建议以服务端调接口确认的为准)
//GetStatus接口文档：https://help.aliyun.com/document_detail/57049.html
GetStatusRequest getStatusRequest = new GetStatusRequest();
getStatusRequest.setBiz(biz);
getStatusRequest.setTicketId(ticketId);
try {
    GetStatusResponse response = client.getAcsResponse(getStatusRequest);
    statusCode = response.getData().getStatusCode();
} catch (ServerException e) {
    e.printStackTrace();
} catch (ClientException e) {
    e.printStackTrace();
}
//7. 服务端获取认证资料
//GetMaterials接口文档：https://help.aliyun.com/document_detail/57641.html
GetMaterialsRequest getMaterialsRequest = new GetMaterialsRequest();
getMaterialsRequest.setBiz(biz);
getMaterialsRequest.setTicketId(ticketId);
if( 1 == statusCode || 2 == statusCode ) { //认证通过or认证不通过
    try {
        GetMaterialsResponse response = client.getAcsResponse(getMaterialsRequest);
        //后续业务处理
    } catch (ServerException e) {
        e.printStackTrace();
    } catch (ClientException e) {
        e.printStackTrace();
    }
}
//常见问题：https://help.aliyun.com/document_detail/57640.html
```

## 人脸验证示例 {#section_oqv_1ts_gfb .section}

**人脸比对验证**

```
//创建DefaultAcsClient实例并初始化
DefaultProfile profile = DefaultProfile.getProfile(
        "cn-hangzhou",             //默认
        "YourAccessKeyID",         //您的Access Key ID
        "YourAccessKeySecret");    //您的Access Key Secret
IAcsClient client = new DefaultAcsClient(profile);
//创建API请求并设置参数
//CompareFaces接口文档：https://help.aliyun.com/document_detail/59317.html
CompareFacesRequest request = new CompareFacesRequest();
//若使用base64上传图片, 需要设置请求方法为POST
request.setMethod(MethodType.POST);
//传入图片资料，请控制单张图片大小在 2M 内，避免拉取超时
request.setSourceImageType("FacePic");
request.setSourceImageValue("base64://iVBORw0KGgoA..."); //base64方式上传图片, 格式为"base64://图片base64字符串", 以"base64://"开头且图片base64字符串去掉头部描述(如"data:image/png;base64,"), 并注意控制接口请求的Body在8M以内
request.setTargetImageType("FacePic"); //若为身份证芯片照则传"IDPic"
request.setTargetImageValue("http://image-demo.img-cn-hangzhou.aliyuncs.com/example.jpg"); //http方式上传图片, 此http地址须可公网访问
//发起请求并处理异常
try {
    CompareFacesResponse response = client.getAcsResponse(request);
    //后续业务处理
} catch (ServerException e) {
    e.printStackTrace();
} catch (ClientException e) {
    e.printStackTrace();
}
//常见问题：https://help.aliyun.com/document_detail/57640.html
```

