# API调用方式 {#reference3225 .reference}

对实人认证服务端API接口调用是通过向API的服务端地址发送HTTPS GET/POST请求，并按照接口说明在请求中加入相应请求参数来完成的；根据请求的处理情况，系统会返回处理结果。

## 请求结构 { .section}

 **服务地址**： 实人认证服务端API的服务接入地址为：cloudauth.aliyuncs.com。

 **通信协议**： 为了保证安全性，实人认证只支持通过HTTPS通道进行请求通信。

 **请求方法**： 支持HTTPS GET/POST方法发送请求，这种方式下请求参数需要包含在请求的URL中。

 **请求参数**： 每个请求的参数都由两部分组成：

-   **公共请求参数**：用于指定API版本号、返回值类型、签名等等，具体字段请参看下文。
-   **Action 及其特有的请求参数**：Action参数对应具体要调用的接口名称（例如GetVerifyToken），其特有的请求参数即为该接口所要求的参数，这一部分的具体字段请参看各个API的说明文档。

 **字符编码**： 请求及返回结果都使用 UTF-8 字符集进行编码。

**说明：** 为了便于进行开发，阿里云还提供[Java](https://help.aliyun.com/document_detail/64074.html)、[PHP](https://help.aliyun.com/document_detail/64081.html)、[Python](https://help.aliyun.com/document_detail/64085.html)、[.NET](https://help.aliyun.com/document_detail/64086.html)、[Node.js](https://help.aliyun.com/document_detail/64088.html)、[Go](https://help.aliyun.com/document_detail/64087.html)语言的SDK开发包，可以免去拼接 HTTPS 请求和对签名算法进行编码的麻烦。

## 公共参数 { .section}

**公共请求参数**

公共请求参数是指每个接口都需要使用到的请求参数。

|名称|类型|是否必需|描述|
|--|--|----|--|
|Format|String|否|返回值的类型，支持 JSON与 XML，默认为 XML。|
|Version|String|是|API 版本号，为日期形式：YYYY-MM-DD，具体请参考[API发布说明](https://help.aliyun.com/document_detail/65922.html)。|
|AccessKeyId|String|是|阿里云颁发给用户的访问服务所用的密钥 ID。|
|Signature|String|是|用 AccessKeySecret 签名的结果串，关于签名的计算方法请参考RPC风格API的[签名机制](../../../../../cn.zh-CN/阿里云API介绍/签名机制/RPC API签名.md#)中的。|
|SignatureMethod|String|是|签名方式，目前支持 HMAC-SHA1。|
|Timestamp|String|是|请求的时间戳。日期格式按照 ISO8601 标准表示，并需要使用 UTC 时间 0 时区的值。格式为 YYYY-MM-DDThh:mm:ssZ。例如，2017-11-11T12:00:00Z（为北京时间 2017年11月11日20点0分0秒）。|
|SignatureVersion|String|是|签名算法版本，目前版本是1.0。|
|SignatureNonce|String|是|唯一随机数，用于防止网络重放攻击。用户在不同请求间要使用不同的随机数值。|

**请求示例**

```
https://cloudauth.aliyuncs.com/?Format=xml
&Version=2017-11-17
&Signature=Pc5WB8gokVn0xfeu%2FZV%2BiNM1dgI%3D
&SignatureMethod=HMAC-SHA1
&SignatureNonce=15215528852396
&SignatureVersion=1.0
&AccessKeyId=key-test
&Timestamp=2017-11-11T12:00:00Z
&<[Action及其特有的请求参数]>

```

**公共返回参数**

用户发送的每次接口调用请求，无论成功与否，系统都会返回一个唯一识别码RequestId给用户。

-   XML返回示例

    ```
    <?xml version="1.0" encoding="UTF-8"?> 
    <!—结果的根结点-->
    <接口名称+Response>
        <!—返回请求标签-->
        <RequestId>4C467B38-3910-447D-87BC-AC049166F216</RequestId>
        <!—返回结果数据-->
    </接口名称+Response>
    
    ```

-   JSON返回示例

    ```
    {
        "RequestId": "4C467B38-3910-447D-87BC-AC049166F216",
        /* 返回结果数据 */
    }
    
    ```


**返回结果**

调用 API 服务后返回数据采用统一格式，返回的 HTTP 状态码为 2xx，代表调用成功。返回 4xx 或 5xx 的 HTTP 状态码代表调用失败。

调用成功返回的数据格式主要有 XML 和 JSON 两种，外部系统可以在请求时传入参数来制定返回的数据格式，默认为 XML 格式。

本文档中的返回示例为了便于用户查看，做了格式化处理，实际返回结果是没有进行换行、缩进等处理的。

-   **成功结果**
    -   XML返回示例（XML返回结果包括请求是否成功信息和具体的业务数据）

        ```
        <?xml version="1.0" encoding="UTF-8"?> 
        <!—结果的根结点-->
        <接口名称+Response>
            <!—返回请求标签-->
            <RequestId>4C467B38-3910-447D-87BC-AC049166F216</RequestId>
            <!—返回结果数据-->
        </接口名称+Response>
        
        ```

    -   JSON返回示例

        ```
        {
            "RequestId": "4C467B38-3910-447D-87BC-AC049166F216",
            /* 返回结果数据 */
        }
        
        ```

-   **错误结果**

    调用接口出错后，将不会返回结果数据。调用方可根据接口文档中的错误码说明来定位错误原因。

    当调用出错时，HTTP 请求返回一个 4xx 或 5xx 的 HTTP 状态码。返回的消息体中是具体的错误代码及错误信息。另外还包含一个全局唯一的请求 ID RequestId和一个您该次请求访问的站点 ID HostId。在调用方找不到错误原因时，可以联系阿里云客服，并提供该HostId和RequestId，以便我们尽快帮您解决问题。

    -   XML返回示例

        ```
        <?xml version="1.0" encoding="UTF-8"?>
        <Error>
        <RequestId>8906582E-6722-409A-A6C4-0E7863B733A5</RequestId>
           <HostId>cloudauth.aliyuncs.com</HostId>
           <Code>UnsupportedOperation</Code>
           <Message>The specified action is not supported.</Message>
        </Error>
        
        ```

    -   JSON返回示例

        ```
        {
            "RequestId": "8906582E-6722-409A-A6C4-0E7863B733A5",
            "HostId": "cloudauth.aliyuncs.com",
            "Code": "UnsupportedOperation",
            "Message": "The specified action is not supported."
        }
        
        ```


