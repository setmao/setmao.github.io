+++
title = 'Django Migrations'
description = 'Django 的 Migrations 除了在能在資料庫中建立新的資料表、修改舊的資料表結構外，我們也能自己寫 code 在新增資料表、修改舊的資料表結構的同時對資料表做 CRUD（Create, Read, Update, Delete）'
slug = 'django-migrations'
tags = ['Django']
date = '2020-05-28T02:46:58+08:00'
draft = false
+++

## 前言
Django 的 Migrations 除了在能在資料庫中建立新的資料表、修改舊的資料表結構外，我們也能自己寫 code 在新增資料表、修改舊的資料表結構的同時對資料表做 CRUD（Create, Read, Update, Delete）

比如以下情境：

我現在要新建一個資料表用來存 Email Template 給客戶使用，希望在建立資料表的同時能將一些 default template 一起寫入資料庫中
原本有兩個資料表分別負責存不同的資料，現在因為一些關係要把這兩個資料表整合在一起，所以我想在新建資料表後將舊的資料表中的資料轉移到新的資料表後將舊的兩個資料表刪除
這些都可以在 Django Migrations 中完成！

---

首先我們先執行下面的指令來生出一個空的 migration

```bash
python manage.py makemigrations --empty yourappname
```

接著在 migration 中加入以下的 code，並將 combine_names 改成你想做的事情

```python
from django.db import migrations

def combine_names(apps, schema_editor):
    # We can't import the Person model directly as it may be a newer
    # version than this migration expects. We use the historical version.
    Person = apps.get_model('yourappname', 'Person')

    for person in Person.objects.all():
        person.name = '%s %s' % (person.first\_name, person.last\_name)
        person.save()

class Migration(migrations.Migration):

    dependencies = [
        ('yourappname', '0001\_initial'),
    ]

    operations = [
        migrations.RunPython(combine_names),
    ]
```

這邊需要注意的是 model 需要用這種方式取得：

```python
Person = apps.get_model('yourappname', 'Person')
```

然後 migrations.RunPython(combine_names) 這行將會在執行 migrate 的同時跑這個 function: combine_names

寫完之後

```bash
python manage.py migrate
```
