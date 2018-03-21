---
layout: rest_framework
title: serializers
date: 2018-03-21 16:17:14
tags:
---

```python
# -*- coding: utf-8 -*-
from __future__ import division

from django.db import transaction
from django.db.utils import IntegrityError
from django.utils import timezone

from rest_framework import serializers

from libs.timestamp_tools.timestamp_util import str_to_standard_timestamp

from bms_manage.models import Administrator


class TimeStampField(serializers.Field):
    def to_representation(self, value):
        try:
            timestamp = str_to_standard_timestamp(str(timezone.localtime(value)))
            timestamp = "%f" % float(timestamp)
        except:
            return "0"
        return timestamp


class AdministratorSerializer(serializers.ModelSerializer):
    last_login = TimeStampField()

    def create(self, validated_data):
        pd = str(validated_data.pop("password"))
        user = Administrator(**validated_data)
        try:
            # 回滚事务
            with transaction.atomic():
                user.save()
                user.set_pd(pd)
                print('Save Forecast Info Success.')
                return user
        except IntegrityError:
            # 归还参数
            validated_data["password"] = pd
            raise IntegrityError("mobile:{} is existed".format(validated_data["mobile"]))
        except Exception as e:
            validated_data["password"] = pd
            raise Exception("An unexpected error:{}".format(e))

    class Meta:
        model = Administrator
        fields = ('id',
                  'name',
                  'nickname',
                  'role_name',
                  'department',
                  'avatar_url',
                  'mobile',
                  'gender',
                  'last_login',
                  'age')

```

使用序列化
```python
class QueryUser(APIView):
    """查找用户"""

    @log_view
    def get(self, request):
        """
        查询用户，仅能查到比自己权限低的
        ---
        parameters:

            - name: Authorization
              description: token
              required: true
              type: string
              paramType: header

            - name: key
              description: 手机号或者名字
              required: false
              type: string
              paramType: query
        """
        key = request.GET.get("key", "")
        print(request.query_params)
        users = models.Administrator.objects.filter(Q(mobile__contains=key) | Q(name__contains=key),
                                                    role__gt=request.user.role)
        serializer_context = {'request': request}
        data = AdministratorSerializer(users, context=serializer_context, many=True).data
        return Response({"data": data}, status=status.HTTP_200_OK)

    def post(self, request):
        print(request.data)
        return Response(status=status.HTTP_200_OK)
```