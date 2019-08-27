# Make API requests {#concept_1697879 .concept}

This topic describes how to make an API request.

## Make API requests {#section_fen_a4u_9l9 .section}

Basic API information

-   Service endpoint: `https://saf.ap-southeast-1.aliyuncs.com`.
-   Protocol: HTTPS
-   Method: POST

**Note:** To facilitate development, Alibaba Cloud also provides Java, Python, PHP, C\#, and Golang SDKs. These SDKs save you the need to concatenate and encode canonical query strings. For more information, see [Make API requests](https://www.alibabacloud.com/help/zh/doc-detail/118498.htm).

## Request parameters {#section_p00_f25_ywa .section}

The following table lists the **common request parameters**.

|Parameter|Type|Required|Description|
|---------|----|--------|-----------|
|Format|String|Yes|The value format of response parameters. Supported formats include JSON and XML. By default, values are in the XML format.|
|Version|String|Yes|The API version, with a date format of YYYY-MM-DD. The current version is 2019-05-21.|
|AccessKeyId|String|Yes|The AccessKey ID issued by Alibaba Cloud for users to access services. For more information, see [../../../../dita-oss-bucket/SP\_27/DNgameshield1843536/EN-US\_TP\_13782.md\#](../../../../intl.en-US/General Reference/Create an AccessKey.md#).|
|Signature|String|Yes|The signature string. For more information about the calculation of signatures, see [Sign API requests](https://www.alibabacloud.com/help/zh/doc-detail/66384.html).|
|SignatureMethod|String|Yes|The algorithm that is used to sign the signature. Currently, HMAC-SHA1 is supported.|
|Timestamp|String|Yes|The timestamp of the API request. The timestamp follows the ISO 8601 format and the UTC time standard. The time format is `YYYY-MM-DDThh:mm:ssZ`. For example, `2014-7-29T12:00:00Z` specifies 20:00:00, July 29, 2014 \(UTC+8\).

 |
|SignatureVersion|String|Yes|The version of the signature algorithm. The current version is 1.0.|
|SignatureNonce|String|Yes|The unique random number that is used to prevent network replay attacks. You must use different random numbers for different requests.|
|Action|String|Yes|The operation that you want to perform. Set this parameter to ExecuteRequest.|
|Service|String|Yes|The name of the service. Set this parameter to intl\_face\_verify.|
|ServiceParameters|String|Yes|The JSON string that contains service parameters. For more information, see the API reference of the specific service.|

## Response parameters {#section_a35_k3w_c8m .section}

After you send an API request, the system returns a `RequestId` UID to you, no matter whether the request is successful or not. The descriptions of other response parameters may vary depending on the service. For more information, see the API reference of the specific service.

-   **Sample success response** 

    ``` {#codeblock_fnw_kas_pwi}
    {
        "Data": "{\"retCode\":\"OK_SUCCESS\",...}",
        "Message": "OK",
        "RequestId": "0CE4BBEF-F149-490C-97CE-CBBD66E25D6C",
        "Code": 200
    }
    ```

-   **Sample error response** 
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

-   **Response codes** 

    |Response code|Response message|
    |-------------|----------------|
    |200|The response code returned because the request is successful.|
    |400|The error code returned because the ServiceParameters parameter is invalid.|
    |402|The error code returned because the number of queries that you can send every day has exceeded the specification. Network traffic is throttled.|
    |403|The error code returned because the request did not have the required permissions. The target service is not activated or has expired.|
    |404|The error code returned because the Service parameter is invalid.|
    |500|The error code returned because an internal error has occurred.|


**Note:** The response examples in this topic are formatted with line breaks or indents for a clear view. The actual response data is not formatted in the same manner as these examples.

