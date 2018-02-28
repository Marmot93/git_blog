---
title: 为什么Django要用check_password
date: 2018-02-28 09:50:52
tags:
---



###为什么Django要用check_password

看了一些check_password的源码
```python
def check_password(password, encoded, setter=None, preferred='default'):
    """
    Returns a boolean of whether the raw password matches the three
    part encoded digest.

    If setter is specified, it'll be called when you need to
    regenerate the password.
    """
    if password is None or not is_password_usable(encoded):
        return False
    # 判定hash方法，主要
    preferred = get_hasher(preferred)
    # 识别hash算法
    hasher = identify_hasher(encoded)

    hasher_changed = hasher.algorithm != preferred.algorithm
    # 如果上面的两个不相等，那么就证明更改过密码的hash算法hasher_changed=True
    must_update = hasher_changed or preferred.must_update(encoded)
    # hasher_changed=True或者用原来的加密方法鉴别密码
    is_correct = hasher.verify(password, encoded)

    # If the hasher didn't change (we don't protect against enumeration if it
    # does) and the password should get updated, try to close the timing gap
    # between the work factor of the current encoded password and the default
    # work factor.
    if not is_correct and not hasher_changed and must_update:
        hasher.harden_runtime(password, encoded)

    if setter and is_correct and must_update:
        setter(password)
    return is_correct
```
主要问题应该是：  
```python
if password is None or not is_password_usable(encoded):  
        return False  
```

如果检查密码写的是

*  if request_password == db_password  可能还好 
 
但是如果是：  
*  if request_password != db_password  
    request_password == None("")之类的就会出问题  
    
继续看一些之后的东西  
```python
preferred = get_hasher(preferred)
# 追踪该方法  
# 判定加密算法用的哪一个
def get_hasher(algorithm='default'):
    """
    Returns an instance of a loaded password hasher.

    If algorithm is 'default', the default hasher will be returned.
    This function will also lazy import hashers specified in your
    settings file if needed.
    """
    if hasattr(algorithm, 'algorithm'):
        return algorithm

    elif algorithm == 'default':
        #  如果是默认的话就使用这个列表的第一个，看一下这个
        return get_hashers()[0]

    else:
        hashers = get_hashers_by_algorithm()
        try:
            return hashers[algorithm]
        except KeyError:
            raise ValueError("Unknown password hashing algorithm '%s'. "
                             "Did you specify it in the PASSWORD_HASHERS "
                             "setting?" % algorithm)
# 追踪get_hashers（）
@lru_cache.lru_cache()
# 这是一个有意思的装饰器，FluntPython里面有讲
# 之前在解欧拉计划的计算斐波拉切数列的时候用过一次，处理递归的栈问题
def get_hashers():
    hashers = []
    for hasher_path in settings.PASSWORD_HASHERS:
        # settings = LazySettings()应该有一个默认settings，有空研究一下这个Class
        hasher_cls = import_string(hasher_path)
        hasher = hasher_cls()
        if not getattr(hasher, 'algorithm'):
            raise ImproperlyConfigured("hasher doesn't specify an "
                                       "algorithm name: %s" % hasher_path)
        hashers.append(hasher)
    return hashers
```