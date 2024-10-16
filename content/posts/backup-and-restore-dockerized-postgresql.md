+++
title = 'Backup/Restore a dockerized PostgreSQL database'
description = '記錄一下要怎麼在 docker 中對 PostgreSQL 備份/匯入資料'
slug = 'backup-and-restore-dockerized-postgresql'
tags = ['Docker', 'PostgreSQL', 'Database']
date = 2020-06-29T23:51:20+08:00
draft = false
+++

## 前言
記錄一下要怎麼在 docker 中對 PostgreSQL 備份/匯入資料

---

## 備份 PostgreSQL
在 docker 中備份 PostgreSQL 的指令如下：
```bash
docker exec -t <container_name> pg_dumpall -c -U <user_name> > dump_`date +%d-%m-%Y"_%H_%M_%S`.sql
```

## 匯入 PostgreSQL
將 SQL 檔匯入 PostgreSQL 的指令如下：
```bash
cat <your_dump>.sql | docker exec -i <container_name> psql -U <user_name>
```

---

## 參考資料
- [Backup/Restore a dockerized PostgreSQL database](https://stackoverflow.com/questions/24718706/backup-restore-a-dockerized-postgresql-database)
