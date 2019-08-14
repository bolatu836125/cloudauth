# SDK UI自定义说明 {#reference_1426509 .reference}

SDK中的活体环节UI，我们支持了自定义能力，具体操作方法请参见本文说明。使用SDK UI自定义前，请确保您已经在实人认证控制台下载安卓或iOS的无线端SDK。

## 活体环节自定图片 {#section_l2z_uup_m9p .section}

操作步骤

-   Android
    1.  从实人认证控制台下载安卓无线SDK，如果本地之前已经下载过无线SDK，则用新下载SDK里的最新版FaceLivenessOpen替换本地旧版的FaceLivenessOpen。
    2.  将需要替换的图片放在assets/facetheme/下。
-   iOS
    1.  从实人认证控制台下载iOS无线SDK，如果本地之前已经下载过无线SDK，则用新下载SDK里的FaceLivenessOpen.framework替换本地旧版的FaceLivenessOpen.framework。
    2.  将需要替换的图片放在FaceTheme.bundle的images目录下。
    3.  将FaceTheme.bundle加入项目中。

原始图片说明

原始图片可在FaceTheme.bundle中的images目录中找到，说明如下：

-   屏幕下方按钮：face\_nav\_button.png
-   屏幕顶端关闭按钮：face\_top\_back.png
-   屏幕顶端声音按钮：
    -   face\_top\_sound\_on.png（声音打开）
    -   face\_top\_sound\_off.png（声音关闭）
-   中间圆内圈-开始时底图：face\_circle\_inner\_bg.png
-   中间圆内圈-检测到人脸时旋转图：face\_circle\_inner\_detected.png
-   中间圆内圈-失败图：face\_circle\_inner\_fail.png
-   中间圆内圈-成功图：face\_circle\_inner\_ok.png
-   中间圆内圈-处理中时旋转图：face\_circle\_inner\_processing.png
-   中间圆外圈-底图：face\_circle\_outer\_bg.png
-   中间圆外圈-旋转图：face\_circle\_outer\_detected.png
-   提示动画圆圈底图：face\_guide\_bg.png

## 活体环节自定文案颜色 {#section_qhn_xj1_ceg .section}

操作步骤

-   Android
    1.  在theme.json修改颜色。
    2.  将theme.json放在assets/facetheme/下。
-   iOS
    1.  在theme.json修改颜色。
    2.  将theme.json放在FaceTheme.bundle的images目录下。
    3.  将FaceTheme.bundle加入项目中。

原始配置说明

原始theme.json可在FaceTheme.bundle中的images目录中找到， 说明如下：

-   Key说明
    -   错误文字颜色（检验不通过...）：color\_error\_text
    -   屏幕下方按钮文字颜色：color\_button\_text
    -   动作提示语文字颜色（请张下嘴...）：color\_prompt\_text
    -   导航首页提示文字颜色（请本人操作...）：color\_nav\_text
    -   悬浮提示文字颜色（请把脸部移入框中...）：color\_tip\_text
-   值说明：值为10进制整型，由16进制颜色（0xAARRGGBB）转化成10进制的整型 ，如0xFFFF5000对应4294922240。
-   theme.json示例

    ``` {#codeblock_sj7_21f_8ho}
    { 
    "color_error_text":4294922240,  
    "color_button_text":4294967295,  
    "color_prompt_text":4281545523, 
    "color_nav_text":4281545523,    
    "color_tip_text":4282204485
    }
    ```


