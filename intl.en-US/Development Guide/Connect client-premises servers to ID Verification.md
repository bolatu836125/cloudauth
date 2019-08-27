# Connect client-premises servers to ID Verification {#task_1698077 .task}

This topic describes how to connect servers on your premises to ID Verification.

## Request parameters {#section_36l_393_86i .section}

You can connect your servers to ID Verification and make API requests to call ID Verification services. For more information, see [Make API requests](intl.en-US/Development Guide/Make API requests.md#). The following table lists the fields that you must pass to the JSON string of the ServiceParameters parameter.

|Parameter|Type|Required|Description|Example|
|---------|----|--------|-----------|-------|
|refFaceContent|String|Yes|The base64 encoded ID photo. Do not include the prefix `data:image/jpeg;base64`. The photo must be in the JPG format. Do not include newlines \(\\r\) or carriage returns \(\\n\). **Note:** We recommend that you use the built-in camera of your device to take photos.

 |/9j/4AAQSkZJRgABAQAAFAAUAAD...|
|authFaceContent|String|Yes|The face photo encoded by using Base64. Do not include the prefix `data:image/jpeg;base64`. The photo must be in the JPG format. Do not include newlines \(\\r\) or carriage returns \(\\n\). **Note:** We recommend that you use the built-in camera of your device to take photos.

 |/9j/4AAQSkZJRgABAQAAFAAUAAD...|
|resourceCode|String|Yes|The resource code of the country that the person being verified belongs to.|For example, 00840000 for Vietnam.|

## Response parameters {#section_hh3_m40_qmc .section}

|Parameter|Type|Required|Description|Example|
|---------|----|--------|-----------|-------|
|Data|String|Yes|The facial recognition result.|`{\"passed\":\"true\",...`|
|Message|String|Yes|The status of the request.|OK|
|RequestId|String|Yes|The UID of the request.|0CE4BBEF-F149-490C-97CE-CBBD66E25D6C|
|Code|Integer|Yes|The response code. For more information, see [Response codes](intl.en-US/Development Guide/Make API requests.md#table_vjy_28s_7em).|200|

-   **The code returned because the request is successfully sent to the API.** 

    ``` {#codeblock_pgq_z4g_rt7}
    {
        "Data": "{\"passed\":\"true\",\"retCode\":\"OK_SUCCESS\",\"retCodeSub\":\"Z1140\",\"retMessage\":\"Facial recognition passed (Z1140)\",\"extInfo\":{\"result\":\"true\",\"score\":\"100.0\",\"faceAttack\":\"false\"}}",
        "Message": "OK",
        "RequestId": "0CE4BBEF-F149-490C-97CE-CBBD66E25D6C",
        "Code": 200
    }
    ```

-   **Sample error response 1** 

    ``` {#codeblock_td8_r0z_tki}
    {
        "Data": "{\"passed\":\"false\",\"retCode\":\"SYSTEM_ERROR\",\"retCodeSub\":\"Z1141\",\"retMessage\":\"resourceCode value invalid\"}",
        "Message": "system error",
        "RequestId": "725E1B4E-3E79-4FAB-AD52-1D09297CA54C",
        "Code": 500
    }
    ```

    Sample error response 2

    ``` {#codeblock_f5l_dbh_9br}
    {
        "Message": "refFaceContent or authFaceContent or resourceCode is null",
        "RequestId": "C5E39498-2148-4DB4-A54F-77CC3BF0ABBF",
        "Code": 400
    }
    ```

-   The following table lists the descriptions of the JSON strings returned by the Data parameter.

    |Parameter|Type|Required|Description|Example|
    |---------|----|--------|-----------|-------|
    |passed|String|Yes|When the system runs a facial recognition task, it returns one of the following results:     -   **false**: Facial recognition failed.
    -   **true**: Facial recognition is passed.
 |true|
    |retCode|String|Yes|The response code.     -   **OK\_SUCCESS**: The facial recognition request has been sent.
    -   **SYSTEM\_ERROR**: An error occurred in your servers.
    -   **UNABLE\_GET\_IMAGE**: The data for comparison is unavailable.
    -   **PROCESSING**: The photo cannot be parsed. Try again later.
 |OK\_SUCCESS|
    |retCodeSub|String|Yes|The subcode in the response. You can use subcodes to troubleshoot errors. Do not use the subcodes in selection statements.|-|
    |retMessage|String|Yes|The request status. Use it for troubleshooting only.|Facial recognition passed \(Z1140\)|
    |extInfo.result|String|Yes|The final result of ID verification.     -   **false**: Failed
    -   **true**: Passed.
 |true|
    |extInfo.score|String|Yes|The similarity between the face and the photo.|54.4566324452|
    |extInfo.faceAttack|String|Yes|Determines whether it is a spoofing attack. For example, attacks that use pictures taken from authenticated pictures or videos to impersonate the real person.     -   **false**: Not a spoofing attack.
    -   **true**: A spoofing attack.
 |false|


## Sample code {#section_z0c_zuj_t1n .section}

**Java** 

``` {#codeblock_rk6_54z_bq7}
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
import com.alibaba.fastjson.JSON;
import com.aliyuncs.CommonRequest;
import com.aliyuncs.CommonResponse;
import com.aliyuncs.DefaultAcsClient;
import com.aliyuncs.IAcsClient;
import com.aliyuncs.exceptions.ClientException;
import com.aliyuncs.exceptions.ServerException;
import com.aliyuncs.http.MethodType;
import com.aliyuncs.http.ProtocolType;
import com.aliyuncs.profile.DefaultProfile;

public class IntlFaceVerifyTest {
    public static void main(String[] args) throws IOException {
        DefaultProfile profile = DefaultProfile.getProfile("cn-shanghai", "<your-access-key-id>", "<your-access-key-secret>");
        IAcsClient client = new DefaultAcsClient(profile);

        CommonRequest request = new CommonRequest();
        request.setMethod(MethodType.POST);
        // Select the nearest region.
        request.setDomain("saf.ap-southeast-1.aliyuncs.com");
        request.setVersion("2019-05-21");
        request.setAction("ExecuteRequest");
        request.setProtocol(ProtocolType.HTTPS);

        // ID Verification parameters.
        Map<String, Object> serviceParams = new HashMap<String, Object>();
        // Do not include this prefix: data:image/jpeg;base64. The photo must be in the JPG format. Do not include newlines (\r) or carriage returns (\n).
        serviceParams.put("authFaceContent",  "/9j/4AAQSkZJRgABAQAAFAAUAAD...") ;
        // Do not include this prefix: data:image/jpeg;base64,). The photo must be in the JPG format. Do not include newlines (\r) or carriage returns (\n).
        serviceParams.put("refFaceContent",  "/9j/4AAQSkZJRgABAQAAFAAUAAD...") ;
        // The resource code of the country that the person being verified belongs to. For example, 00840000 for Vietnam.
        serviceParams.put("resourceCode", "00840000");

        request.putQueryParameter("ServiceParameters", JSON.toJSONString(serviceParams));
        // The default value.
        request.putQueryParameter("Service", "intl_face_verify");

        try {
            CommonResponse response = client.getCommonResponse(request);
            System.out.println(response.getData());
        } catch (ServerException e) {
            e.printStackTrace();
        } catch (ClientException e) {
            e.printStackTrace();
        }
    }
}
```

**PHP** 

``` {#codeblock_3r1_s4j_0s4}
<? php
include_once 'aliyun-openapi-php-sdk/aliyun-php-sdk-core/Config.php';
use saf\Request\V20180919 as saf;

// Initialize the class.
$iClientProfile = DefaultProfile::getProfile("cn-shanghai", "<your-access-key-id>", "<your-access-key-secret>");
$client = new DefaultAcsClient($iClientProfile);

// Set parameters.
$request = new saf\ExecuteRequestRequest();
$request->setService('intl_face_verify');
$request->setServiceParameters('{"authFaceContent": "/9j/4AAQSkZJRgABAQAAFAAUAAD...","resourceCode": "00840000","refFaceContent": "/9j/4AAQSkZJRgABAQAAFAAUAAD..."}') ;

// Initiate the request.
$response = $client->getAcsResponse($request);

print_r("<br>");
print_r("test");
print_r("\r\n");
print_r($response);

print_r($client);

? >
```

**Python** 

``` {#codeblock_00c_87u_ojh}
from aliyunsdkcore import client
from aliyunsdksaf.request.v20180919 import ExecuteRequestRequest

clt = client.AcsClient('<your-access-key-id>','<your-access-key-secret>','cn-shanghai')

# Set parameters.
request = ExecuteRequestRequest.ExecuteRequestRequest()
request.set_accept_format('json')
request.set_version("2019-05-21")

request.add_query_param('Service', 'intl_face_verify')
request.add_query_param('ServiceParameters', '{"authFaceContent": "/9j/4AAQSkZJRgABAQAAFAAUAAD...","resourceCode": "00840000","refFaceContent": "/9j/4AAQSkZJRgABAQAAFAAUAAD..."}')

# Initiate the request.
response = clt.do_action_with_exception(request)
print(response)
```

**Golang** 

``` {#codeblock_p4j_dr1_r7h}
package main

import (
  "fmt"
  "github.com/aliyun/alibaba-cloud-sdk-go/services/saf"
)

func main() {
  client, err := saf.NewClientWithAccessKey("cn-shanghai", "<your-access-key-id>", "<your-access-key-secret>")
  request := saf.CreateExecuteRequestRequest()
  // For more information about the Service parameter, see the Common request parameters table in the Make API requests topic.
  request.Service = "intl_face_verify"
  request.ServiceParameters = `{"authFaceContent": "/9j/4AAQSkZJRgABAQAAFAAUAAD...","resourceCode": "00840000","refFaceContent": "/9j/4AAQSkZJRgABAQAAFAAUAAD..."}`
  request.Scheme = "https"
  response, err := client.ExecuteRequest(request)
  if err ! = nil {
    fmt.Print(err.Error())
  }
  fmt.Printf("response code is %#v\n", response.Code)
  fmt.Printf("response date is %#v\n", response.Data)
  fmt.Printf("response message is %#v\n", response.Message)
  fmt.Printf("response requestId is %#v\n", response.RequestId)
}
```

