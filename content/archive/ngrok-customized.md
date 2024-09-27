---
title: "Ngrok Customized"
date: 2019-11-07
categories:
- note
tags:
- ngrok
---



## client



1.  Run-SetupModel-newClientModel建立ngrokd-ngroko信道

   > 好像無加密
2.  ctl-clientModel.Run-control開始使用model向ngrokd建立連接

   > newClientModel??為什么給configPath不是config,因為服务器給出新authToken更新
3.  clientModel.update更新control

   > tunnel下分拆可能會引到重復NewTunnel
   > Proxy Agent
   > SaveAuthToken保存完Token不更新內存中的conifg???
4. asset脚本會先把asset文件的數据生成assets_release.go二進制導入版,然后編譯時直接放進程序中,在請求時會直接把路徑換成已轉二進制數据寫入

  > ngrok->ngrokd
  > <-msg.Auth驗証建立信道
  > ->msg.AuthResp請求結果
  > <-msg.ReqTunnel請求建立Tunnel
  > ->NewTunnel註冊結果
  > **
  > ->Pong心跳
  > **
  > ->ReqProxy請求
  > #<-如果有設HTTPPROXY測轉發連接,失敗結束
  > <-msg.ReqProxy确認
  > ->msg.StartProxy服务器确認返回URL比對自已URL
  > ###

5. Control

6. ClientModel
  ###ENV
  DEBUG



## server



1. net.Listen-conn.Listen-http.startHttpListener
2. tunnelListener開启註冊監听
!TODO: 加密
!TODO: HTTP信道
!TODO: 一個服务多個同協議
3. ctl.manager核心
4. ctl.in讀入ctl,ctl.out ctl寫出???有點困暈
5. ctl.stopper等待其他gorounit shutdown信号

6. 轉發請求dstip會是進入网卡內网地址,註冊請求dstip會是127.0.0.1/0.0.0.0,或者知道服务器主机請況下設的某网卡內网地址,
0.0.0.0可以任何地址,127.0.0.1只能本地->先試用轉發DIP失敗再0.0.0.0->失敗,因為127.0.0.1本机回環,DIP亦會回環,同時覆蓋註冊网卡情況
更新startTcp/HttpListener註冊一個對外提供服务的Listener,使用conn.Listener包裝的net.Listener
tcp/httpHandler處理請求
tunnel要怎确定?getTunnelKey
NewTunnel分別處理http/tcp請求,getTunnelKey->getListener/StartListen->initTunnel
**隱患:只重构server,client沒有理清conn,net有可能會資源泄漏**
***名字沒有整理好..公開私有亂來中..**
1. ControlRegistry: map client id to control
2. TunnelRegistry: map tunnel url to tunnel

**主程序關閉沒關到監听tunnel grouting...**



### ENV

conn.Conn連接處理,通過网絡壙
conn.Tee連接處理,通過本地內存IO



ENV PS1影响了VSCODE DEBUG
