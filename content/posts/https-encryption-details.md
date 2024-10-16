+++
title = 'HTTPS 加密了哪些東西？'
description = 'HTTPS 加密了哪些東西？'
slug = 'https-encryption-details'
tags = ['Network']
date = 2021-07-04T23:51:20+08:00
draft = false
+++

HTTPS 會加密 path, query string, request body 但是 domain name 不一定會加密（如果沒有使用 SNI 才會把 domain name 加密）

