---
title: Python面试题30道（下）
date: 2018-02-26 15:49:33
tags:
---
###Q-16: How Does The Ternary Operator Work In Python?
```python
x, y = 35, 75
smaller = x if x < y else y
print(smaller)
```

###Q-17: What Does The <Self> Keyword Do?
self就是self呀

###Q-18: What Are Different Methods To Copy An Object In Python?
基础的l2=l1\[:]  
copy.copy()  
*  外层copy,内层会随着原对象改变，抄了作业还要跟着改
copy.deepcopy()  
*  内层copy，抄完作业就不改了

###Q-19: What Is The Purpose Of Doc Strings In Python?
给你一个BB机会
```python
class A(object):
    """我就在这里BB"""


a = A()
print(a.__doc__)
```
###Q-20: Which Python Function Will You Use To Convert A Number To A String?
str()

###Q-21: How Do You Debug A Program In Python? Is It Possible To Step Through Python Code?
```python
import pdb
pdb.set_trace()
```

###Q-22: List Down Some Of The PDB Commands For Debugging Python Programs?
*  Add breakpoint – b  
*  Resume execution – c 
*  Step by step debugging – s
*  Move to next line – n 
*  List source code – l 
*  Print an expression – p 

###Q-23: What Is The Command To Debug A Python Program?
```bash
python -m pdb debug_file.py
```

###Q-24: How Do You Monitor The Code Flow Of A Program In Python?
```python
import sys
def trace_calls(frame, event, arg):
    # The 'call' event occurs before a function gets executed.
    if event != 'call':
        return
    # Next, inspect the frame data and print information.
    print('Function name=%s, line num=%s' % (frame.f_code.co_name, frame.f_lineno))
    return

def demo2():
    print('in demo2()')

def demo1():
    print('in demo1()')
    demo2()

sys.settrace(trace_calls)
demo1()
```
###Q-25: Why And When Do You Use Generators In Python?
###Q-26: What Does The <Yield> Keyword Do In Python?
协程
###Q-27: How To Convert A List Into Other Data Types?
to String
```python
weekdays = ['sun','mon','tue','wed','thu','fri','sat']
listAsString = ' '.join(weekdays)
print(listAsString)
```
to Tuple
```python
weekdays = ['sun','mon','tue','wed','thu','fri','sat']
listAsTuple = tuple(weekdays)
print(listAsTuple)
```
to Set
```python
weekdays = ['sun','mon','tue','wed','thu','fri','sat','sun','tue']
listAsSet = set(weekdays)
print(listAsSet)
```
to Dictionary
```python
weekdays = ['sun','mon','tue','wed','thu','fri']
listAsDict = dict(zip(weekdays[0::2], weekdays[1::2]))
print(listAsDict)
```
###Q-28: How Do You Count The Occurrences Of Each Item Present In The List Without Explicitly Mentioning Them?
```python
weekdays = ['sun','mon','tue','wed','thu','fri','sun','mon','mon']
print(weekdays.count('mon'))
```
###Q-29: What Is NumPy And How Is It Better Than A List In Python?
Numpy底层结构不一样，c写的，而且优化的很好
###Q-30: What Are Different Ways To Create An Empty NumPy Array In Python?
看Numpy的文档吧 0.0