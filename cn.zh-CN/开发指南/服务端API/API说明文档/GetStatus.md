# GetStatus {#reference2834 .reference}

查询指定业务场景下一个认证ID的认证状态。

## 请求参数 { .section}

|名称|类型|是否必需|描述|
|--|--|----|--|
|Action|String|是|要执行的操作。取值：GetStatus。|
|RegionId|String|是|服务所在地域。取值：cn-hangzhou。|
|Biz|String|是|要查询的业务场景。请先参考[业务设置](https://help.aliyun.com/document_detail/59975.html)在控制台上完成创建。|
|TicketId|String|是|要查询的认证任务ID。通常由业务使用方指定，方便关联业务场景的其他内容。**说明：** 需要与当前认证任务在[GetVerifyToken](https://help.aliyun.com/document_detail/57050.html)时的认证ID保持一致。

|

## 返回参数 { .section}

|名称|类型|是否必需|描述|
|--|--|----|--|
|StatusCode|Integer|是|认证任务所处的认证状态，取值：-   -1： 未认证。表示没有提交记录。
-   0： 认证中。表示已提交认证，系统正在审核认证资料。
-   1： 认证通过。表示最近一次提交的认证资料已通过审核，当前认证任务完结。
-   2： 认证不通过。表示最近一次提交的认证资料未通过审核，当前认证任务还可以继续发起提交。

|
|SimilarityScore|Float|是|认证过程中所提交的人脸照片和身份证上的头像的相似程度分值。取值范围为\[0,100\]，分数越大相似度越高。**说明：** 只有提交的认证资料中同时包含人脸照片和身份证人像面照片，该值才有意义。

|
|AuditConclusions|String|是|认证状态为“认证不通过”时的原因描述。不通过原因包括但不限于以下几种情况：-   身份证照片模糊，光线问题造成字体无法识别。
-   身份证照片信息与认证提交信息不一致。
-   提交的照片非身份证照片。建议您请按引导提交本人有效身份证照片。

|

## 错误码 { .section}

|错误代码|英文描述|中文描述|HTTP 状态码|
|----|----|----|--------|
|Error.InternalError|The request processing has failed due to some unknown error.|服务内部出现未知错误，请联系技术支持排查。|500|
|InvalidParam.ParamMissing|Please check if there is param empty in your imput.|参数错误，请检查参数是否为空。|400|
|Error.NoPermissionAccess|No permission to access our service.|没有权限访问服务，请确认是否服务已开通。|403|
|InvalidParam.InvalidBiz|The biz is not valid.|参数Biz不合法，请与控制台上创建的保持一致。|400|

## 示例 { .section}

关于**使用SDK开发包**的示例，请参考以下具体SDK 开发包使用文档中的认证方案说明：[Java](https://help.aliyun.com/document_detail/64074.html)、[PHP](https://help.aliyun.com/document_detail/64081.html)、[Python](https://help.aliyun.com/document_detail/64085.html)、[.NET](https://help.aliyun.com/document_detail/64086.html)、[Node.js](https://help.aliyun.com/document_detail/64088.html)、[Go](https://help.aliyun.com/document_detail/64087.html)。

以下是**拼接HTTPS请求**的相关示例：

**请求示例**

```
https://cloudauth.aliyuncs.com/?Action=GetStatus
&RegionId=cn-hangzhou
&Biz=RPBasic
&TicketId=39ecf51e-2f81-4dc5-90ee-ff86125be683
&<[公共请求参数]>

```

**返回示例**

-   XML格式

    ```language-xml
    <?xml version='1.0' encoding='UTF-8'?>
    <GetStatusResponse>
        <Data>
            <StatusCode>1</StatusCode>
    	<SimilarityScore>93.12</SimilarityScore>
    	<AuditConclusions>不通过原因</AuditConclusions>
        </Data>
        <Success>true</Success>
        <Code>1</Code>
    </GetStatusResponse>
    
    ```

-   JSON格式

    ```language-json
    {
      "Data": {
        "SimilarityScore": 93.12,
    	"StatusCode": 1,
    	"AuditConclusions": "不通过原因"
      },
      "Success": true
    }
    
    ```


