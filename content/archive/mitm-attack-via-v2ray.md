---
title: "MITM Attack Via V2ray"
date: 2020-03-28
categories:
- note
tags:
- hacker
---

> 前两天有新闻JD和GITHUB被劫持，和最近生活中有些情况令我想到关于劫持页面重导向试试实现(其实想指定情况下在我手机上劫持微信跳转页面到我自己的页面下

## 环境
1. V2Ray 4.22.1 (V2Fly, a community-driven edition of V2Ray.) Custom (go1.13.8 linux/amd64)

## 实现
1. v2配置
设置outbounds规则，设置劫持转发的目标点，在routing设置转发

> https://www.v2fly.org/chapter_02/protocols/freedom.html
```json
    "outbounds": [
        {
            "protocol": "freedom",
            "settings": {
                "redirect": "127.0.0.1:58885"
            },
            "tag": "mitm"
        }
    ],
```
> https://www.v2fly.org/chapter_02/03_routing.html
```json
    "routing": {
        "rules": [
            {
                "domain": [
                    "<劫持页面>"
                ],
                "outboundTag": "mitm",
                "type": "field"
            }
        ]
```

2. 写了个简单劫持
指定了在固定情况下发生劫持..其他情况正常

```python
@app.route(path, methods=["GET"])
def hpass():
    if query == parse_qs(request.query_string):
        return page, 200
    url = list(urlsplit(request.url))
    url[1] = netloc
    url = urlunsplit(url)
    resp = requests.get(url)
    return resp.content, resp.status_code


if __name__ == "__main__":
    basicConfig(level=DEBUG)
    app.run(port=58885,
            ssl_context=('./signature/device.crt',
                         './signature/device.key'))
```

3. 最后..
因为HTTPS关系,劫持成功了,自己的小实验失败了,因为证书,之后自签也无法通过,也是预料之内..就这样吧,挺好玩的
