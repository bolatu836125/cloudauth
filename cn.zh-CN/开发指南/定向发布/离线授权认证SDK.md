# 离线授权认证SDK {#concept_ygt_td4_hhb .concept}

阿里云实人认证提供离线授权认证SDK，帮助您实现在弱网或离网环境下的人脸认证。

## 简介 {#section_zs4_5d4_hhb .section}

离线授权认证SDK在Android 设备终端集成，支持在弱网或离线环境下的人脸认证应用。离线授权认证SDK包含人脸检测、活体检测、人脸1:1比对、人脸1：N检索、人脸库管理等能力，并全部离线化、本地化。SDK在授权激活后，可完全在无网环境下工作，所有数据皆在Android设备本地运行处理，可根据业务需要进行灵活的上层业务开发。

## 核心功能 {#section_bxb_wd4_hhb .section}

离线授权认证SDK提供以下核心能力：

-   **人脸检测**

    输入摄像头采集的数据，返回检测到的人脸位置。

-   **RGB可见光活体检测**

    针对视频流，通过采集人像的破绽（摩尔纹、成像畸形等）来判断目标对象是否为活体，可有效防止屏幕二次翻拍和复印二次翻拍等作弊攻击。

-   **NIR近红外活体检测**

    针对视频流，利用近红外成像原理，实现夜间或无自然光条件下的活体判断。

-   **人脸比对**

    本地1:1人脸比对功能，针对一张预设的图片，和摄像头实时采集的符合条件的人脸进行比对，比对是否为同一人。

-   **人脸检索**

    本地1：N人脸检索功能，针对摄像头实时采集的符合条件的人脸，从本地人脸底库中检索是否存在此人的信息。

-   **人脸库管理** 

    提供本地人脸底库的新增、删除等管理维护功能。


## 接入说明 {#section_qf4_zd4_hhb .section}

**人脸算法对设备的要求**

硬件设备：

-   系统：Android 4.4及以上
-   系统类型：32位、64位
-   处理器：4核，1.45GHz及以上
-   内存：1G及以上

镜头：

-   分辨率：RGB镜头1080p， 红外镜头800X600及以上（需要使用红外功能的需要关注）
-   固定30帧，无拖影，RGB镜头建议是宽动态
-   照片清晰可辨认，人脸区域宽度大于100像素，人脸角度yaw ≤ ±15°， pitch ≤ ±15°

**授权激活和有效期**

离线授权认证SDK首次初始化时需要联网激活，以获得授权，被授权的设备和APP，在有效期内可以运行SDK。

以下几种场景会导致授权失效，需要重新联网激活：

-   设备刷机
-   清除数据
-   恢复出厂设置

免费测试：我们为每个客户提供2个免费测试授权，用于SDK试用，有效期为自激活日期起的3个月。

正式购买：客户根据业务需要购买相应时长的SDK使用授权，具体定价可联系阿里云客户经理咨询。

## 接入时序图 {#section_txc_3f4_hhb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/154192/155437150143251_zh-CN.png)

时序图说明：

-   Step1~Step4：可选。若设备未激活（如首次使用SDK、设备刷机、恢复出厂设置等），则需要调用实人认证服务端接口CreateAuthKey获得授权token，再进行初始化。若设备已激活，则直接从step5开始，传入授权token进行SDK初始化。
-   Step5~Step6：调用SDK的initWithToken函数，传入授权token进行初始化，SDK会回调初始化结果。
-   Step7~Step8：可选。若使用人脸1：N检索，则需要进行用户库操作。若使用人脸1:1比对，则不需要该步骤。
-   Step9~Step11：进行人脸比对或检索，其中活体检测（翻拍和红外活体）根据接入方需要可开启或关闭，比对/检索结果SDK会回调告知。

## SDK端接入 {#section_lbf_rf4_hhb .section}

**SDK下载**

离线授权认证SDK暂未提供线上自助下载，若有使用需求，可联系阿里云客户经理咨询。

**SDK初始化及相关设置**

接入方只需关注 SDK 提供的VerifySDKManager类，上层与 SDK 的交互都是通过VerifySDKManager进行调用。

