# 下载离线人脸识别SDK {#reference_185668 .reference}

## 异步提交SDK下载任务 {#section_41q_0tt_qhu .section}

**描述**

接口名：CreateVerifySDK

提交无线应用，异步生成离线人脸识别SDK，一般可在1分钟内生成完成。

请求方法：支持以HTTPS POST和GET方法发送请求。

**请求参数** 

|名称|类型|参数位置|是否必需|描述|
|--|--|----|----|--|
|Url|String|Query|是|接入方无线应用程序的可访问链接。实人认证服务端会从该地址下载无线应用，进行SDK生成，接入方务必保证该地址可访问，否则SDK会生成失败。|

**返回参数** 

|名称|类型|描述|
|--|--|--|
|TaskId|String|生成SDK的任务ID，用于查询SDK生成结果，一般生成可以在1分钟内完成。|

## 获取SDK生成结果 {#section_iii_dth_ry7 .section}

**描述**

接口名：DescribeVerifySDK

根据生成离线人脸识别SDK的任务ID获取SDK生成结果。

请求方法：支持以HTTPS POST和GET方法发送请求。

**请求参数** 

|名称|类型|参数位置|是否必需|描述|
|--|--|----|----|--|
|TaskId|String|Query|是|生成SDK的任务ID。|

**返回参数** 

|名称|类型|描述|
|--|--|--|
|Url|String|SDK下载地址。不为空时，表示生成完成。|

## 示例 {#section_237_kc3_fyu .section}

使用SDK开发包的示例，请参见Java、PHP、Python、.NET、Node.js、Go中的接入说明及离线人脸识别SDK下载示例部分的代码。（推荐以该方式接入）

以下是拼接HTTPS请求的相关示例：

异步提交SDK下载任务

-   请求示例

    ``` {#codeblock_7ql_df0_css}
    https://cloudauth.aliyuncs.com/?Action= CreateVerifySDK
    &Url=https://www.xxx.com
    &<[公共请求参数]>                    
    ```

    其中公共请求参数参见API调用方式。

-   返回示例
    -   XML格式

        ``` {#codeblock_8q2_gg3_ad4}
        <?xml version='1.0' encoding='UTF-8'?>
        <CreateVerifySDKResponse>
            <Data>
                <TaskId>1KQMcnLd4m37LN2D0F0WCD</TaskId>
            </Data>
            <Success>true</Success>
            <Code>1</Code>
        </CreateVerifySDKResponse>
        								
        ```

    -   JSON格式

        ``` {#codeblock_cy6_z87_i3x}
        {
          "Data": {
            "TaskId":1KQMcnLd4m37LN2D0F0WCD
          },
          "Success": true
        }
        								
        ```


获取SDK生成结果

-   请求示例

    ``` {#codeblock_e2h_saa_atf}
    https://cloudauth.aliyuncs.com/?Action=DescribeVerifySDK
    &TaskId=1KQMcnLd4m37LN2D0F0WCD
    &<[公共请求参数]>
    						
    ```

-   返回示例
    -   XML格式

        ``` {#codeblock_s7m_v5l_x0p}
        <?xml version='1.0' encoding='UTF-8'?>
        <DescribeVerifySDKResponse>
            <Data>
                <Url>https://www.xxx.com</Url>
            </Data>
            <Success>true</Success>
            <Code>1</Code>
        </DescribeVerifySDKResponse>
        								
        ```

    -   JSON格式

        ``` {#codeblock_obc_xq3_e79}
        {
          "Data": {
            "Url":https://www.xxx.com
          },
          "Success": true
        }
        								
        ```


