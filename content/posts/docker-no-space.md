+++
title = 'Docker: no space left on device'
description = '最近在 build docker image 的時候遇到 No space left on device 的問題。因為在解的過程發現好像蠻多人都有遇過這個問題，於是就把解的過程記錄下來，希望能幫到其他人。'
slug = 'docker-no-space'
tags = ['Docker']
date = 2020-06-09T23:51:20+08:00
draft = false
+++

## 前言
最近在 build docker image 的時候遇到 No space left on device 的問題。
因為在解的過程發現好像蠻多人都有遇過這個問題，於是就把解的過程記錄下來，希望能幫到其他人。

---

## 問題描述
今天在 build docker image 的時候出現以下錯誤：
```bash
ERROR: Could not install packages due to an EnvironmentError: [Errno 28] No space left on device
```

因為錯誤出現在 `pip install` 的步驟，所以我先往 pip 的方向去找，找到以下的解決辦法：

1. 在家目錄底下開一個新的 tmp 資料夾，讓系統使用該資料夾來解決 tmp 空間不足的問題。
2. 使用以下命令：
```bash
pip install --no-cache-dir <package_name>
```

選擇了第二個方法，但問題依然存在。後來發現這其實是 Docker 的暫存空間被塞滿導致的問題。

---

## 解決方案
1. 使用 `docker system prune` 指令來清理 Docker 暫存空間：
```bash
docker system prune
```
這將會刪除：
- 所有停止的容器
- 沒有被至少一個容器使用的網路
- 所有的 dangling images 和 build cache

2. 若還是無法解決，則進一步檢查容器的 log 檔：
```bash
du -d1 -h /var/lib/docker/containers | sort -h
```

找到占用空間大的 log 檔後，使用以下指令清除 log：
```bash
sudo sh -c "cat /dev/null > /var/lib/docker/containers/<container_id>/<container_log_name>"
```

---

## 最後
最終發現是 Ubuntu 的系統空間不足，通過清理 apt 和 log 檔後問題得以解決：
```bash
sudo apt-get autoremove
sudo apt-get autoclean

journalctl --vacuum-time=3d
```

---

## 參考資料
- [Errno 28] No space left on device #5816
- [Docker error : no space left on device](https://stackoverflow.com/questions/30604846/docker-error-no-space-left-on-device)
- [7 Simple Ways to Free Up Space on Ubuntu](https://itsfoss.com/free-up-space-ubuntu-linux/)