首次初始化时，授权token需要在初始化之前调用实人认证服务端接口CreateAuthKey申请获得。并且，使用initWithToken初始化方法，需要开启设备管理功能setNeedDeviceManage。

具体提供以下接口：

-   **使用授权token进行初始化**

    接口名：initWithToken

    接口描述：使用授权token进行初始化，初始化结果通过回调告知。

    |名称|类型|描述|
    |--|--|--|
    |context|Context|当前activity的上下文。|
    |token|String|激活使用的授权key，从实人认证服务端申请获得。|
    |initWithTokenCallback|InitWithTokenCallback|回调函数。|

    使用SDK相关设置函数，可以修改人脸检测的最小尺寸、是否开启端侧翻拍检测、是否开启红外活体检测、设置翻拍检测阈值、设置红外活体阈值、设置人脸检索匹配阈值等。

-   **设置是否开启设备管理功能**

    接口名：setNeedDeviceManage

    接口描述：使用initWithToken初始化方法，需要开启设备管理功能。

    |名称|类型|描述|
    |--|--|--|
    |needDeviceManage|boolean|是否开启设备管理。|

-   **设置最小检测人脸**

    接口名：setMinFaceDetectSize

    接口描述：设置最小检测人脸，图像中的人脸大小超过该值时，才能检测到人脸，否则无法检测到人脸。

    |名称|类型|描述|
    |--|--|--|
    |minFaceDetectSize|float|人脸检测的最小尺寸，取值范围0-1，定义为占图像min\(宽，高）的比例，默认值0.1。|

-   **设置人脸位置有效区域边界**

    接口名：setBorders

    接口描述：设置人脸位置有效区域边界。

    |名称|类型|描述|
    |--|--|--|
    |leftBorder|float|人脸位置有效区域的左边界，定义为占旋转后图像宽度的比例，默认值0.1。|
    |rightBorder|float|人脸位置有效区域的右边界，定义为占旋转后图像宽度的比例，默认值0.9。|
    |topBorder|float|人脸位置有效区域的上边界，定义为占旋转后图像高度的比例，默认值0.1。|
    |bottomBorder|float|人脸位置有效区域的下边界，定义为占旋转后图像高度的比例，默认值0.9。|

-   **设置是否开启离线翻拍检测**

    接口名：setNeedRecapCheck

    接口描述： 设置是否开启离线翻拍检测。

    |名称|类型|描述|
    |--|--|--|
    |needRecapCheck|boolean|是否开启离线翻拍检测。|

-   **设置是否开启红外防彩色图片翻拍检测**

    接口名：setNeedNirSeniorRecapCheck

    接口描述：设置是否开启红外防彩色图片翻拍检测。

    |名称|类型|描述|
    |--|--|--|
    |needNirSeniorRecapCheck|boolean|是否开启红外防彩色图片翻拍检测。|

-   **设置是否需要红外活体能力**

    接口名：setNeedNirLiveness

    接口描述：设置是否需要红外活体能力，若设置需要红外活体能力，那么setNirFrameW、setNirFrameH、setNirAngle、setRgbAngle为必要参数。

    |名称|类型|描述|
    |--|--|--|
    |needNirLiveness|boolean|是否需要红外活体能力。|

-   **设置红外图像的帧高度**

    接口名：setNirFrameH

    接口描述：设置红外图像的帧高度，默认值为720。

    |名称|类型|描述|
    |--|--|--|
    |nirFrameH|int|红外图像的帧高度，默认值为720。|

-   **设置红外图像的帧宽度**

    接口名：setNirFrameW

    接口描述：设置红外图像的帧宽度，默认值为720。

    |名称|类型|描述|
    |--|--|--|
    |nirFrameW|int|红外图像的帧宽度，默认值为720。|

-   **设置红外图像的旋转角度**

    接口名：setNirAngle

    接口描述：设置红外图像的旋转角度，默认值为0。

    |名称|类型|描述|
    |--|--|--|
    |nirAngle|int|红外图像的旋转角度，默认值为0。|

-   **设置RGB图像的旋转角度**

    接口名：setRgbAngle

    接口描述：设置RGB图像的旋转角度，默认值为0。

    |名称|类型|描述|
    |--|--|--|
    |rgbAngle|int|RGB图像的旋转角度，默认值为0。|

-   **设置翻拍检测阈值**

    接口名：setRecapThreshold

    接口描述：设置翻拍检测阈值，目前翻拍默认分值是80f，取值范围0~100，翻拍得分大于等于80f则认为是翻拍，小于80f则认为是真人。接入方可根据业务实际需要进行阈值调整。

    |名称|类型|描述|
    |--|--|--|
    |recapThreshold|float|设置翻拍检测阈值，默认值80f。|

-   **设置红外活体阈值**

    接口名：setNirScoreThreshold

    接口描述：设置红外活体阈值，目前默认分值是0f，取值范围0~1，接入方可根据业务实际需要进行阈值调整。

    |名称|类型|描述|
    |--|--|--|
    |nirScoreThreshold|float|设置红外活体阈值，默认值0f。|

-   **设置人脸匹配阈值**

    接口名：setFaceMatchThreshold

    接口描述：设置人脸匹配阈值，目前默认分值是0.44f，取值范围0~1，接入方可根据业务实际需要进行阈值调整。

    |名称|类型|描述|
    |--|--|--|
    |faceMatchThreshold|float|设置人脸匹配阈值，默认值0.44f。|


初始化调用示例

```
VerifySDKManager.getInstance().setNeedNirLiveness(true). setNeedNirSeniorRecapCheck(true).setNirFrameH(nirFrameH).setNirFrameW(nirFrameW).setNirAngle(nirAngle).setRgbAngle(rgbAngle).setNirSocreThreshold(nirSocreThreshold).initWithToken(context, token, initWithTokenCallback);
```

## SDK用户库操作 {#section_m4k_hk4_hhb .section}

初始化成功后，如果要使用SDK的人脸1：N检索能力，需要进行相应用户库操作，向人脸库中添加人脸图片，否则人脸检索将无法匹配。

如果要使用SDK的人脸1:1比对能力，则可以不关注用户库操作，直接参考[人脸识别](#)部分的说明。

-   **加载用户库**

    接口名：loadUserLib

    |名称|类型|描述|
    |--|--|--|
    |listener|VerifyLibEventListener|用户库操作回调。|

-   **添加本地用户照片**

    接口名：addUser

    接口描述：添加本地用户照片，操作结果以回调方式通知。

    |名称|类型|描述|
    |--|--|--|
    |isSync|boolean|是否同步调用，建议上层调用入库自己管理线程，该参数传true，否则传false。|
    |id|String|用户id。|
    |type|int|生物特征类型，目前只支持Type.BIOLOGY\_FACE。|
    |featureData|byte\[\]|用户照片的byte源数据，支持jpg、png格式。|
    |verifyLibEventListener|VerifyLibEventListener|用户库操作回调。|

-   **删除指定用户生物数据**

    接口名：removeUser

    接口描述：删除指定用户生物数据，操作结果以回调方式通知。

    |名称|类型|描述|
    |--|--|--|
    |isSync|boolean|是否同步调用，建议上层调用入库自己管理线程，该参数传true，否则传false。|
    |id|String|用户id。|
    |verifyLibEventListener|VerifyLibEventListener|用户库操作回调。|

-   **清空所有用户数据**

    接口名：clearUserLib

    接口描述：清除本地用户库。

    |名称|类型|描述|
    |--|--|--|
    |isSync|boolean|是否同步调用，建议上层调用入库自己管理线程，该参数传true，否则传false。|
    |verifyLibEventListener|VerifyLibEventListener|用户库操作回调。|

-   **单用户数据更新回调**

    接口名：onSingleUserLibUpdate

    接口描述：用户库操作回调，单用户数据更新。

    |名称|类型|描述|
    |--|--|--|
    |id|String|用户id。|
    |errorCode|int|错误码，0为正确。|

-   **用户库加载完成回调**

    接口名：onUserLibLoaded

    接口描述：用户库操作回调，用户库加载完成。

    |名称|类型|描述|
    |--|--|--|
    |errorCode|int|错误码，0为正确。|

-   **用户库清除回调**

    接口名：onUserLibEmpty

    接口描述：用户库操作回调，用户库清除完成。

    |名称|类型|描述|
    |--|--|--|
    |errorCode|int|错误码，0为正确。|


## 人脸识别 {#face .section}

**人脸1:1比对说明**

SDK支持实时视频流和人脸图片进行比对，这是最为常见的1：1对比类型。针对一张事先获取的图片（通常为身份证芯片照、证件照片等），与摄像头实时采集的符合条件的人脸图片进行比对。通常适用于有人值守的场景。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/154192/155437150143274_zh-CN.png)

**人脸1：N检索**

将需要识别的人脸图片注册到本地人脸库中，当有用户需要识别身份时，从视频流中实时采集符合条件的人脸图片，与人脸库中的人脸集合比对，得到检索结果。如果是无人值守的情况，建议可以开启翻拍检测或红外活体保障安全性。

如果在初始化时设置开启了翻拍检测或红外活体检测，则摄像头采集的人脸图片必须同时通过活体检测，才能进入人脸检索环节，任一活体检测未通过，都不会进行人脸检索。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/154192/155437150243275_zh-CN.png)

