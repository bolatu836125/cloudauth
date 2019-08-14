# Web端接入 {#concept_473656 .concept}

Web刷脸的页面需要通过iframe的形式嵌入接入方的原始页面中。

## 获取metainfo {#section_fs6_7a6_7tv .section}

在接入方页面头部引入：

``` {#codeblock_05r_tks_kxz}

<script src="https://zoloz.cloud.alipay.com/gjj/getmetainfo.js"></script>
<script>
    let info = getMetaInfo();
    /* metaInfo参考格式
        {
            metaInfo: { apdidToken: "APDIDJS_zorro_0a89a4c92b6e08ba07f0f483a0ba22b6", userAgent: "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.13; rv:62.0) Gecko/20100101 Firefox/62.0", deviceType: "H5" },
            retCode: 100,
            retCodeSub: 'Z1400',
            retMessageSub: '浏览器版本支持(Z1400)'
        }
    */
    //retCode部分是用来判断浏览器版本是否支持
    //给服务端参数赋值为metaInfo
    let metaInfo = info.metaInfo;
</script>

				
```

如无法访问外网可使用：[getmetainfo.js.zip \(22 KB\)](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/121117/cn_zh/1559613868326/getmetainfo.js.zip)

## 嵌入页面的方法 {#section_75u_dhk_itq .section}

界面上引入iframe的格式如下：

-   `<iframe src="https://zoloz.cloud.alipay.com/gjj/identification_jry.html?zimId={接口返回的zimId}" allow="camera" width="640px" height="480px"></iframe>`
-   iframe中会进行摄像头预览，宽高尺寸要求4:3。

接入方的页面通过如下方法监听刷脸的结果：

``` {#codeblock_akm_tcy_l46}

window.addEventListener('message', function (event) {
    //处理数据
  console.log(JSON.parse(event.data));
    /* 
     * 取值如下
    {
      retCode: 1000,//人脸验证结果，比对成功，需要根据zimid进行认证结果查询
      retCodeSub: 'Z1140', //业务不用关心
      retMessageSub: '刷脸通过(Z1140)' //人脸提示，仅做参考
    }
    {
      retCode: 1003,//客户端报错
      retCodeSub: 'Z1415', //业务不用关心
      retMessageSub: '用户主动退出(Z1415)' //人脸提示，仅做参考
    }
    {
      retCode: 2006,//人脸验证结果，比对失败，不需要进行认证结果查询
      retCodeSub: 'Z1141', //业务不用关心
      retMessageSub: '抱歉，系统出错了，请您稍后再试(Z1141)'
    }
    */
}, false);

				
```

监听到结果后，可以从界面上删除iframe元素，进行后续流程。

## 环境要求 {#section_lcc_l13_k8b .section}

-   摄像头要求：普通前置摄像头或USB摄像头，分辨率640\*480以上。
-   PC电脑要求：Windows 7以上，分辨率在1024\*768以上。
-   浏览器建议使用新版，支持的浏览器如下：

    |浏览器|支持的版本|最新版本|
    |---|-----|----|
    |IE|10|edge 17|
    |Firefox|41+|65|
    |Chrome（搜狗、360、QQ浏览器的极速模式）|38+|72|
    |Safari|11+|12|


