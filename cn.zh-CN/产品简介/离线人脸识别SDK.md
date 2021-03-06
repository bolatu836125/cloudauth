# 离线人脸识别SDK {#concept_1443667 .concept}

阿里云实人认证提供离线人脸识别SDK，帮助您实现在弱网或离网环境下的人脸认证。

**说明：** 离线人脸识别 SDK 的实际应用效果与硬件配置和设备所处环境密切相关，目前只通过项目合作方式输出，前期需要评估方案可行性。

## 什么是离线人脸识别SDK {#section_k8s_j29_39m .section}

离线人脸识别SDK在Android 设备终端集成，支持在弱网或离线环境下的人脸认证应用。离线人脸识别SDK包含人脸检测、活体检测、人脸1:1比对、人脸1：N检索、人脸库管理等能力，并全部离线化、本地化。SDK在授权激活后，可完全在无网环境下工作，所有数据皆在Android设备本地运行处理，可根据业务需要进行灵活的上层业务开发。

## 核心功能 {#section_4al_jnl_e8l .section}

离线人脸识别SDK提供以下核心能力：

-   人脸检测：输入摄像头帧数据，返回检测到的人脸坐标位置。
-   人脸采集：输入摄像头帧数据，返回符合质量的人脸位置。
-   RGB可见光活体检测：针对视频流帧数据，通过采集人像的破绽（摩尔纹、成像畸形等）来判断目标对象是否为活体，可有效防止屏幕二次翻拍和复印二次翻拍等作弊攻击。
-   NIR近红外活体检测：针对视频流帧数据，利用近红外成像原理，实现夜间或无自然光条件下的活体判断。
-   1:1人脸比对：本地1:1人脸比对功能，针对一张预设的图片，和摄像头实时采集的符合条件的人脸进行比对，比对是否为同一人。
-   1：N人脸检索：本地1：N人脸检索功能，针对摄像头实时采集的符合条件的人脸，从本地人脸底库中检索是否存在此人的信息。
-   人脸库管理：提供本地人脸底库的新增、删除等管理维护功能。

## 人脸算法对接入设备的要求 {#section_zq8_ggz_eys .section}

-   硬件设备
    -   系统：Android 4.4及以上
    -   系统类型：32位、64位
    -   处理器：4核，1.45GHz及以上
    -   内存：1G及以上
-   镜头
    -   分辨率：RGB镜头1080p， 红外镜头800X600及以上（使用红外功能时需要关注）
    -   固定30帧，无拖影，RGB镜头建议是宽动态
    -   照片清晰可辨认，人脸区域宽度大于100像素，人脸角度yaw ≤ ±15°， pitch ≤ ±15°