-   **人脸1:1比对调用**

    接口名：doFaceMatchWithImage

    接口描述：摄像头帧数据与人脸图片进行1:1比对。

    |名称|类型|描述|
    |--|--|--|
    |data|byte\[\]|检测到合格人脸时的RGB帧数据。|
    |width|int|帧数据宽度。|
    |height|int|帧数据高度。|
    |cameraRotation|int|读取到合格人脸时的相机旋转角度，取值0/90/180/270，从摄像头的回调接口中获取。|
    |targetBitmap|Bitmap|待比对人脸图片。|
    |listener|FaceMatchWithImageListener|1:1比对回调。|

-   **人脸1:1比对结果回调**

    接口名：onMatchResult

    接口描述： 人脸1:1比对结果回调，可获得相应比对分。

    |名称|类型|描述|
    |--|--|--|
    |result|FaceMatchResult|人脸1:1比对结果。|

-   **人脸1:N检索调用**

    接口名：feedPreviewFrame

    接口描述：RGB摄像头帧数据与人脸库进行1：N检索，结果以回调方式通知。适用于设备上只有RGB单目摄像头，或者有双目摄像头但未开启红外活体检测的情况，可以使用该接口进行检索比对。

    |名称|类型|描述|
    |--|--|--|
    |data|byte\[\]|检测到合格人脸时的RGB帧数据。|
    |width|int|帧数据宽度。|
    |height|int|帧数据高度。|
    |imageFormat|int|帧数据格式，Android默认选择ImageFormat.NV21。|
    |imageAngle|int|读取到合格人脸时的图片旋转角度，取值0/90/180/270，从摄像头的回调接口中获取。|
    |cameraRotation|int|读取到合格人脸时的相机旋转角度，取值0/90/180/270，从摄像头的回调接口中获取。|
    |faceDetectWithMatchListener|FaceDetectWithMatchListener|人脸检测、检索比对、翻拍检测回调。|

    接口名：feedPreviewFrameWithNir

    接口描述：RGB和红外摄像头帧数据与人脸库进行1：N检索，结果以回调方式通知。适用于设备上有双目红外的摄像头，在开启红外活体检测的情况下，可以使用该接口进行检索比对。

    |名称|类型|描述|
    |--|--|--|
    |nirData|byte\[\]|检测到合格人脸时的红外帧数据。|
    |rgbData|byte\[\]|检测到合格人脸时的RGB帧数据。|
    |width|int|RGB帧数据宽度。|
    |height|int|RGB帧数据高度。|
    |imageFormat|int|帧数据格式，Android默认选择ImageFormat.NV21。|
    |imageAngle|int|读取到合格人脸时的图片旋转角度，取值0/90/180/270，从摄像头的回调接口中获取。|
    |cameraRotation|int|读取到合格人脸时的相机旋转角度，取值0/90/180/270，从摄像头的回调接口中获取。|
    |nirFaceDetectListener|FaceMatchWithImageListener|人脸检测、检索比对、翻拍检测回调。|

