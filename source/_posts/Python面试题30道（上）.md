---
title: Python面试题30道（上）
date: 2018-02-26 15:15:35
tags:
---

###Q-1: What Is Python? What Are The Benefits Of Using Python? What Do You Understand Of PEP 8?
关于对Python的认识
###Q-2: What Is The Output Of The Following Python Code Fragment? Justify Your Answer.
一个老例子了  
```python
def extendList(val, list=[]):
    list.append(val)
    return list  

list1 = extendList(10)
list2 = extendList(123,[])
list3 = extendList('a')  

print("list1 = %s" % list1)
print("list2 = %s" % list2)
print("list3 = %s" % list3)
```
相当于没有初始化，输出结果成了
```python
list1 = [10, 'a']
list2 = [123]
list3 = [10, 'a']
```
解决方案
```python
def extendList(val, list=None):
  if list is None:
    list = []
  list.append(val)
  return list
```

###Q-3: What Is The Statement That Can Be Used In Python If The Program Requires No Action But Requires It Syntactically?
善用pass

###Q-4: What’s The Process To Get The Home Directory Using ‘~’ In Python?
```python
import os
print (os.path.expanduser('~'))
```
日常
```bash
cd ~
```

###Q-5: What Are The Built-In Types Available In Python?
Immutable built-in types of Python  
*  Numbers  
*  Strings  
*  Tuples  

Mutable built-in types of Python  
*  List  
*  Dictionaries  
*  Sets  

###Q-6: How To Find Bugs Or Perform Static Analysis In A Python Application?
PyChecker和Pylint 但是我都没有用过。。。。

###Q-7: When Is The Python Decorator Used?
装饰器就用的比较多了

###Q-8: What Is The Key Difference Between A List And The Tuple?
z主要还是是否可变，其实
```python
(1,[2,3])
```
这种的内层可以改

###Q-9: How Does Python Handle The Memory Management?
内存管理和垃圾回收  
垃圾回收主要是是统计内存引用  
但是内存管理有个很有意思的，小于某一个值（我忘了）的会取同一块地址

###Q-10: What Are The Principal Differences Between The Lambda And Def?
####匿名函数（lambda）和def的区别   
def可以保存多个表达式，而lambda是一个单表达式函数。  
def生成一个函数并指定一个名称以便稍后调用它，lambda形成一个函数并返回函数本身  
def可以有一个return语句，lambda不能有return语句（这个好像是废话）  
**lambda支持在列表和字典中使用**

###Q-11: Write A Reg Expression That Confirms An Email Id Using The Python Reg Expression Module <Re>?
当你准备用正则表达式解决一个问题的时候，恭喜你，你现在有两个问题了  
当然正则要学跑不掉的

###Q-12: What Do You Think Is The Output Of The Following Code Fragment? Is There Any Error In The Code?
这个东西，太多了，多犯错，就记得了

###Q-13: Is There A Switch Or Case Statement In Python? If Not Then What Is The Reason For The Same?
没有Switch，滚！  
塞尔达真他妈好玩！

###Q-14:  What Is A Built-In Function That Python Uses To Iterate Over A Number Sequence?
迭代器是个好东西比如
```python
book_kind_info = [_.get_simple() for _ in bs]
```

###Q-15: What Are The Optional Statements That Can Be Used Inside A <Try-Except> Block In Python?
```python
try:
    2/0
except Exception as e:
    print(e)
else:
    print("我可没有犯错")
finally:
    print("我小猪佩琪今天非要执行这个")
```