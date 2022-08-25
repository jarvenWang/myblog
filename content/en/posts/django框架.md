---
title: "django框架"
date: 2020-01-25T06:40:51+09:00
description: tabs, code-tabs, expand, alert, warning, notice, img, box
draft: false
hideToc: false
enableToc: true
enableTocContent: false
tocPosition: inner
tags:
- shortcode
series:
-
categories:
-
image: images/feature3/django.png
---

## 一、安装
+ pip安装

{{< boxmd >}}
pip install django
{{< /boxmd >}}
安装成功后会有 <font color=#FF0000 >**django-admin**</font> 命令

+ 创建新项目

{{<boxmd>}}
django-admin startproject website(项目名)
{{</boxmd>}}
+ 开启项目

{{<boxmd>}}
python manage.py startapp app --不能重名
{{</boxmd>}}

+ 启动项目

{{<boxmd>}}
python manage.py runserver
{{</boxmd>}}

+ 指定 IP+端口 启动项目

{{<boxmd>}}
python manage.py runserver 0.0.0.0:8080
{{</boxmd>}}

![截个图](/images/feature3/screen-1.png)

## 二、迁移文件操作
### 1、创建迁移文件内容
+添加表结构app/models.py
``` python
class AssessmentInfo(models.Model):
    '''业务考核表'''
    apply_id = models.IntegerField('考核申请ID', default=0, db_index=True)
    user = models.CharField('用户名', max_length=64, default='')
    userText = models.CharField('中文名', max_length=64, default='', db_column='user_text')
    leader = models.CharField('业务考核人用户名', max_length=64, default='')
    leaderText = models.CharField('业务考核人中文名', max_length=64, default='', db_column='leader_text')
    kpi = JSONField('KPI', default=[])
    score = models.FloatField('评分', default=0)
    level = models.CharField('等级', max_length=10, default='')
    comment = models.TextField('评语', max_length=1000, default='')
    dept = models.CharField('服务部门', max_length=255, default='')
    position = models.CharField('职位', max_length=20, default='')
    entryDate = models.DateField('入场时间', blank=True, null=True, db_column='entry_date')
    # 0 - 未提交， 1- 未考核， 2 - 已考核
    status = models.IntegerField('状态', default=0)
    created = models.DateTimeField('创建时间', auto_now_add=True)
    updated = models.DateTimeField('更新时间', auto_now=True)
    STATUS_OFF = 4
    class Meta:
        db_table = 'app_assessment_info'
        index_together = [
            ['user', 'apply_id'],
            ['leader', 'apply_id'],
        ]
```
+修改表结构app/models.py
``` python
class Migration(migrations.Migration):

    dependencies = [
        ('UserManage', '0001_initial'),
    ]

    operations = [
        migrations.CreateModel(
            name='Org',
            fields=[
                ('id', models.AutoField(verbose_name='ID', serialize=False, auto_created=True, primary_key=True)),
                ('oid', models.CharField(default='', max_length=20, db_index=True)),
                ('name', models.CharField(default='', max_length=20)),
                ('level', models.IntegerField(default=0)),
                ('parent', models.IntegerField(default=0)),
                ('created', models.DateTimeField(auto_now_add=True)),
                ('updated', models.DateTimeField(auto_now=True)),
            ],
            options={
                'db_table': 'user_manage_orgs',
            },
            bases=(models.Model, common.utils.models.toDictMixin),
        ),
        migrations.AddField(
            model_name='user',
            name='entryDate',
            field=models.DateField(null=True, db_column='entry_date', blank=True),
        ),
        migrations.AddField(
            model_name='user',
            name='oid',
            field=models.CharField(default='', max_length=20),
        ),
        migrations.AddField(
            model_name='user',
            name='position',
            field=models.CharField(default='', max_length=20),
        ),
        migrations.AlterField(
            model_name='user',
            name='last_login',
            field=models.DateTimeField(null=True, blank=True),
        ),
    ]
```

### 2、执行迁移文件命令
{{<boxmd>}}
python manage.py makemigration
python manage.py migrate
{{</boxmd>}}
#### 2.1 项目添加新的数据表

##### 步骤一:
先刷新一下当前数据库的表结构，避免出现table already exists
{{<boxmd>}}
python manage.py makemigrations
python manage.py migrate app --fake
python manage.py migrate UserManage --fake
{{</boxmd>}}
##### 步骤二:
加表结构
编写新的models
##### 步骤三:
执行migrate
{{<boxmd>}}
python manage.py makemigrations
python manage.py migrate app
python manage.py migrate UserManage
{{</boxmd>}}

## 三、orm增、删、改、查操作
>ORM：Object Relational Mapping(对象关系映射)，是使用面向对象的思维来操作数据库。

### *增加
#### 方式一 ： 类名.objects.create()
{{<boxmd>}}
例子：
models.Person.objects.create(name=username,pwd=password)
{{</boxmd>}}

#### 方式二 ： obj=models.类（属性=XX）     obj.save()
{{<boxmd>}}
举子：
person_obj = models.Person(name=username,pwd=password)
person_obj.save()
{{</boxmd>}}

### *删除
#### 方式一 ： 类名.objects.get(xxx).delete()
{{<boxmd>}}
例子：
person_obj = models.Person.objects.get(id=ids)  # 获取 person对象
person_obj.delete()  # 删除数据库中的记录
{{</boxmd>}}
#### 方式二 ： 类名.objects.filter(xxx).delete()
{{<boxmd>}}
例子：
ret = models.Person.objects.filter(id=ids)  # filter() 根据条件进行过滤。
ret.delete()
{{</boxmd>}}


### *修改
#### 方式一 ： 
obj =类名.objects.get(‘xxx’)
obj.zz = xx
obj.save()
{{<boxmd>}}
例子：
person_obj = models.Person.objects.get(id=id)
person_obj.name = usrname
person_obj.age = age
person_obj.birthday = bir
person_obj.save()
{{</boxmd>}}

#### 方式二 ： 类名.objects.filter(‘xxx’).update(关键字（类中的属性）=值.....)
{{<boxmd>}}
例子：
models.Person.objects.filter(id=id).update(name=usrname, age=age, birthday=bir)
{{</boxmd>}}


### *查询
#### 方式一 ： 类名.objects.all() 获取所有记录
{{<boxmd>}}
例子：
models.Person.objects.all()
models.Person.objects.filter(is_active=True)
注意：单独查询要用try except
try:
  auth=Author.objects.get(id=1)
  auth.delete()
except:
  print(删除失败)
{{</boxmd>}}

## 四、路由
```python
from django.conf.urls import include, url

urlpatterns = [
    url(r'^$', Home),
    url(r'^wxlogin/$', WXLogin),
    url(r'^app/', include('app.urls')),
    url(r'^accounts/', include('UserManage.urls')),
    url(r'^os_web/partners_kpi/api/wxlogin/$', WXLogin),
    url(r'^os_web/partners_kpi/api/app/', include('app.urls')),
    url(r'^os_web/partners_kpi/api/accounts/', include('UserManage.urls')),

]   
```