-   **人脸1：N检测到人脸回调**

    接口名：onFaceDetected

    接口描述：RGB帧数据中检测到人脸时，会回调该函数，调用方可根据此回调绘制人脸框。

    |名称|类型|描述|
    |--|--|--|
    |data|byte\[\]|RGB帧数据。|
    |width|int|RGB帧数据宽度。|
    |height|int|RGB帧数据高度。|
    |imageFormat|int|帧数据格式，Android默认选择ImageFormat.NV21。|
    |imageAngle|int|图片旋转角度。|
    |cameraRotation|int|相机旋转角度。|
    |faceInfo|FaceInfo|检测到的人脸信息在帧中的坐标，宽高。|

    接口名：onFaceDetectedInNIR

    接口描述：红外帧数据中检测到人脸时，会回调该函数，调用方可根据此回调做相应处理。

    |名称|类型|描述|
    |--|--|--|
    |nirData|byte\[\]|红外帧数据。|
    |width|int|红外帧数据宽度。|
    |height|int|红外帧数据高度。|
    |nirAngle |int|图片旋转角度。|
    |nirFaceInfo|NirFaceInfo|检测到的红外人脸信息在帧中的坐标，宽高。|

-   **人脸1：N未检测到人脸回调**

    接口名：onNofaceDetected

    接口描述：RGB帧数据中未检测到人脸，可以在这个回调中更新UI，移除之前绘制的人脸框。

    |名称|类型|描述|
    |--|--|--|
    |data|byte\[\]|RGB帧数据。|
    |width|int|RGB帧数据宽度。|
    |height|int|RGB帧数据高度。|
    |imageFormat|int|帧数据格式，Android默认选择ImageFormat.NV21。|
    |imageAngle|int|图片旋转角度。|
    |cameraRotation|int|相机旋转角度。|

    接口名：onNofaceDetectedInNIR

    接口描述：红外帧数据中未检测到人脸。

    |名称|类型|描述|
    |--|--|--|
    |nirData|byte\[\]|红外帧数据。|
    |width|int|红外帧数据宽度。|
    |height|int|红外帧数据高度。|
    |nirAngle|int|图片旋转角度。|

