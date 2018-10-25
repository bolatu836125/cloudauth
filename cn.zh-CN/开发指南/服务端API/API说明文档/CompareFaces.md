# CompareFaces {#reference4131 .reference}

人脸比对验证。

## 描述 { .section}

指定两张人脸图片进行比对，输出两张图片中人脸的相似度分值作为结果。

-   指定的比对图片中至少应含有一个图片类型是人脸照（FacePic）。
-   一张图片中含有多个人脸时，算法会自动选取图片中占幅最大的人脸。
-   两张比对的图片中，如果其中一张检测不到人脸，系统会返回“未检测到人脸”的错误提示。

**关于上传图片地址的说明**

在传入图片时，您需要上传图片对应的HTTP/OSS地址或base64编码。

-   HTTP地址：可访问的公网HTTP地址。例如，http://image-demo.img-cn-hangzhou.aliyuncs.com/example.jpg。
-   OSS地址：可公开访问的OSS文件地址，格式为oss://<bucket\>:<path\_to\_file\>。
-   base64编码：通过base64编码的图片，格式为base64://<图片 base64 字符串\>。

**说明：** 

-   不支持本地图片的相对路径或绝对路径。
-   单张图片大小请控制在2M内，避免算法拉取超时。
-   单个请求的Body有8M的大小限制，请计算好请求中所有图片和其他信息的大小，不要超限。
-   使用base64传递图片时，接口的请求方法需要改成 POST；图片base64字符串需要去掉头部描述，如`data:image/png;base64,`。

## 请求参数 { .section}

|名称|类型|是否必需|描述|
|--|--|----|--|
|Action|String|是|要执行的操作。取值：CompareFaces。|
|RegionId|String|是|服务所在地域。取值：cn-hangzhou。|
|SourceImageType|String|是|图片1的类型，取值：-   FacePic： 用户人脸照
-   IDPic： 用户二代身份证芯片中的头像照（通常由二代身份证阅读仪设备读取并解码获得）

|
|SourceImageValue|String|是|图片1的地址。具体请参考[关于上传图片地址的说明](#)。|
|TargetImageType|String|是|图片2的类型，取值：-   FacePic： 用户人脸照
-   IDPic： 用户二代身份证芯片中的头像照（通常由二代身份证阅读仪设备读取并解码获得）

|
|TargetImageValue|String|是|图片2的地址。具体请参考[关于上传图片地址的说明](#)。|

## 返回参数 { .section}

|名称|类型|是否必需|描述|
|--|--|----|--|
|SimilarityScore|Float|是|两个图片中人脸的相似程度。取值范围为\[0, 100\]，值越大相似度越高。|
|ConfidenceThresholds|String|是|人脸比对置信度阈值。返回内容是JSON Object，具体结构为`"key":"value"`。其中，-   `key`是误识率，即将其他人误识别为指定人员的概率。
-   `value`是对应的阈值。

|

## 错误码 { .section}

|错误代码|英文描述|中文描述|HTTP状态码|
|----|----|----|-------|
|Error.InternalError|The request processing has failed due to some unknown error.|服务内部出现未知错误，请联系技术支持排查。|500|
|InvalidParam.ParamMissing|Please check if there is param empty in your imput.|参数错误，请检查参数是否为空。|403|
|Error.NoPermissionAccess|No permission to access our service.|没有权限访问服务，请确认是否服务已开通。|403|
|Error.CompareFacesError|CompareFaces action failed due to internal error.|人脸比对过程中出现内部未知错误，请联系技术支持排查。|500|
|InvalidParam.ImageTypeEmpty|The image type is empty.|SourceImageType 或者 TargetImageType 字段为空。|400|
|InvalidParam.FacePicNotGiven|At least one of the two given images has a type of FacePic.|进行比对的两个图片中，至少其中一个图片的类型应该设置为人脸照`FacePic`。|400|
|InvalidParam.ImageValuePatternError|The pattern of value of given image is not acceptable.|图片地址格式错误，请检查 ImageValue 是否符合图片地址格式的要求。|400|
|InvalidParam.ImageTypeInvalid|The image type is not valid.|SourceImageType / TargetImageType 取值不为 `FacePic` 或者 `IDPic`。|400|
|Error.NoFaceDetected|No face detected from given images.|指定图片中没有检测到人脸。|400|

## 示例 { .section}

关于**使用SDK开发包**的示例，请参考以下具体SDK 开发包使用文档中的认证方案说明：[Java](https://help.aliyun.com/document_detail/64074.html)、[PHP](https://help.aliyun.com/document_detail/64081.html)、[Python](https://help.aliyun.com/document_detail/64085.html)、[.NET](https://help.aliyun.com/document_detail/64086.html)、[Node.js](https://help.aliyun.com/document_detail/64088.html)、[Go](https://help.aliyun.com/document_detail/64087.html)。

以下是**拼接HTTPS请求**的相关示例：

**请求示例**

```
https://cloudauth.aliyuncs.com/?Action=CompareFaces
&RegionId=cn-hangzhou
&SourceImageType=FacePic
&SourceImageValue=http%3A%2F%2Fjiangsu.china.com.cn%2Fuploadfile%2F2015%2F0114%2F1421221304095989.jpg
&TargetImageType=IDPic
&TargetImageValue=http%3A%2F%2Fjiangsu.china.com.cn%2Fuploadfile%2F2015%2F0114%2F1421221304095989.jpg
&<公共请求参数>

```

**返回示例**

-   XML格式

    ```language-xml
    <?xml version='1.0' encoding='UTF-8'?>
    <CompareFacesResponse>
        <Data>
            <ConfidenceThresholds>{"0.0001":"90.07","0.001":"80.02","0.01":"70.01"}</ConfidenceThresholds>
            <SimilarityScore>0.9825757</SimilarityScore>
        </Data>
        <Success>true</Success>
        <Code>1</Code>
    </CompareFacesResponse>
    
    ```

-   JSON格式

    ```language-json
    {
      "Code": "1",
      "Data": {
        "ConfidenceThresholds": "{\"0.0001\":\"90.07\",\"0.001\":\"80.01\",\"0.01\":\"70.02\"}",
        "SimilarityScore": 98.7913
      },
      "Success": true
    }
    
    ```


**说明：** 关于示例中的人脸比对置信度阈值（confidenceThresholds）：

-   `"0.0001": "90.07"`表示误识率为0.01%时的对应阈值为90.07。
-   `"0.001": "80.01"`表示误识率为0.1%时的对应阈值为80.01。
-   `"0.01": "70.02"`表示误识率为1%时的对应阈值为70.02。

置信度阈值会根据不同图像和算法动态给出，请不要将置信度阈值持久化。

