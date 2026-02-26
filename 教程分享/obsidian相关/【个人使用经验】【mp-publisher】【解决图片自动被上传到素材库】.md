# 个人使用经验

## 解决图片自动被上传到素材库（个人不喜欢）

【现象】：如果要发布的文章中含有图片，会自动将此图片上传到 `微信公众号的素材库`，这样会影响我后续选择封面，故不想自动上传到素材库

<img width="1554" height="477" alt="Image" src="https://github.com/user-attachments/assets/9ae0e597-9002-463e-95f0-0b1a2e949968" />

* 这两个图片都是发布的文章中的图片，被自动上传到素材库了

【解决方法】

此现象产生的原因在于：点击发布后，会调用 `上传永久素材` 接口，自动将图片上传到素材库

【接口说明】[[素材管理 / 永久素材 / 上传永久素材](https://developers.weixin.qq.com/doc/service/api/material/permanent/api_addmaterial.html)](https://developers.weixin.qq.com/doc/service/api/material/permanent/api_addmaterial.html)

在 `main.js` 中查看

<img width="1929" height="720" alt="Image" src="https://github.com/user-attachments/assets/6c255e90-d77f-4c42-807e-411a85ebbf3f" />

我们只需将此接口改为 `新增临时素材` 接口即可（临时素材不会上传到素材库，但 media_id 存在，三天后会自动删除）

<img width="1448" height="618" alt="Image" src="https://github.com/user-attachments/assets/71c001ad-6b86-410e-8eb3-b5c6211f4208" />

【接口说明】[[素材管理 / 临时素材 / 新增临时素材](https://developers.weixin.qq.com/doc/service/api/material/temporary/api_uploadtempmedia.html)](https://developers.weixin.qq.com/doc/service/api/material/temporary/api_uploadtempmedia.html)