-   **人脸1：N检索结果回调**

    接口名：onFaceMatched

    接口描述：人脸检索匹配结果回调。

    |名称|类型|描述|
    |--|--|--|
    |data|byte\[\]|RGB帧数据。|
    |width|int|RGB帧数据宽度。|
    |height|int|RGB帧数据高度。|
    |imageAngle|int|图片旋转角度。|
    |cameraRotation|int|相机旋转角度。|
    |matchResult|FaceMatchResult|匹配结果，极端情况下可能存在多个人的情况，比如双胞胎。|
    |costTime|long|人脸检索比对耗时，性能分析调试用。|

-   **检测到翻拍回调**

    接口名：onRecapDetected

    接口描述：检测到翻拍，且初始化时开启了翻拍检测时，会执行这个回调。

    |名称|类型|描述|
    |--|--|--|
    |data|byte\[\]|RGB帧数据。|
    |width|int|RGB帧数据宽度。|
    |height|int|RGB帧数据高度。|
    |imageAngle|int|图片旋转角度。|
    |cameraRotation|int|相机旋转角度。|
    |recapScore|float|翻拍得分。|

-   **人脸移动时的回调**

    接口名：onFaceMoving

    接口描述：检测到人脸在移动时，会回调该函数。

    |名称|类型|描述|
    |--|--|--|
    |isMoving|boolean|人脸是否移动。|


错误码

