# 云端API调用方法 {#concept_1697879 .concept}

本文介绍了API接口的调用方式和参数。

## 调用方式 {#section_fen_a4u_9l9 .section}

接口基本信息：

-   服务请求地址：`https://saf.ap-southeast-1.aliyuncs.com`
-   协议：HTTPS
-   方式：POST

**说明：** 为了便于进行开发，阿里云还提供了Java、Python、PHP、C\#、Golang语言的SDK开发包，可以免去拼接HTTPS请求和对签名算法进行编码的操作，具体请参见 [调用方式](https://www.alibabacloud.com/help/zh/doc-detail/118498.htm)。

## 调用参数 {#section_p00_f25_ywa .section}

**公共请求参数**如下表所示。

|名称|类型|是否必需|描述|
|--|--|----|--|
|Format|String|是|返回值的类型，支持JSON与XML，默认为XML。|
|Version|String|是|API版本号，为日期形式：YYYY-MM-DD，本版本对应为2019-05-21。|
|AccessKeyId|String|是|阿里云颁发给用户的访问服务所用的密钥ID，请参见 [创建AccessKey](https://help.aliyun.com/document_detail/66453.html)。|
|Signature|String|是|签名结果串，关于签名的计算方法，请参见 [签名机制](https://help.aliyun.com/document_detail/66384.html)。|
|SignatureMethod|String|是|签名方式，目前支持HMAC-SHA1。|
|Timestamp|String|是|请求的时间戳。日期格式按照ISO8601标准表示，并需要使用UTC时间。格式为：`YYYY-MM-DDThh:mm:ssZ`。 例如，`2014-7-29T12:00:00Z`（为北京时间2014年7月29日的20点0分0秒）。

 |
|SignatureVersion|String|是|签名算法版本，目前版本是1.0。|
|SignatureNonce|String|是|唯一随机数，用于防止网络重放攻击。用户在不同请求间要使用不同的随机数值。|
|Action|String|是|取值必须是ExecuteRequest。|
|Service|String|是|具体服务名，本服务该参数固定为intl\_face\_verify。|
|ServiceParameters|String|是|具体服务参数的json格式串，具体定义见各种接入方式下的服务端接入文档。|

## 响应参数 {#section_a35_k3w_c8m .section}

用户每次发送的接口调用请求，无论成功与否，系统都会返回一个唯一识别码`RequestId`给用户。其他返回参数根据服务不同有不同的业务含义，详情见具体服务。

-   **响应成功示例** 

    ``` {#codeblock_fnw_kas_pwi}
    {
        "Data": "{\"retCode\":\"OK_SUCCESS\",...}",
        "Message": "OK",
        "RequestId": "0CE4BBEF-F149-490C-97CE-CBBD66E25D6C",
        "Code": 200
    }
    ```

-   **响应失败示例** 
    -   ``` {#codeblock_pmr_zqu_l35}
{
    "Data": "{\"retCode\":\"SYSTEM_ERROR\",...}",
    "Message": "system error",
    "RequestId": "725E1B4E-3E79-4FAB-AD52-1D09297CA54C",
    "Code": 500
}
```

    -   ``` {#codeblock_i19_pu6_8tl}
{
    "Message": "name parameter is null",
    "RequestId": "C5E39498-2148-4DB4-A54F-77CC3BF0ABBF",
    "Code": 400
}
```

-   **返回Code说明** 

    |Code|Message|
    |----|-------|
    |200|请求正常|
    |400|参数ServiceParameters不合法|
    |402|日QPS超过已购规格，限流|
    |403|权限不足，服务未开通或已到期|
    |404|参数Service不合法|
    |500|内部服务器错误|


**说明：** 文档中的返回示例为了便于阅读，做了格式化处理，实际返回结果是没有进行换行、缩进等处理。

