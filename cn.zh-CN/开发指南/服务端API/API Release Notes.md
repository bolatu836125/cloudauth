# API Release Notes {#reference857 .reference}

下表记录了历次API更新说明。

|API版本号|SDK版本号|发布说明|
|------|------|----|
|2018-08-07|1.1.4|更新内容如下：-   版本 1.1.3 的问题修复。
-   发起认证请求接口（GetVerifyToken）增加一个可选入参（VerifyConfigs）。

|
|2018-07-03|1.1.3|更新内容如下：-   发起认证请求接口（GetVerifyToken），增加认证页面 URL（CloudauthPageUrl）字段。
-   增加人脸属性检测接口（DetectFaceAttributes）。

**说明：** 此版本类库发布存在问题，获取认证资料接口（GetMaterials）的证件有效期起始日期（IdCardStartDate）、民族（EthnicGroup）字段不能正常返回，请改用 1.1.4 版本。

|
|2018-05-04|1.1.2|更新内容如下：获取认证资料接口（GetMaterials），增加身份证 OCR 识别的证件有效期起始日期（IdCardStartDate）、民族（EthnicGroup）字段。|
|2017-11-17|1.1.1|更新内容如下：查询认证状态接口（GetStatus），增加认证不通过原因（AuditConclusions）字段。|
|2017-10-10|1.1.0|更新内容如下：-   人脸比对验证接口CompareFaces首次发布。
-   查询认证状态接口GetStatus首次发布。
-   发起认证请求接口GetVerifyToken首次发布。
-   提交认证资料接口SubmitMaterials首次发布。
-   获取认证资料接口GetMaterials首次发布。

|

