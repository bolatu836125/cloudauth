# SDK端接入 {#concept_xlw_gzn_hhb .concept}

介绍离线授权认证SDK的SDK端接入方式。

## 授权方式说明 {#section_y5w_rb4_hhb .section}

离线授权认证SDK目前支持在线联网激活，以获得SDK使用授权，被授权的设备和APP，在有效期内可以运行SDK，离线完成包括人脸库管理、人脸检测、人脸比对、人脸检索、活体判断等功能。授权结束之后，将无法使用任何功能，需要重新激活获得授权。

授权有效，不会消耗新的授权，仅需联网重新拉取授权：

-   删除SDK
-   设备刷机
-   清除数据
-   恢复出厂设置

授权无效，需要联网激活，且会消耗新的授权：

-   授权有效期到期
-   新设备首次使用

免费测试：我们为每个接入方提供2个免费测试授权，用于SDK接入试用，测试授权在激活后的1个月内有效。

正式购买：客户根据业务需要购买相应时长的SDK使用授权，具体定价可联系阿里云客户经理咨询。

## 步骤一：SDK下载 {#section_7ib_eol_14a .section}

离线授权认证SDK可通过服务端接口进行下载，具体参看[下载离线授权认证SDK](cn.zh-CN/离线授权认证SDK/接入指南/服务端接入/下载离线授权认证SDK.md#)。

## 步骤二：在工程中导入SDK {#section_sy9_djc_j3s .section}

解压离线授权认证SDK包，将以下Android依赖包引入到您的应用工程中：

-   verifysdk-2.0.1.3-20190329-model-release.aar
-   SecurityBodySDK-external-release.aar
-   SecurityGuardSDK-external-release.aar

1.  设定依赖包所在的路径：

    ``` {#codeblock_ozm_x6h_mqv}
    apply plugin: 'com.android.application'
     repositories {
         flatDir {
             dirs '../libs'
         }
     }
    						
    ```

2.  设定引入的本地库所在路径，将需要引入的依赖包都放在../libs目录，包含所有需要的库。
3.  在gradle文件中引入以下需要的库依赖：

    ``` {#codeblock_3fj_uqn_7h2}
    compile (name:'verifysdk-2.0.1.3-20190329-model-release',ext:'aar')
    compile (name:'SecurityGuardSDK-external-release-5.4.121',ext:'aar')
     compile (name:'SecurityBodySDK-external-release-5.4.79',ext:'aar')
    						
    ```

    **说明：** 未来各依赖库的版本号会有所变化，实际版本号以下载到的SDK为准。


**关于SDK签名图片** 

-   解压已下载的离线授权认证SDK包，获得yw\_1222\_\*.jpg签名图片文件，该文件在SDK授权时需要使用。
-   把该图片文件导入到工程中res\\drawable\\目录，如果没有这个文件夹，请先在工程中创建，否则将无法正常工作。
-   如果在安卓工程打包时启用了shrinkResources true，还需要在keep.xml文件中添加以下内容：

    ``` {#codeblock_sck_wue_1en}
    <resources xmlns:tools="http://schemas.android.com/tools" tools:keep="@drawable/yw_1222_*" />
    ```

-   离线授权认证SDK会与APP包名（package name）+签名（keystore）绑定，所以若APP的package name或keystore有变化都需要重新下载SDK，若无变化，则不需要重新下载。

**关于 ABI 类型**

离线授权认证SDK 目前支持 armeabi、armeabi-v7a、arm64-v8a，请接入方按需在build.gradle中增加abifilters配置。例如，接入方仅需要支持其中 armeabi 和 arm64-v8a，则配置如下：

``` {#codeblock_fw5_ktd_1gx}
defaultConfig {
    ndk {
        abiFilters "armeabi", "arm64-v8a"
    }
}
				
```

## 步骤三：SDK初始化及相关配置 {#section_ufx_1qe_1ik .section}

接入方只需关注 SDK 提供的 VerifySDKManager 类，上层与 SDK 的交互都是通过 VerifySDKManager 进行调用。

在使用SDK前必须通过setNeedDeviceManage开启设备管理功能，且通过initWithToken方法进行初始化。

-   **设置是否开启设备管理功能** 

    接口名：setNeedDeviceManage

    接口描述：使用initWithToken初始化方法，需要开启设备管理功能。

    |名称|类型|描述|
    |--|--|--|
    |needDeviceManage|boolean|是否开启设备管理，true为开启，false为不开启。|

    示例代码：

    ``` {#codeblock_3ls_3hi_6a5}
    VerifySDKManager.getInstance().setNeedDeviceManage(true);
    ```

-   **使用授权key进行初始化** 

    接口名：initWithToken

    接口描述：使用授权key，初始化结果通过回调告知。

    |名称|类型|描述|
    |--|--|--|
    |context|Context|当前activity的上下文。|
    |token|String|激活使用的授权key，从实人认证服务端申请获得。|
    |initWithTokenCallback|InitWithTokenCallback|回调函数，获取初始化结果。|

    **说明：** 如果设备目录/sdcard/verify/verifysdk/ab.bin存在而且有效，可以使用token=””直接进行本地断网初始化。如果本地初始化失败，需要联网在线初始化，激活使用的授权key，从实人认证服务端CreateAuthKey接口获得，在线联网初始化会将最新的授权文件ab.bin下载到本地，完成SDK授权。

    示例代码：

    ``` {#codeblock_eg3_7vq_fs3}
    // 初始化结果字段
    private int regCode = -1;
    // 回调函数
    private InitWithTokenCallback initWithTokenCallback = new InitWithTokenCallback(){
        @Override
        public void onInitResult(int code) {
            regCode = code;
            Log.i("initData","return code: " + code);
        }
    };
    // activity onCreate方法中进行SDK初始化
    VerifySDKManager.getInstance().setNeedDeviceManage(true);
    VerifySDKManager.getInstance().initWithToken(getApplicationContext(),"",initWithTokenCallback);
    if(regCode !=0){
          // TODO 调用接入方服务端，服务端通过调用实人认证服务端接口CreateAuthKey获取
          // String token = 
    VerifySDKManager.getInstance().initWithToken(getApplicationContext(),token,initWithTokenCallback);
      }                        
    ```

    SDK提供相关设置函数，可以修改人脸检测的最小尺寸、是否开启翻拍检测、是否开启红外活体检测、设置翻拍检测阈值、设置红外活体阈值、设置人脸检索匹配阈值等，接入方可以按需设置。

-   **设置最小检测人脸** 

    接口名：setMinFaceDetectSize

    接口描述：设置最小检测人脸，图像中的人脸大小超过该值时，才能检测到人脸，否则无法检测到人脸。

    |名称|类型|描述|
    |--|--|--|
    |minFaceDetectSize|float|人脸检测的最小尺寸，取值范围0-1，定义为占图像min\(宽，高）的比例，默认值0.1。|

    示例代码：

    ``` {#codeblock_8of_zzo_4wn}
    VerifySDKManager.getInstance().setMinFaceDetectSize(0.05f);
    ```

-   **设置人脸位置有效区域边界** 

    接口名：setBorders

    接口描述：设置人脸位置有效区域边界。

    |名称|类型|描述|
    |--|--|--|
    |leftBorder|float|人脸位置有效区域的左边界，定义为占旋转后图像宽度的比例，默认值0.1。|
    |rightBorder|float|人脸位置有效区域的右边界，定义为占旋转后图像宽度的比例，默认值0.9。|
    |topBorder|float|人脸位置有效区域的上边界，定义为占旋转后图像高度的比例，默认值0.1。|
    |bottomBorder|float|人脸位置有效区域的下边界，定义为占旋转后图像高度的比例，默认值0.9。|

    示例代码：

    ``` {#codeblock_o4c_yxc_xee}
    VerifySDKManager.getInstance().setBorders(0.1f,0.9f,0.1f,0.9f);
    ```

-   **设置是否开启离线翻拍检测** 

    接口名：setNeedRecapCheck

    接口描述： 设置是否开启离线翻拍检测。

    |名称|类型|描述|
    |--|--|--|
    |needRecapCheck|boolean|是否开启离线翻拍检测。|

    示例代码：

    ``` {#codeblock_ovc_2he_u0e}
    VerifySDKManager.getInstance().setNeedRecapCheck(true);
    ```

-   **设置是否开启红外防彩色图片翻拍检测** 

    接口名：setNeedNirSeniorRecapCheck

    接口描述：设置是否开启红外防彩色图片翻拍检测。

    |名称|类型|描述|
    |--|--|--|
    |needNirSeniorRecapCheck|boolean|是否开启红外防彩色图片翻拍检测。|

    示例代码：

    ``` {#codeblock_oge_ps6_juc}
    VerifySDKManager.getInstance().needNirSeniorRecapCheck(true);
    ```

-   **设置是否需要红外活体能力** 

    接口名：setNeedNirLiveness

    接口描述：设置是否需要红外活体能力，若设置需要红外活体能力，那么setNirFrameW、setNirFrameH、setNirAngle、setRgbAngle为必要参数，最后再调用init方法进行生效。

    |名称|类型|描述|
    |--|--|--|
    |needNirLiveness|boolean|是否需要红外活体能力。|

    示例代码：

    ``` {#codeblock_yvu_905_2n5}
    VerifySDKManager.getInstance().needNirLiveness(true).setNirAngle(0).setNirFrameH(480).setNirFrameW(640).setRgbAngle(0).init(getApplicationContext());
    ```

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

    示例代码：

    ``` {#codeblock_yl4_z7d_543}
    VerifySDKManager.getInstance().setRecapThreshold(80f);
    ```

-   **设置红外活体阈值** 

    接口名：setNirScoreThreshold

    接口描述：设置红外活体阈值，目前默认分值是0f，取值范围0~1，接入方可根据业务实际需要进行阈值调整。

    |名称|类型|描述|
    |--|--|--|
    |nirScoreThreshold|float|设置红外活体阈值，默认值0f。|

    示例代码：

    ``` {#codeblock_hei_wwh_ziy}
    VerifySDKManager.getInstance().setNirScoreThreshold(0f);
    ```

-   **设置人脸匹配阈值** 

    接口名：setFaceMatchThreshold

    接口描述：设置人脸匹配阈值，目前默认分值是0.44f，取值范围0~1，接入方可根据业务实际需要进行阈值调整。

    |名称|类型|描述|
    |--|--|--|
    |faceMatchThreshold|float|设置人脸匹配阈值，默认值0.44f。|

    示例代码：

    ``` {#codeblock_hqw_2sr_ur6}
    VerifySDKManager.getInstance().setFaceMatchThreshold(0f);
    ```


## 步骤四：SDK用户库操作 {#section_6zj_ic7_5cq .section}

初始化成功后，如果要使用SDK的人脸1：N检索能力，需要进行相应用户库操作，向人脸库中添加人脸图片，否则人脸检索将无法匹配。

如果要使用SDK的人脸1:1比对能力，则可以不关注用户库操作，直接参考[人脸识别](#section_fpd_k2d_8fc)部分的说明。

-   **加载用户库** 

    接口名：loadUserLib

    接口描述：用于初始时加载全量用户库。

    |名称|类型|描述|
    |--|--|--|
    |listener|VerifyLibEventListener|用户库操作回调。|

    示例代码：

    ``` {#codeblock_q97_23f_owx}
    // 加载用户数据到内存
    VerifySDKManager.getInstance().loadUserLib(verifyLibEventListener);
    						
    ```

-   **添加本地用户照片** 

    接口名：addUser

    接口描述：添加本地用户照片到用户库，操作结果以回调方式通知，完成后不必重新loadUserLib。添加后，用户照片数据会存储在设备本地数据库，适用于人脸底库数量不大或设备存储空间相对充裕的情况。

    **说明：** 使用该函数添加用户照片，优势是当人脸算法模型更新升级时，接入方不需要再重新入库一次用户照片，SDK会根据本地已有的照片数据重新提取特征值，支持在新模型下的运行；劣势是照片数据会占用一定的设备存储空间。

    |名称|类型|描述|
    |--|--|--|
    |isSync|boolean|是否同步调用，建议上层调用入库自己管理线程，该参数传true，否则传false。|
    |id|String|用户ID，需要保证唯一性。|
    |type|int|生物特征类型，目前只支持Type.BIOLOGY\_FACE。|
    |featureData|byte\[\]|用户照片的byte源数据，支持jpg、png格式。|
    |verifyLibEventListener|VerifyLibEventListener|用户库操作回调。|

    示例代码：

    ``` {#codeblock_0ic_s4w_n92}
    VerifySDKManager.getInstance().addUser(true, String.valueOf(i), Type.BIOLOGY_FACE,FileUtil.getFileBuffer(path),verifyLibEventListener);
    ```

    接口名：addUserWithoutFeatureData

    接口描述：添加本地用户照片，操作结果以回调方式通知。添加后，用户照片数据不会存储在设备本地数据库，适用于人脸底库数量大或设备存储空间有限的情况。

    **说明：** 使用该函数添加用户照片，优势是不占用设备存储空间；劣势是当人脸算法模型更新升级时，本地已有的特征库无法适配新模型，SDK会运行失败，需要接入方再重新入库一次用户照片，以便新模型重新提取特征值，确保SDK正常运行。

    **说明：** 使用该函数的接入方，需要在onUserLibLoaded回调函数中处理errorCode=104的错误码，详见onUserLibLoaded调用示例代码。

    |名称|类型|描述|
    |--|--|--|
    |isSync|boolean|是否同步调用，建议上层调用入库自己管理线程，该参数传true，否则传false。|
    |id|String|用户ID，需要保证唯一性。|
    |type|int|生物特征类型，目前只支持Type.BIOLOGY\_FACE。|
    |featureData|byte\[\]|用户照片的byte源数据，支持jpg、png格式。|
    |verifyLibEventListener|VerifyLibEventListener|用户库操作回调。|

    示例代码：

    ``` {#codeblock_zic_zlu_4wh}
    VerifySDKManager.getInstance().addUserWithoutFeatureData(true, String.valueOf(i), Type.BIOLOGY_FACE,FileUtil.getFileBuffer(path),verifyLibEventListener);
    ```

-   **删除指定用户生物数据** 

    接口名：removeUser

    接口描述：删除指定用户生物数据，操作结果以回调方式通知，无需重新loadUserLib。

    |名称|类型|描述|
    |--|--|--|
    |isSync|boolean|是否同步调用，建议上层调用入库自己管理线程，该参数传true，否则传false。|
    |id|String|用户ID。|
    |verifyLibEventListener|VerifyLibEventListener|用户库操作回调。|

    示例代码：

    ``` {#codeblock_d4s_v4f_32m}
    VerifySDKManager.getInstance().removeUser(false,id,verifyLibEventListener);
    ```

-   **清空所有用户数据** 

    接口名：clearUserLib

    接口描述：清除本地用户库。

    |名称|类型|描述|
    |--|--|--|
    |isSync|boolean|是否同步调用，建议上层调用入库自己管理线程，该参数传true，否则传false。|
    |verifyLibEventListener|VerifyLibEventListener|用户库操作回调。|

    示例代码：

    ``` {#codeblock_6u6_6md_cvj}
    VerifySDKManager.getInstance().clearUserLib(false,verifyLibEventListener);
    ```

-   **单用户数据更新回调** 

    接口名：onSingleUserLibUpdate

    接口描述：用户库操作回调，单用户数据更新。

    |名称|类型|描述|
    |--|--|--|
    |id|String|用户ID。|
    |errorCode|int|错误码，0为正确。|

-   **用户库加载完成回调** 

    接口名：onUserLibLoaded

    接口描述：loadUserLib操作回调，用户库加载完成。

    |名称|类型|描述|
    |--|--|--|
    |errorCode|int|错误码，0为正确。 **说明：** 使用addUserWithoutFeatureData函数添加用户的接入方，需要特殊处理该回调函数errorCode=104的错误码，详见下方示例代码中说明。

 |

-   **用户库清除回调** 

    接口名：onUserLibEmpty

    接口描述：clearUserLib操作回调，用户库清除完成。

    |名称|类型|描述|
    |--|--|--|
    |errorCode|int|错误码，0为正确。|

    示例代码：

    ``` {#codeblock_64o_aia_2sw}
    // 用户库操作回调
    final VerifySDKManager.VerifyLibEventListener verifyLibEventListener = new VerifySDKManager.VerifyLibEventListener() {
            @Override
            public void onSingleUserLibUpdate(String id, final int errorCode) {
            //TODO 单用户更新操作完成后处理
            }
            @Override
            public void onBatchUserLibUpdate(int errorCode) {
            //TODO 批量用户更新，暂时不支持
            }
           @Override
            public void onUserLibLoaded(int errorCode) {
            //TODO loadUserLib操作完成后处理
            //特殊说明：使用addUserWithoutFeatureData函数添加用户的接入方，需要在该回调中特殊处理errorCode=104的错误码，操作步骤如下：
            //第一步：调用clearUserLib清除本地数据
            //第二步：接入方重新入库用户数据
            }
            @Override
            public void onUserLibEmpty(int errorCode) {
            //TODO clearUserLib操作完成后处理
            }
    };
    							
    ```


## 步骤五：SDK人脸识别 {#section_fpd_k2d_8fc .section}

**人脸1:1比对说明**

SDK支持实时视频流和人脸图片进行比对，这是最为常见的1：1对比类型。针对一张事先获取的图片（通常为身份证芯片照、证件照片等），与摄像头实时采集的符合条件的人脸图片进行比对。通常适用于有人值守的场景。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/154180/155894031144662_zh-CN.png)

**人脸1：N检索**

将需要识别的人脸图片注册到本地人脸库中，当有用户需要识别身份时，从视频流中实时采集符合条件的人脸图片，与人脸库中的人脸集合比对，得到检索结果。如果是无人值守的情况，建议可以开启翻拍检测或红外活体保障安全性。

如果在初始化时设置开启了翻拍检测或红外活体检测，则摄像头采集的人脸图片必须同时通过活体检测，才能进入人脸检索环节，任一活体检测未通过，都不会进行人脸检索。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/154180/155894031144663_zh-CN.png)

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

    示例代码：

    ``` {#codeblock_gnj_jmg_8et}
    //1:1匹配后的回调
    VerifySDKManager.FaceMatchWithImageListener faceMatchWithImageListener = new
    VerifySDKManager.FaceMatchWithImageListener() {
        @Override
        public void onMatchResult(FaceMatchResult faceMatchResult) {
         //TODO 与目标图片匹配后的处理
    }
    };
    //1:1比对调用
    VerifySDKManager.getInstance().doFaceMatchWithImage(data,width, height,cameraRotation,data, faceMatchWithImageListener)
    							
    ```

-   **人脸1:1比对结果回调** 

    接口名：onMatchResult

    接口描述： 人脸1:1比对结果回调，可获得相应比对分。

    |名称|类型|描述|
    |--|--|--|
    |result|FaceMatchResult|人脸1:1比对结果。|

    示例代码：见上述”人脸1:1比对调用”代码示例。

-   **人脸1:N检索调用** 
    -   接口名：feedPreviewFrame

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

        示例代码：

        ``` {#codeblock_71m_fqp_kyf}
        // 当未开启红外活体功能时，使用FaceDetectWithMatchListener类型做回调
        final VerifySDKManager.FaceDetectWithMatchListener
        faceDetectListener = new VerifySDKManager. FaceDetectWithMatchListener () {
                @Override
                public void onFaceDetected(byte[] data, final int width, final int height, int imageFormat, int imageAngle, int cameraRotation, final FaceInfo faceInfo) {
            //TODO 识别到人脸时
                }
                @Override
                public void onNofaceDetected(byte[] data, int width, int height, int imageFormat, int imageAngle, int cameraRotation) {
            //TODO 没有识别到人脸时
                }
                @Override
                public void onFaceMatched(byte[] data, int width, int height, int imageAngle, int cameraRotation, final FaceMatchResult matchResult, final long costTime) {
            //TODO 识别到人脸并在用户库中匹配用户
                }
                @Override
                public void onRecapDetected(byte[] data, int width, int height, int imageAngle, int cameraRotation, float recapScore) {
            //TODO 检查到人脸翻拍
                }
                @Override
                public void onFaceMoving(boolean isMoving) {
            //TODO 检查到人脸移动
                }
        //1：N检索接口调用，不带红外活体
        VerifySDKManager.getInstance().feedPreviewFrame(data,width, height,ImageFormat.NV21,degree,cameraRotation, faceDetectWithMatchListener);
        									
        ```

    -   接口名：feedPreviewFrameWithNir

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

        示例代码：

        ``` {#codeblock_0z9_62o_11y}
        //当开启红外检测时，使用NirFaceDetectListener类型做回调
        final VerifySDKManager.NirFaceDetectListener nirFaceDetectListener = new VerifySDKManager.NirFaceDetectListener() {
                @Override
                public void onFaceDetected(byte[] data, final int width, final int height, int imageFormat, int imageAngle, int cameraRotation, final FaceInfo faceInfo) {
            //TODO 识别到人脸时
                }
                @Override
                public void onNofaceDetected(byte[] data, int width, int height, int imageFormat, int imageAngle, int cameraRotation) {
            //TODO 没有识别到人脸时
                }
                @Override
                public void onFaceMatched(byte[] data, int width, int height, int imageAngle, int cameraRotation, final FaceMatchResult matchResult, final long costTime) {
            //TODO 识别到人脸并在用户库中匹配用户
                }
                @Override
                public void onRecapDetected(byte[] data, int width, int height, int imageAngle, int cameraRotation, float recapScore) {
            //TODO 检查到人脸翻拍
                }
                @Override
                public void onFaceMoving(boolean isMoving) {
            //TODO 检查到人脸移动
                }
                @Override
                public void onFaceDetectedInNIR(byte[] nirData, int width, int height, int nirAngle, NirFaceInfo nirFaceInfo){
                    //TODO 红外摄像头检查到人脸
                }
                @Override
                public void onNofaceDetectedInNIR(byte[] nirData, int width, int height, int nirAngle){
            //TODO 红外摄像头没有检查到人脸
                }
            };
        //1：N检索接口调用，带红外活体
        VerifySDKManager.getInstance().feedPreviewFrameWithNir(nirData,data,width, height,ImageFormat.NV21,degree,cameraRotation,nirFaceDetectListener);
        									
        ```

-   **人脸1：N检测到人脸回调** 
    -   接口名：onFaceDetected

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

        示例代码：见1：N检索调用代码。

    -   接口名：onFaceDetectedInNIR

        接口描述：红外帧数据中检测到人脸时，会回调该函数，调用方可根据此回调做相应处理。

        |名称|类型|描述|
        |--|--|--|
        |nirData|byte\[\]|红外帧数据。|
        |width|int|红外帧数据宽度。|
        |height|int|红外帧数据高度。|
        |nirAngle|int|图片旋转角度。|
        |nirFaceInfo|NirFaceInfo|检测到的红外人脸信息在帧中的坐标，宽高。|

        示例代码：见1：N检索调用代码。

-   **人脸1：N未检测到人脸回调** 
    -   接口名：onNofaceDetected

        接口描述：RGB帧数据中未检测到人脸，可以在这个回调中更新UI，移除之前绘制的人脸框。

        |名称|类型|描述|
        |--|--|--|
        |data|byte\[\]|RGB帧数据。|
        |width|int|RGB帧数据宽度。|
        |height|int|RGB帧数据高度。|
        |imageFormat|int|帧数据格式，Android默认选择ImageFormat.NV21。|
        |imageAngle|int|图片旋转角度。|
        |cameraRotation|int|相机旋转角度。|

        示例代码：见1：N检索调用代码。

    -   接口名：onNofaceDetectedInNIR

        接口描述：红外帧数据中未检测到人脸。

        |名称|类型|描述|
        |--|--|--|
        |nirData|byte\[\]|红外帧数据。|
        |width|int|红外帧数据宽度。|
        |height|int|红外帧数据高度。|
        |nirAngle|int|图片旋转角度。|

        示例代码：见1：N检索调用代码。

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

    示例代码：见1：N检索调用代码。

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

    示例代码：见1：N检索调用代码。

-   **人脸移动时的回调** 

    接口名：onFaceMoving

    接口描述：检测到人脸在移动时，会回调该函数。

    |名称|类型|描述|
    |--|--|--|
    |isMoving|boolean|人脸是否移动。|

    示例代码：见1：N检索调用代码。


## SDK错误码 {#section_r6g_wx2_vyr .section}

|错误信息|错误描述|错误码|
|----|----|---|
|VERIFYSDK\_ERR\_CODE\_BAD\_PARAM|参数错误。|100|
|VERIFYSDK\_ERR\_CODE\_ACCESS\_WORK\_FAILED|访问工作路径错误。|101|
|VERRORCODE\_NEED\_MANUAL\_UPDATE|人脸特征库与算法不匹配，SDK启动失败。|104|
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
|VERIFYSDK\_ERR\_CODE\_SECURITY\_TOKEN\_SOCKET\_TIMEOUT|获取激活key超时，即初始化失败或未完成。|7001|
|VERIFYSDK\_ERR\_CODE\_GET\_STATIC\_DATA\_STORE\_COMP|SDK安全组件获取appkey失败。|7003|
|VERIFYSDK\_ERR\_CODE\_GET\_SECURE\_SIGNATURE\_COMP|SDK安全组件获取加签失败。|7004|
|VERIFYSDK\_ERR\_CODE\_SG\_SECEXCEPTION|SDK安全组件异常。|7002|
|VERIFYSDK\_ERR\_CODE\_GET\_LICENSE\_INFO\_TOKEN\_NULL|带授权key激活时，key为空。|8001|
|VERIFYSDK\_ERR\_CODE\_GET\_LICENSE\_INFO\_NATIVE\_FAILED|获取授权失败。|8002|
|VERIFYSDK\_ERR\_CODE\_GET\_LICENSE\_CHARSETNAME\_EXCEPTION|授权key编码转换出错。|8003|
|VERIFYSDK\_ERR\_CODE\_USER\_LIB\_NOT\_LOADED|人脸库还未初始化完成。|9001|
|VERIFYSDK\_ERR\_CODE\_UNKNOWN|人脸模块未知错误。|9999|
|VERIFYSDK\_ERR\_CODE\_ACTIVATE\_LICENSE\_REQUEST\_FAILED|请求失败，激活SDK失败。|10001|
|VERIFYSDK\_ERR\_CODE\_ACTIVATE\_LICENSE\_DEVICE\_NOT\_AUTH|未授权，激活SDK失败。|10002|
|VERIFYSDK\_ERR\_CODE\_ACTIVATE\_LICENSE\_DEVICE\_AUTH\_EXPIRED|授权到期失效，激活SDK失败。|10003|
|VERIFYSDK\_ERR\_CODE\_ACTIVATE\_LICENSE\_DATA\_NULL|授权文件为空，激活SDK失败。|10004|

