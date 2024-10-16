+++
title = 'Hexo 配置'
description = '因為不小心手殘把 local 的 Hexo 設定砍了...所以藉此來紀念我的手殘及記錄一下我的 Hexo blog 做了哪些設定'
slug = 'hexo-configuration'
tags = ['Hexo']
date = 2021-07-17T23:51:20+08:00
draft = false
+++

## 前言

因為不小心手殘把 local 的 Hexo 設定砍了...
所以藉此來紀念我的手殘及記錄一下我的 Hexo blog 做了哪些設定

---

### 設定 deploy 到 GitHub page 上

安裝套件：

```bash
npm install --save hexo-deployer-git
```

調整 `_config.yml`：

```yaml
deploy:
  type: git
  repo: <repository url>
```

### NexT

安裝 NexT：

```bash
npm install hexo-theme-next --save
```

將 NexT 的設定檔複製出來：

```bash
cp node_modules/hexo-theme-next/_config.yml _config.next.yml
```

把 `_config.yml` 中的 `theme` 改成使用 NexT：

```yaml
theme: next
```

### Sitemap

安裝套件：

```bash
npm install hexo-generator-sitemap --save
```

### 外部連結 nofollow

安裝套件：

```bash
npm install hexo-filter-nofollow --save
```

### Minifier

安裝套件：

```bash
npm install hexo-all-minifier --save
```

在 `_config.yml` 中加入：

```yaml
all_minifier: true
```

### 搜尋

安裝套件：

```bash
npm install hexo-generator-searchdb --save
```

### 打開人數統計

把 `busuanzi_count` 的 `enable` 改成 `true`：

```yaml
busuanzi_count:
  enable: true
```

### 使用 Gitalk 當留言板

[Gitalk 评论系统](https://hexo-next.readthedocs.io/zh_CN/latest/next/advanced/gitalk-评论系统/)

### 新增 404 頁面

[(23) 試著學 Hexo - SEO 篇 - 新增你的 404 頁面](https://ithelp.ithome.com.tw/articles/10249685)

### 文章置頂

安裝套件：

```bash
npm install hexo-generator-topindex --save
```

---

## 參考資料

- [Hexo 套件推薦](https://shengyu7697.github.io/hexo-plugins/)
- [Gitalk 评论系统](https://hexo-next.readthedocs.io/zh_CN/latest/next/advanced/gitalk-评论系统/)
- [(23) 試著學 Hexo - SEO 篇 - 新增你的 404 頁面](https://ithelp.ithome.com.tw/articles/10249685)

---