|错误信息|错误描述|错误码|
|----|----|---|
|VERIFYSDK\_ERR\_CODE\_BAD\_PARAM|参数错误。|100|
|VERIFYSDK\_ERR\_CODE\_ACCESS\_WORK\_FAILED|访问工作路径错误。|101|
|VERIFYSDK\_ERR\_CODE\_INVALID\_PARAM|人脸引擎参数无效。|1000|
|VERIFYSDK\_ERR\_CODE\_SDK\_NOT\_INIT|人脸引擎未初始化。|1001|
|VERIFYSDK\_ERR\_CODE\_INIT\_MULT\_TIME|人脸引擎初始化多次。|1002|
|VERIFYSDK\_ERR\_CODE\_FILE\_NOT\_EXIST|人脸模型文件不存在。|1100|
|VERIFYSDK\_ERR\_CODE\_FILE\_SIZE\_ERROR|人脸模型文件大小错误。|1101|
|VERIFYSDK\_ERR\_CODE\_VERSION\_NOT\_MATCH|人脸模型版本不匹配。|1102|
|VERIFYSDK\_ERR\_CODE\_VERSION\_NOT\_MATCH\_FD|人脸模型版本不匹配子错误。|11020|
|VERIFYSDK\_ERR\_CODE\_VERSION\_NOT\_MATCH\_LDM|人脸模型版本不匹配子错误。|11021|
|VERIFYSDK\_ERR\_CODE\_VERSION\_NOT\_MATCH\_LDC|人脸模型版本不匹配子错误。|11022|
|VERIFYSDK\_ERR\_CODE\_L\_VERSION\_NOT\_MATCH\_FE|人脸模型版本不匹配子错误。|11023|
|VERIFYSDK\_ERR\_CODE\_VERSION\_NOT\_MATCH\_FEENC|人脸模型版本不匹配子错误。|11024|
|VERIFYSDK\_ERR\_CODE\_CANCEL|取消。|1103|
|VERIFYSDK\_ERR\_CODE\_BUSY|数据库并发操作。|1104|
|VERIFYSDK\_ERR\_CODE\_FEATURE\_SIZE\_ERROR|人脸特征大小错误。|1201|
|VERIFYSDK\_ERR\_CODE\_LICENCE|授权错误。|5000|
|VERIFYSDK\_ERR\_CODE\_LICENCE\_INPUT|输入参数错误。|5001|
|VERIFYSDK\_ERR\_CODE\_LICENCE\_DATA\_FORMAT|授权文件格式错误。|5002|
|VERIFYSDK\_ERR\_CODE\_LICENCE\_SIGN|签名校验错误。|5003|
|VERIFYSDK\_ERR\_CODE\_LICENCE\_APKPKG|包名校验错误。|5004|
|VERIFYSDK\_ERR\_CODE\_LICENCE\_PUBKEY|公钥校验错误。|5005|
|VERIFYSDK\_ERR\_CODE\_LICENCE\_EXPIRE|授权过期。|5006|
|VERIFYSDK\_ERR\_CODE\_LICENCE\_NOT\_CHECK|未进行授权检查。|5007|
|VERIFYSDK\_ERR\_CODE\_LICENCE\_CLIENT\_ID|终端标识校验错误。|5009|
|VERIFYSDK\_ERR\_CODE\_LICENCE\_EXPIRE\_DATE\_MODIFIED|授权失效时间被修改。|5010|
|VERIFYSDK\_ERR\_CODE\_NOT\_SUPPORT\_DEVICE|不支持的硬件型号。|6000|
|VERIFYSDK\_ERR\_CODE\_LOAD\_LIBARY\_FAILED|加载so失败。|6001|
|VERIFYSDK\_ERR\_CODE\_LICENCE\_NOT\_EXIST|授权文件不存在。|6002|
|VERIFYSDK\_ERR\_CODE\_SECURITY\_TOKEN\_SOCKET\_TIMEOUT|获取激活token超时，即初始化失败或未完成。|7001|
|VERIFYSDK\_ERR\_CODE\_GET\_STATIC\_DATA\_STORE\_COMP|SDK安全组件获取appkey失败。|7003|
|VERIFYSDK\_ERR\_CODE\_GET\_SECURE\_SIGNATURE\_COMP|SDK安全组件获取加签失败。|7004|
|VERIFYSDK\_ERR\_CODE\_SG\_SECEXCEPTION|SDK安全组件异常。|7002|
|VERIFYSDK\_ERR\_CODE\_GET\_LICENSE\_INFO\_TOKEN\_NULL|带授权token激活时，token为空。|8001|
|VERIFYSDK\_ERR\_CODE\_GET\_LICENSE\_INFO\_NATIVE\_FAILED|获取授权失败。|8002|
|VERIFYSDK\_ERR\_CODE\_GET\_LICENSE\_CHARSETNAME\_EXCEPTION|授权token编码转换出错。|8003|
|VERIFYSDK\_ERR\_CODE\_USER\_LIB\_NOT\_LOADED|人脸库还未初始化完成。|9001|
|VERIFYSDK\_ERR\_CODE\_UNKNOWN|人脸模块未知错误。|9999|
|VERIFYSDK\_ERR\_CODE\_ACTIVATE\_LICENSE\_REQUEST\_FAILED|请求失败，激活SDK失败。|10001|
|VERIFYSDK\_ERR\_CODE\_ACTIVATE\_LICENSE\_DEVICE\_NOT\_AUTH|未授权，激活SDK失败。|10002|
|VERIFYSDK\_ERR\_CODE\_ACTIVATE\_LICENSE\_DEVICE\_AUTH\_EXPIRED|授权到期失效，激活SDK失败。|10003|
|VERIFYSDK\_ERR\_CODE\_ACTIVATE\_LICENSE\_DATA\_NULL|授权文件为空，激活SDK失败。|10004|

