+++
title = 'HTTP GET method vs POST method'
description = 'HTTP 的 method 中最常被用到的應該就是 GET 及 POST 了但是 GET 與 POST 除了使用場景不一樣之外，到底還有哪些區別呢？'
slug = 'http-get-post-comparison'
tags = ['Network']
date = 2021-07-04T23:51:20+08:00
draft = false
+++

HTTP 的 method 中最常被用到的應該就是 GET 及 POST 了但是 GET 與 POST 除了使用場景不一樣之外，到底還有哪些區別呢？
我們知道 GET 通常都透過 query string 來傳遞參數，POST 則是使用 request body但是其實 GET 也可以透過 request body 來傳遞參數，同理 POST 其實也能使用 query string（只是上述兩種方式都不推薦）

## GET vs POST 差異點

1. **POST** 不會被 cache，GET 會
2. **POST** 不會出現在瀏覽器的訪問記錄裡，GET 會
3. **POST** 不能被存成書籤，GET 能

[HTTP GET method vs POST method](https://setmao.github.io/2021-07-04/HTTP-GET-method-vs-POST-method/)
