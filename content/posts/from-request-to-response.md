+++
title = '從瀏覽器輸入網址到看到網頁到底發生了哪些事？'
description = '從瀏覽器輸入網址到看到網頁到底發生了哪些事？'
slug = 'from-request-to-response'
tags = ['Network']
date = 2021-07-04T23:51:20+08:00
draft = false
+++

![network](/img/network.jpeg)

1. **在 browser 輸入網址**
2. **browser 跟 OS 要網址的 IP address**
3. **OS 向 DNS 查詢 IP address（如果有 cache 會直接回傳 IP address 給 OS）**
4. **DNS 向 Name Server 查詢 IP address**
5. **OS 將從 DNS/Name Server 查詢到的 IP address 回傳給 browser**

![request](/img/request.jpeg)

6. **browser 拿到 IP address 後對 IP address 做 HTTP/HTTPS request**
   1. **如果使用 HTTPS 會先使用 TLS 對憑證做認證並產生加密的 session key，並對 request 的資料內容作加密**
   2. **browser 與 Server 做 TCP 的三項交握確認 Server 現在可以接收資料**
7. **Server 上的 Nginx/Apache 接收到 request，並將 request pass 到 code base 中（Python/PHP/JavaScript）**
8. **code base 將 request 處理完之後回傳 response**
9. **browser 接收到 response 後根據 response 的內容將頁面 render 出來**