## 服务端接入 {#section_lpn_z44_hhb .section}

**服务端接入准备**

-   [获取 AccessKey](cn.zh-CN/开发指南/服务端API/获取AccessKey.md#)
-   [API调用方式](cn.zh-CN/开发指南/服务端API/API调用方式.md#)

**接口描述**

离线授权认证SDK在首次初始化时，需要接入方服务端提前调用该实人认证服务端接口，获取授权token，用于SDK激活。

接口名：CreateAuthKey

请求方法：支持以HTTPS POST和GET方法发送请求。

**请求参数**

|名称|类型|参数位置|是否必需|描述|
|--|--|----|----|--|
|BizType|String|Query|否|业务类型，比如不同的人脸使用场景等，都可以通过该字段做备注。|
|UserDeviceId|String|Query|否|用户设备ID。|
|Test|Boolean|Query|否|测试标识。true表示使用测试授权，授权时长默认为30天；false表示授权时长根据AuthYears进行授权。|
|AuthYears|Integer|Query|否|当Test标识为false或空时，AuthYears必填，单位为年，范围为\[1,100\]，取值100表示永久授权。|

**返回参数**

|名称|类型|描述|
|--|--|--|
|AuthKey|String|可用于授权激活的token。|

**示例**

关于使用SDK开发包的示例，请参考以下具体SDK开发包中的离线授权认证SDK接入说明：[Java](cn.zh-CN/开发指南/服务端API/使用SDK开发包/Java SDK.md#)、[PHP](cn.zh-CN/开发指南/服务端API/使用SDK开发包/PHP SDK.md#)、[Python](cn.zh-CN/开发指南/服务端API/使用SDK开发包/Python SDK.md#)、[.NET](cn.zh-CN/开发指南/服务端API/使用SDK开发包/.NET SDK.md#)、[Node.js](cn.zh-CN/开发指南/服务端API/使用SDK开发包/Node.js SDK.md#)、[Go](cn.zh-CN/开发指南/服务端API/使用SDK开发包/Go SDK.md#)。

以下是拼接HTTPS请求的相关示例：

请求示例

```
https://cloudauth.aliyuncs.com/?Action=CreateAuthKey
&BizType=FACE_TEST
&UserDeviceId=3iJ1AY$oHcu7mC69
&Test=false
&AuthYears=1
&<[公共请求参数]>
```

返回示例

-   XML格式

    ```
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

    ```
    {
      "Data": {
        "AuthKey":auth.1KQMcnLd4m37LN2D0F0WCD-1qtQI$
      },
      "Success": true
    }
    ```


