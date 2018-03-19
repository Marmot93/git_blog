---
title: Django扩展命令
date: 2018-03-19 17:54:06
tags: django
---
Django扩展命令  

在Django项目下 /management/commands/  
建立待执行命令.py 
```python
from django.core.management import BaseCommand
from django.db import IntegrityError

from bms_manage.models import Administrator


class Command(BaseCommand):
    help = """ex: python manager.py createadmin [mobile] [password]\n创建一个admin，若手机号存在且为管理员，则为其重置密码"""

    def add_arguments(self, parser):
        parser.add_argument('admin_info', nargs='+', type=str)

    def handle(self, *args, **options):
        try:
            a = Administrator.objects.create(mobile=options['admin_info'][0], role=0)
            a.set_pd(options['admin_info'][1])
            self.stdout.write("创建管理员成功")
        except IndexError:
            self.stdout.write("请指定管理员登陆手机号和密码")
        except IntegrityError:
            try:
                a = Administrator.objects.get(mobile=options['admin_info'][0], role=0)
                a.set_pd(options['admin_info'][1])
                self.stdout.write("重置管理员成功")
            except Administrator.DoesNotExist:
                self.stdout.write("该手机号不存在或不是管理员")
```