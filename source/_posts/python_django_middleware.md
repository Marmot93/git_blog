---
title: Python Django middleware
date: 2018-01-23 16:52:12
tags: python
---

在Django公处理请求参数，参数和文件整理到request.data和request.files

```python
from django.utils.deprecation import MiddlewareMixin
import json


class DataMiddleware(MiddlewareMixin):
    """把request里面的的各种数据整理放在req.data里面"""

    def process_request(self, request):
        data = {}
        # 判定请求方式
        content_type = request.META.get('CONTENT_TYPE', '')
        print(content_type)
        # 请求为json
        if 'application/json' in content_type:
            try:
                data = json.loads(request.body.decode())
            except Exception as e:
                print('中间件解析错误', e)
        # 请求为表单
        elif 'multipart/form-data' in content_type:
            try:
                data, files = request.parse_file_upload(request.META, request)
                request.files = files
            except Exception as e:
                print('中间件解析错误', e)
        elif 'application/x-www-form-urlencoded' in content_type:
            data = request.POST
        # params
        # if data == {}:
        else:
            data = request.GET
        request.data = data
        print(request.method, request.data)

```