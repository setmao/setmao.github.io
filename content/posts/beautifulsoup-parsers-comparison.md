+++
title = 'BeautifulSoup Parsers Comparison'
description = '記錄一下要怎麼在 docker 中對 PostgreSQL 備份/匯入資料'
slug = 'beautifulSoup-parsers-comparison'
tags = ['Crawler']
date = 2021-05-06T23:51:20+08:00
draft = false
+++

## 前言

最近常常在用 Python 寫爬蟲
就好奇 BeautifulSoup 不同的 Parser 之間有什麼差別
於是寫了這篇文來記錄一下

---

```python
import requests
from bs4 import BeautifulSoup

url = "https://google.com/"

resp = requests.get(url)
soup = BeautifulSoup(resp.text, "html.parser")
# or
soup = BeautifulSoup(resp.text, "lxml")
```

網路上的爬蟲教學常常會看到以上兩種寫法，可以看到差別就是 html.parser 跟 lxml
這個其實是在跟 BeautifulSoup 說我們要用哪種 Parser 去解析 HTML
但是到底 BeautifulSoup 支援多少種 Parser，及每種 Parser 到底差在哪？
於是就隨手 google 了一下發現了 StackOverflow 上的[這篇](https://stackoverflow.com/questions/45494505/beautifulsoup-whats-the-difference-between-lxml-and-html-parser-and-html5) 及 BeautifulSoup 的 [doc](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#installing-a-parser)

以下是不同的 Parser 的比較表格
> tl;dr
>
>  速度最快：lxml
>
>  相容性最高：html5lib
>
>  剩下用：html.parser
>
>

| Parser | 優點 | 缺點 |
| --- | --- | --- |
| html.parser | Python 內建，不需額外安裝 | 速度跟相容性都普通 |
| lxml | 快 | 需要額外安裝（C dependency） |
| html5lib | 相容性最高，所有版本的 Python 都能用 | 慢 |

---

參考資料

----

* [BeautifulSoup: what’s the difference between ‘lxml’ and ‘html.parser’ and ‘html5lib’ parsers?](https://stackoverflow.com/questions/45494505/beautifulsoup-whats-the-difference-between-lxml-and-html-parser-and-html5)
* [Installing a parser](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#installing-a-parser)
