---
title: "用了半个下午解决的一对引号的问题"
date: 2024-03-18T16:55:53+08:00
summary: 在调用 cloudflare worker kv API 传递参数时由于一个 value 值是数据导致调用失败
tags:

- blog
- cloudflare
- worker
- kv
categories:
- troubleshot
draft: false
---

## 源头

由于本博客头部的名言警句的生成逻辑比较单一，希望提供[更丰富的功能]({{<ref "todo">}})，因此将名言警句通过调用 Cloudflare API
写入 Worker KV 存储中, [代码](https://github.com/lostsquirrel/quotes)如下

```py
    path = f"/client/v4/accounts/{config.CF_ACCOUNT}/storage/kv/namespaces/{config.CF_KV_NS_ID}/bulk"
    url = f"https://api.cloudflare.com{path}"
    headers = {
        "Authorization": f"Bearer {config.CF_API_TOKEN}",
        "Content-Type": "application/json",
    }
    resp = requests.put(url, headers=headers, json=data, timeout=30)
```

过程中不管如何修改代码，使用原生请求，都会调用失败，无法解析请求数据，查看官方文档

```sh
curl --request PUT \
  --url https://api.cloudflare.com/client/v4/accounts/account_id/storage/kv/namespaces/namespace_id/bulk \
  --header 'Authorization: Bearer undefined' \
  --header 'Content-Type: application/json' \
  --data '[
  {
    "base64": false,
    "expiration": 1578435000,
    "expiration_ttl": 300,
    "key": "My-Key",
    "metadata": {
      "someMetadataKey": "someMetadataValue"
    },
    "value": "Some string"
  }
]'
```
尝试本地数据, 并只保留一个对象
```sh
curl --request PUT \
  --url https://api.cloudflare.com/client/v4/accounts/account_id/storage/kv/namespaces/namespace_id/bulk \
  --header 'Authorization: Bearer undefined' \
  --header 'Content-Type: application/json' \
  --data '[
  {

    "key": "total",
    "value": 35
  }
]'
```
发现错误依旧，问题就在这里，仔细对比了一下，猜测 value 的值需要字符串，修改为 `"35"` 请求成功了，回头来看文档

```

`value` string
A UTF-8 encoded string to be stored, up to 25 MiB in length.

`<= 26214400 characters`
Example: Some string
```

还是文档没看仔细