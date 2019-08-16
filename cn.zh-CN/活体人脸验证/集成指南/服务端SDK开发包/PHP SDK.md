# PHP SDK {#reference_jy2_fss_gfb .reference}

本文介绍了如何使用阿里云实人认证服务的PHP SDK。

## 获取地址 {#section_aq3_hss_gfb .section}

单击前往[GitHub](https://github.com/aliyun/openapi-sdk-php)。

## 使用说明 {#section_cz4_l5s_gfb .section}

参见GitHub中的说明。

## 示例代码 {#section_594_dqa_aak .section}

``` {#codeblock_eqb_8zu_eqn}
<?php
require_once 'vendor/autoload.php';

use AlibabaCloud\Client\AlibabaCloud;
use AlibabaCloud\Client\Exception\ClientException;
use AlibabaCloud\Client\Exception\ServerException;

/**
 * 创建一个全局客户端
 */
AlibabaCloud::accessKeyClient('xxx', 'xxxxxx')->regionId('cn-hangzhou')->asDefaultClient();
```

## 发起认证请求DescribeVerifyToken示例代码 {#section_04n_j8k_yy5 .section}

-   RPBasic、RPManual、FDBioOnly认证方案

    ``` {#codeblock_5wi_y0d_ghp}
    try {
        // 访问产品 APIs
        $request = AlibabaCloud::Cloudauth()->V20190307()->DescribeVerifyToken();
        $result = $request->withBizType('实人认证控制台上创建场景时对应的场景标识') //创建方法参见[业务设置](../../../../cn.zh-CN/快速入门/业务设置.md#)
                          ->withBizId('认证ID, 由接入方指定, 发起不同的认证任务需要更换不同的认证ID')
                          ->connectTimeout(10)
                          ->timeout(10)
                          ->request();
        // Convert the result to an array and print
        print_r($result->toArray());
    } catch (ClientException $e) {
        // Print the error message
        echo $e->getErrorMessage() . PHP_EOL;
    } catch (ServerException $e) {
        // Print the error message
        echo $e->getErrorMessage() . PHP_EOL;
        print_r($e->getResult()->toArray());
    }
    ```

-   RPBioID、RPBioOnlyPro、RPBioOnly、RPH5BioOnly认证方案

    ``` {#codeblock_p66_6s7_6dz}
    try {
        // 访问产品 APIs
        $request = AlibabaCloud::Cloudauth()->V20190307()->DescribeVerifyToken();
    
        $result = $request->withBizType('实人认证控制台上创建场景时对应的场景标识') //创建方法参见[业务设置](../../../../cn.zh-CN/快速入门/业务设置.md#)
                          ->withBizId('认证ID, 由接入方指定, 发起不同的认证任务需要更换不同的认证ID')
                          ->withIdCardNumber('用户正确的身份证号')
                          ->withName('用户正确的姓名')
                          ->connectTimeout(10)
                          ->timeout(10)
                          ->request();
        // Convert the result to an array and print
        print_r($result->toArray());
    } catch (ClientException $e) {
        // Print the error message
        echo $e->getErrorMessage() . PHP_EOL;
    } catch (ServerException $e) {
        // Print the error message
        echo $e->getErrorMessage() . PHP_EOL;
        print_r($e->getResult()->toArray());
    }
    ```

-   FVBioOnly认证方案

    ``` {#codeblock_ajz_0iy_30e}
    try {
        // 访问产品 APIs
        $request = AlibabaCloud::Cloudauth()->V20190307()->DescribeVerifyToken();
    
        $result = $request->withBizType('实人认证控制台上创建场景时对应的场景标识') //创建方法参见[业务设置](../../../../cn.zh-CN/快速入门/业务设置.md#)
                          ->withBizId('认证ID, 由接入方指定, 发起不同的认证任务需要更换不同的认证ID')
                          ->withFaceRetainedImageUrl('公网可访问的图片http/https链接')
                          ->connectTimeout(10)
                          ->timeout(10)
                          ->request();
        // Convert the result to an array and print
        print_r($result->toArray());
    } catch (ClientException $e) {
        // Print the error message
        echo $e->getErrorMessage() . PHP_EOL;
    } catch (ServerException $e) {
        // Print the error message
        echo $e->getErrorMessage() . PHP_EOL;
        print_r($e->getResult()->toArray());
    }
    ```


## 查询认证结果DescribeVerifyResult示例代码 {#section_ic3_9ln_n57 .section}

``` {#codeblock_sp1_9uq_9y1}
try {
    // 访问产品 APIs
    $request = AlibabaCloud::Cloudauth()->V20190307()->DescribeVerifyResult();

    $result = $request->withBizType('实人认证控制台上创建场景时对应的场景标识') //创建方法参见[业务设置](../../../../cn.zh-CN/快速入门/业务设置.md#)
                      ->withBizId('认证ID, 由接入方指定, 发起不同的认证任务需要更换不同的认证ID')
                      ->connectTimeout(10)
                      ->timeout(10)
                      ->request();
    // Convert the result to an array and print
    print_r($result->toArray());
} catch (ClientException $e) {
    // Print the error message
    echo $e->getErrorMessage() . PHP_EOL;
} catch (ServerException $e) {
    // Print the error message
    echo $e->getErrorMessage() . PHP_EOL;
    print_r($e->getResult()->toArray());
}
```

## RPMin认证方案示例代码 {#section_vq6_3cl_dhj .section}

``` {#codeblock_89v_bom_txm}
try {
    // 访问产品 APIs
    $request = AlibabaCloud::Cloudauth()->V20190307()->VerifyMaterial();

    $result = $request->withBizType('实人认证控制台上创建场景时对应的场景标识') //创建方法参见[业务设置](../../../../cn.zh-CN/快速入门/业务设置.md#)
                      ->withBizId('认证ID, 由接入方指定, 发起不同的认证任务需要更换不同的认证ID')
                      ->withName('用户正确的姓名')
                      ->withIdCardNumber('用户正确的身份证号')
                      ->withFaceImageUrl('公网可访问的图片http/https链接')
                      ->connectTimeout(10)
                      ->timeout(10)
                      ->request();
    // Convert the result to an array and print
    print_r($result->toArray());
} catch (ClientException $e) {
    // Print the error message
    echo $e->getErrorMessage() . PHP_EOL;
} catch (ServerException $e) {
    // Print the error message
    echo $e->getErrorMessage() . PHP_EOL;
    print_r($e->getResult()->toArray());
}
```

## 人脸比对示例代码 {#section_dn6_sqt_7io .section}

``` {#codeblock_37d_qsk_mxr}
try {
    // 访问产品 APIs
    $request = AlibabaCloud::Cloudauth()->V20190307()->CompareFaces();

    $result = $request->withSourceImageType('FacePic')
                      ->withTargetImageType('FacePic')
                      ->withSourceImageValue('公网可访问的图片http/https链接')
                      ->withTargetImageValue('公网可访问的图片http/https链接')
                      ->connectTimeout(10)
                      ->timeout(10)
                      ->request();
    // Convert the result to an array and print
    print_r($result->toArray());
} catch (ClientException $e) {
    // Print the error message
    echo $e->getErrorMessage() . PHP_EOL;
} catch (ServerException $e) {
    // Print the error message
    echo $e->getErrorMessage() . PHP_EOL;
    print_r($e->getResult()->toArray());
}
```

## 人脸属性检测示例代码 {#section_ep6_a3w_r8i .section}

``` {#codeblock_0xo_oxf_706}
try {
    // 访问产品 APIs
    $request = AlibabaCloud::Cloudauth()->V20190307()->DetectFaceAttributes();

    $result = $request->withRetAttributes('facetype,headpose,age') //具体的属性由接入方按需传入
                      ->withMaterialValue('公网可访问的图片http/https链接')
                      ->withMaxFaceNum(1)
                      ->withDontSaveDB('false')
                      ->withClientTag('null')
                      ->withMaxNumPhotosPerCategory(1)
                      ->connectTimeout(10)
                      ->timeout(10)
                      ->request();
    // Convert the result to an array and print
    print_r($result->toArray());
} catch (ClientException $e) {
    // Print the error message
    echo $e->getErrorMessage() . PHP_EOL;
} catch (ServerException $e) {
    // Print the error message
    echo $e->getErrorMessage() . PHP_EOL;
    print_r($e->getResult()->toArray());
}
```

