---
title: "Openresty WAF via p0pr0ck5/lua-resty-waf"
description: "使用开源的WAF规则引擎简单搭一个Openresty WAF DEMO"
lead: ""
date: 2021-04-21T10:23:51+08:00
lastmod: 2021-04-21T10:23:51+08:00
weight: 50
tags:
 - WAF
 - openresty
---

## Environment

1. Ubuntu 18.04
2. Openresty-1.19.3.1

## GO

1. Get Openresty

  ```bash
  $ curl -O https://openresty.org/download/openresty-1.19.3.1.tar.gz
  $ tar xf openresty-1.19.3.1.tar.gz
  $ cd openresty-1.19.3.1.tar.gz
  ```

2. Install Depends

  ```bash
  $ sudo apt udpate
  $ apt install -y gcc make liblua5.1 libpcre3-dev libssl-dev zlib1g-dev
  ```

3. Compile Openresty

  ```bash
  $ ./configure --prefix=/usr/local/openresty --with-luajit --with-http_stub_status_module --with-pcre --with-pcre-jit
  $ make
  $ sudo make install
  $ ldd bin/openresty
          linux-vdso.so.1 (0x00007fff2b9df000)
          libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007ff8091ee000)
          libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007ff808fcf000)
          libcrypt.so.1 => /lib/x86_64-linux-gnu/libcrypt.so.1 (0x00007ff808d97000)
          libluajit-5.1.so.2 => /usr/local/openresty/luajit/lib/libluajit-5.1.so.2 (0x00007ff808b16000)
          libpcre.so.3 => /lib/x86_64-linux-gnu/libpcre.so.3 (0x00007ff8088a4000)
          libssl.so.1.1 => /usr/lib/x86_64-linux-gnu/libssl.so.1.1 (0x00007ff808617000)
          libcrypto.so.1.1 => /usr/lib/x86_64-linux-gnu/libcrypto.so.1.1 (0x00007ff80814c000)
          libz.so.1 => /lib/x86_64-linux-gnu/libz.so.1 (0x00007ff807f2f000)
          libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007ff807b3e000)
          /lib64/ld-linux-x86-64.so.2 (0x00007ff8097d4000)
          libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007ff8077a0000)
          libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007ff807588000)
  ```

4. Get Waf Lua Script ( Via p0pr0ck5/lua-resty-waf to test )

  ```bash
  $ git clone https://github.com/p0pr0ck5/lua-resty-waf.git
  $ cd lua-resty-waf
  $ sudo apt install python luarocks
  $ git submodule update --init --recursive
  $ make
  # FAQ: https://github.com/p0pr0ck5/lua-resty-waf/pull/334
  $ sudo PATH=/usr/local/openresty/bin:$PATH make install
  # Makefile default openresty prefix is /usr/local/openresty
  ```

5. Configure Nginx

  - nginx.conf
    > https://github.com/p0pr0ck5/lua-resty-waf#synopsis
    >
    > https://github.com/p0pr0ck5/lua-resty-waf#mode
    ```text
    http {
        lua_package_path "...";

        init_by_lua_block {
            -- use resty.core for performance improvement, see the status note above
            require "resty.core"

            -- require the base module
            local lua_resty_waf = require "resty.waf"

            -- perform some preloading and optimization
            lua_resty_waf.init()
        }

        server {
            location / {

                access_by_lua_block {
                    local lua_resty_waf = require "resty.waf"

                    local waf = lua_resty_waf:new()

                    -- define options that will be inherited across all scopes
                    waf:set_option("debug", true)
                    waf:set_option("mode", "ACTIVE")

                    -- this may be desirable for low-traffic or testing sites
                    -- by default, event logs are not written until the buffer is full
                    -- for testing, flush the log buffer every 5 seconds
                    --
                    -- this is only necessary when configuring a remote TCP/UDP
                    -- socket server for event logs. otherwise, this is ignored
                    waf:set_option("event_log_periodic_flush", 5)

                    -- run the firewall
                    waf:exec()
                }

                header_filter_by_lua_block {
                    local lua_resty_waf = require "resty.waf"

                    -- note that options set in previous handlers (in the same scope)
                    -- do not need to be set again
                    local waf = lua_resty_waf:new()

                    waf:exec()
                }

                body_filter_by_lua_block {
                    local lua_resty_waf = require "resty.waf"

                    local waf = lua_resty_waf:new()

                    waf:exec()
                }

                log_by_lua_block {
                    local lua_resty_waf = require "resty.waf"

                    local waf = lua_resty_waf:new()

                    waf:exec()
                }
            }

            ....
        }
    }

    ```

6. Test WAF

    ```bash
    $ curl -IL "http://localhost?a=alert(1)"
    HTTP/1.1 403 Forbidden
    Server: openresty/1.19.3.1
    Date: Wed, 21 Apr 2021 09:44:57 GMT
    Content-Type: text/html
    Content-Length: 159
    Connection: keep-alive

    $ curl -IL "http://localhost?a=1"
    HTTP/1.1 200 OK
    Server: openresty/1.19.3.1
    Date: Wed, 21 Apr 2021 09:45:01 GMT
    Content-Type: text/html
    Content-Length: 1097
    Last-Modified: Wed, 21 Apr 2021 07:43:55 GMT
    Connection: keep-alive
    ETag: "607fd7bb-449"
    Accept-Ranges: bytes

    $ curl -IL "http://localhost"
    HTTP/1.1 200 OK
    Server: openresty/1.19.3.1
    Date: Wed, 21 Apr 2021 09:45:03 GMT
    Content-Type: text/html
    Content-Length: 1097
    Last-Modified: Wed, 21 Apr 2021 07:43:55 GMT
    Connection: keep-alive
    ETag: "607fd7bb-449"
    Accept-Ranges: bytes
    ```

