# 申请授权key {#reference_185670 .reference}

## 描述 {#section_6ou_unr_804 .section}

接口名：CreateAuthKey

获取授权key，用于离线人脸识别SDK激活。

请求方法：支持以HTTPS POST和GET方法发送请求。

**说明：** 授权key在30分钟内有效，且不可重复使用，建议在每次激活前重新获取。

## 请求参数 {#section_dpa_jk3_20d .section}

|名称|类型|参数位置|是否必需|描述|
|--|--|----|----|--|
|BizType|String|Query|否|业务类型。不超过64字符。可用于对具体业务进行备注，例如可以取接入方不同的人脸使用场景，或者待交付的客户标识等。建议传入该参数。|
|UserDeviceId|String|Query|否|用户设备ID。不超过64字符。可用于标识具体设备，建议可以使用设备物理编号。建议传入该参数。|
|Test|Boolean|Query|否|测试标识。为true表示使用测试授权，授权时长默认为30天；为false，则授权时长根据AuthYears进行授权。|
|AuthYears|Integer|Query|否|当Test标识为false或空时，AuthYears必填，单位为年，范围为\[1,100\]，取值100表示永久授权。|

## 返回参数 {#section_1o2_m1h_69q .section}

|名称|类型|描述|
|--|--|--|
|AuthKey|String|可用于授权激活的key。授权key在30分钟内有效，且不可重复使用，建议在每次激活前重新获取。|

## 示例 {#section_kck_0p2_i1d .section}

使用SDK开发包的示例，请参见Java、PHP、Python、.NET、Node.js、Go中的接入说明及离线人脸识别SDK获取授权key示例部分的代码。（推荐以该方式接入）

以下是拼接HTTPS请求的相关示例：

-   请求示例

    ``` {#codeblock_x6v_z7h_ec3}
    https://cloudauth.aliyuncs.com/?Action=CreateAuthKey
    &BizType=FACE_TEST
    &UserDeviceId=3iJ1AY$oHcu7mC69
    &Test=false
    &AuthYears=1
    &<[公共请求参数]>                
    ```

    其中公共请求参数参见API调用方式。

-   返回示例
    -   XML格式

        ``` {#codeblock_1am_f17_6dj}
        <?xml version='1.0' encoding='UTF-8'?>
        <CreateAuthKeyResponse>
            <Data>
                <AuthKey>auth.1KQMcnLd4m37LN2D0F0WCD-1qtQI$</AuthKey>
            </Data>
            <Success>true</Success>
            <Code>1</Code>
        </CreateAuthKeyResponse>
        								
        ```

    -   JSON格式

        ``` {#codeblock_hcj_fjf_l00}
        {
          "Data": {
            "AuthKey":auth.1KQMcnLd4m37LN2D0F0WCD-1qtQI$
          },
          "Success": true
        }
        								
        ```


