# Java SDK {#reference_jy2_fss_gfb .reference}

## 获取地址 {#section_aq3_hss_gfb .section}

-   aliyun-java-sdk-core：[mvnrepository](https://mvnrepository.com/artifact/com.aliyun/aliyun-java-sdk-core) / [maven.org](https://search.maven.org/search?q=a:aliyun-java-sdk-core) / [GitHub](https://github.com/aliyun/aliyun-openapi-java-sdk/tree/master/aliyun-java-sdk-core)
-   aliyun-java-sdk-cloudauth：[mvnrepository](https://mvnrepository.com/artifact/com.aliyun/aliyun-java-sdk-cloudauth) / [maven.org](https://search.maven.org/search?q=a:aliyun-java-sdk-cloudauth) / [GitHub](https://github.com/aliyun/aliyun-openapi-java-sdk/tree/master/aliyun-java-sdk-cloudauth)

**说明：** 两个SDK都必须引入，其中aliyun-java-sdk-core为阿里云的核心SDK，aliyun-java-sdk-cloudauth为实人认证的SDK。

若您使用的是aliyun-java-sdk-core 4.0.0~4.0.2 版本，那么在调用https接口时需要在`profile`中额外加上以下内容：`profile.getHttpClientConfig().setIgnoreSSLCerts(true);`

## 安装说明 {#section_nww_lss_gfb .section}

**方法1：使用Maven（推荐）**

如果您使用Maven管理Java项目，可以通过在pom.xml文件中添加Maven依赖：

```
<dependency>
    <groupId>com.aliyun</groupId>
    <artifactId>aliyun-java-sdk-core</artifactId>
    <version>4.1.2</version>
</dependency>
<dependency>
    <groupId>com.aliyun</groupId>
    <artifactId>aliyun-java-sdk-cloudauth</artifactId>
    <version>1.3.0</version>
</dependency>
```

**说明：** `version`的值以SDK获取地址中的最新版本为准。

**方法2：在集成开发环境（IDE）中导入jar文件**

**Eclipse安装**

1.  将下载的aliyun-java-sdk-xxx.jar文件复制到您的项目文件夹中。
2.  在Eclipse中打开您的项目，右键单击该项目，单击**Properties**。
3.  在弹出的对话框中，单击**Java Build Path** \> **Libraries** \> **Add JARs**添加下载的JAR文件。
4.  单击**Apply and Close**。

**IntelliJ 安装**

1.  将下载的aliyun-java-sdk-xxx.jar文件复制到您的项目文件夹中。
2.  在IntelliJ中打开您的项目，在菜单栏中单击**File** \> **Project** \> **Structure**。
3.  单击**Apply**，然后单击**OK**。

## RPBasic认证方案示例 {#RPBasic .section}

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

## RPManual认证方案示例 {#RPManual .section}

代码示例同[RPBasic](#)。其中biz须传入您在控制台上创建的、采用RPManual认证方案的场景标识。

## RPBioID认证方案示例 {#RPBioID .section}

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
getVerifyTokenRequest.setMethod(MethodType.POST);
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

## RPBioOnlyPro认证方案示例 {#RPBioOnlyPro .section}

代码示例同[RPBioID](#)。其中biz须传入您在控制台上创建的、采用RPBioOnlyPro认证方案的场景标识。

## RPBioOnly认证方案示例 {#RPBioOnly .section}

代码示例同[RPBioID](#)。其中biz须传入您在控制台上创建的、采用RPBioOnly认证方案的场景标识。

## FVBioOnly认证方案示例 {#FVBioOnly .section}

```
//创建DefaultAcsClient实例并初始化
DefaultProfile profile = DefaultProfile.getProfile(
        "cn-hangzhou",             //默认
        "YourAccessKeyID",         //您的Access Key ID
        "YourAccessKeySecret");    //您的Access Key Secret
IAcsClient client = new DefaultAcsClient(profile);
String biz = "YourFVBioOnlyBiz"; //您在控制台上创建的、采用FVBioOnly认证方案的认证场景标识, 创建方法：https://help.aliyun.com/document_detail/59975.html
String ticketId = UUID.randomUUID().toString(); //认证ID, 由使用方指定, 发起不同的认证任务需要更换不同的认证ID
String token = null; //认证token, 表达一次认证会话
int statusCode = -1; //-1 未认证, 0 认证中, 1 认证通过, 2 认证不通过
//1. 服务端发起认证请求, 获取到token
//GetVerifyToken接口文档：https://help.aliyun.com/document_detail/57050.html
GetVerifyTokenRequest getVerifyTokenRequest = new GetVerifyTokenRequest();
getVerifyTokenRequest.setBiz(biz);
getVerifyTokenRequest.setTicketId(ticketId);
getVerifyTokenRequest.setMethod(MethodType.POST);
//通过binding参数传入业务已经采集的认证资料，其中人像留底照片为必要字段
//请控制单张图片大小在 2M 内，避免拉取超时
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

## RPH5BioOnly认证方案示例 {#RPH5BioOnly .section}

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
getVerifyTokenRequest.setMethod(MethodType.POST);
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
//5. 认证流程结束跳转指定的重定向URL(指定方法参看：https://help.aliyun.com/document_detail/58644.html?#H5Server)
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

## RPMin认证方案示例 {#RPMin .section}

```
//创建DefaultAcsClient实例并初始化
DefaultProfile profile = DefaultProfile.getProfile(
"cn-hangzhou", //默认
"YourAccessKeyID", //您的Access Key ID
"YourAccessKeySecret"); //您的Access Key Secret
IAcsClient client = new DefaultAcsClient(profile);
String biz = "YourRPMinBiz"; //您在控制台上创建的、采用RPMin认证方案的认证场景标识, 创建方法：https://help.aliyun.com/document_detail/59975.html
String ticketId = UUID.randomUUID().toString(); //认证ID, 由使用方指定, 发起不同的认证任务需要更换不同的认证ID
//提交认证材料
SubmitVerificationRequest submitRequest = new SubmitVerificationRequest();
submitRequest.setBiz(biz);
submitRequest.setTicketId(ticketId);
//创建要提交的认证材料列表, 请根据 认证方案 中的说明传入相应字段
List<SubmitVerificationRequest.Material> verifyMaterials = new ArrayList<SubmitVerificationRequest.Material>();
SubmitVerificationRequest.Material identificationNumber = new SubmitVerificationRequest.Material();
identificationNumber.setMaterialType("IdentificationNumber");
identificationNumber.setValue("330110201711110101");
verifyMaterials.add(identificationNumber);
SubmitVerificationRequest.Material name = new SubmitVerificationRequest.Material();
name.setMaterialType("Name");
name.setValue("张三");
verifyMaterials.add(name);
//传入图片资料，请控制单张图片大小在 2M 内，避免拉取超时
SubmitVerificationRequest.Material facePic = new SubmitVerificationRequest.Material();
facePic.setMaterialType("FacePic");
facePic.setValue("base64://iVBORw0KGgoA..."); //base64方式上传图片, 格式为"base64://图片base64字符串", 以"base64://"开头且图片base64字符串去掉头部描述(如"data:image/png;base64,"), 并注意控制接口请求的Body在8M以内
verifyMaterials.add(facePic);
SubmitVerificationRequest.Material idCardFrontPic = new SubmitVerificationRequest.Material();
idCardFrontPic.setMaterialType("IdCardFrontPic");
idCardFrontPic.setValue("http://image-demo.img-cn-hangzhou.aliyuncs.com/example.jpg"); //http方式上传图片, 此http地址须可公网访问
verifyMaterials.add(idCardFrontPic);
SubmitVerificationRequest.Material idCardBackPic = new SubmitVerificationRequest.Material();
idCardBackPic.setMaterialType("IdCardBackPic");
idCardBackPic.setValue("oss://verify-img:715559d76a40774OSS.JPG"); //oss方式上传图片, 此oss文件地址须可公开访问
verifyMaterials.add(idCardBackPic);
submitRequest.setMaterials(verifyMaterials);
try {
SubmitVerificationResponse response = client.getAcsResponse(submitRequest);
//后续业务处理
} catch (Exception e) {
e.printStackTrace();
}
//常见问题：https://help.aliyun.com/document_detail/57640.html
```

## 人脸比对验证示例 {#CompareFaces .section}

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

## 离线授权认证SDK下载示例 {#section_p3g_chm_jhb .section}

```
DefaultProfile profile = DefaultProfile.getProfile(
"cn-hangzhou", // 可用区域id，目前只支持cn-hangzhou
"your access key id", // 您的Access Key ID
"your access secret"); // 您的Access Key Secret
DefaultProfile.addEndpoint("cn-hangzhou", "Cloudauth", "cloudauth.aliyuncs.com"); //手动添加域名
client = new DefaultAcsClient(profile);

try {
CreateVerifySDKRequest createRequest = new CreateVerifySDKRequest();
createRequest.setAppUrl("https://app"); //app的可访问地址
CreateVerifySDKResponse createResponse = client.getAcsResponse(createRequest);
String taskId = createResponse.getTaskId(); //获取生成sdk任务的taskId
String sdkUrl = null;
do {
//使用taskId轮询结果，一般生成可以在1分钟内完成
Thread.sleep(TimeUnit.SECONDS.toMillis(15));
DescribeVerifySDKRequest request = new DescribeVerifySDKRequest();
request.setTaskId(taskId);
DescribeVerifySDKResponse describeVerifySDKResponse = null;
describeVerifySDKResponse = client.getAcsResponse(request);
sdkUrl = describeVerifySDKResponse.getSdkUrl();
} while (sdkUrl == null || sdkUrl.isEmpty());
//sdkUrl为生成的sdk可访问链接，下载后进行集成
} catch (ClientException e) {
//生成异常
} catch (InterruptedException e) {

}
```

## 离线授权认证SDK获取授权key示例 {#section_jcg_hhm_jhb .section}

```
DefaultProfile profile = DefaultProfile.getProfile(
"cn-hangzhou", // 可用区域id，目前只支持cn-hangzhou
"your access key id", // 您的Access Key ID
"your access secret"); // 您的Access Key Secret
DefaultProfile.addEndpoint("cn-hangzhou", "Cloudauth", "cloudauth.aliyuncs.com"); //手动添加域名
client = new DefaultAcsClient(profile);

//发起获取授权key的请求
CreateAuthKeyRequest request = new CreateAuthKeyRequest();
request.setTest(Boolean.FALSE); //测试标识
request.setAuthYears(1);//授权年限
request.setBizType("biz type"); //业务类型
request.setUserDeviceId("device id"); //可自定义的用户设备id
CreateAuthKeyResponse createAuthKeyResponse = client.getAcsResponse(request);
String authKey = createAuthKeyResponse.getAuthKey();
//获取到授权key调用离线授权认证SDK的initWithToken进行设备激活
```

