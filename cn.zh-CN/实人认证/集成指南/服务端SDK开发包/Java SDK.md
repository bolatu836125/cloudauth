# Java SDK {#reference_jy2_fss_gfb .reference}

本文介绍了如何使用阿里云实人认证服务的Java SDK，具体包括SDK的获取和安装方法以及SDK代码示例。

[单击查看老版本接入文档说明。](../../../../cn.zh-CN/老系统文档说明/老版本接入文档说明.md#)

## 获取地址 {#section_aq3_hss_gfb .section}

您需要引入两个SDK，包括aliyun-java-sdk-core（阿里云核心SDK）和aliyun-java-sdk-cloudauth（实人认证SDK）。每个SDK都提供了Maven Repository、Central Repository、GitHub的获取方式，您可以选择合适的方式获取SDK。

-   aliyun-java-sdk-core：从以下方式中单击选择一种方式。

    -   [mvnrepository](https://mvnrepository.com/artifact/com.aliyun/aliyun-java-sdk-core)
    -   [maven.org](https://search.maven.org/search?q=a:aliyun-java-sdk-core)
    -   [GitHub](https://github.com/aliyun/aliyun-openapi-java-sdk/tree/master/aliyun-java-sdk-core)
    **说明：** 若您使用的是aliyun-java-sdk-core 4.0.0~4.0.2 版本，那么在调用HTTPS接口时需要在`profile`中额外添加以下内容：`profile.getHttpClientConfig().setIgnoreSSLCerts(true);`。

-   aliyun-java-sdk-cloudauth：从以下方式中单击选择一种方式。
    -   [mvnrepository](https://mvnrepository.com/artifact/com.aliyun/aliyun-java-sdk-cloudauth)
    -   [maven.org](https://search.maven.org/search?q=a:aliyun-java-sdk-cloudauth)
    -   [GitHub](https://github.com/aliyun/aliyun-openapi-java-sdk/tree/master/aliyun-java-sdk-cloudauth)

## 安装说明 {#section_nww_lss_gfb .section}

方法1：使用Maven（推荐）

如果您使用Maven管理Java项目，可以通过在pom.xml文件中添加Maven依赖：

``` {#codeblock_636_x3r_pk7}
<dependency>
    <groupId>com.aliyun</groupId>
    <artifactId>aliyun-java-sdk-core</artifactId>
    <version>4.4.3</version>
</dependency>
<dependency>
    <groupId>com.aliyun</groupId>
    <artifactId>aliyun-java-sdk-cloudauth</artifactId>
    <version>2.0.0</version>
</dependency>
```

**说明：** `version`的值以SDK获取地址中的最新版本为准。

方法2：在集成开发环境（IDE）中导入jar文件

-   Eclipse安装
    1.  将下载的aliyun-java-sdk-xxx.jar文件复制到您的项目文件夹中。
    2.  在Eclipse中打开您的项目，右键单击该项目，单击**Properties**。
    3.  在弹出的对话框中，单击**Java Build Path** \> **Libraries** \> **Add JARs**添加下载的JAR文件。
    4.  单击**Apply and Close**。
-   IntelliJ 安装
    1.  将下载的aliyun-java-sdk-xxx.jar文件复制到您的项目文件夹中。
    2.  在IntelliJ中打开您的项目，在菜单栏中单击**File** \> **Project** \> **Structure**。
    3.  单击**Apply**，然后单击**OK**。

## RPBasic、RPManual、FDBioOnly认证方案示例代码 {#section_nc6_862_spm .section}

``` {#codeblock_a5e_vo9_4m3}
DefaultProfile profile = DefaultProfile.getProfile(
                    "cn-hangzhou",          // 您的可用区ID
                    "AccessKey",      // 您的Access Key ID
                    "AccessSecret"); // 您的Access Key Secret
DefaultProfile.addEndpoint("cn-hangzhou", "cn-hangzhou", "Cloudauth", "cloudauth.aliyuncs.com");
IAcsClient client = new DefaultAcsClient(profile);

DescribeVerifyTokenRequest request = new DescribeVerifyTokenRequest();
request.setBizId("认证ID, 由接入方指定, 发起不同的认证任务需要更换不同的认证ID");
request.setBizType("实人认证控制台上创建场景时对应的场景标识"); //创建方法请参见https://help.aliyun.com/document_detail/127885.htm

DescribeVerifyTokenResponse response = client.getAcsResponse(request);
String verifyToken = response.getVerifyToken();

//2. 接入方服务端将token传递给接入方无线客户端
//3. 接入方无线客户端用token调起无线认证SDK
//4. 用户按照由无线认证SDK组织的认证流程页面的指引，提交认证资料
//5. 认证流程结束退出无线认证SDK，进入客户端回调函数
//6. 接入方服务端获取认证状态和认证资料(注：客户端无线认证SDK回调中也会携带认证状态, 但建议以服务端调接口获取的为准进行业务上的判断和处理)
DescribeVerifyResultRequest verifyResultRequest = new DescribeVerifyResultRequest();
verifyResultRequest.setBizId("调用GetVerifyToken时传入的bizId");
verifyResultRequest.setBizType("调用GetVerifyToken时传入的bizType");
DescribeVerifyResultResponse verifyResultResponse = client.getAcsResponse(verifyResultRequest);
```

## RPBioID、RPBioOnlyPro、RPBioOnly、RPH5BioOnly认证方案示例代码 {#section_hur_jng_51s .section}

``` {#codeblock_lcl_w4m_ovk}
DefaultProfile profile = DefaultProfile.getProfile(
                    "cn-hangzhou",          // 您的可用区ID
                    "AccessKey",      // 您的Access Key ID
                    "AccessSecret"); // 您的Access Key Secret
DefaultProfile.addEndpoint("cn-hangzhou", "cn-hangzhou", "Cloudauth", "cloudauth.aliyuncs.com");
IAcsClient client = new DefaultAcsClient(profile);

DescribeVerifyTokenRequest request = new DescribeVerifyTokenRequest();
request.setBizId("认证ID, 由接入方指定, 发起不同的认证任务需要更换不同的认证ID ");
request.setBizType("实人认证控制台上创建场景时对应的场景标识"); //创建方法请参见https://help.aliyun.com/document_detail/127885.htm
request.setName("用户正确的姓名");
request.setIdCardNumber("用户正确的身份证号");

DescribeVerifyTokenResponse response = client.getAcsResponse(request);
String verifyToken = response.getVerifyToken();
// verifyPageUrl仅在RPH5BioOnly认证方案下返回
String verifyPageUrl = response.getVerifyPageUrl();

//2. 接入方服务端将token传递给接入方无线客户端
//3. 接入方无线客户端用token调起无线认证SDK
//4. 用户按照由无线认证SDK组织的认证流程页面的指引，提交认证资料
//5. 认证流程结束退出无线认证SDK，进入客户端回调函数
//6. 接入方服务端获取认证状态和认证资料(注：客户端无线认证SDK回调中也会携带认证状态, 但建议以服务端调接口获取的为准进行业务上的判断和处理)
DescribeVerifyResultRequest verifyResultRequest = new DescribeVerifyResultRequest();
verifyResultRequest.setBizId("调用GetVerifyToken时传入的bizId ");
verifyResultRequest.setBizType("调用GetVerifyToken时传入的bizType ");
DescribeVerifyResultResponse verifyResultResponse = client.getAcsResponse(verifyResultRequest);
```

## FVBioOnly认证方案示例代码 {#section_lt1_rmy_uia .section}

``` {#codeblock_a4t_chv_3bf}
DefaultProfile profile = DefaultProfile.getProfile(
                    "cn-hangzhou",          // 您的可用区ID
                    "AccessKey",      // 您的Access Key ID
                    "AccessSecret"); // 您的Access Key Secret
DefaultProfile.addEndpoint("cn-hangzhou", "cn-hangzhou", "Cloudauth", "cloudauth.aliyuncs.com");
IAcsClient client = new DefaultAcsClient(profile);

DescribeVerifyTokenRequest request = new DescribeVerifyTokenRequest();
request.setBizId("认证ID, 由接入方指定, 发起不同的认证任务需要更换不同的认证ID ");
request.setBizType("实人认证控制台上创建场景时对应的场景标识"); //创建方法请参见https://help.aliyun.com/document_detail/127885.htm

request.setFaceRetainedImageUrl("公网可访问的图片http/https链接");

DescribeVerifyTokenResponse response = client.getAcsResponse(request);
String verifyToken = response.getVerifyToken();

//2. 接入方服务端将token传递给接入方无线客户端
//3. 接入方无线客户端用token调起无线认证SDK
//4. 用户按照由无线认证SDK组织的认证流程页面的指引，提交认证资料
//5. 认证流程结束退出无线认证SDK，进入客户端回调函数
//6. 接入方服务端获取认证状态和认证资料(注：客户端无线认证SDK回调中也会携带认证状态, 但建议以服务端调接口获取的为准进行业务上的判断和处理)
DescribeVerifyResultRequest verifyResultRequest = new DescribeVerifyResultRequest();
verifyResultRequest.setBizId("调用GetVerifyToken时传入的bizId ");
verifyResultRequest.setBizType("调用GetVerifyToken时的bizType");
DescribeVerifyResultResponse verifyResultResponse = client.getAcsResponse(verifyResultRequest);
```

## RPMin认证方案示例代码 {#section_8en_4eg_8qv .section}

RPMin认证方案的人脸照片入参，支持公网可访问的http/https链接，也支持接入方使用实人认证提供的SDK将base64的图片上传到实人认证OSS Bucket后生成https链接。

``` {#codeblock_8ab_axn_829}
DefaultProfile profile = DefaultProfile.getProfile(
                    "cn-hangzhou",          // 您的可用区ID
                    "AccessKey",      // 您的Access Key ID
                    "AccessSecret"); // 您的Access Key Secret
DefaultProfile.addEndpoint("cn-hangzhou", "cn-hangzhou", "Cloudauth", "cloudauth.aliyuncs.com");
IAcsClient client = new DefaultAcsClient(profile);

//若接入方的人脸图片是本地资源，则需要将base64图片直传到实人认证OSS并获取到图片地址
CloudAuthClientUploader uploader = CloudAuthClientUploader.getClentUploader(client); // 获取上传oss的实例
String faceImageUrl = uploader.uploadBase64("待上传的base64图片资源"); // 上传oss并获取图片链接

VerifyMaterialRequest verifyMaterialRequest = new VerifyMaterialRequest();
verifyMaterialRequest.setBizId("认证ID, 由接入方指定, 发起不同的认证任务需要更换不同的认证ID ");
verifyMaterialRequest.setBizType("实人认证控制台上创建场景时对应的场景标识"); //创建方法请参见https://help.aliyun.com/document_detail/127885.htm

verifyMaterialRequest.setFaceImageUrl(faceImageUrl); // faceImageUrl可以是通过直传OSS获取到的链接，也可以是接入方公网可访问的人脸图片链接，支持http/https
verifyMaterialRequest.setName("用户正确的姓名");
verifyMaterialRequest.setIdCardNumber("用户正确的身份证号");

VerifyMaterialResponse verifyMaterialResponse = client.getAcsResponse(verifyMaterialRequest);
int statusCode = verifyMaterialResponse.getVerifyStatus(); //同步返回认证状态和相应材料
```

``` {#codeblock_jdj_870_k1x}

```

## 人脸比对验证示例 {#CompareFaces .section}

``` {#codeblock_xzz_gma_g0e}
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

## 离线人脸识别SDK下载示例 {#section_p3g_chm_jhb .section}

``` {#codeblock_bbq_c2q_pbb}
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

## 离线人脸识别SDK获取授权key示例 {#section_jcg_hhm_jhb .section}

``` {#codeblock_zvn_chp_4rm}
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
//获取到授权key调用离线人脸识别SDK的initWithToken进行设备激活
```

