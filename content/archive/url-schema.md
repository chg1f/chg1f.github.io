---
title: "URI Schema"
description: ""
tags:
- uri
TODO:
---

```
<SCHEME>://<NETLOC><PATH>?<PARAMS>#<FRAGMENT>

<NETLOC> => <HOST>:<PORT>
<HOST> => <DOMAIN>/<IPv4>/<IPv6>
<DOMAIN> => <SUBDOMAIN>.<FLD>
<FLD> => #.<SLD>.<TLD>

e.g. www.example.com
TLD => com.
SLD => example.com.
FLD => example.com.
e.g. www.example.com.cn
TLD => cn.
SLD => com.cn.
FLD=> example.com.cn.

```

|part|description|
|:-:|:-|
| TLD | Top level domain () |
| gTLD | |
| SLD | Second level domain |
| FLD | Free level domain |

> REF: [List of Top-Level Domains](https://data.iana.org/TLD/tlds-alpha-by-domain.txt)

// TODO:
