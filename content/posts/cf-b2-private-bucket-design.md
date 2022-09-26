---
title: "Cloudflare + Blackblaze b2 私有 bucket 搭建图床"
date: 2022-08-22T16:46:28+08:00
tags: 
    - cloudflare
    - blackblaze
    - image
categories: 
- design
draft: false

---

## 总体流程

{{< b2img "/cf_b2_private_bucket_design.jpg" "总体流程时序图" >}}

## 流程说明

1. 在 [blackblaze](https://www.backblaze.com/) 使用用户或应用 `keyId`,`key` 调用 [b2_authorize_account](https://www.backblaze.com/b2/docs/b2_authorize_account.html) 接口取得 `authorizationToken`， `apiUrl`， `downloadUrl`
2. 继续在 `blackblaze`  使用上一步取得的 `authorizationToken`， `apiUrl` 调用 [b2_get_download_authorization](https://www.backblaze.com/b2/docs/b2_get_download_authorization.html) 接口取得 `authorizationToken`
3. 在 [cloudflare](https://dash.cloudflare.com/) Worker > KV > 创建命令空间取得对应 id
4. 在[cloudflare](https://dash.cloudflare.com/) 使用 api token 将第 1 步的 `downloadUrl` 和 第 2 步 `authorizationToken` 存第 3 步创建的 KV 存储中
5. 在 [cloudflare](https://dash.cloudflare.com/) 上传脚本到 worker 并装其与第 3 步创建的 KV 存储绑定，这样就能在脚本中使用存入的数据访问 `blackblaze` 私有桶中的图片
6. 由于第 2 步获取到的 `token` 有效期最长为 7 天(第二个参考资料，未验证)， 所以需要制定合理的定时任务计划
7. token 刷新程序 https://github.com/lostsquirrel/b2auth-cfworker-go，
8. worker 脚本 https://github.com/lostsquirrel/b2worker，基本功能实现，

## TODO

- token 刷新程序
    - 基本还未完成
    - 添加使用文档
    - 减少手动操作

- worker 脚本
    - 添加异常处理，
    - 防盗链方案，
    - 增加配置减少手动操作
## 遇到的坑

- cloudflare worker 在使用 KV 存储时需要在函数中传入 `request` 外的另一个参数 `env`, 通过 `env.KVNAME.get('my-key')` 在能访问到其中的内存，否则会报 `KVNAME` 不存在
- 使用 `wrangler` 本地调度 worker 脚本时，不使用代理访问不到，使用代理会报 ssl 版本错误，暂没办法解决，目前使用 gitpod 暂时规避

## 参考资料

- https://mitsea.medium.com/backblaze-b2-cloudflare-%E6%90%AD%E5%BB%BA%E5%9B%BE%E5%BA%8A-94afa1f762dc
- https://github.com/Backblaze/gists/blob/b5a73fef3e4d3ae14121decc1b364008acc18515/b2AuthorizeCfWorker.py#L53

## 注
    参考资料2 中使用将 `token` 直接写入脚本然后，第次上传新脚本的方式，这样需要每次更新脚本，但避免了引入使用 KV 的复杂度