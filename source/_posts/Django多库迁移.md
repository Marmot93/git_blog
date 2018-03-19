---
title: Django多库迁移
date: 2018-03-19 17:58:21
tags: django
---

Django多库迁移


settings.py配置database_mapping

DATABASE_APPS_MAPPING = {
    # example:  
    # 'app_name':'database_name',  
    'bms_service': 'timescale',  
    'bms_system': 'default',#TODO  
    'bms_manage': 'default',  
}

python manager.py migrate --database timescale

migrate默认只迁移default数据库
