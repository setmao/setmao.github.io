+++
title = 'Django 1.11 to 2.2'
description = '因為 Django 1.11 在今年四月開始停止更新了，所以花了一天時間幫公司從 Django 1.11 升級到 2.2，於是想用這篇來記錄一下升級的過程'
slug = 'django-1.11-to-2.2'
tags = ['Django']
date = 2020-06-08T23:51:20+08:00
draft = false
+++

## 前言
因為 Django 1.11 在今年四月開始停止更新了，所以花了一天時間幫公司從 Django 1.11 升級到 2.2
於是想用這篇來記錄一下升級的過程

---

## 前置作業：
我在升級前找了很多資料跟別人升級的心得，主要參考以下幾篇

博客将 Django 1.11+ 升级到 Django 2.2+ 遇到的问题及规避方法
Django1和2的区别
Upgrading Django to a newer version
第一篇主要是別人實際更新上遇到個問題

第二篇是再講 Django 1.11 跟 Django 2.X 主要差別在哪

第三篇是官方文件

開始！
python -Wa manage.py test

先用以下指令掃一下升級後現在的 code 可能會在哪裡報錯

python -Wa manage.py test
TypeError: __init__() missing 1 required positional argument: ‘on_delete’

用步驟一的指令掃完後，報了很多下面的錯誤

TypeError: __init__() missing 1 required positional argument: 'on_delete'
出現這個的原因是因為 Django 2.X 開始 ForeignKey 的 on_delete 參數變成必填欄位（1.11 預設值為 on_delete=models.CASCADE），所以我們得先手動將 on_delete=models.CASCADE 加上去

# 以下兩種寫法是等價的
# django 1.11
creator = models.ForeignKey(User)
# django 2.2
creator = models.ForeignKey(User, on_delete=models.CASCADE)
P.S. TreeForeignKey, OneToOneField..等也算 ForeignKey 的一種（可以去看 source code，其實這些都是繼承 ForeignKey 實作出來的），這些都要加，但是 GenericForeignKey 不用

補充一下 on_delete 的幾種選擇

on_delete=models.CASCADE,     # 刪除有 ForeignKey 的 model 時將所以有有關係的 model 一起刪除
on_delete=models.DO_NOTHING,  # 刪除有 ForeignKey 的 model 時
on_delete=models.PROTECT,     # 刪除有 ForeignKey 的 model 時觸發錯誤 ProtectedError
on_delete=models.SET_NULL,    # 刪除有 ForeignKey 的 model 時將已經有關聯的欄位設為 NULL
on_delete=models.SET_DEFAULT, # 刪除有 ForeignKey 的 model 時將已經有關聯的欄位設為 default 值
on_delete-models.SET(),       # 刪除有 ForeignKey 的 model 後執行一個函數
Package’s migration

加完 on_delete 後就會發現報了一些套件的 models 問題，一樣也是 on_delete 的問題（還有一些是 url 的問題，這個後面再說）

這種問題應該只有更新套件才有解了，不過通常都是 django 開頭的套件才會出錯

這邊列出幾個這次升級連帶一起升級的套件供參

django-avatar                4.0.0 -> 5.0.0
django-bootstrap-pagination  1.6.3 -> 1.7.1
django-celery-beat           1.0.1 -> 2.0.0
django-money                 0.11.4 -> 1.1
django-screamshot            0.8.1 -> 0.8.5
django-allauth               0.40.0 -> 0.42.0
‘indexes’ refers to the nonexistent field

修改完 on_delete 的問題後，再用步驟一掃應該掃不出什麼了

這時候就可以直接

python manage.py runserver 0.0.0.0:8000
接著應該會看到一堆的 waring

其中一個 waring 是這個

'indexes' refers to the nonexistent field
會報錯的原因是因為在有 ForeignKey 的 model 中做 index 時，在 Django 1.11 中以下寫法是被允許的，因為其實 ForeignKey 在 db 中的做法也是在這個資料表中開一個欄位存放關聯對象的 id

class EdmRecipient(BaseModel):
	team = models.ForeignKey(Team)
	app = models.ForeignKey(App)

	class Meta:
		indexes = [
			models.Index(fields=['team_id', 'edm_id'], name='edmrecipient_team_edm_cat'),
		]
但是在 Django 2.2 一定得這樣寫，不能用 team_id 的寫法，只能用 team

class EdmRecipient(BaseModel):
	team = models.ForeignKey(Team, on_delete=models.CASCADE)
	app = models.ForeignKey(App, on_delete=models.CASCADE)

	class Meta:
		indexes = [
			models.Index(fields=['team_id', 'edm_id'], name='edmrecipient_team_edm_cat'),
		]
from django.core.urlresolvers import reverse -> from django.urls import reverse

這個應該沒什麼好說的，就是換個位置 import 而已

url() -> path()

接著就來到應該算是這次升級改動最大的地方了

在 Django 1.11 中大家應該最常用

url(r'^index/$', views.IndexView.as_view())
但在 Django 2.2 中將 url() 改成 path()，而且 path 內建了幾個 type 來篩選 url 的參數

舉個例子

# Django 1.11
url(r'^smarts/(?P<slug>\[a-zA-Z0-9-_]+)/(?P<uuid>[a-zA-Z0-9-]+)/edit/$', views.SmartEdit.as_view(), name='smart_edit'),

# Django 2.2
path('smarts/<slug:slug>/<uuid:uuid>/edit/', views.SmartEdit.as_view(), name='smart_edit'),
是不是簡潔很多！

簡單來說 type:parameter_name

支援的 type 有以下幾種

str - default，不吃 ‘/‘
int - 就是 int
slug - 只吃英、數、-、_
uuid - 只吃 uuid
path - str，但是吃 ‘/‘
使用 uuid 時會回傳型別為 uuid 的資料，如果有特別需要對資料做處理時要注意

ref: Path converters

那如果我還是有用到 re 的需求該怎麼辦？用下面這個

re_path()
P.S. 在看 source code 的時候我發現了這個

def url(regex, view, kwargs=None, name=None):
	return re_path(regex, view, kwargs, name)
source code: https://github.com/django/django/blob/stable/2.2.x/django/conf/urls/\_\_init\_\_.py

這代表了其實 url 還是可以用，但是其實背後是用 re_path，而且要改成

from django.conf.urls import url
Django TypeError: render() got an unexpected keyword argument ‘renderer’

其實原本做完步驟六就已經上線了，結果才發現這個問題QQ

Django TypeError: render() got an unexpected keyword argument ‘renderer’

看上面這篇說 renderer 這個參數已經從 render() 中移除了

後來才找到會報這個是因為 django-ckeditor 這個套件的關係

升級它就搞定啦！

django-ckeditor  5.4.0 -> 5.9.0
參考資料
博客将 Django 1.11+ 升级到 Django 2.2+ 遇到的问题及规避方法
Django1和2的区别
Upgrading Django to a newer version
Path converters
Django TypeError: render() got an unexpected keyword argument ‘renderer’
