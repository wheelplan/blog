---
title: Python 基础入门
date: 2019-03-20 23:01:21
top_img: https://lh3.googleusercontent.com/proxy/RgAJhfoXxo7zzRBnh0-iYMVkHapNw6nLRLSRC2iTue8COaHayWB6aG3TOre8_GOm1Y1tclrJt3AFy9aQESJFVFJtHyybMEHLZaWaYfpdXcRawzBzQhkwJoXDHJaEmRlg5S6FaIvKTH_vpX-F4tlv1niaonG0iOghuOmm__A
keywords: python 基础
tags:
 - python
---

## 注释与变量声明

```python
# ### 注释的分类： 
# 1.单行注释 
# print("i have a dream!")
a = "i have a dream!"
print(a,type(a),id(a))

# 2.多行注释 
"""
'''
print('Hi')
'''

print('I Have a Dream !')
"""
# ### 变量：可以改变的量，实际上是指内存中的一块存储空间
"""
# 变量的命名规范
1. 变量一般由字母数字下划线组成
2. 变量区分大小写
3. 变量的命名要求能见名知义
4. 变量名称不能以数字开头
5. 变量名称不能为关键字
6. 变量名称避免使用中文
"""

# 查看关键字
import keyword
print(keyword.kwlistvar)


# 变量的声明
a = 1
a = b = 1
print(a,b)

a,b = 2,3
print(a,b)

# 变量的交换
a,b = 5,6
a,b = b,a
print(a,b)

tmp = a
a = b
b = tmp
print(a,b)
```



## 数据类型

python 的数据类型可大致可分为 数字 字符串 列表 元组 集合 字典

### 1. 数字类型

数字类型可分为 整型 浮点型 布尔型 复数型

##### 整型 int

```python
# 整型 int
intvar = 1
print(intvar)			# ==> 1

# 获取类型
print(type(intvar))		# ==> <class 'int'>						

# 获取内存地址
print(id(intvar))		# ==> 1700364400

# 二进制整型
intvar = 0b111
print( intvar,type(intvar),id(intvar) )		# ==> 7 <class 'int'> 1700364592

# 八进制整型
intvar = 0o111
print( intvar,type(intvar),id(intvar) )		# ==> 73 <class 'int'> 1700366704

# 十六进制整型
intvar = 0x111
print( intvar,type(intvar),id(intvar) )		# ==> 273 <class 'int'> 2930551795408

# 输出整型变量的各种进制数值
intvar = 511
print( '%x' % intvar,'%d' % intvar,'%o' % intvar,bin(intvar) )	# ==> 1ff 511 777 0b111111111
print( bin(intvar),oct(intvar),hex(intvar) )		# ==> 0b111111111 0o777 0x1ff
```



##### 浮点型 float

```python
# 浮点型 float
floatvar = 3.14
print( floatvar,type(floatvar),id(floatvar) )		# ==> 3.14 <class 'float'> 1465280307656

floatvar = 314e-2
print( floatvar,type(floatvar),id(floatvar) )		# ==> 3.14 <class 'float'> 1465280307656

floatvar = 0.314e1
print( floatvar,type(floatvar),id(floatvar) )		# ==> 3.14 <class 'float'> 1465280307656
```



##### 布尔型 bool

```python
# 布尔型 bool
# True 真   False 假
boolvar = True
print( boolvar,type(boolvar),id(boolvar) )			# ==> True <class 'bool'> 1699858864

boolvar = False
print( boolvar,type(boolvar),id(boolvar) )			# ==> False <class 'bool'> 1699858896
```



##### 复数型 complex

```python
# 复数型 complex
"""
复数 = 实数 + 虚数
        6  +  3j
j 如果有一个数，它的平方等于 -1，那么这个数就是 j
"""

complexvar = 6 + 3j
print( complexvar,type(complexvar),id(complexvar) )		# ==> (6+3j) <class 'complex'> 2516763973808

complexvar = 33j
print( complexvar,type(complexvar),id(complexvar) )		# ==> 33j <class 'complex'> 2516733177552

complexvar = complex(3,2)
print( complexvar,type(complexvar),id(complexvar) )		# ==> (3+2j) <class 'complex'> 2516763973776
```



### 2.字符串类型 str

```python
# 字符串类型 str
"""
转义字符 \
1. 把无意义的字符变得有意义
2. 把有意义的字符变得无意义

\n    	换行
\r		回车，将当前位置移到本行开头
\t 		水平制表符，Tab	
"""

# 单引号里面的字符串
strvar = 'I am alan\rI have a \'dream\''
print( strvar,type(strvar) )		# ==> I have a 'dream' <class 'str'>

# 单双引号组合使用
strvar = "I am alan\rI have a 'dream'"
print( strvar,type(strvar) )		# ==> I have a 'dream' <class 'str'>

# 三引号里面的字符串
strvar = '''
"I am alan"				
"I have a 'dream'"
'''
print( strvar,type(strvar) )

strvar = """
'''
"I am alan"
"I have a 'dream'"
'''
"""
print( strvar,type(strvar) )

# 带有元字符的字符串不转义输出
strvar = r'C:\APP\nXshell\rLanguages'
print( strvar,type(strvar) )		# ==> C:\APP\nXshell\rLanguages <class 'str'>

# ### 字符串的格式化
# %d 整型占位符
strvar = 'I am %d years old' % (3)
print( strvar,type(strvar) )		# ==> I am 3 years old <class 'str'>

strvar = 'I am %3d years old' % (3)
print( strvar,type(strvar) )		# ==> I am   3 years old <class 'str'>

strvar = 'I am %-3d years old' % (3)
print( strvar,type(strvar) )		# ==> I am 3   years old <class 'str'>

# %f 浮点型占位符
strvar = 'I Have $ %10f' % (99.99)
print( strvar,type(strvar) )		# ==> I Have $  99.990000 <class 'str'>

strvar = 'I Have $ %10.3f' % (99.99)
print( strvar,type(strvar) )		# ==> I Have $     99.990 <class 'str'>

strvar = 'I Have $ %-10.3f' % (99.99)
print( strvar,type(strvar) )		# ==> I Have $ 99.990     <class 'str'>

# %s 字符串占位符
strvar = '%s good' % ('I hope the world to be')
print( strvar,type(strvar) )		# ==> I hope the world to be good <class 'str'>

# 字符串的特点： 可获取 不可修改 有序
# 定义一个字符串
# 正向索引		 0123456789 ................
strvar = 			"I hope the world to be good"
# 逆向索引		 ................        -2-1
print(strvar[3])		# ==> o
# strvar[3] = bad	# 修改会显示 Error

# 字符串的拼接 +
strvar = 'abc' + 'efg'
print (strvar)			# ==> abcefg

# 字符串的重复 *
strvar = 'abc' * 3
print (strvar)			# ==> abcabcabc

# 字符串的跨行拼接 \
strvar = 'abc' \
'123'
print (strvar)			# ==> abc123

# 字符串的索引
#		  012
strvar = 'abc'
#		-3-2-1
print (strvar[-1])		# ==> c

# 字符串的切片
# 语法: 字符串[开始索引:结束索引:间隔值]
strvar = 'abcdefg'

# 从开始索引截取到最后一个字符
var = strvar[1:]
print(var)				# ==> bcdefg

# 从开头截取到指定的结束索引之前
var = strvar[:3]
print(var) 				# ==> abc

# 从指定的开始索引截取到指定的结束索引之前
var = strvar[2:-1]
print(var)				# ==> cdef

# 从指定的开始索引截取到指定的结束索引之前按照指定的间隔截取
var = strvar[1:6:2] 	# ==> bdf
print(var)

# 不指定开始索引和结束索引,默认从开始截取到最后，即全部截取

# 正向截取
var = strvar[::4] 		# ==> ae
print(var)

# 逆向截取
var = strvar[::-1]	 	# ==> gfedcba	字符串反转
print(var)

var = strvar[::-2] 		# ==> geca
print(var)

# 截取所有字符串
var = strvar[:]
print(var)			# ==> bcdefg

var = strvar[::]
print(var)			# ==> abcdefg
```



### 3.列表类型 list

```python
# 列表型 list []
# 特点：可获取 可修改 有序

# 定义一个空列表
listvar = []
print( listvar,type(listvar) )		# ==> [] <class 'list'>

# 定义一个普通列表
# 正向索引 0   1    2     3   4     5
listvar = [1,1.5,'alan',True,3j,"I am C"]
# 逆向索引 -6  -5   -4    -3  -2   -1
print( listvar,type(listvar) )	# ==> [1, 1.5, 'alan', True, 3j, 'I am C'] <class 'list'>

# 获取列表中的一个数据
listvar = [1,1.5,'alan',True,3j,"I am C"]
var = listvar[5]
print( var,type(var) )		# ==> I am C <class 'str'>

var = listvar[-1]
print( var,type(var) )		# ==> I am C <class 'str'>

# 获取容器类型数据的长度或元素个数(数字类型无法获取) len
listvar = [1,1.5,'alan',True,3j,"I am C"]
var = len(listvar[2])
print( var,type(var) )		# ==> 4 <class 'int'>

var = len(listvar)
print( var,type(var) )		# ==> 6 <class 'int'>

print( len(listvar[2]),len(listvar) )		# ==> 4 6

# 修改列表中的元素
listvar = [1,1.5,'alan',True,3j,"I am C"]
listvar[2] = 'yui'
print(listvar)		# ==> [1, 1.5, 'yui', True, 3j, 'I am C']

# append 在列表尾部添加一个元素
listvar = [1,1.5,'alan',True,3j,"I am C"]
listvar.append('Hi')
print(listvar)		# ==> [1, 1.5, 'alan', True, 3j, 'I am C', 'Hi']

# extend 在列表尾部添加多个元素
listvar = [1,1.5,'alan',True,3j,"I am C"]
listvar.extend(['dream','over'])
print(listvar)		# ==> [1, 1.5, 'alan', True, 3j, 'I am C', 'dream', 'over']

listvar += [7,'Have']
print(listvar)	# ==> [1, 1.5, 'alan', True, 3j, 'I am C', 'dream', 'over', 7, 'Have']

# insert 在列表中插入一个元素
listvar = [1,1.5,'alan',True,3j,"I am C"]
listvar.insert(1,'pig')	# 将 pig 这个元素插入到该列表中 下标为 1 (即第二个元素)
print(listvar)		# ==> [1, 'pig', 1.5, 'alan', True, 3j, 'I am C']

# remove 从列表中删除一个元素
listvar = [1,1.5,'alan',True,3j,"I am C"]
listvar.remove(True)
print(listvar)		# ==> [1.5, 'alan', True, 3j, 'I am C']

# del 语句 删除一个元素
listvar = [1,1.5,'alan',True,3j,"I am C"]
del listvar[0]
print(listvar)		# ==> [1.5, 'alan', True, 3j, 'I am C']

# pop 删除/取出一个元素
listvar = [1,1.5,'alan',True,3j,"I am C"]
listvar.pop(1)		# 如果 () 里面为空，则默认 删除/取出 最后一个元素
print (listvar)		# ==> [1, 'alan', True, 3j, 'I am C']


# ### 列表分片
# 原列表不会发生任何改变
# 打印列表中从下标为 1 ~ 3 之间的所有元素(不包括 3)
listvar = [1,1.5,'alan',True,3j,"I am C"]
print(listvar[1:3])		# ==> [1.5, 'alan']

# 打印列表中从下标为 0 ~ 3 之间的所有元素(不包括 3)
listvar = [1,1.5,'alan',True,3j,"I am C"]
print(listvar[:3])		# ==> [1, 1.5, 'alan']

# 打印列表中从下标为 1 ~ 最后一个 之间的所有元素(包括 最后一个)
listvar = [1,1.5,'alan',True,3j,"I am C"]
print(listvar[1:])		# ==> [1.5, 'alan', True, 3j, 'I am C']

# 打印列表中从下标为 0 ~ 最后一个 之间的所有元素(包括 最后一个)
listvar = [1,1.5,'alan',True,3j,"I am C"]
listvar2 = (listvar[:])	# 相当于复制一个列表，这个列表改变了，原列表也不会发生任何变化
listvar2.append('ADD')
print(listvar2)		# ==> [1, 1.5, 'alan', True, 3j, 'I am C', 'ADD']
print(listvar)		# ==> [1, 1.5, 'alan', True, 3j, 'I am C']

# count 查看一个元素在该列表中所出现的次数
listvar = [1,1,1,1,5,1,18]
print(listvar.count(1))			# ==> 5

# reverse 将列表原地翻转
# 即第 0 个元素与第 -1 个元素调换，第 1 个元素与 -2 个元素调换，依次全部调换
listvar = [1,1,1,1,5,1,18]
listvar.reverse()
print(listvar)			# ==> [18, 1, 5, 1, 1, 1, 1]

# sort 将列表进行排序
listvar = [1,1,1,1,5,1,18]
listvar.sort()
print(listvar)			# ==> [1, 1, 1, 1, 1, 5, 18]

listvar.sort(reverse=True) 	# 按逆序排序
print(listvar)			# ==> [18, 5, 1, 1, 1, 1, 1]
```



### 4.元组类型 tuple

```python
# 元组 tuple ()
# 特点： 可获取 不可修改 有序

# 正向索引	 0    1     2
tuplevar = 	(1,'dream',True)
# 逆向索引   -3   -2    -1

print( tuplevar,type(tuplevar),tuplevar[2],type(tuplevar[2]) )
# ==> (1, 'dream', True) <class 'tuple'> True <class 'bool'>

# 逗号是区分是否为元组的标识
tuplevar = 1,'dream',True
print( tuplevar,type(tuplevar),tuplevar[2],type(tuplevar[2]) )
# ==> (1, 'dream', True) <class 'tuple'> True <class 'bool'>

# 元组内容不可修改
# tuplevar[1] = 'TTY'  # 显示 Error

# 定义一个空元组
tuplevar = ()
print( tuplevar,type(tuplevar) )		# ==> () <class 'tuple'>

# 在元组中插入元素
tuplevar=(1,2,3,4,5,6)
tuplevar = tuplevar[:3] + ('insert',) + tuplevar[3:]
print(tuplevar)			# ==> (1, 2, 3, 'insert', 4, 5, 6)

```



### 5.集合类型 set

```python
# 集合类型 set{}
"""
特点: 无序 自动去重 无法获取元素 交叉并补

定义集合的值的规范：
如下类型才被允许 (可哈希数据类型)
Number(int,bool,float,complex),str,tuple

不允许的类型 (不可哈希数据类型)
list dict set
"""

# 定义一个集合
setvar = {'alan','yui','totoro','cat'}
print( setvar,type(setvar) )		# ==> {'cat', 'totoro', 'yui', 'alan'} <class 'set'>

setvar = {'alan','yui','totoro','cat','yui','totoro'}
print( setvar,type(setvar) )		# ==> {'cat', 'totoro', 'yui', 'alan'} <class 'set'>

# 定义一个空的集合
setvar = set()
print( setvar,type(setvar) )		# ==> set() <class 'set'>

# 集合类型无法获取获取元素
# print(setvar[1])    				# ==> Error

```



### 6.字典类型 dict

```python
# 字典类型 dict {}
"""
键值对的形式 表面有序 实际无序 
{"键1":"值1","键2":"值2"}

定义字典的键的规范： 
如下类型才被允许 (可哈希数据类型)
Number(int,bool,float,complex),str,tuple

不允许的类型 (不可哈希数据类型)
list dict set
"""

# 定义一个字典
dictvar = {"top":"dog","middle":"fox","bottom":"cinder"}
print( dictvar,type(dictvar) )	
# ==> {'top': 'dog', 'middle': 'fox', 'bottom': 'cinder'} <class 'dict'>

dictvar = {6:"A",True:"B",3.14:"C",3+2j:"D","i am C":"E",('a','cc',1):"F","ME":"G"}
print(dictvar)
# ==> {6: 'A', True: 'B', 3.14: 'C', (3+2j): 'D', 'i am C': 'E', ('a', 'cc', 1): 'F', 'ME': 'G'}

# 获取字典中元素的值
print(dictvar["ME"])			# ==> G
print(dictvar[('a','cc',1)])	# ==> F

# 修改字典中元素的值
dictvar["ME"] = "cat"
print(dictvar)
# ==> {6: 'A', True: 'B', 3.14: 'C', (3+2j): 'D', 'i am C': 'E', ('a', 'cc', 1): 'F', 'ME': 'cat'}

# 添加字典中的元素
dictvar["bottom"] = "pig"
print(dictvar)
# ==> {6: 'A', True: 'B', 3.14: 'C', (3+2j): 'D', 'i am C': 'E', ('a', 'cc', 1): 'F', 'ME': 'cat', 'bottom': 'pig'}

# 定义一个空字典
dictvar = {}
print( dictvar,type(dictvar) )		# ==> {} <class 'dict'>

# 判断一个变量的类型是否为指定的类型
var = {'a':1,'b':2}
print(isinstance(var,int))			# ==> False
print(isinstance(var,dict))			# ==> True
```



## 数据的缓存机制

```python
# 同一个文件中 数据的缓存机制
# 对于整型而言，-5 ~ 正无穷范围内的相同数值 id 一致
a = 3333
b = 3333
print( id(a),id(b) )	# ==> 2113489894160 2113489894160

# 对于浮点型而言，非负数范围内的相同数值 id 一致
a = 3.14
b = 3.14
print( id(a),id(b) )	# ==> 2113460117960 2113460117960

a = -3.14
b = -3.14
print( id(a),id(b) )	# ==> 2113460118152 2113460118176

# 对于布尔型而言，值相同则 id 一致
a = True
b = True
print( id(a),id(b) )	# ==> 1699858864 1699858864

a = True
b = False
print( id(a),id(b) )	# ==> 1699858864 1699858896

# 对于复数型而言，仅仅在只有虚数的情况下且虚数值相同并在正数范围内时 id 一致
# 实数 + 虚数 数值相同 id 不一致
a = 3+2j
b = 3+2j
print( id(a),id(b) )	# ==> 2113490884944 2113490884976

# 正数范围内，仅有虚数且数值相同 id 一致
a = 2j
b = 2j
print( id(a),id(b) )	# ==> 2113460088528 2113460088528

# 负数范围内，仅有虚数且数值相同 id 不一致
a = -3j
b = -3j
print( id(a),id(b) )	# ==> 2113490885008 2113490885040

# 对于 字符串类型 和空元组 而言，值相同的情况下 id 一致
a = 'alan'
b = 'alan'
print( id(a),id(b) )	# ==> 2113489785216 2113489785216

a = ()
b = ()
print( id(a),id(b) )	# ==> 2113459781704 2113459781704

# 对于 列表 元组(非空元组) 集合 字典 而言，无论什么情况下 id 都不一致
a = [1,2,3] 
b = [1,2,3]
print( id(a),id(b) )	# ==> 2113490807880 2113489806984

a = (1,2,3) 
b = (1,2,3)
print( id(a),id(b) )	# ==> 2113490800000 2113490800072

a = {1,2,3}
b = {1,2,3}
print( id(a),id(b) )	# ==> 2113489904808 2113490817544

a = {'x':7,'y':3}
b = {'x':7,'y':3}
print( id(a),id(b) )	# ==> 2113489759688 2113489759760

a = {}
b = {}
print( id(a),id(b) )  	# ==> 2113489760120 2113489759688
# 为什么 b 的 id 会和上一个 a 的 id 一致 ？
```



## 数据类型的强制转换

### 1.数字类型的强制转换

```python
# Number 数字类型的强制转换 (int float complex bool)
a = 2
b = -3.14
c = 3-2j
d = True
e = '5113'
f = 'alan1'

# 强制转换成 int
var = int(b)				# -3.14 ==> -3
print( var,type(var) )		# -3 <class 'int'>
		
# var = int(c)				# Error 复数类型无法转换为整型   				
# print( var,type(var) )

var = int(d)				# True ==> 1  False ==> 0
print( var,type(var) )		# ==> 1 <class 'int'>

var = int(e)				# '5113' ==> 5113
print( var,type(var) )		# ==> 5113 <class 'int'>

# var = int(f)				# 'alan1' ==> Error 	
# print( var,type(var) )   


# 强制转换成 float
var = float(a)				# 2 ==> 2.0
print( var,type(var) )		# ==> 2.0 <class 'float'>

# var = float(c)			# Error 复数类型无法转换为浮点型
# print( var,type(var) )

var = float(d)				# True ==> 1.0  False ==> 0.0
print( var,type(var) )		# ==> 1.0 <class 'float'>

var = float(e)				# '5113' ==> 5113.0
print( var,type(var) )		# ==> 5113.0 <class 'float'>

# var = float(f)			# 'alan1' ==> Error
# print( var,type(var) )


# 强制转换成 complex
var = complex(a)			# 2 ==> (2+0j)
print( var,type(var) )		# ==> (2+0j) <class 'complex'>

var = complex(b)			# -3.14 ==> (-3.14+0j)
print( var,type(var) )		# ==> (-3.14+0j) <class 'complex'>

var = complex(d)			# True ==> (1+0j)  False ==> (0j)
print( var,type(var) )		# ==> (1+0j) <class 'complex'>

var = complex(e)			# 5113 ==> (5113+0j)
print( var,type(var) )		# ==> (5113+0j) <class 'complex'>

# var = complex(f)			# 'alan1' ==> Error
# print( var,type(var) )

# 强制转换成 bool
# 非 0 0.0 0j False '' [] () {} set() None 的情况下均为 True 
var = bool(a)				# 2 ==> True
print( var,type(var) )		# ==> True <class 'bool'>

var = bool(b)				# -3.14 ==> True
print( var,type(var) )		# ==> True <class 'bool'>

var = bool(c)				# 3-2j ==> True
print( var,type(var) )		# ==> True <class 'bool'>

var = bool(e)				# '5113' ==> True
print( var,type(var) )		# ==> True <class 'bool'>

var = bool(f)				# 'alan1' ==> True
print( var,type(var) )		# ==> True <class 'bool'>

# 各类数字类型的初始值
intvar = int()
print(intvar,type(intvar))			# ==> 0 <class 'int'>
floatvar = float()
print(floatvar,type(floatvar))		# ==> 0.0 <class 'float'>
complexvar = complex()
print(complexvar,type(complexvar))	# ==> 0j <class 'complex'>
boolvar = bool()
print(boolvar,type(boolvar))		# ==> False <class 'bool'>


# 针对数字型的自动类型转换 int float complex bool
# 默认将精度从低向高进行转换
# bool ==> int ==> float ==> complex

# bool + int
var = True + 10				# 1 + 10 = 11
print( var,type(var) )		# ==> 11 <class 'int'>	

# bool + float
var = False + 3.14			# 0.0 + 3.14 = 3.14
print( var,type(var) )		# ==> 3.14 <class 'float'>	

# bool + complex
var = True + (10 + 2j)	# (1 + 0j) + (10 + 2j) = (11 + 2j)
print( var,type(var) )	# ==> (11+2j) <class 'complex'>		

# int + float
var = 3 + 3.14			# 3.0 + 3.14 = 6.140000000000001
print( var,type(var) )	# ==> 6.140000000000001 <class 'float'>				

# int + complex
var = 10 + (3 + 6j)		# (10 + 0j) + (3 + 6j) = (13 + 6j) 
print( var,type(var) )	# ==> (13+6j) <class 'complex'>		

# float + complex
var = 3.14 + (3 + 6j)	# (3.14 + 0j) + (3 + 6j) = (6.140000000000001 + 6j) 
print( var,type(var) )	# ==> (6.140000000000001+6j) <class 'complex'>
```



### 2.容器类型的强制转换

```python
# 容器类型的强制转换 str list tuple set dict
a = 'alan'
b = [3,a,1]
c = ("a","7","c")
d = {3,(5,6,7)}
e = {"a":1,"b":2}
f = 3.14

# 强制转换成字符串 str
# 仅是单纯的将当前数据类型加上了引号
var = str(b)					# [3,a,1] ==> '[3,'alan',1]'
print( var,type(var) )			# ==> [3, 'alan', 1] <class 'str'>

# 原型化输出 引号会被显示出来
print(repr(var))				# ==> "[3, 'alan', 1]"

var = str(e)					# {"a":1,"b":2} ==> '{"a":1,"b":2}'
print( var,type(var) )			# ==> {'a': 1, 'b': 2} <class 'str'>


# 强制转换成列表 list
"""
如果是字符串类型，则会将里面的每个字符都当成列表中的一个元素
如果是字典类型，则只会将字典里面的键名保留
否则只是相当于将数据最外层的括号变成了 [] 列表
"""
# 如果是字符串类型，则会将里面的每个字符都当成列表中的一个元素
var = list(a)					# 'alan' ==> ['a','l','a','n']
print( var,type(var) )			# ==> ['a', 'l', 'a', 'n'] <class 'list'>

# 如果是字典类型，则只会将字典里面的键名保留
var = list(e)					# {"a":1,"b":2} ==> ['a','b']
print( var,type(var) )			# ==> ['a', 'b'] <class 'list'>

# 除字符串和字典类型外，则只是相当于将数据最外层的括号变成了 []
var = list(d)						# {3,(5,6,7)} ==> [3,(5,6,7)]
print( var,type(var) ,var[1][1])	# ==> [3, (5, 6, 7)] <class 'list'> 6

# 数字类型无法转换为列表型
# var = list(f)						# 3.14 ==> Error
# print( var,type(var) )


# 强制转换成元组 tuple
"""
如果是字符串类型，则会将里面的每个字符都当成列表中的一个元素
如果是字典类型，则只会将字典里面的键名保留
否则只是相当于将数据最外层的括号变成了 () 元组
"""
var = tuple(a)					# 'alan' ==> ('a','l','a','n')
print( var,type(var) )			# ==> ('a', 'l', 'a', 'n') <class 'tuple'>

var = tuple(d)					# {3,(5,6,7)} ==> (3,(5 6,7))
print( var,type(var) )			# ==> (3, (5, 6, 7)) <class 'tuple'>

var = tuple(e)					# {"a":1,"b":2} ==> ('a','b')
print( var,type(var) )			# ==> ('a', 'b') <class 'tuple'>


# 强制转换成集合 set
"""
如果是字符串类型，则会将里面的每个字符都当成列表中的一个元素
如果是字典类型，则只会将字典里面的键名保留
否则只是相当于将数据最外层的括号变成了 {} 集合
"""
var = set(a)					# 'alan' ==> {'a','n','l'}
print( var,type(var) )			# ==> {'a', 'n', 'l'} <class 'set'>

var = set(b)					# [3,a,1] ==> {1,3,'alan'}
print( var,type(var) )			# ==> {1, 3, 'alan'} <class 'set'>

var = set(e)					# {"a":1,"b":2} ==> {'b','a'}
print( var,type(var) )			# ==> {'b', 'a'} <class 'set'>


# 二级容器(列表 元组 集合 字典)

# 二级列表
lst = [1,2,3,[5,6,7]]
print(lst[-1][1],lst)			# ==> 6 [1, 2, 3, [5, 6, 7]]

# 二级元组
tup = (1,2,3,(5,6,7))
print(tup[-1][1],tup)			# ==> 6 (1, 2, 3, (5, 6, 7))

# 二级集合
setvar = {1,1,3,(5,5,7)}		# 集合里无法放入列表集合和字典
print(setvar)					# ==> {1, 3, (5, 5, 7)}

# 二级字典
dictvar = {'a':1,'b':2,'c':{"d":3,"e":5}}
print(dictvar['c']['e'])			# ==> 5

# 三级容器
container = [1,2,(3,5,{'a':1,'b':2})]
print(container[-1][-1]['a'])		# ==> 1

# 四级容器
container = (1,2,[3,5,{'a':1,'b':2,'c':{1,1,3},'d':['a','b','c']}])
print(container[-1][-1]['c'],type(container[-1][-1]['c']))	# ==> {1, 3} <class 'set'>
print(container[-1][-1]['d'][2])		# ==> c


# 等长的二级容器
# 容器里面的元素都是容器且容器里面的元素个数都相同
lst = [(1,2,3),['a','b','c']]
lst = {'a':[1,2],'b':(6,7)}
lst = ([1,2],(6,9))

# 强制转换成字典
# 要求：等长的二级容器且里面的元素个数为 2

lst = [['a',1],['b',2]]
dictvar = dict(lst)
print(dictvar,type(dictvar))		# ==> {'a': 1, 'b': 2} <class 'dict'>

tupvar = (['a',1],('b',2))
dictvar = dict(tupvar)
print(dictvar,type(dictvar))		# ==> {'a': 1, 'b': 2} <class 'dict'>

setvar = { ('a',1),('b',2),('c',3)}
dictvar = dict(setvar)				# ==> {'c': 3, 'a': 1, 'b': 2} <class 'dict'>
print(dictvar,type(dictvar))		# ==> {'c': 3, 'b': 2, 'a': 1} <class 'dict'>

# 语法上正确，不推荐使用，因为集合无序，难以满足需求
lst = [{'a',2}]
dictvar = dict(lst)					# ==> {2: 'a'} <class 'dict'>
print(dictvar,type(dictvar))		# ==> {'a': 2} <class 'dict'>

# 如果是字符串的形式，字符串的长度只能为 2，否则 Error
lst = ['a1','y2','z3']
dictvar = dict(lst)
print(dictvar,type(dictvar))		# ==> {'a': '1', 'y': '2', 'z': '3'} <class 'dict'>
```



## 运算符

### 1. 算术运算符

```python
# 算术运算符 + - * / // % **

# / 结果永远为 浮点型
result = 6 / 3
print(result)			# ==> 2.0

# // 地板除 默认将计算结果仅保留整数位，如果除数或被除数为小数则结果为仅保留整数位的浮点型
result = 7 // 2
print(result)			# ==> 3

result = 7 // 2.5
print(result)			# ==> 2.0

# % 取余
result = 7 % 5
print(result)			# ==> 2

result = -11 % 7
print(result)			# ==> 3

result = -7 % 11
print(result)			# ==> 4

result = 11 % -7
print(result)			# ==> -3

result = -11 % -7
print(result)			# ==> -4

# ** 幂运算
result = 2 ** 7
print(result)			# ==> 128
```



### 2. 比较运算符

```python
# 比较运算符 > < >= <= == !=
# 所返回的结果只有 True 或 False
result = 1 > 3
print(result)			# ==> False

result = 3 >= 1
print(result)			# ==> True

result = 1 != 1
print(result)			# ==> False

result = 3 < 4 < 5		# 等同于 3 < 4 and 4 < 5
print(result)			# ==> True
```



### 3. 赋值运算符

```python
# 赋值运算符 = += -= *= /= //= %= **=
# 将一个常量或变量的值赋值给一个变量
# = 
a = 90
b = 100
a = b
print(a)		# ==> 100

# += 加法赋值运算符
a = 90
b = 100
a += b			# a = a + b
print(a)		# ==> 190

# **= 幂赋值运算符
a = 2
b = 9
a **= b 		# a = a ** b	
print(a)		# ==> 512
```



### 4. 成员运算符

```python
# 成员运算符 in 和 not in 
# 仅针对容器类型数据，结果返回 True 或 False

# 针对字符串(判断的内容必须是连续性的)
var = 'I Have A Dream !'
result = ' Ha' in var
print(result)			# ==> True

result = 'IH' in var
print(result)			# ==> False

# 针对列表 元组 集合
var = [1,2,3,57]
result = 7 in var
print(result)			# ==> False

var = {'alan','yui','yutou'}
result = 'yu' in var
print(result)			# ==> False

var = ['alan','yui','yutou',(1,2,3)]
result = 2 in var
print(result)			# ==> False

result = (1,2,3) in var
print(result)			# ==> True

var = {'alan','yui','yutou',('a','b','c')}
result = 'yu' not in var
print(result)			# ==> True

# 针对字典(仅仅只是键名去判断)
var = {'a':1,'b':2,'c':3}
result = 1 in var
print(result)			# ==> False

result = a in var
print(result)			# ==> False

result = 'a' in var
print(result)			# ==> True
```



### 5. 身份运算符

```python
# 身份运算符 is 和 is not
# 判断两个数据在内存中的物理地址是否一致，返回 True 或 False
# 无法直接判断列表里面嵌套列表里面的元素

# 针对于整型 -5 ~ 正无穷，如果两个值相同则 id 一致
a = 100
b = 100
print(a is b)			# ==> True

a = -6
b = -6
print(a is b)			# ==> False

# 针对于浮点型非负数范围内的两个值相同则 id 一致
a = 3.14
b = 3.14
print(a is b)			# ==> True

a = -3.14
b = -3.14
print(a is b)			# ==> False

# 针对于 字符串类型 和空元组 而言，值相同的情况下 id 一致
a = 'abc'
b = 'abc'
print(a is b)			# ==> True

# 针对于列表 元组(非空元组) 集合 字典 而言，无论什么情况下 id 都不一致
a = [1,2,3]
b = [1,2,3]
print(a is b)			# ==> False
```



### 6. 逻辑运算符

```python
# 逻辑运算符 and or not
# 逻辑运算符的优先级 () > not > and > or

# and 逻辑与
# 一假则假 全真则真
result = True and True
print(result)			# ==> True

result = True and False
print(result)			# ==> False

result = False and True
print(result)			# ==> False

result = False and False
print(result)			# ==> False

# or 逻辑或
# 一真则真 全假则假 
result = True or True
print(result)			# ==> True

result = True or False
print(result)			# ==> True

result = False or True
print(result)			# ==> True

result = False or False
print(result)			# ==> False

# not 逻辑非
# 真为假 假为真
result = not True
print(result)


# 逻辑短路现象
"""
True or print('exit')
False and print('exit')
以上两种情况下，逻辑运算符后面的代码不会执行
"""

result = 3 or 2
print(result)			# ==> 3		

result = 3 and 2
print(result)			# ==> 2

result = 3 or 2 and 1
print(result)			# ==> 3

result = 0 or 2 and 1
print(result)			# ==> 1

result = 0 and 2 and 1
print(result)			# ==> 0

result = 0 and 2 or 1
print(result)			# ==> 1

result = (3 or 2) and 1
print(result)			# ==> 1

result = not (3 or 2) and 1
print(result)			# ==> False

result = not (3 or 2) or 1
print(result)			# ==> 1

result = 3 > 2 and 5 < 1 or 7 > 9
print(result)			# ==> False

result = 2 > 3 and 5 < 7 or 3 < 9
print(result)			# ==> True

result = 5 > 7 and 3 < 6 or 2 < 5 and 8 > 9 and 6 < 7 or 1 > 2
print(result)			# ==> False

"""
逻辑运算符的优先级 () > not > and > or
result = 5 > 7 and 3 < 6 or 2 < 5 and 8 > 9 and 6 < 7 or 1 > 2
result = (False and True) or (True and False and True) or False
result = False or False or False
result = False
"""
```



### 7. 位运算符

```python
# 位运算符 & | ^ << >> ~
# 位运算符优先级 ： "<< >>"  >  "&"  >  "^"  >  "|"

# & 按位与
a = 13				# 1101
b = 11				# 1011
var = a & b			# 1001			and
print(var)			# ==> 9

# | 按位或
a = 13				# 1101
b = 11				# 1011
var = a | b			# 1111			or
print(var)			# ==> 15

# ^ 按位异或
a = 13				# 1101
b = 11				# 1011
var = a ^ b 		# 0110		同位 相同为 0 不同为 1
print(var)			# ==> 6

# << 左移 
var = 3 << 3		# 乘以 2 的 n 次幂
print(var)			# ==> 24
"""
00011
向左移动 3 位
11000
"""

# >> 右移
var = 10 >> 2		# // 除以 2 的 n 次幂
print(var) 			# ==> 2
"""
1010
# 向右移动 2 位
0010
"""

# ~ 按位非
var = ~(-9)			# 结果为 -(n + 1)
print(var)			# ==> 8	
```



### 8. 符号的优先级问题

```python
# 算术运算符 > 位运算符 > 比较运算符 > 身份运算符 > 成员运算符 > 逻辑运算符 > 赋值运算符
"""
幂运算			**				# 当 ** 的右侧是一个一元运算符的时候优先级会比它低
正负号			~ + -			# 一元运算符
算术操作符		* / // + -
位运算符		<< >> & ^ |
比较操作符		< <= > >= == !=
身份运算符		is is not
成员运算符		in not in
逻辑操作符		not and or
"""
var = 5 + 2 << 7 // 3 is 28 and False
print(var)		# ==> False
'''
var = 7 << 2 is 28 and False
var = 28 is 28 and False
var = True and False
var = False 
'''
# 提升代码的可读性，可使用 ()
var = (5 + 2) << (7 // 3) is 28 and False
```



## 流程控制

```python
# 流程控制
'''
流程: 代码执行的过程
流程控制: 对代码执行过程的把控

三大结构：
	顺序结构: 从上到下依次执行
	分支结构: 可分为 1.单项分支 2.双项分支 3.多项分支 4.巢状分支
'''
# 代码块 以冒号作为开始，用缩进来划分相同的作用域
if 3 == 5:
	print(1) 
	print(2)
print(3)

# 缩进不能混用，选择 全部使用 Tab 键缩进 或是 全部使用 4 个空格键


# 单项分支
name = 'alan'
if name == 'alan':
	print('I hope the world to be good')

# 双项分支
name = 'accc'
if name == 'alan':
	print('I hope the world to be good !')
else:
	print('Are you okay ?')

# 如果条件表达式为 True，执行 if 这个代码块，也称为真区间
# 如果条件表达式为 False，执行 else 这个代码块，也称为假区间

# 练习题：登录网站
"""
账号是 admin
密码是 linux
如果条件满足，打印 登录成功，否则，打印 登陆失败 
"""
'''
username = input('Please input username: ')
password = input('Please input password: ')
if username == 'admin' and password == 'linux' :
	print('login successful')
else:
	print('Account and password verification failed')
'''

# 多项分支
rich = True
handsome = True

if rich == True:
	print('I like you')
elif handsome == True:
	print('I love u')
else:
	print('You are a good man')
'''
先判断条件表达式 1 是否为 True，为 True 则执行对应的代码块，结束判断
为 False，则判断 elif 这个分支的条件是否为 True，为 True 则执行对应的代码块，结束判断
为 False，则执行 else 这个分支的代码块
elif + 表达式		可以是 0 个 或者 多个
else 不能加表达式		可以是 0 个 或者 1 个
'''

# 巢状分支
rich = True
handsome = True
energetic = False

if rich == True:
	if handsome == True:
		if energetic == True:
			print('I love u')
		else:
			print("Let's be friends first")
else:
	print('You are a good man')


# 小练习 身高测试
# 输入身高，判断属于哪一类身高标准
'''
100 cm ~ 150 cm		bad
150 cm ~ 165 cm 	short
165 cm ~ 175 cm		normal
175 cm ~ 185 cm		good
185 cm ~ 250 cm		giant	
'''
'''
height = int(input('Please input your height(cm): '))
if 100 <= height <= 150:
	print('bad')
elif 150 < height <= 165:
	print('short')
elif 165 < height <= 175:
	print('normal')
elif 175 < height <= 185:
	print('good')
elif 185 < height <= 250:
	print('giant')
else:
	print('Please re-enter as required !') 
'''
```



## 循环结构

### 1.while 循环

```python
# 循环结构 while
# 打印 1 ~ 100
i = 1				# 初始化变量
while i <= 100:		# 判断条件 为 true 执行代码块，为 False 则跳出循环
	print(i)		# 代码块内容
	i += 1			# 自增自减的值

'''
第一步 初始化变量 i
判断 i <= 100 雕件成立， 返回 True,执行循环体
print(i)		==> 打印 1
i += 1			==> i = 2

第二步
判断 i <= 100,即 2 <= 100 条件成立，返回 True,执行循环体
print(i)		==> 打印 2
i += 1			==> i = 3

最后一步
当 i = 101 时
判断 101 <= 100 条件不成立，返回 False,跳出循环体，程序结束
'''

# 死循环
'''
i = 1
while True:
	i += 1
	print(i)
'''


# 计算整数 1 ~ 100 之间的累加和
# 方法一
i = 0
sum = 0
while i < 100:
	i += 1
	sum += i
print(sum)

# 方法二
i = 0
sum = 0
while True:
	i += 1
	sum += i
	if i == 100:
		break
print(sum)


# 单项循环练习
# 1.打印 一行十个小星星 ★ ☆
n = 0
while n < 10:
	n += 1
	print('☆',end='')

print('\n--------------')

# 2.用变量拼接字符串的形式，打印 一行十个小星星 ★ ☆
strvar = '' 
n = 0
while n < 10:
	n += 1
	strvar += '★'
print(strvar)

print('\n--------------')

# 3.打印 一行十个小星星，且奇数打印 ★ ，偶数打印 ☆
n = 0
while n < 10:
	n += 1
	if n % 2 != 0:
		print('★',end='')
	else:
		print('☆',end='')

print('\n--------------')


# 4.一个循环 打印十行十列小星星 ★ ☆
n = 0
while n < 100:
	n += 1
	print('☆',end='')
	if n % 10 == 0:
		print()

print('\n--------------')

# 5.一个循环 打印 十行十列 隔列 变色的小星星 ★ ☆
n = 0
while n < 100:
	n += 1
	if n % 2 != 0:
		print('☆',end='')
	else:
		print('★',end='')
	if n % 10 == 0:
		print()

print('\n--------------')

# 6.一个循环 打印 十行十列 隔行 变色的小星星 ★ ☆
n = 0
while n < 100:

	if n // 10 % 2 == 0:
		print('☆',end='')
	else:
		print('★',end='')
	
	n += 1

	if n % 10 == 0:
		print()

print('\n--------------')


# 双层循环练习 
# 1.打印十行十列的小星星 ★ ☆
i = 0
while i < 10:
	i += 1
	j = 0
	while j < 10:
		j += 1
		print('☆',end='')

	print()

print('\n--------------')

# 2.打印十行十列隔列换色的小星星 ★ ☆
i = 0
while i < 10:
	i += 1
	j = 0
	while j < 10:
		j += 1
		if j % 2 != 0:
			print('☆',end='')
		else:
			print('★',end='')
	print()

print('\n--------------')

# 3. 打印十行十列隔行换色的小星星 ★ ☆
i = 0
while i < 10:
	i += 1
	j = 0
	while j < 10:
		j += 1
		if i % 2 != 0:
			print('☆',end='')
		else:
			print('★',end='')
	print()

print('\n--------------')

# 4. 打印 99 乘法表
# 方法一
i = 0
while i < 9:
	j = 0
	i += 1
	while j < i:
		j += 1
		print('%d × %d = %-2d  ' % (i,j,i * j),end='')
	print()

print('\n--------------')

# 方法二
i = 10
while i > 0:
	j = 0
	i -= 1
	while j < i:
		j += 1
		print('%d × %d = %-2d  ' % (i,j,i * j),end='')
	print()

print('\n--------------')

# 方法三
i = 0
while i < 9:
	j = 0
	i += 1
	print('            ' * (9 - i),end='')
	while j < i:
		j += 1
		print('%d × %d = %2d  ' % (i,j,i * j),end='')
	print()

print('\n--------------')

# 方法四
i = 10
while i > 0:
	j = 0
	i -= 1
	print('            ' * (9 - i),end='')
	while j < i:
		j += 1
		print('%d × %d = %2d  ' % (i,j,i * j),end='')
	print()

print('\n--------------')


# 在数字 100 ~ 999 之间找 吉利数字(如： 111 333 123 321 666 888 ……)
# 方法一
n = 100
while n <= 999:

	units = n % 10
	tens = n % 100 // 10
	hundreds = n // 100

	if units == tens == hundreds or \
	units == tens - 1 == hundreds - 2 or \
	units == tens + 1 == hundreds + 2:
		print(n)

	n += 1

print('\n--------------')

# 方法二
n = 100
while n <= 999:

	units = int(str(n)[2])
	tens = int(str(n)[1])
	hundreds = int(str(n)[0])

	if units == tens == hundreds or \
	units == tens - 1 == hundreds - 2 or \
	units == tens + 1 == hundreds + 2:
		print(n)

	n += 1
```



### 2.for 循环

```python
# 循环结构 for
'''
for 一般用来遍历数据
Iterable 可迭代性数据 : 容器类型数据  range 对象 迭代器
for 变量 in Iterable:
	代码块
'''

# 遍历字符串
container = 'I love u ?'
for i in container:
	print(i)

# 遍历 列表
container = ['a','b','c','d','e']
for i in container:
	print(i)

# 遍历 元组
container = ('a','b','c','d','e')
for i in container:
	print(i)

# 遍历 集合
container = {'a','b','c','d','e'}
for i in container:
	print(i)

# 遍历 字典 (字典类型，默认遍历的对象只是键名)
container = {'a':1,'b':2,'c':3}
for i in container:
	print(i)

# 变量的解包
a,b,c = 1,2,3
print(a,b,c)

a,b,c = {1,2,3}
print(a,b,c)

a,b,c = [1,2,3]
print(a,b,c)

# 遍历等长的 二级容器
container = (['aaa','abb','acc'],('111','222','333'))
for a,b,c in container:
	print(a,b,c)

# 遍历不等长的 二级容器
container = (['aaa','abb'],('111','222','333'),['Hi'])
for i in container:
	for j in i:
		print(j)

# range 函数 创建一个整数列表
# 语法： range(start, stop, step)
# 一个参数的情况
for i in range(10):
	print(i)			# range(10) ==> 0 ~ 9

# 二个参数的情况
for i in range(1,9):
	print(i)			# range(1,9) ==> 1 ~ 8

# 三个参数的情况
for i in range(1,9,3):
	print(i)			# range(1,9,3) ==> 1 4 7

# 倒序的情况
for i in range(10,1,-3):
	print(i)			# range(10,1,-3) ==> 10 7 4

    
# 使用 for 循环打印 99 乘法表
for i in range(1,10):
	for j in range(1,i+1):
		print('%d × %d = %-2d  ' % (i,j,i*j),end='')
	print()

    
# 公鸡一个五块钱，母鸡一个三块钱，小鸡三个一块钱，现在要用一百块钱买一百只鸡，问公鸡、母鸡、小鸡各多少只
for i in range(0,100 // 5):
	for j in range(0,100 // 3):
		k = 100 - i - j
		if i * 5 + j * 3 + k / 3 == 100:
			print('cocks %-2d, hens %-2d, chicks %-2d' % (i,j,k))
```



### 3.改变循环执行的状态

```python
# 循环中的关键字 pass break continue
# pass 占位 可放在代码块中 暂时不去写代码块不会报错
if 5 == 5:
	pass
print('ok')


# break 提前终止当前循环
i = 1
while i <= 10:
	if i == 5:
		break
	print(i)			# ==> 1 2 3 4
	i+=1

print('---------------------')

i = 1
while i <= 3:
	j = 1
	while j <= 3:
		print(i,j)		# ==> 1 1  1 2  2 1  2 2  3 1  3 2    
		if j == 2:
			break
		j+=1		
	i+=1

    
# continue 跳过当前循环,从下一次开始
# 打印 1 ~ 10 跳过 5
i = 1
while i <= 10:
	if i == 5:
		i += 1
		continue
	print(i)			# ==> 1 2 3 4 6 7 8 9 10
	i+=1

print('---------------------')

for i in range(1,11):
	if i == 5:
		continue
	print(i)			# ==> 1 2 3 4 6 7 8 9 10

    
# 打印 1 ~ 100 所有不含有 4 的数字
# 方法一
i = 1
while i<=100:
	if i % 10 == 4 or i // 10 == 4:
		i+=1
		continue
	print(i)
	i+=1
	
# 方法二
i = 1
while i <= 100:
	str_num = str(i)
	if "4" in str_num:
		i+=1
		continue
	print(i)	
	i+=1

# 方法三	
for i in range(1,101):
	str_num = str(i)
	if "4" in str_num:
		continue
	print(i)

# 方法四
for i in range(1,101):
	if "4" not in str(i):
		print(i)
```



## 字符串的格式化

```python
# 字符串的格式化 format

# 顺序传参
strvar = "{} is {}'s idoli".format("Bruce Lee","tom")
print(strvar)

# 索引传参
strvar = "{1} is {0}'s idoli".format("Bruce Lee","tom")
print(strvar)

# 关键字传参
strvar = "{who1} is {who2}'s idoli".format(who1="Bruce Lee",who2="tom")
print(strvar)

# 容器类型数据(列表,元组)传参
strvar = "{0[1]} hugs {1[1]}".format( ("YUI","Tom","jack") , ["Linda","Annie","Alan"] )
print(strvar)

# 字典类型传参,键不需要加上引号
strvar = "{group1[1]} hugs {group2[ld]}".format( group1 = ("YUI","Tom","jack") , group2 = {"ld":"Linda","an":"Annie","aa":"Alan"} )
print(strvar)


# format 语法
'''
^ 原字符串居中
> 原字符串居右
< 原字符串局左

语法：
	{who:.^9}
	{关键字：填充的字符 填充的位置 原字符串与填充字符的总长度 }
'''

strvar = '{who:·^9} works in {where:-^12}'.format(who='Tom',where='Shanghai')
print(strvar)			# ==> ···Tom··· works in --Shanghai--

# 进制转换等特殊符号的使用( :d :f :s :, )
# :d  整型占位符
strvar = "Han Meimei's watch is worth ${:d}".format(30000)
print(strvar)			# ==> Han Meimei's watch is worth $30000

# :6d 占用两位,默认居右
strvar = "Han Meimei's watch is worth ${:6d}".format(30000)
print(strvar)			# ==> Han Meimei's watch is worth $ 30000

# :f 浮点型占位符
strvar = "Han Meimei's watch is worth ${:f}".format(30000.99)
print(strvar)			# ==> Han Meimei's watch is worth $30000.990000

# :.1f 保留一位小数，默认会四舍五入
strvar = "Han Meimei's watch is worth ${:.1f}".format(30000.99)
print(strvar)			# ==> Han Meimei's watch is worth $30001.0

# :s 字符串占位符
strvar = "{:s}".format("hello")
print(strvar)			# ==> hello

# :, 金钱占位符
strvar = "{:,}".format(666666666)
print(strvar)			# ==> 666,666,666
```



## 字符串相关函数

```python
# 字符串相关函数

# capitalize 将字符串首字母大写
strvar = 'i hope the world to be good'
var = strvar.capitalize()
print(var)			# ==> I hope the world to be good

# title 将每个单词的首字母大写
strvar = 'i hope the world to be good'
var = strvar.title()
print(var)			# ==> I Hope The World To Be Good

# upper 将所有字母变成大写
strvar = 'i hope the world to be good'
var = strvar.upper()
print(var)			# ==> I HOPE THE WORLD TO BE GOOD

# lower 将所有字母变成小写
strvar = 'ABC'
var = strvar.lower()
print(var)			# ==> abc

# swapcase 大小写互换
strvar = 'aBcD'
var = strvar.swapcase()
print(var)			# ==> AbCd

# count 统计字符串中的某个元素的数量
strvar = 'i hope the world to be good'
var = strvar.count('e')
print(var)			# ==> 3

# find 查找某个字符串 第一次 出现的索引位置
# find('要查找的字符串'[,开始索引值,结束索引值]) 
strvar = 'i hope the world to be good'
var = strvar.find('hope')
print(var)			# ==> 2

# 没有查找到，则返回 -1
var = strvar.find('hope',7)
print(var)			# ==> -1

# 查找索引号从 10 到 15 之间的指定内容
var = strvar.find('o',10,15)
print(var)			# ==> 12

# index 查找某个字符串 第一次 出现的索引位置
# 与 find 功能相同，但是 index 如果查找不到相关的内容就会报错
# var = strvar.index('abc')
# print(var)		# ==> Error

# startswith 判断是否以某个字符或字符串开头
# startswith('要查找的字符串'[,开始索引值,结束索引值]) 
strvar = 'i hope the world to be good'
var = strvar.startswith('I')
print(var)			# ==> False

# 指定范围进行判定
var = strvar.startswith('o',18)		# 判断在 下标为 18 的元素开始之后的内容是否以字符 o 开头
print(var)			# ==> True

# endswith 判断是否以某个字符或字符串结尾
var = strvar.endswith('good')
print(var)			# ==> True

var = strvar.endswith('oo',-4,-1)
print(var)			# ==> True

# split 将字符串分割成列表(默认分割字符是空格,从左向右分割)
strvar = 'Do you like to read books ?'
var = strvar.split()
print(var)			# ==> ['Do', 'you', 'like', 'to', 'read', 'books', '?']

var = strvar.split('o')
print(var)			# ==> ['D', ' y', 'u like t', ' read b', '', 'ks ?']

var = strvar.split('o',2)	# 以字符 o 为分割符，从左向右，分割的次数为 2
print(var)			# ==> ['D', ' y', 'u like to read books ?']

# rsplit 从右向左分割
var = strvar.rsplit('o',2)	# 以字符 o 为分割符，从左向右，分割的次数为 2
print(var)			# ==> ['Do you like to read b', '', 'ks ?']


# join 按照一个指定的字符将列表拼接成字符串(任意容器类型均可)
lst = ['Are','you','okay','?']
var = '+'.join(lst)
print(var)			# ==> Are+you+okay+?


# replace 替换字符串(可选择替换的次数)
strvar = 'are you okay okay okay'
var = strvar.replace('ok','OK')
print(var)			# ==> are you OKay OKay OKay

var = strvar.replace('ok','OK',1)
print(var)			# ==> are you OKay okay okay


# isdigit 检测字符串 二进制字节流 是否全部由数字组成
strvar = '123'
var = strvar.isdigit()
print(var)			# ==> True

strvar = b'ABC23'
print(strvar , type(strvar))	# ==> b'ABC23' <class 'bytes'>
var = strvar.isdigit()
print(var)			# ==> False

# isdecimal 检测字符串是否以数字组成 必须是纯数字
strvar = "1234"
res = strvar.isdecimal()
print(res)			# ==> True

# len 计算容器类型的长度
strvar = 'abcdef'
print(len(strvar))			# ==> 6

strvar = [1,2,3,'a','b']
print(len(strvar))			# ==> 5

# strip 去除首尾双边指定的字符(默认为空白符)
strvar = ' a b    '
var = strvar.strip()
print(var)			# ==> 'a b'

strvar = ' #a b ##'
var = strvar.strip('#')
print(var)			# ==> ' #a b '
```



## 列表的相关操作

```python
# 列表的拼接(同元组)
lst = [1,2,3] + [4,5,6]
print(lst)			# ==> [1, 2, 3, 4, 5, 6]

# 列表的重复(同元组)
lst = [1,2,3] * 3
print(lst)			# ==> [1, 2, 3, 1, 2, 3, 1, 2, 3]


# 列表的切片(同元组)
# 从指定的开始索引截取到最后
lst = ['a','b','c','d','e','f','g']
var = lst[5:]
print(var)			# ==> ['f', 'g']

# 从开头截取到指定的结束索引之前
lst = ['a','b','c','d','e','f','g']
var = lst[:3]
print(var)			# ==> ['a','b','c']

# 从指定的开始索引截取到指定的结束索引之前
lst = ['a','b','c','d','e','f','g']
var = lst[1:5]
print(var)			# ==> ['b','c','d','e']

# 从指定的开始索引截取到指定的结束索引之前，且按照指定的间隔截取列表元素
lst = ['a','b','c','d','e','f','g']
var = lst[1:6:2]		# 1 3 5
print(var)			# ==> ['b','d','f']

# 截取所有列表元素
lst = ['a','b','c','d','e','f','g']
var = lst[:]
print(var)			# ==> ['a', 'b', 'c', 'd', 'e', 'f', 'g']

var = lst[::]				
print(var)			# ==> ['a', 'b', 'c', 'd', 'e', 'f', 'g']


# 列表的获取(同元组)
lst = [1,2,3]
print(lst[2])		# ==> 3

# 列表的修改
lst = [1,2,3,4,5,6]
lst[1] = 0
print(lst)			# ==> [1, 0, 3, 4, 5, 6]

# 切片式修改列表(修改的值必须是可迭代性数据 Iterable(容器类型数据 range对象 迭代器))
lst = [1,2,3,4,5,6]
lst[1:3] = 'abcdef'
print(lst)			# ==> [1, 'a', 'b', 'c', 'd', 'e', 'f', 4, 5, 6]

lst[::4] = 'ABC'	# ==> 带有间隔值的切片修改,必须切片数与修改数相同
print(lst)			# ==> ['A', 'a', 'b', 'c', 'B', 'e', 'f', 4, 'C', 6]

# 列表的删除操作
lst = [1,2,3,4,5,6]
del lst[-3]
print(lst)			# ==> [1, 2, 3, 5, 6]

# 利用切片删除元素
lst = [1,2,3,4,5,6]
del lst[::2]
print(lst)			# ==> [2, 4, 6]
```



## 列表相关函数 

```python
# 增加元素
lst = ['tom','jack']
# 1.append 在列表的尾部添加新的元素
lst.append('alan')
print(lst)			# ==> ['tom', 'jack', 'alan']

# 2.insert 在指定的索引前面插入元素
lst.insert(1,'yui')
print(lst)			# ==> ['tom', 'yui', 'jack', 'alan']

# 3.extend 追加新的元素列表来扩展原来的列表
lst.extend(range(3))
print(lst)			# ==> ['tom', 'yui', 'jack', 'alan', 0, 1, 2]

lst.extend(['a','b','c'])
print(lst)			# ==> ['tom', 'yui', 'jack', 'alan', 0, 1, 2, 'a', 'b', 'c']

# 删除元素
# 1.pop 通过指定的索引删除元素，若没有指定索引默认会删除最后的一个元素
lst = [1,2,3]
lst.pop()
print(lst)			# ==> [1, 2]

lst.pop(1)
print(lst)			# ==> [1]

# 2.remove 通过元素的值来删除,如果多个相同元素,默认删除第一个
lst = [1,3,5,1,7,1]
lst.remove(5)
print(lst)			# ==> [1,3,1,7,1]

lst.remove(1)
print(lst)			# ==> [3,1,7,1]

lst.remove(1)
print(lst)			# ==> [3,7,1]

# 3.clear 清空列表
lst.clear()
print(lst)			# ==> []


# index 获取某个值在列表中的索引
lst = ['A','B','C']
var = lst.index('B')
print(var)			# ==> 1

# 若该值在不列表中则会报错
# var = lst.index('e')
# print(var)			# ==> Error

# count 查看一个元素在该列表中所出现的次数
list = [1,1,1,1,5,1,18]
print(list.count(1))			# ==> 5

# sort 将列表进行排序(默认从小到大)
lst = [3,1,6,1,7,20,16]
lst.sort()
print(lst)			# ==> [1, 1, 3, 6, 7, 16, 20]

# 倒序
lst.sort(reverse=True)
print(lst)			# ==> [20, 16, 7, 6, 3, 1, 1]

# 字母排序，按照 ascii 编码排序
lst = ['alan','yui','tom','sufa','yoka']
lst.sort()
print(lst)			# ==> ['alan', 'sufa', 'tom', 'yoka', 'yui']


# reverse 将列表翻转
# 即第 0 个元素与第 -1 个元素调换，第 1 个元素与 -2 个元素调换，依次全部调换
lst.reverse()
print(lst)			# ==> ['yui', 'yoka', 'tom', 'sufa', 'alan']


# 对于元组来说，元素的值不能改变，但元组中的列表可以改变
tupvar = (1,2,['a','b'])
tupvar[-1][-1] = 'hi'
print(tupvar)			# ==> (1, 2, ['a', 'hi'])
```



## 深拷贝与浅拷贝

```python
# 默认情况下将列表 a 的值赋值给列表 b,当列表 a 的值发生改变时，列表b 的值也会发生改变
a = [1,2,3]
b = a
a.append(100)
print(a,b)			# ==> [1, 2, 3, 100] [1, 2, 3, 100]

# import 引入 copy 模块
import copy
'''
模块方法
copy.copy()		浅拷贝
copy.deepcopy()	深拷贝
'''

# 浅拷贝 仅仅复制了容器中元素的地址
#方法一
lst1 = [1,2,3]
lst2 = lst1.copy()
lst1.append(6)
print(lst1,lst2)			# ==> [1, 2, 3, 6] [1, 2, 3]

#方法二
lst1 = [1,2,3]
lst2 = copy.copy(lst1)
lst1.append(6)
print(lst1,lst2)			# ==> [1, 2, 3, 6] [1, 2, 3]

lst1 = [1,2,['a','b']]
lst2 = lst1.copy()
lst1[2].append(7)
print(lst1,lst2)			# ==> [1, 2, ['a', 'b', 7]] [1, 2, ['a', 'b', 7]]

# 深拷贝 拷贝所有层级的容器数据,都形成独立的一份
lst1 = [1,2,['a','b']]
lst2 = copy.deepcopy(lst1)
lst1[2].append(7)
print(lst1,lst2)			# ==> [1, 2, ['a', 'b', 7]] [1, 2, ['a', 'b']]

lst1 = [1,{'a':1,'b':[5,6]}]
lst2 = copy.deepcopy(lst1)
lst1[-1]['b'].append('ok')
print(lst1,lst2)			# ==> [1, {'a': 1, 'b': [5, 6, 'ok']}] [1, {'a': 1, 'b': [5, 6]}]
```



## 字典相关函数

```python
# 增加
dicvar = {}

dicvar['a'] = 1
print(dicvar)			# ==> {'a': 1}

# formkeys() 使用一组键和默认值创建字典
keys = ["a","b"]
dicvar = {}.fromkeys(keys,3)
print(dicvar)			# ==> {'a': 3, 'b': 3}

dicvar = {}.fromkeys(keys,[])
print(dicvar)			# ==> {'a': [], 'b': []}

dicvar['a'].append(1)
print(dicvar)			# ==> {'a': [1], 'b': [1]}

# 删除
# pop() 通过键名去删除键值对 (若没有该键名可设置默认值,预防报错)
dicvar = {'a':1,'b':2,'c':3}
dicvar.pop('b')
print(dicvar)			# ==> {'a': 1, 'c': 3}

# popitem() 删除最后一个键值对
dicvar.popitem()
print(dicvar)			# ==> {'a': 1}

# clear() 清空字典
dicvar.clear()
print(dicvar)			# ==> {}

# 修改
# update() 批量更新 该键存在就更新，不存在则添加
dicvar = {'a':1}
dicvar2 = {'a':3,'b':5}
dicvar.update(dicvar2)
print(dicvar)			# ==> {'a': 3, 'b': 5}

# 查看
# get()  通过键名获取值(若没有该键可设置默认值,预防报错)
dicvar =  {'a':1,'b':2,'c':3}
var = dicvar.get('b')
print(var)				# ==> 2

var = dicvar.get('bb')
print(var)				# ==> None

# 设置默认值
var = dicvar.get('bb','unable to find')
print(var)				# ==> unable to find

# keys() 将字典的键组成新的可迭代对象(返回一个字典所有的键)
dicvar =  {'a':1,'b':2,'c':3}
var = dicvar.keys()
print(var,type(var))	# ==> dict_keys(['a', 'b', 'c']) <class 'dict_keys'>

# values() 将字典中的值组成新的可迭代对象
var = dicvar.values()
print(var,type(var))	# ==> dict_values([1, 2, 3]) <class 'dict_values'>

# items() 将字典的键值对凑成一个个元组,组成新的可迭代对象(返回可遍历的(键, 值) 元组列表)
var = dicvar.items()
print(var,type(var))	# ==> dict_items([('a', 1), ('b', 2), ('c', 3)]) <class 'dict_items'>

for i in dicvar.items():
	print(i)
"""
('a', 1)
('b', 2)
('c', 3)
"""
for k,v in dicvar.items():
	print(k,v)
"""
a 1
b 2
c 3
"""
```



## 集合相关操作

```python
# 集合的相关操作(交叉并补)

# intersection() 交集
set1 = {'a','b','c'}
set2 = {'c','d','e'}
var = set1.intersection(set2)
print(var)				# ==> {'c'}

# 简写
var = set1 & set2
print(var)				# ==> {'c'}


# difference() 差集
set1 = {'a','b','c'}
set2 = {'c','d','e'}
var = set1.difference(set2)
print(var)				# ==> {'a', 'b'}

# 简写
var = set1 - set2
print(var)				# ==> {'a', 'b'}


# union() 并集
set1 = {'a','b','c'}
set2 = {'c','d','e'}
var = set1.union(set2)
print(var)				# ==> {'a', 'd', 'e', 'c', 'b'}

# 简写
var = set1 | set2
print(var)				# ==> {'a', 'd', 'e', 'c', 'b'}


# symmetric_difference() 对称差集
set1 = {'a','b','c'}
set2 = {'c','d','e'}
var = set1.symmetric_difference(set2)
print(var)				# ==> {'d', 'b', 'a', 'e'}

# 简写
var = set1 ^ set2
print(var)				# ==> {'d', 'b', 'a', 'e'}


# issubset() 判断是否是子集
set1 = {'a','b','c','d','e','f'}
set2 = {'c','d','e'}

var = set1.issubset(set2)
print(var)				# ==> False

# 简写 <
var = set1 < set2
print(var)				# ==> False


# issuperset() 判断是否是父集
set1 = {'a','b','c','d','e','f'}
set2 = {'c','d','e'}

var = set1.issuperset(set2)
print(var)				# ==> True

# 简写 <
var = set1 > set2
print(var)				# ==> True


# isdisjoint() 判断两个集合之间是否 不相交(相交 为 False,不相交 为 True)
set1 = {'a','b','c','d','e','f'}
set2 = {'c','d','e'}

var = set1.isdisjoint(set2)
print(var)				# ==> False

set1 = {'a','b','f'}
set2 = {'c','d','e'}

var = set1.isdisjoint(set2)
print(var)				# ==> True
```



## 集合相关函数

```python
# 增加元素
# add() 在集合中添加一个元素
setvar = {'a','b'}
setvar.add('z')
print(setvar)			# ==> {'z', 'b', 'a'}

# update() 迭代增加，一次可增加多个元素，要求数据类型为 Iterable 可迭代性数据(即容器类型数据,range对象,迭代器)
setvar.update([1,2])
print(setvar)			# ==> {1, 2, 'z', 'a', 'b'}

# 删除元素
# pop() 随机删除集合中的一个元素
var = setvar.pop()
print(var,setvar)		# ==> 1 {2, 'a', 'b', 'z'}

# remove() 删除集合中指定的值对应的元素，若该元素不存在则会报错
#setvar.remove('bb')
#print(setvar)			# ==> Error

# clear() 清空集合
setvar.clear()
print(setvar)			# ==> set()
```



## 冰冻集合 frozenset 

```python
# frozenset 可强转容器类型数据变成冰冻集合
# 冰冻集合一旦创建,不能在进行任何修改,只能做交叉并补操作
set1 = frozenset(['a','b'])
print(set1,type(set1))	# ==> frozenset({'b', 'a'}) <class 'frozenset'>

set2 = frozenset(['a','c'])
print(set2,type(set2))	# ==> frozenset({'a', 'c'}) <class 'frozenset'>

# 交集
var = set1 & set2
print(var)			# ==> frozenset({'a'})

# 并集
var = set1 | set2
print(var)			# ==> frozenset({'c', 'a', 'b'})

# 添加元素
# var = set1.add(1)			# Error
```



## 文件相关操作

```python
# 文件操作相关
# fp = open(文件名,模式，字符编码)

# 文件的写入操作
# 1.打开文件(如果文件不存在，则会创建)
fp = open('test1.txt',mode='w',encoding='utf-8')
print(fp)
# 2.写入内容
fp.write('no bb')
# 3.关闭文件
fp.close()

# 文件的读取操作
# 1.打开文件
fp = open('test1.txt',mode='r',encoding='utf-8')
print(fp)
# 2.读取文件
res = fp.read()
print(res)			# ==> no bb
# 3.关闭文件
fp.close()


# 写入和读取字节流数据(无需指定 encoding)
# encode() 	编码 将字符串转化为字节流(Bytes 流) 
# decode()	解码 将字节流转化为字符串

strvar = 'hello'.encode('utf-8')	# 编码 将字符串转化为字节流(Bytes 流) 
print(strvar,type(strvar))	# ==> b'hello' <class 'bytes'>

res = strvar.decode('utf-8')		# 解码 将字节流转化为字符串
print(res,type(res))		# ==> hello <class 'str'>


strvar = '你好'.encode('utf-8')	# 编码 将字符串转化为字节流(Bytes 流) 
print(strvar,type(strvar))	# ==> b'\xe4\xbd\xa0\xe5\xa5\xbd' <class 'bytes'>

res = strvar.decode('utf-8')	# 解码 将字节流转化为字符串
print(res,type(res))		# ==> 你好 <class 'str'>

# 写入二进制字节流
strvar = '你好'.encode('utf-8')
print(strvar)			# ==> b'\xe4\xbd\xa0\xe5\xa5\xbd'
aa = open('test2.txt',mode='wb')
aa.write(strvar)
aa.close()

# 读取二进制字节流
aa = open('test2.txt',mode='rb')
strvar = aa.read()
aa.close()
res = strvar.decode('utf-8')
print(res)			# ==> 你好

# 复制文件
# 音频 视频 图片 等文件里面的内容都为 二进制字节流
# 读取图片当中所有内容(二进制字节流)
fp = open('cat.jpg',mode='rb')
bytes_date = fp.read()
fp.close()
print(bytes_date)
print(type(bytes_date))			# ==> <class 'bytes'>

# 将字节流写入到新的文件中
fp = open('cat_bak.png',mode='wb')
fp.write(bytes_date)
fp.close()


# 文件操作
'''
在 utf-8 编码格式下，默认一个中文字符占用 3 个字节，一个英文或符号占用 1 个字节
read()	功能：读取字符的个数 	()里面的参数代表字符个数
seek()	功能：调整指针的位置	()里面的参数代表字节个数
		seek(0)	将光标移动到文件开头 	
		seek(0,2) 将光标移动到文件末尾
tell() 	功能：当前光标前面所有的字节数	 (）里面的参数代表返回字节数
'''

# read 
fp = open('test.txt',mode='w+',encoding='utf-8')
fp.write('iloveu')
fp.seek(0)
res = fp.read(3)		# ==> 仅读取所在光标以后的 3 个字符
print(res)				# ==> ilo

# seek
fp.seek(5)
res = fp.read()
print(res)				# ==> u

# tell 
res = fp.tell()
print(res)				# ==> 6
fp.seek(2)
res = fp.tell()
print(res)				# ==> 2
fp.close()

# r+ 先读后写
# 需先创建 test3.txt 文件
fp = open('test3.txt',mode='r+',encoding='utf-8')
# 读取
res = fp.read()
print(res)
# 写入
res = fp.write('Beautiful')
print(res)					# ==> 9 这里记录的只是字符数量
fp.close()

# r+ 先写后读
# read 默认从当前光标处向右进行读取
fp = open('test3.txt',mode='r+',encoding='utf-8')
# 写入
fp.write('Nice')			# ==> 此时的文件内容 ==> Nicetiful
fp.seek(0,2)				# ==> 将光标移动到文件末尾
fp.write('Good')			# ==> 此时的文件内容 ==> NicetifulGood
# 读取
fp.seek(0)					# ==> 将光标移动到文件开头 
res = fp.read()
print(res)					# ==> NicetifulGood
fp.close()

# w+ 可读可写 
# w 写入会清空原有内容
fp = open('test4.txt',mode='w+',encoding='utf-8')
# 写入
fp.write('porcelain')
# 读取
fp.seek(0)
res = fp.read()
print(res)					# ==> porcelain
fp.close()			

# a+ 追加写入,可读写
# 写入内容时,会强制先把光标移动到最后
fp = open('test5.txt',mode='a+',encoding='utf-8')
# 写入
fp.write('great')
# 读取
fp.seek(0)
res = fp.read()
print(res)					# ==> great
fp.seek(3)
fp.write('hi')				# ==> 将光标移动当 第 3 个字符之后，尝试写入
fp.seek(0)
res = fp.read()
print(res)					# ==> greathi
fp.close()			


# 关于中文字符混合的情况
'''
由于在 utf-8 编码中一个中文占用 3 个字节
seek 移动的单位是字节个数而不是字符
当移动到中文字符的字节中间的时候就会产生报错 
fp = open('test6.txt',mode='a+',encoding='utf-8')
fp.write('美好的beautiful')
fp.seek(5)
res = fp.read(3)
print(res)
'''

# ### with 语法 (可以省略掉 close 操作)
'''
语法： with open() as fp:

			code ……

#  读取图片当中所有内容(二进制字节流)
fp = open('cat.jpg',mode='rb')
bytes_date = fp.read()
fp.close()

# 将字节流写入到新的文件中
fp = open('cat_bak.png',mode='wb')
fp.write(bytes_date)
fp.close()
'''
# 如果使用 with 语法可修改为
with open('cat.jpg',mode='rb') as fp1,open('cat_bak1.png',mode='wb') as fp2:
	bytes_date = fp1.read()
	fp2.write(bytes_date)	
```



## 文件相关函数

```python
# flush 刷新缓冲区
'''
1.当文件关闭的时候自动刷新缓冲区
2.当整个程序运行结束的时候自动刷新缓冲区
3.当缓冲区写满了 会自动刷新缓冲区
4.手动刷新缓冲区
'''
fp = open("test6.txt",mode="a+",encoding="utf-8")
fp.write("flush")
# 使用 flush 立刻把缓冲区中的内容刷入到文件里
fp.flush()
#while True:
#	pass


# 判断文件是否具有可读可写的属性
# readable()	    功能: 判断文件对象是否可读
# writable()	    功能: 判断文件对象是否可写
fp = open("test6.txt",mode="r",encoding="utf-8")
res = fp.readable()
print(res)					# ==> True
res = fp.writable()
print(res)					# ==> Flase

# readline() 从文件读取整行，包括 "\n" 字符
# 如果指定了一个非负数的参数，则返回指定字符个数的内容，包括 "\n" 字符
with open('test7.txt',mode='w+',encoding='utf-8') as fp:
	fp.write('hello\nalan')
	fp.seek(0)
	res = fp.readline(3)
	print(res)				# ==> hel
	fp.seek(0)
	res = fp.readline()
	print(res)				# ==> hello

# 打印文件内的所有内容
with open('test7.txt',mode='r',encoding='utf-8') as fp:
	res = fp.readline()
	while res:
		print(res)
		res = fp.readline()


# readlines() 文件中的内容以行为单位读取到列表当中(一行为一个元素)
with open('test7.txt',mode='r',encoding='utf-8') as fp:
	lst = fp.readlines()
	print(lst)			# ==> ['hello\n', 'alan']

	# 插入元素
	lst.insert(1,'yui')
	print(lst)			# ==> ['hello\n', 'yui', 'alan']

	# 去掉空白字符
	lst1 = []
	for i in lst:
		strvar = i.strip()
		lst1.append(strvar)
	print(lst1)			# ==> ['hello', 'yui', 'alan']


# writelines() 向文件中写入一个序列的字符串
# 参数要求： 1.容器类型等可迭代性数据 2.内容为字符串
lst =  ['窗前明月光\n', '疑是地上霜\n']
with open('test7.txt',mode='w+',encoding='utf-8') as fp:
	fp.writelines(lst)
	strvar = ['举头望明月\n', '低头思故乡\n']
	fp.writelines(strvar)
	fp.seek(0)
	var = fp.read()
	print(var)

# truncate() 将要截取的字符串提取出来,然后清空内容将提取的字符串重新写入文件中 (字节)
with open('test7.txt',mode='r+',encoding='utf-8') as fp:
	fp.truncate(9)			# ==> 文件内容为 ==> 窗前明


# read readline 字符个数
# seek truncate 字节个数 
```



## 函数

```python
# 函数
'''
定义函数
	def 函数名():
		code1 ...
		code2 ...

调用函数
	函数名()
'''

# 定义一个函数
def multiplication_table():
	for i in range(1,10):
		for j in range(1,i+1):
			print('%d × %d = %-2d  ' % (i,j,i*j),end='')
		print()

# 调用一个函数
multiplication_table()


# 函数的参数
"""
参数:
1.形参:形式参数,在函数的定义处
2.实参:实际参数,在函数的调用处

形参的种类:
	1.普通(位置)形参 2.默认形参 3.普通收集形参 4.关键字收集形参 5.命名关键字形参

实参的种类:
	1.普通实参 2.关键字实参

形参和实参必须一一对应 否则会报错
"""

# 普通形参

# 函数的定义处
# row ,column 普通形参
def function(row,column):
	for i in range(1,column+1):
		for j in range(1,row+1):
			print('☆',end='')
		print()

# 函数的调用处
# 6,3 普通实参
function(6,3)

# 默认形参
# 函数的定义处
# row=9 ,column=9 在定义处有默认值,叫做默认形参
# 若没有给与实际参数,则使用默认参数自带的值,若给与了,则使用实际参数值
def function(row=6,column=2):
	for i in range(1,column+1):
		for j in range(1,row+1):
			print('☆',end='')
		print()

# 函数的调用处	
function(3,3)
function()

# 普通形参 + 默认形参
# 语法要求：必须将 普通形参 放在 默认形参 的前面
def function(row,column=3):
	for i in range(1,column+1):
		for j in range(1,row+1):
			print('☆',end='')
		print()

# 函数的调用处	
function(1)
function(2,2)

# 关键字实参
# 1.若使用关键字实参进行赋值调用,关键字实参的顺序可以任意颠倒
def function(row,column=5):
	for i in range(1,column+1):
		for j in range(1,row+1):
			print('☆',end='')
		print()

# 函数的调用处	
function(column=2,row=5)

# 2.使用 普通形参,调用时使用 关键字实参
# 那么该参数身后所有的形参都必须使用关键字实参,进行调用
# 函数的定义处
def function(row,a,b,column=5):
	for i in range(1,column+1):
		for j in range(1,row+1):
			print('☆',end='')
		print()

# 函数的调用处
# 关键字实参必须跟在普通实参的后面	
# function(1,a=1,2,2) # ==> Error
function(1,b=2,a=5,column=7)


# 普通收集形参
'''
语法： def fuction(*args):
作用： 收集多余的普通实参，收集后生成一个元组
'''
# 函数的定义处
def function(a,b,*args):
	print(a,b)			# ==> 1 2
	print(args)			# ==> (3, 4, 5, 6)
	
# 函数的调用处
function(1,2,3,4,5,6)

# 计算所有参数的累加和
def function(*args):
	sum = 0
	for i in args:
		sum += i
	
	print(sum)			# ==> 55
function(1,2,3,4,5,6,7,8,9,10)

# 关键字收集形参
'''
语法： def fuction(**kwargs):
作用： 收集多余的关键字实参，收集后生成一个字典
'''
def function(a,b,**kwargs):
	print(a,b)			# ==> 1 2
	print(kwargs)		# ==> {'c': 3, 'd': 4, 'e': 5}

function(a=1,b=2,c=3,d=4,e=5)

# 将所有参数拼接生成字符串
def function(**kwargs):
	strvar1 = ""
	strvar2 = ""
	dicvar = {"P":"Python","C":"C++"}
	print(kwargs)	# ==> {'P': 'ppp', 'C': 'ccc', 'extra1': 'pig', 'extra2': 'dog'}

	for k,v in kwargs.items():	# 将上面的字典的键值对凑成一个个元组,然后将键赋值给 k，键值赋值给 v
		print(k,v)
		# 如果当前键在dic当中,在进行下一步的处理;
		if k in dicvar:
			strvar1 += dicvar[k] + ":" + kwargs[k] + "\n"
		else:
			strvar2 += v + " "
	
	print(strvar1)
	print("extra:",strvar2)			# ==> extra: pig dog

function(P="Alan",C="Tom",extra1="pig",extra2="dog")


# 命名关键字形参
"""
定义方法:
(1) def function(a,b,*,c,d)  在 * 后面定义的参数叫做 命名关键字形参
(2) def function(*args,e,**kwargs)  在 普通收集形参 和 关键字收集形参 之间的参数叫做 命名关键字形参
命名关键字形参在调用时,必须使用关键字实参调用
"""

# 定义方式一
def function(a,b,*,c,d):
	print(a,b)		# ==> 1 2
	print(c,d)		# ==> 3 5
	
function(1,2,d=5,c=3)

# 定义方式二
def function(*args,e,f,**kwargs):
	print(args) 		# ==> (1, 2, 3, 4, 5, 6)
	print(kwargs) 		# ==> {'a': 1, 'b': 2, 'c': 3}
	print(e) 			# ==> 11
	print(f) 			# ==> 10
function(1,2,3,4,5,6,a=1,b=2,c=3,f=10,e=11) # 关键字实参必须跟在普通实参的后面	

# 定义方式三
def function(a,b,*,c=10):
# def function(a,b,c=10):
	print(a,b)
	print(c)
function(1,2)
function(1,2,c=11) # 在 * 后面定义的参数叫做 命名关键字形参 
# function(1,2,11)   # Error 命名关键字形参在调用时,必须使用关键字实参调用


# * 和 ** 的用法
"""
* , ** 在函数的定义处,用来打包收集参数
* , ** 在函数的调用处,用来解包参数值
"""
# 函数的定义处 1
def function(a,b,*,c,d):
	print(a,b)			# ==> 1 2
	print(c,d)			# ==> 5 6

# 函数的调用处 1
lst = (1,2)

# *lst 把列表或者元组中的每一个元素,都单独拿出来,传参到 function 函数中
function(*lst,c=5,d=6) 		# ==> function(1,2,c=5,d=6)


# 函数的定义处 2
def function(a,b,*,c,d):
	print(a,b)			# ==> 1 2
	print(c,d)			# ==> 5 6

# 函数的调用处 2
dic = {"c":5,"d":6}

# **dic 将字典中的每一个元素,都单独拿出来,形成关键字实参的形式传参到 function 函数中
function(1,2,**dic) 		# ==> function(1,2,c=5,d=6)

# 综合
def function(a,b,*,c,d,e):
	print(a,b)			# ==> 1 2
	print(c,d,e)		# ==> 5 6 7
lst = (1,2)
dic = {"c":5,"d":6,'e':7}

# 通过这样的一种方式,间接对函数的参数的长度,进行了控制 ?
function(*lst,**dic) 		# ==> function(1,2,c=5,d=6,e=7)

"""
参数的顺序: 
	普通参数 -> 默认参数 -> 普通收集参数 -> 命名关键字参数 ->关键字收集参数
	
def function(*args,**kwargs): 这种定义参数的形式,可以接收到所有参数值
"""
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)

# f1(1, 2) 					# ==> a=1,b=2,c=0,args=(),kw={}
# f1(1, 2, c=3) 			# ==> a=1,b=2,c=3,args=() kw={}
# f1(1, 2, 3, 'a', 'b') 	# ==> a=1,b=2,c=3,args=("a","b") kw={}
f1(1, 2, 3, 'a', 'b', x=99) # ==> a=1,b=2,c=3,args={"a","b"} kw={x:99}


def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)

f2(1, 2, d=99, ext=None) # ==> a=1,b=2,c=0,d=99 kw={ext:none}


def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)

args = (1, 2, 3, 4)
kw = {'d': 99, 'x': '#'}
f1(*args, **kw) # ==> a=1,b=2,c=3,args=(4,),kw={"x":#,"d":99}


def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)

myargs = (1, 2, 3)
mykw = {'d': 88, 'x': '#'}
f2(*myargs, **mykw) # ==> a=1,b=2,c=3,d=88,kw={"x":#}


def f1(a, b, c=0, *args,d,**kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)
    print(d)

f1(1,2,3, 'a', 'b',d=67, x=99,y=77) # ==> a=1,b=2,c=3,args=(a,b) d=67 kw={x:99,y:77}
```



### 函数的返回值

```python
# 函数的返回值 return
'''
return 返回值 把后面的返回值返回到函数的调用处
如果一个函数没有自定义任何返回值,默认返回 None
(1) return + 六大标准数据类型 ,除此之外,还可以返回函数,类,对象
(2) 一旦执行了 return,意味着函数立刻终止,后面的代码不再执行
'''
# (1) return + 六大标准数据类型 ,除此之外,还可以返回函数,类,对象
def function():
	# return 100
	# return 9.13
	# return [1,2,3]
	# return "abc"
	return ('a','b','c','d')
res = function()	# ==> ('a', 'b', 'c', 'd')
print(res)

# (2) 一旦执行了 return,意味着函数立刻终止,后面的代码不再执行
def function():
	print(1)			
	return 'a'
	print(2)
res = function()
print(res)			# ==> 1		# ==> a

def function():
	for i in range(3):
		if i == 2:
			return 0
		print(i)
res = function()
print(res)			# ==> 0 # ==> 1 # ==> 0


# 使用 return 做计算器
def calculator(num1,sign,num2):
	if sign == '+':
		res = num1 + num2
	elif sign == '-':
		res = num1 - num2
	elif sign == '*':
		res = num1 * num2
	elif sign == '/':
		if num2 == 0:
			return 'Error'
		res = num1 / num2
	else:
		return 'please re-input'
	return res

res = calculator(3,'/',10)
print(res)


#  __doc__ 查看帮助文档 魔术属性
res = print.__doc__
print(res)

def function():
	'''
	How are you ?
	'''

	print('I Have a Dream !')

res = function.__doc__
print(res)			# ==> How are you ?
```



### 函数名的使用

```python
# 函数名的使用
# 创建 销毁 作为参数传递 作为值返回，称为 第一类对象

# 1.函数名也是一个特殊的变量，可作为变量赋值
def function():
	return 'I am function !'

a = 1
a = function
print(a,type(function))	# ==> <function function at 0x000001E261471EA0> <class 'function'>
res = a()
print(res)			# ==> I am function !

# 2.函数名可以作为容器类型数据的元素
def function1():
	return 'function1 !'

def function2():
	return 'function2 !'

lst = [function1,function2]

for i in lst:
	print (i)
	res = i()
	print(res) 
'''

<function function1 at 0x0000021DF6CD6620>
function1 !
<function function2 at 0x0000021DF6CD6268>
function2 !
'''

# 3.函数名可以作为函数的参数
def function(funname):
	res = funname()
	print(res)

function(function2)

print('----------------------------------')

# 4.函数名作为函数的返回值
def function(funname):
	return funname

res = function(function2)
print(res)			# ==> <function function2 at 0x000001B0B3C46268>
print(res())		# ==> function2 !

# 若一个函数没有自定义任何返回值，默认返回 None
def function():
	print('test')	# ==> test

res = function()
print(res)			# ==> None

print('----------------------------------')

# 自定义返回值
def function(n):
	print(n)
	return 'OK'

res = function('Hi')	# ==> Hi
print(res)			# ==> OK
```



### 全局变量与局部变量

```python
# 全局变量 与 局部变量
"""
局部变量:在函数内部定义的变量,作用域在函数内部
全局变量:在函数外部定义的变量或者在函数内部用 global 关键字定义的变量,作用域横跨整个文件
"""
# 1.局部变量的获取和修改
def function():
	c = 6
	print(c)			# ==> 6
	c = 7
	print(c)			# ==> 7

function()
# print(c)		# Error 在函数内部定义的变量,作用域在函数内部

# 2.全局变量的获取和修改
d = 5					
print(d)			# ==> 5
d = 3					
print(d)			# ==> 3

# 3.函数内部可以获取到全局变量
def function():
	print(d)
function()			# ==> 3


# 4.在函数内部可以通过 global 修改全局变量
"""
如果函数外部有这个变量,那么通过 global可以在函数内部进行修改
如果函数外部没有这个变量,那么通过 global 可以在函数内部定义全局变量
"""
a = 1
def function():
	global a
	a = 100

function()
print(a)			# ==> 100

# 全局变量和局部变量的生命周期 : 生效时间的长短
'''
内置命名空间的生命周期 > 全局变量的生命周期 > 局部变量的生命周期

# 作用域:作用范围
命名空间 : 划分一块区域保存所有数据,以字典的方式存储(变量与值形成映射关系)
(1)内建命名空间:解释器启动时创建,直到解释器运行结束,生存周期最长
(2)全局命名空间:文件运行时创建,直到解释器运行结束,生存周期较长
(3)局部命名空间:函数调用时,里面的局部变量才创建,调用结束后即释放,生存周期较短
命名空间的提出是为了划分和控制变量是否可见,以及生存周期的长短

# 命名空间 创建顺序:
python 解释器启动 -> 创建内建命名空间 -> 创建全局命名空间 -> 创建局部命名空间

# 命名空间 销毁顺序:
函数调用结束后 -> 销毁函数对应的局部命名空间数据 -> 销毁全局命名空间数据 -> 销毁内建命名空间数据
'''
```



### 函数的嵌套

```python
# 函数的嵌套
'''
互相嵌套的两层函数:
	嵌套在外层的是外函数
	嵌套在里层的时内函数
'''
def outer():
	def inner():
		print('I am inner function ！')
# inner()		# ==> Error 内部函数 不可以 直接在函数外部调用
# outer()
# inner()		# ==> Error 调用外部函数后,内部函数 不可以 在函数外部调用

	inner()		# ==> 内部函数可以在函数内部调用
outer()
# 内部函数在函数内部调用时,需要 先定义 再调用,不能颠倒顺序


# 三层函数，调用最里层的函数
def outer():
	def inner():
		def innermost():
			print('I am innermost function ！')
		innermost()
	inner()
outer()			# ==> I am innermost function ！

def outer():
	def inner():
		def innermost():
			print(id)
		innermost()
	inner()
outer()	

'''
LEGB 原则: 就近找变量原则
找寻变量的调用顺序采用 LEGB 原则,即就近原则
B —— Builtin(Python) Python内置模块的命名空间	(内建作用域)
G —— Global(module) 函数外部所在的命名空间			(全局作用域)
E —— Enclosing function locals 外部嵌套函数的作用域	(嵌套作用域)
L —— Local(function) 当前函数内的作用域			(局部作用域)
依据就近原则,从下往上 从内向外 依次寻找
'''

# nonlical 修改局部变量
"""
1.nonlocal符合 LEGB 原则,就近找变量,用来修改当前空间上一层的局部变量
2.如果上一层找不到了,在继续向上寻找,直到找到为止,如果都没有,则会报错
"""
def outer():
	a = 10
	def inner():
		nonlocal a
		a = 20		
	inner()
	print(a)
outer()				# ==> 20

a = 66 		# 全局变量,nonlocal 无法修该变量
def outer():
	a = 100
	def inner():
		def innermost():
			nonlocal a
			a = 200
		innermost()
		print(a)		# ==> 200
	inner()
	print(a)		# ==> 200
outer()
print(a)			# ==> 66

# 通过列表也能修改局部变量
def outer():
	lst = [1,2,3]
	def inner():
		lst[-1] += 10
	inner()
	print(lst)
outer()			# ==> [1, 2, 13]
```



### 闭包函数

```python
# 闭包函数
"""
内函数使用了外函数的 局部变量
且外函数把内函数返回出来的过程叫做 闭包
这个内函数叫做 闭包函数
"""
def outer():
	a = 99
	def inner():
		print(a)
	return inner

res = outer()		# res = inner
res()			# ==> inner() ==> 99

# 闭包的特征
'''
内函数使用了外函数的局部变量
那么该变量会和内函数发生 绑定关系 不释放
延长该变量的生命周期
'''
def outer():
	core = 2

	def inner_1():
		nonlocal core
		core += 2
		print(core)

	def inner_2():
		nonlocal core
		core += 4
		print(core)

	def inner_sum():
		return [inner_1,inner_2]

	return inner_sum

res = outer()()
print(res)			# ==> [inner_1,inner_2]
for x in res:
	x()				# ==> 4 	# ==> 8

print('-----------------------------------')

def outer(num):
	def inner(val):
		return num + val
	return inner

function = outer(5) # ==> num = 5  function = inner
print(function)		# ==> <function outer.<locals>.inner at 0x000002B9ECD267B8>

res = function(10)	# ==> inner(10) val = 10  res = 5 + 10 
print(res)			# ==> 15


# 闭包的意义
# 闭包可以优先使用外函数中的变量,并对闭包中的值起到了封装保护的作用,外部无法访问

# 全局变量用来计数,由于作用域太大,容易被串改
num = 0
def click_num():
	global num
	num += 1
	print(num)
click_num()
num = 100
click_num()
click_num()

print('-----------------------------------')

# 使用闭包函数改进
def function():
	num = 0
	def click_num():
		nonlocal num
		num += 1
		print(num)
	return click_num

res = function()
res()
num = 1000
res()
res()


# locals 函数 会以字典类型返回当前位置的全部局部变量(若在全局空间,获取的是所有全局变量)

# 当前作用域在 全局空间
test_a = 1
test_b = 2
def function():
	test_c = 100
dicvar = locals()
test_d = 3
function()
print(dicvar)
# print(dicvar['test_c'])		# ==> Error
print(dicvar['test_d'])			# ==> 3

# 当前作用域在 局部空间
test_e = 2
def function():
	test_f = 100
	dicvar = locals()
	print(dicvar)				# ==> {'test_f': 100}
	# print(dicvar['test_e'])		# ==> Error
	print(dicvar['test_f'])			# ==> 100
function()

# globals 函数 会以字典类型返回当前位置的全部全局变量
# 当前作用域在 全局空间
test_h = 1
test_i = 2
def function():
	test_j = 101
dicvar = globals()
test_k = 3
function()
print(dicvar)
# print(dicvar['test_j'])			# ==> Error
print(dicvar['test_k'])				# ==> 3

# 当前作用域在 全局空间
test_m = 2
def function():
	test_n = 100
	dicvar = globals()
	print(dicvar)
	print(dicvar['test_m'])			# ==> 2
	# print(dicvar['test_n'])		# ==> Error
function()

# 利用 globals 来定义全局变量
# globals() 以字典的形式存放了所有的全局变量,在这个字典中,添加键值对,就等于定义了一个变量
dicvar = globals()
dicvar['wow'] = 'HoH'
print(wow)
print(globals())
```



### 匿名函数

```python
# 匿名函数
"""
lambda 关键字表达匿名函数,函数没名字
特点: 用一句来表达只有返回值的函数;
优点: 简洁高效;
语法: lambda 参数 : 返回值
"""
# 1.无参数的 lambda 表达式
# 原型:
def function():
	return 123

# 改写:
function = lambda : 123
print(function())				# ==> 123

# 2.有参数的 lambda 表达式
# 原型:
def function(i):
	return type(i)

# 改写:
function = lambda i : type(i)
print(function("abc"))			# ==> <class 'str'>

# 3.带有判断条件的 lambda 表达式
# 原型:
def function(i):
	if i % 2 == 0:
		return "even"
	else:
		return "odd"
res = function(0)
print(res)				# ==> even

# 三目运算符 
"""
语法: 真值 if 表达式 else 假值
如果表达式为真,返回真值
如果表达式为假,返回假值

注意: 三目运算符只能针对于双项分支进行改造
"""
i = 1
res = 'even' if i % 2 == 0 else 'odd'
print(res)			# ==> odd

# 改写：
function = lambda i : 'even' if i % 2 == 0 else 'odd'
print(function(100))		# ==> even


# 传递两个参数,返回值较大的参数
function = lambda a,b : a if a > b else b
print(function(511,377)) 	# ==> 511
```



### 递归函数

```python
# 递归函数
"""
递归函数: 自己调用自己的函数
递: 去
归: 回
一去一回即递归
"""
def function(num):
	print(num,end=' ')
	if num > 0:
		function(num-1)
	print(num,end=' ')
function(3)				# ==> 3 2 1 0 0 1 2 3 

'''
执行过程
num = 3 ==> print(3) ==> 3 > 0 ==> function(3-1) ==> print(3) 暂停 ==> 执行 function(2)
num = 2 ==> print(2) ==> 2 > 0 ==> function(2-1) ==> print(2) 暂停 ==> 执行 function(1)
num = 1 ==> print(1) ==> 1 > 0 ==> function(1-1) ==> print(1) 暂停 ==> 执行 function(0)
num = 0 ==> print(0) ==> 0 > 0 ==> print(0)
继续执行完暂停项
print(1) ==> print(2) ==> print(3)

调用函数就是在开辟空间,这个空间叫做栈帧空间
1.递归函数是不停的开辟空间和不停的释放空间的一个过程,每一个空间都相对独立
2.递归函数存在 回 的过程:
	(1)当最后一层栈帧空间代码全部执行结束的之后,触发回的过程
	(2)遇到 return 的时候,触底反弹,回到上一次调用的位置,往下执行
	(3)递归调用函数的层数过多,就不推荐使用,官方默认提供的层数为 1000 层,实际 996~1000;如果过多,会过度耗费内存资源,导致蓝屏死机,所以务必要给递归一个跳出的条件
'''

# 求任意一个数的阶乘
print('------------------------------------')
num = 5
sum = 1
while num > 1:
	sum *= num
	if num > 1:
		num -= 1
print(sum)

print('------------------------------------')

sum = 1
def function(num):
	global sum
	sum *= num
	if num > 1:
		function(num-1)
function(6)
print(sum)

print('------------------------------------')

def outer():
	sum = 1
	def inner(n):
		nonlocal sum
		sum *= n
		if n > 1:
			inner(n-1)
		return sum
	return inner

res = outer()
print(res(5))

print('------------------------------------')

def function(n):
	if n < 0:
		return "Error"
	elif n <= 1:
		return 1
	
	return n * function(n-1)
	
res = function(5)
print(res)
```



### 迭代器

```python
# ### 迭代器
# 迭代器：能被 next() 函数调用并不断返回下一个值的对象称为迭代器(Iterator 迭代器是对象)
# 特征:迭代器会生成惰性序列,它通过计算把值依次的返回,一边循环一边计算而不是一次性得到所有数据
# 优点:需要数据的时候,一次取一个,可以大大节省内存空间.而不是把所有数据放进内存

# 可迭代对象：再数据当中,含有 __iter__ 方法,就称为可迭代对象

# dir : 可以获取当前数据所有内部成员
strvar = {'a','b'}
res = dir(strvar)
print(res)
# ['__and__', '__class__', '__contains__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__iand__', '__init__', '__init_subclass__', '__ior__', '__isub__', '__iter__', '__ixor__', '__le__', '__len__', '__lt__', '__ne__', '__new__', '__or__', '__rand__', '__reduce__', '__reduce_ex__', '__repr__', '__ror__', '__rsub__', '__rxor__', '__setattr__', '__sizeof__', '__str__', '__sub__', '__subclasshook__', '__xor__', 'add', 'clear', 'copy', 'difference', 'difference_update', 'discard', 'intersection', 'intersection_update', 'isdisjoint', 'issubset', 'issuperset', 'pop', 'remove', 'symmetric_difference', 'symmetric_difference_update', 'union', 'update']


# 迭代器
"""
为什么 for 循环能够遍历数据?
因为 for 循环底层是先把该数据变成迭代器,转化成之后通过 next 这个方法一个一个将值取出

当数据当中,含有 __iter__ 和 __next__ 这两个方法,就可以称为迭代器

(1) 如何判断迭代器 
	1. dir 获取当前数据所有内部成员 查看是否含有 __iter__ 和 __next__
	2. from collections import Iterator,Iterable

(2) 如何变成迭代器
	1. iter  2. __iter__()

(3) 如何遍历迭代器
	1. next  2. for  3. for + next

如果当前数据是迭代器时,它一定是一个可迭代对象
如果当前数据是可迭代对象时,它并不一定是迭代器
可迭代对象和迭代器是包含和被包含之间的关系
可迭代对象: 容器类型数据 , range对象, 迭代器 , fp文件对象等

可迭代对象 和 迭代器之间 
实际上是把数据 从不能够被直接调用 变为 可直接被调用
"""
# isinstance 判断数据类型
# 判断的类型 : int float bool complex str list tuple set dic
# 用法一
# res = isinstance (要判断的数据,要判断的类型)
res = isinstance(3,float)
print(res)			# ==> False

# 用法二
# res = isinstance (要判断的数据, (写上可能的数据类型,满足一个条件,就返回 True) )
res = isinstance("abc", (int,float,list,str)  )
print(res)			# ==> True


# 判断迭代器
setvar = {"a","b"}

lst = dir(setvar)
res = "__iter__" in lst and "__next__" in lst
print(res)			# ==> Flase

from collections import Iterator,Iterable
res = isinstance(setvar,Iterator)	# 判断是否为 迭代器
print(res)			# ==> Flase

res = isinstance(setvar,Iterable)	# 判断是否为 可迭代对象
print(res)			# ==> True

# 变成迭代器
setvar = {"a","b"}
it = iter(setvar)
print(it)		# ==> <set_iterator object at 0x000001B12718D9D8>

lst = dir(it)
res = "__iter__" in lst and "__next__" in lst
print(res)			# ==> True

from collections import Iterator,Iterable
res = isinstance(it,Iterator)	# 判断是否为 迭代器
print(res)			# ==> True

res = isinstance(it,Iterable)	# 判断是否为 可迭代对象
print(res)			# ==> True

# 遍历迭代器
it = iter(range(1,6))			# 生成一个迭代器
print(isinstance(it,Iterator))		# ==> True

# 1. next 在调用迭代器时,是单向不可逆,如果没有数据可调用会提示 Error StopIteration
res = next(it)
print(res)			# ==> 1
res = next(it)
print(res)			# ==> 2
res = next(it)
print(res)			# ==> 3
res = next(it)
print(res)			# ==> 4
res = next(it)
# res = next(it) error
print(res)			# ==> 5

print("-----------------------------------------")

# 2. for
it = iter(range(1,6))	# 重置迭代器
for i in it:
	print(i)		# ==> 1 # ==> 2 # ==> 3 # ==> 4 # ==> 5

# 3. for + next 应用在控制遍历次数上
it = iter(range(1,6))	# 重置迭代器
for i in range(3): 		# 控制遍历的次数
	res = next(it) 		# 调用迭代器
	print(res)		# ==> 1 # ==> 2 # ==> 3
```



### 高阶函数

```python
# 高阶函数 : 能够把函数当成参数传递的就是高阶函数 (map,reduce,sorted,filter)

# map(function,Iterable)
# 参数：
# 		function: 自定义函数 或 内置函数 
# 		Iterable: 可迭代对象(容器类型数据, range 对象, 迭代器)
# 功能：把 Iterable 中的值,一个一个拿出来,放到 function 函数当中进行处理,再把处理的结果放入迭代器当中,最后返回迭代器
# 返回值：迭代器

# 1. ["1","2","3","4"] ==> [1,2,3,4]
lst = ["1","2","3","4"]
lst_new = []
for i in lst:
	res = int(i)
	lst_new.append(res)
print(lst_new)			# ==> [1, 2, 3, 4]

# 2. 使用 map ["1","2","3","4"] ==> [1,2,3,4]
lst = ["1","2","3","4"]
it = map(int,lst)		# [1, 2, 3, 4]
print(it)			# ==> <map object at 0x0000023BB4D847F0>
print(list(it))			# ==> [1, 2, 3, 4]


# 判断是否为 迭代器
lst = ["1","2","3","4"]
it = map(int,lst)
from collections import Iterator
res = isinstance(it,Iterator)				
print(res)			# ==> True

# next 遍历迭代器
lst = ["1","2","3","4"]
it = map(int,lst)

res = next(it)
print(res)		# ==> 1
res = next(it)
print(res)		# ==> 2
res = next(it)
print(res)		# ==> 3
res = next(it)
print(res)		# ==> 4
# res = next(it) error
# print(res)

# for 遍历迭代器
lst = ["1","2","3","4"]
it = map(int,lst)

for i in it:
	print(i)

# for + next 遍历迭代器
lst = ["1","2","3","4"]
it = map(int,lst)

for i in range(4):
	res = next(it)
	print(res)

# list 强转 一次性拿出所有数据 
lst = ["1","2","3","4"]
it = map(int,lst)

lst = list(it)
print(lst)


# 将 [ 1,2,3,4] ==> [3,6,9,12]
lst = [ 1,2,3,4 ]
lst_new = []
for i in lst:	
	res = i * 3
	lst_new.append(res)
print(lst_new)		# ==> # ==> [3, 6, 9, 12]

# 使用 map [ 1,2,3,4] ==> [3,6,9,12]
# 参数 n 和 return 返回值是必要的
lst = [ 1,2,3,4 ]
def function(n):
	return n*3
it = map(function,lst)
lst = list(it)
print(lst)			# ==> [3, 6, 9, 12]

# 将 {97:"a",98:"b",99:"c"} ==> ["a":97,"b":98,"c":99] ,通过 ["a","b","c"] 列表获取 [97,98,99]
dic = {97:"a",98:"b",99:"c"}
# 字典键值对反转
dic_new = {}
for k,v in dic.items():
	print(k,v)
	dic_new[v] = k
print(dic_new)

lst = ["a","b","c"]
lst_new = []
for i in lst:
	res = dic_new[i]
	lst_new.append(res)
print(lst_new)			# ==> [97, 98, 99]

# 使用 map {97:"a",98:"b",99:"c"} ==> ["a":97,"b":98,"c":99] ,通过 ["a","b","c"] 列表获取 [97,98,99]
dic = {97:"a",98:"b",99:"c"}
lst = ["a","b","c"]

def function(n):			# n 分别接收参数 a , b , c
	dic = {97:"a",98:"b",99:"c"}
	dic_new = {}
	
	for k,v in dic.items():		# 让字典的键值对反转
		dic_new[v] = k

	return dic_new[n]

it = map(function,lst)
print(list(it))			# ==> [97, 98, 99]


# reduce(function,iterable)
# 功能:	一次性先从 iterable 中拿出两个数据,放到 function 中进行计算处理,
#		再把计算的结果在和 iterable 中的第 3 个元素放入 function 当中进行处理计算
#		依次类推,直到 iterable 中的数据全部计算完毕,返回最终的计算结果
# 参数:
#		function : 自定义函数
#		iterable : 可迭代对象(容器类型数据, range 对象, 迭代器等)
# 返回值: 计算结果

# [5,4,8,8] ==> 5488
# 方法一
lst = [5,4,8,8]
res = ""
for i in lst:
	res += str(i)
res = int(res)
print(res,type(res))		# ==> 5488 <class 'int'>

# 方法二
lst = [5,4,8,8]
it = iter(lst)
num1 = next(it)
num2 = next(it)
res = num1 * 10 + num2		# ==> 5 * 10 + 4 = 54
for i in it:				
	res = res * 10 + i 		# ==> 54 * 10 + i
print(res,type(res))		# ==> 5488 <class 'int'>

# 方法三
from functools import reduce
lst = [5,4,8,8]
def function(x,y):
	return x*10+y
res = reduce(function,lst)
print(res,type(res))		# ==> 5488 <class 'int'>
"""
执行过程:
	先拿出两个元素 5 和 4
	放到 function 函数中, return 5 * 10 + 4 = 54
	取出 54 和 iterable 中的下一个值 8 
	放入 function 函数中, return 54 * 10 + 8 = 548
	取出 548 和 iterable 中的下一个值 8
	放入 function 函数中, return 548 * 10 + 8
	返回结果 5488
"""

# "789" ==> 789 不能使用 int 强转
def func_map(n):
	dic = {"0":0,"1":1,"2":2,"3":3,"4":4,"5":5,"6":6,"7":7,"8":8,"9":9}
	return dic[n]
it = map(func_map,"789")

def func(x,y):
	return x*10+y
strvar = "789"
res = reduce(func,it)
print(res,type(res))		# ==> 789 <class 'int'>



# sorted(iterable, reverse=False, key=函数)
# 功能: 排序
# 参数:
#		iterable: 可迭代对象(容器类型数据, range 对象, 迭代器等)
#		reverse : 是否倒序
#		key     : 内置函数,自定义函数 (自定义排序的规则)
# 返回值: 排序后的结果(列表)

# 默认从小到大排序
tup = (45,10,71,100,-1,-101)
res = sorted(tup)
print(res)			# ==> [-101, -1, 10, 45, 71, 100]

# 从大到小排序
res = sorted(tup,reverse=True)
print(res)			# ==> [100, 71, 45, 10, -1, -101]

# 按照绝对值排序
res = sorted(tup,key=abs)	# abs() 返回数字的绝对值
print(res)			# ==> [-1, 10, 45, 71, 100, -101]

# 按照数值的个位数大小进行排序
setvar = {19,-25,-42,38,77,63,101,516}
def function(n):
	return abs(n) % 10 
res = sorted(setvar,key=function)
print(res)			# ==> [101, -42, 63, -25, 516, 77, 38, 19]

# 字符串默认按照 ascii 编码进行排序
strvar = "igabc"
res = sorted(strvar)
print(res)			# ==> ['a', 'b', 'c', 'g', 'i']

# 对比 sort 和 sorted
# sort:		1.只能针对列表这个单一类型数据能够排序 2.基于原有列表进行修改
# sorted: 	1.所有容器类型数据都可以	2.返回一个新列表


# filter(function,iterable)
# 功能: 过滤
#	根据自定义函数中的返回值来决定,这个数据是否保留
#	return True  保留
#	return False 舍弃
# 参数: 
#	function: 自定义函数
#	iterable: 可迭代对象(容器类型数据, range 对象, 迭代器等)
# 返回值: 迭代器

# 打印列表中的偶数
lst = [1,2,4,34,4,545,65,6,5]
for i in lst:
	if i % 2 == 0:
		print(i)

# 使用 filter 打印列表中的偶数
lst = [1,2,4,34,4,545,65,6,5]
def function(i):
	if i % 2 == 0:
		return True
it = filter(function,lst)
for i in it:
	print(i)

# 使用 lambda 表达式印列表中的偶数
lst = [1,2,4,34,4,545,65,6,5]
it = filter(lambda i: True if i % 2 == 0 else False, lst)
for i in it:
	print(i)
```



## 推导式

```python
# 推导式
# 通过一行循环判断,遍历出一系列数据的方式是推导式
# 语法: val for val in Iterable (把想要的值写在 for 的左侧)
# 右侧是一行循环判断，根据套在推导式外层的括号来定义推导式的类型

# 推导式的种类:
# [val for val in Iterable]  列表推导式
# {val for val in Iterable}  集合推导式
# {a:b for a,b in iterable}  字典推导式


# 1.单循环推导式
lst = [ i for i in range(1,10) ]
print(lst)			# ==> [1, 2, 3, 4, 5, 6, 7, 8, 9]

# 2.带有判断条件的单循环推导式
# 原型 将一个列表中的奇数提取出来，重新生成一个列表
lst = [1,2,3,4,5,6,7]
lst_new = []
for i in lst:
	if i % 2 == 1:
		lst_new.append(i)
print(lst_new)			# ==> [1, 3, 5, 7]


# 单循环推导式 将一个列表中的奇数提取出来，重新生成一个列表
lst = [1,2,3,4,5,6,7]
lst_new = [i for i in lst if i % 2 == 1]
print(lst_new)			# ==> [1, 3, 5, 7]


# 3.双循环推导式
# 原型 通过两组列表生成 ['A+a', 'A+b', 'A+c', 'B+a', 'B+b', 'B+c', 'C+a', 'C+b', 'C+c']
lst1 = ["A","B","C"]
lst2 = ["a","b","c"]
strvar = ""
lst = []
for i in lst1:
	for j in lst2:
		strvar = i + "+"  + j
		lst.append(strvar)
print(lst)

# 双循环推导式 通过两组列表生成 ['A+a', 'A+b', 'A+c', 'B+a', 'B+b', 'B+c', 'C+a', 'C+b', 'C+c']
lst1 = ["A","B","C"]
lst2 = ["a","b","c"]
lst = [i + "+"  + j for i in lst1 for j in lst2]
print(lst)


# 4.带有判断条件的双循环推导式
# 原型 通过两组列表生成 ['A=a', 'B=b', 'C=c']
lst1 = ["A","B","C"]
lst2 = ["a","b","c"]
strvar = ""
lst = []
for i in lst1:
	for j in lst2:
		if lst1.index(i) == lst2.index(j):
			strvar = i + "="  +j
			lst.append(strvar)
print(lst)

# 双循环推导式 通过两组列表生成 ['A=a', 'B=b', 'C=c']
lst1 = ["A","B","C"]
lst2 = ["a","b","c"]
lst = [i + "="  +j for i in lst1 for j in lst2 if lst1.index(i) == lst2.index(j)]
print(lst)


# 集合推导式
"""
案例:
	满足年龄在 18 到 21,存款大于等于 5000 且小于等于 5500 的人
	开卡格式为: VIP卡 姓氏 先生/女士,否则开卡格式为: 普通卡 姓氏 先生/女士	
	把开卡的种类统计出来
"""
listvar = [
	{"name":"王家辉","age":18,"money":10000},
	{"name":"王水机","age":19,"money":5100},
	{"name":"谢鹏","age":20,"money":4800},
	{"name":"王站","age":21,"money":2000},
	{"name":"李小龙","age":180,"money":20}
]

setvar = set()
for i in listvar:
	if 18 <= i["age"] <= 21 and 5000 <= i["money"] <= 5500:
		strvar = "VIP卡 {}先生/女士".format(i["name"][0])
		setvar.add(strvar)
	else:
		strvar = "普通卡 {}先生/女士".format(i["name"][0])
		setvar.add(strvar)

print(setvar)
# {'普通卡 李先生/女士', '普通卡 谢先生/女士', '普通卡 王先生/女士', 'VIP卡 王先生/女士'}

# 使用集合推导式完成案列
listvar = [
	{"name":"王家辉","age":18,"money":10000},
	{"name":"王水机","age":19,"money":5100},
	{"name":"谢鹏","age":20,"money":4800},
	{"name":"王站","age":21,"money":2000},
	{"name":"李小龙","age":180,"money":20}
]

setvar = {"VIP卡 {}先生/女士".format(i["name"][0]) if 18 <= i["age"] <= 21 \
and 5000 <= i["money"] <= 5500 else "普通卡 {}先生/女士".format(i["name"][0]) \
for i in listvar }
print(setvar)
# {'普通卡 李先生/女士', '普通卡 谢先生/女士', '普通卡 王先生/女士', 'VIP卡 王先生/女士'}


# 字典推导式
"""
enumerate(iterable,[start=0])
功能: 枚举, 将索引号和 iterable 中的值,一个一个拿出来配对组成元组放入迭代器中
参数:
    iterable: 可迭代性数据 (迭代器, 容器类型数据, 可迭代对象 range 等) 
    start:  可以选择开始的索引号(默认从 0 开始索引)
返回值: 迭代器
"""
lst = ["a","b","c"]
it = enumerate(lst)
res = list(it)
print(res)			# ==> [(0, 'a'), (1, 'b'), (2, 'c')]

it = enumerate(lst,start=10)
res = list(it)
print(res)			# ==> [(10, 'a'), (11, 'b'), (12, 'c')]

# 1.利用 enumerate, 完成字典推导式
lst = ["a","b","c"]
it = enumerate(lst)
dic = {a:b for a,b in it}
print(dic,type(dic))		# ==> {0: 'a', 1: 'b', 2: 'c'} <class 'dict'>

# 2.利用 dict 强制迭代器转换为字典类型
it = enumerate(lst)
dic = dict(it)
print(dic,type(dic))		# ==> {0: 'a', 1: 'b', 2: 'c'} <class 'dict'>


# zip(iterable, ... ...)
# 功能: 将多个 iterable 中的值,一个一个拿出来 配对 组成元组放入迭代器中
# iterable: 可迭代性数据 (迭代器, 容器类型数据, 可迭代对象 range 等) 
# 返回值: 迭代器 

# 如果没有值和相应的数据配对,那么这个值会被舍弃

lst1 = ["叶伟明","张军","陈书杰","陈源威"]
lst2 = ["梁永浩","江源","程锴"]
it = zip(lst1,lst2)
print(list(it))	# ==> [('叶伟明', '梁永浩'), ('张军', '江源'), ('陈书杰', '程锴')]

lst1 = ["叶伟明","张军","陈书杰","陈源威"]
lst2 = ["梁永浩","江源","程锴"]
lst3 = ["赵皎洁","林永辉"]
it = zip(lst1,lst2,lst3)
print(list(it))	# ==> [('叶伟明', '梁永浩', '赵皎洁'), ('张军', '江源', '林永辉')]

from collections import Iterator
res = isinstance(it,Iterator)
print(res)		# ==> True

# 1.利用 zip 完成字典推导式
lst1 = ["a","b","c"]
lst2 = ["AA","BB","CC"]
it = zip(lst1,lst2)

dic = {a:b for a,b in it}
print(dic)		# ==> {'a': 'AA', 'b': 'BB', 'c': 'CC'}

# 2.利用 dict 强制迭代器转换为字典
lst1 = ["a","b","c"]
lst2 = ["AA","BB","CC"]
it = zip(lst1,lst2)

dic = dict(it)
print(dic)		# ==> {'a': 'AA', 'b': 'BB', 'c': 'CC'}
```



## 生成器

```python
# 生成器表达式 generator
"""
# 元组推导式的返回值是一个生成器对象,简称生成器,生成器本质就是迭代器

# 迭代器和生成器区别:
	迭代器本身是系统内置的,无法重写,而生成器是用户自定义的,可以重写迭代逻辑

# 生成器可以用两种方式创建:
    1.生成器表达式  (里面是推导式,外面用圆括号)
    2.生成器函数    (用 def 定义,里面含有 yield)
"""

# 通过生成器表达式,创建生成器
gen = (i for i in range(10))
print(gen)		# ==> <generator object <genexpr> at 0x000002273CECC7D8>
from collections import Iterator
res = isinstance(gen,Iterator)
print(res)		# ==> True

# 遍历生成器
# 1.for 循环
gen = (i for i in range(10))
for i in gen:
	print(i)

# 2.next 方法
gen = (i for i in range(10))
res = next(gen)
print(res)			# ==> 0
res = next(gen)
print(res)			# ==> 1

# 3.for + next
gen = (i for i in range(10))
for i in range(3):
	res = next(gen)
	print(res)		# ==> 0  # ==> 1  # ==> 2

    
# 生成器函数
"""
# yield 类似于 return
共同点在于: 执行到这个语句时都会返回值
不同点在于: yield 每次返回时,会记住上次离开时执行的位置, 下次在调用生成器, 会从上次执行的位置往下走
		   而 return 直接终止函数,每次都会重新调用
语法： yield 6 或 yield(6)
"""

from collections import Iterator

# 1.定义一个生成器函数
from collections import Iterator
def function():
	print("one")
	yield 1
	
	print("two")
	yield 2
	
	print("three")
	yield 3

gen = function()
print(isinstance(gen,Iterator))		# ==> True

# 第一次调用
res = next(gen)
print(res)			# ==> one  # ==> 1
# 第二次调用
res = next(gen)
print(res)			# ==> two  # ==> 2
# 第三次调用
res = next(gen)
print(res)			# ==> three  # ==> 3
# 第四次调用
# res = next(gen)		
# print(res) 		# ==> Error StopIteration 

# 2.优化生成器函数
def function():
	for i in range(1,101):
		yield "No.{}".format(i)

gen = function()
for i in range(30):
	res = next(gen)
	print(res)


# 3.send 给上一个 yield 所在的位置传值
"""
# next 和 send 区别:
	next 只能取值
	send 能取值和发送值
# send 注意点:
	第一个 send 不能给 yield 传值 默认只能写 None
	最后一个 yield 无法给 send 返回值
"""
def function():
	print("start")
	
	res = yield 1
	print(res,"inner")
	
	res = yield 2
	print(res,"inner")
	
	res = yield 3
	print(res,"inner")
	print("end")

gen = function()
# 通过 send 调用数据时,第一次还没有遇到 yield ,只能发送 None 作为初始化
# 第一次发送
res = gen.send(None)
print(res)			# ==> start  # ==> 1

# 第二次发送
res = gen.send('send 2')
print(res)			# ==> send 2 inne  # ==> 2

# 第三次发送
res = gen.send('send 3')
print(res)			# ==> send 3 inne  # ==> 3

# 第四次发送
# res = gen.send('send 4')
# print(res)		# ==> Error StopIteration  # ==> send 4 inner  # ==> end

# 4.yield from: 将一个可迭代对象变成一个迭代器返回	
def function():
	yield [1,2,3]
    
gen = function()
print(gen)		# ==> <generator object function at 0x0000025A433AC0A0>
for i in gen:	
	print(i)    # ==> [1, 2, 3]
    
	
def function():
	yield from [1,2,3]

gen = function()
print(gen)		# ==> <generator object function at 0x000001F51119C0A0>
for i in gen:	
	print(i)	# ==> 1  # ==> 2  # ==> 3


# 5.斐波那契数列
# 1 1 2 3 5 8 13 21 34
# 第 n 个元素 是多少

def function(n):
	a,b = 0,1
	i = 0
	while i < n:
		yield b
		a,b = b,a + b
		i += 1

n= 7
gen = function(n)

for i in range(n):
	res = next(gen)
	if i == n - 1:
		print(res)
```



## 内置函数

```python
# 内置函数

# abs 绝对值函数
print(abs(-3))			# ==> 3

# round 四舍五入((n.5 当 n 为偶数则舍去 n.5 n为奇数则进一，偶舍奇进)
print(round(3.3))		# ==> 3
print(round(3.5))		# ==> 4
print(round(-3.2))		# ==> -3
print(round(-3.5))		# ==> -4
print(round(-4.1))		# ==> -4
print(round(-4.5))		# ==> -4
print(round(-4.51))		# ==> -5

# sum 计算一个序列的累加和
lst = [1,3,5,7,9,100]
print(sum(lst))			# ==> 125

# max 获取一个序列里的最大值
lst = [1,3,5,7,9,100]
print(max(lst))			# ==> 100

# min 获取一个序列里的最小值
lst = [1,3,5,7,9,100]
print(min(lst))			# ==> 1

lst = [("yui",25),("alan",15),("tom",19)]
def function(n):
	return n[1]
# res = max(lst,key=function)
res = min(lst,key=function)
print(res)			# ==> ('alan', 15)

# pow 计算某个数值的 n 次方
print(pow(2,3))			# ==> 8

print(pow(2,3,2))		# ==> 0  先计算次幂,再和第三个参数计算取余

# range 产生指定范围数据的可迭代对象
for i in range(3):
	print(i)			# ==> 0  # ==> 1  # ==> 2

for i in range(1,3):
	print(i)			# ==> 1  # ==> 2

for i in range(1,10,5):	
	print(i)			# ==> 1  # ==> 6


# bin 将十进制数据转化为二进制
print(bin(255))			# ==> 0b11111111

# oct 将十进制数据转化为八进制
print(oct(25))			# ==> 0o31

# hex 将十进制数据转化为十六进制
print(hex(25))			# ==> 0x19

# chr 将 ASCII 编码转换为字符
print(chr(65))			# ==> A

# ord 将字符转换为 ASCII 编码
print(ord('A'))			# ==> 65


# eval 将字符串当作 python 代码执行
strvar = 'print(1)'
eval(strvar)			# ==> 1

# strvar = 'a = 3'
# eval(strvar)			# ==> Error

# exec 将字符串当作 python 代码执行(相对于 eval 功能更完善)
strvar = 'a = 6'
exec(strvar)
print(a)			# ==> 6

strvar = 'print(7)'
exec(strvar)		# ==> 7

strvar = """
for i in range(5):
	print(i,end='')
"""
exec(strvar)		# ==> 01234

print('-----------------------------------')

# repr 不转义字符输出字符串
res = repr(str([1,2,3]))
print(res)			# ==> '[1, 2, 3]'

res = repr('a\n\rb\nc')
print(res)			# ==> 'a\n\rb\nc'


# input 接收输入字符串
# res = input('Please input anything: ')
# print(res)


# hash 生成哈希值
strvar1 = 'abc'
strvar2 = 'abc'
print(id(strvar1),id(strvar2))	# ==> 1213843602656 1213843602656
res1 = hash(strvar1)
res2 = hash(strvar2)
print(res1,res2)			# ==> 910053544713526705 910053544713526705

strvar1 = 'abc'
strvar2 = 'abcd'
print(id(strvar1),id(strvar2))	# ==> 2142814502112 2142815471576
res1 = hash(strvar1)
res2 = hash(strvar2)
print(res1,res2)			# ==> -3245526725922590342 -8877769855893350421

# hash 校验两个文件的内容是否相同
# 文件 test1.txt 内容为 hello
# 文件 test2.txt 内容为 hello

with open('test1.txt',mode='r',encoding='utf-8') as fp1, open('test2.txt',mode='r',encoding='utf-8') as fp2:
	res1 = hash(fp1.read())
	res2 = hash(fp2.read())
print(res1,res2)		# ==> -8769311301827870167 -8769311301827870167
```



## math 数学模块

```python
# math 数学模块
import math

# ceil() 向上取整操作(类似内置函数 round)
print(math.ceil(3.1))		# ==> 4
print(math.ceil(-3.1))		# ==> -3

# floor() 向下取整操作(类似内置函数 round)
print(math.floor(3.1))		# ==> 3
print(math.floor(-3.1))		# ==> -4

# pow() 计算一个数值的 n 次方,结果为浮点型数值(似内置函数 pow)
print(math.pow(3,3))		# ==> 27.0
print(math.pow(3.3,3))		# ==> 35.937

# sqrt() 计算一个数值的平方根,结果为浮点型数值
print(math.sqrt(3))			# ==> 1.7320508075688772
print(math.sqrt(9))			# ==> 3.0

# fabs() 计算一个数值的绝对值,结果为浮点型数值(似内置函数 abs)
print(math.fabs(-9.9))		# ==> 9.9

# modf() 将一个数值拆分为小数和整数两个浮点型的数值并组成元组
print(math.modf(5.11))		# ==> (0.11000000000000032, 5.0)

# copysign() 将参数的第二个数值的正负号传递给第一个数值，结果为浮点型
print(math.copysign(8,-9))	# ==> -8.0
print(math.copysign(-8,9))	# ==> 8.0

# fsum() 将一个容器数据中的数据进行累加求和运算,结果为浮点型(似内置函数 sum)
lst = [1,5,8,10,73]
print(math.fsum(lst))		# ==> 97.0

# 圆周率常数 pi
print(math.pi)			# ==> 3.141592653589793
```



## time 时间模块

```python
# time 时间模块
import time

# time() 获取本地时间戳
print(time.time())			# ==> 1579340122.5295157

# mktime() 通过 [时间元组] 获取 [时间戳] 参数为 [时间元组]
ttp = (2020,1,18,17,37,17,5,0,0)
print(time.mktime(ttp))		# ==> 1579340237.0

# localtime() 通过 [时间戳] 获取 [时间元组] 默认为当前时间
print(time.localtime())			# ==> time.struct_time(tm_year=2020, tm_mon=1, tm_mday=18, tm_hour=17, tm_min=42, tm_sec=19, tm_wday=5, tm_yday=18, tm_isdst=0)
print(time.localtime(1579340237.0)) # ==> time.struct_time(tm_year=2020, tm_mon=1, tm_mday=18, tm_hour=17, tm_min=37, tm_sec=17, tm_wday=5, tm_yday=18, tm_isdst=0)

# ctime() 通过 [时间戳] 获取 [时间字符串] 默认为当前时间
print(time.ctime())			# ==> Sat Jan 18 17:44:43 2020
print(time.ctime(1579340122.5295157))	# ==> Sat Jan 18 17:35:22 2020

# asctime() 通过 [时间元组] 获取 [时间字符串] 参数是 [时间元组]
# 必须手动设置周几,无法自动获取
ttp = (2020,1,18,17,40,30,5,0,0)
print(time.asctime(ttp))	# ==> Sat Jan 18 17:40:30 2020

# 自动获取
ttp = (2020,1,18,17,40,30,0,0,0)
res = time.mktime(ttp)
print(res)			# ==> 1579340430.0
strvar = time.ctime(res)
print(strvar)		# ==> Sat Jan 18 17:40:30 2020

# strftime() 通过 [时间元组] 格式化 [时间字符串] (格式化字符串,[可选时间元组参数])
print(time.strftime("%Y-%m-%d %H:%M:%S"))	# ==> 2020-01-18 17:54:11
print(time.strftime("%Y-%m-%d %H:%M:%S", (2020,1,18,18,00,00,0,0,0) ))	# ==> 2020-01-18 18:00:00

# sleep() 程序睡眠等待
# time.sleep(2)
# print("sleep ...")

# perf_counter() 计算程序运行的时间
starttime = time.perf_counter()
for i in range(100000):
	print('.',end='')
endtime = time.perf_counter()
print('\n',endtime-starttime)			# ==>  0.2814196
```



## pickle 序列化模块

```python
# pickle 序列化模块
# 序列化: 把不能够直接存储到文件中的数据变得可存储
# 反序列化: 把存储的数据拿出来,恢复成原来的数据类型

import pickle
lst = [1,2,3]

# dumps 把任意对象序列化成为 bytes 数据
res = pickle.dumps(lst)
print(res,type(res))	# ==> b'\x80\x03]q\x00(K\x01K\x02K\x03e.' <class 'bytes'>

# loads 把任意 bytes 数据反序列化成原来的数据
lst = pickle.loads(res)
print(lst,type(lst))	# ==> [1, 2, 3] <class 'list'>

# dump 将对象序列化后写入到 file-like Object (即文件对象)
with open('test.txt',mode='wb') as fp:
	pickle.dump(lst,fp)

# load 将 file-like Object(即文件对象)中的内容拿出来,反序列化成为原数据
with open('test.txt',mode='rb') as fp:
	res = pickle.load(fp)
print(res,type(res))		# ==> [1, 2, 3] <class 'list'>

# 将函数序列化
import pickle
def function():
	print('abc')

with open('text.txt',mode='wb') as fp:
	pickle.dump(function,fp)

with open('text.txt',mode='rb') as fp:
	res = pickle.load(fp)

print(res,type(res))	# ==> <function function at 0x0000028E59E81EA0> <class 'function'>
res()			# ==> abc
```



## json 模块

```python
# json 模块
"""
json 所有编程语言都能够识别的数据格式
json 可以序列化数据,但是 仅限于 (int float bool)(str list tuple dict None) 类型

json 是一个字符串类型的数据,里面的字符串看起来像一个字典
json 也可以进行 序列化 和 反序列化,转化之后的数据类型是字符串,而 pickle 是字节流
json 应用的方向在不同语言形成数据交流时,使用 json 格式的数据
"""
import json

# dumps 和 loads ,对数据进行 序列化 和 反序列化,通过字符串的形式
dicvar = {"name":"汤姆","sex":"male","age":18,"family":["father","mother"]}

# ensure_ascii=False 支持显示中文
res = json.dumps(dicvar,ensure_ascii=False,sort_keys=True)
print(res,type(res))		# ==> {"age": 18, "family": ["father", "mother"], "name": "汤姆", "sex": "male"} <class 'str'>

dicvar = json.loads(res)
print(dicvar,type(dicvar))	# ==> {'age': 18, 'family': ['father', 'mother'], 'name': '汤姆', 'sex': 'male'} <class 'dict'>

print('------------------------------------')

# dump 和 load ,对数据进行 序列化 和 反序列化
# dump 将字典数据序列化为字符串写入到文件中
dicvar = {"name":"汤姆","sex":"male","age":18,"family":["father","mother"]}
with open('text.txt',mode='w',encoding='utf-8') as fp:
	json.dump(dicvar,fp)

# load 将文件中的字符串数据反序列化成为字典
with open('text.txt',mode='r',encoding='utf-8') as fp:
	res = json.load(fp)
print(res,type(res))		# ==> {'name': '汤姆', 'sex': 'male', 'age': 18, 'family': ['father', 'mother']} <class 'dict'>


# json 和 pickle 之间的区别
# json 可以连续 dump,但是不能连续 load
# load 一次性读取所有数据,看作一个整体

dic1 = {"a":1,"b":2}
dic2 = {"c":3,"d":4}

# 可以连续 dump 存储数据
with open("test.txt",mode="w",encoding="utf-8") as fp:
	json.dump(dic1,fp)
	fp.write("\n")			# ==> 通过 loads 读取连续 dump 存储数据，必须分隔各段数据
	json.dump(dic2,fp)
	fp.write("\n")

# 但无法通过 load 反序列化数据,一次性拿出所有数据,但实际是 2个字典,不能够识别,显示报错
'''
with open("test.txt",mode="r",encoding="utf-8") as fp:
	dic = json.load(fp)
	print(dic)
'''
# 通过 loads 解决上述问题
from collections import Iterator,Iterable

with open("test.txt",mode="r",encoding="utf-8") as fp:
	res1 = isinstance(fp,Iterator)			# ==> True
	res2 = isinstance(fp,Iterable)			# ==> True
	print(res1,res2)
	# 直接遍历 fp 文件 io 对象,会自动一行一行读取内容
	for i in fp:
		dic = json.loads(i)
		print(dic,type(dic))	# ==> {'a': 1, 'b': 2} <class 'dict'>  # ==> {'c': 3, 'd': 4} <class 'dict'>

print('------------------------------------')

# pickle 可以连续 dump,也可以连续 load
# 在存储数据的时,每 dump 一次,都会在该数据的末尾加入结束符,每 load 一次,就去找对应的一个数据
import pickle

dic1 = {"a":1,"b":2}
dic2 = {"c":3,"d":4}

with open("test.txt",mode="wb") as fp:
	pickle.dump(dic1,fp)
	pickle.dump(dic2,fp)

with open("test.txt",mode="rb") as fp:
	dic = pickle.load(fp)
	print(dic,type(dic))
	dic = pickle.load(fp)
	print(dic,type(dic))

# 如果不知道文件中有多少数据时,可使用 while True  
with open("test.txt",mode="rb") as fp:
	try:
		while True:
			dic = pickle.load(fp)
			print(dic.type(dic))
	except:
		pass

"""
异常处理: try .. except .. 能抑制异常错误,但无法抑制语法错误
try:
	有可能错误的代码
except:
	如果代码有错误,会跳过并执行 except 这个代码块
	无错误则不会执行

json 和 pickle 两个模块的区别:
1.json 序列化之后的数据类型是 str,所有编程语言都能识别,但仅限于(int float bool)(str list tuple dict None)
  json不能连续 load,只能一次性拿出所有数据
2.pickle 序列化之后的数据类型是 bytes,所有数据类型都可转化,但仅限于 python 之间的存储传输
  pickle可以连续 load,多套数据放到同一个文件中
"""
```



## random 模块

```python
# random 模块
import random

# random() 获取随机 0 ~ 1 之间的小数(左闭右开 0 <= x < 1) 
print(random.random())		# ==> 0.21023416304119014
print(random.random())		# ==> 0.09746143716957811

# randrange() 随机获取指定范围内的整数(包含开始值,不包含结束值,间隔值)
res = random.randrange(3) 		# ==> 0 ~ 2
print(res)

res = random.randrange(1,4)		# ==> 1 ~ 3
print(res)

res = random.randrange(1,5,3) 	# ==> 1 或 4 
print(res)

# randint() 随机产生指定范围内的随机整数(必须指定两个参数)
res = random.randint(3,5) 		# ==> 3 ~ 5
print(res)

# uniform() 获取指定范围内的随机小数(左闭右开)
res = random.uniform(1,3)	# ==> 1 <= x < 3
print(res)			# ==> 2.192019182135744

res = random.uniform(1,-3)	# ==> -3 <= x < 1
print(res)			# ==> 0.14737240258114603

# choice() 随机获取序列中的值(多选一)
lst = ["C","B","A"]
res = random.choice(lst)
print(res)

# sample() 随机获取序列中的值(多选多) [返回列表]
res = random.sample(lst,2)
print(res)

# shuffle() 随机打乱序列中的值(会打乱原序列)
random.shuffle(lst)
print(lst)


# 验证码案例
import random
def verify():
	strvar = ""
	for i in range(4):
		# 小写字母
		ll = chr(random.randrange(97,123))
		# 大写字母
		cl = chr(random.randrange(65,91))
		# 0 ~ 9 数字
		num = str(random.randrange(10))
		
		# 将随机生成的字母和数字放入列表中随机挑选
		lst = [ll,cl,num]
		strvar += random.choice(lst)		
	return strvar

res = verify()
print(res)			# ==> O7rm
```



## os 模块

```python
# os 执行系统命令
import os

# system() 在 python 中执行系统命令
# os.system("calc")		# 在 win 中打开计算器
# os.system("mspaint")	# 在 win 中打开画图工具
os.system("ipconfig")	# 在 win 中查看 IP 配置(会显示乱码)

# popen() 执行系统命令返回对象,通过 read 方法读出字符串
obj = os.popen("ipconfig")
print(obj)
res = obj.read()
print(res)

# listdir() 获取指定文件夹中所有内容的名称列表
print(os.listdir(r"C:\app"))	# 打印指定文件夹中的名称列表
print(os.listdir())			# 打印当前文件夹中的名称列表

# getcwd() 获取当前文件所在的默认路径
print(os.getcwd())		# ==> C:\Users\Alan\Documents\python_basis
print(__file__)			# ==> C:\Users\Alan\Documents\python_basis/8-1.py

# chdir() 修改当前文件工作的默认路径
os.chdir(r"D:\py")
# os.mkdir("test_chdir")
# os.rmdir("test_chdir")	
print(os.getcwd())

# environ 获取或修改系统环境变量
"""
os.environ 返回的是系统的字典,通过 path 这个键找到所有的路径
当执行一个命令的时候,会优先从 path 中的路径中寻找相应的执行文件,如果都找不到则会报错
所以我们需要预先把命令的路径添加到 path 当中,让其进行查找,从而执行命令,防止报错
"""
print(os.environ["PATH"],type(os.environ["PATH"]))
# os.environ["PATH"] += ";C:\APP\QQ\Bin;"
# os.system("QQ")

# os 模块属性
# name 获取系统标识   linux,mac --> posix		 windows --> nt
print(os.name)			# ==> nt

# sep 获取路径分割符号	linux,mac --> /		window --> \
print(os.sep)			# ==> \

# linesep 获取系统的换行符号	linux,mac --> \n 	window --> \r\n 或 \n
print(repr(os.linesep))		# ==> '\r\n'
```



### os.path 路径模块

```python
# os.path 路径模块
import os

# basename() 返回文件名部分
strvar = r'C:\Users\Alan\Documents\python_basis'
print(os.path.basename(strvar))			# ==> pythone_note

strvar = r'C:\Users\Alan\Documents\python_basis\1-1.py'
print(os.path.basename(strvar))			# ==> 1-1.py

# dirname() 返回路径备份
strvar = r'C:\Users\Alan\Documents\python_basis'
print(os.path.dirname(strvar))			# ==> C:\Users\Alan\Documents

strvar = r'C:\Users\Alan\Documents\python_basis\1-1.py'
print(os.path.dirname(strvar))			# ==> C:\Users\Alan\Documents\python_basis

# split() 将路径拆分成单独的文件部分和路径部分并组合成一个元组
strvar = r'C:\Users\Alan\Documents\python_basis'
print(os.path.split(strvar))	# ==> ('C:\\Users\\Alan\\Documents', 'pythone_note')

strvar = r'C:\Users\Alan\Documents\python_basis\1-1.py'
print(os.path.split(strvar))	# ==> ('C:\\Users\\Alan\\Documents\\pythone_note', '1-1.py')			

# join() 将多个路径和文件组成新的路径(会自动根据不同的系统加不同的斜杠)
path1 = r'c:'
path2 = r'Users'
path3 = r'Alan'

# windows
pathvar = path1 + os.sep + os.path.join(path2,path3)
print(pathvar)			# ==> c:\Users\Alan

# linux
pathvar = os.sep + os.path.join(path2,path3)
print(pathvar)			# ==> \Users\Alan


# splitext() 将路径分割为 后缀 和 其他部分组成一个元组
strvar = r'C:\Users\Alan\Documents\python_basis\1-1.py'
print(os.path.splitext(strvar))  # ==> ('C:\\Users\\Alan\\Documents\\pythone_note\\1-1', '.py')

# 使用 split 将路径分割为 后缀 和 其他部分组成一个列表
lst = strvar.split('.')
print(lst)		# ==> ['C:\\Users\\Alan\\Documents\\pythone_note\\1-1', 'py']

# getsize() 获取文件的大小(无法直接获取文件夹的大小)
strvar = r'C:/Users/Alan/Documents/python_basis/test.py'
print(os.path.getsize(strvar))

# isdir() 检测路径是否是一个文件夹
print(os.path.isdir(strvar))			# ==> False

# isfile() 检测路径是否是一个文件
print(os.path.isfile(strvar))			# ==> True

# islink() 检测路径是否是一个链接
print(os.path.islink(strvar))			# ==> False

# getctime() 获取 windows 文件的创建时间或 linux 权限的改动时间的时间戳
strvar = r'C:\Users\Alan\Documents\python_basis\1-1.py'
print(os.path.getctime(strvar))			# ==> 1578558882.287988

import time
print(time.ctime(1578558882.287988))	# ==> Thu Jan  9 16:34:42 2020

# getmtime() 获取文件最后一次修改时间的时间戳
print(os.path.getmtime(strvar))			# ==> 1578925872.1558475

# getatime() 获取文件最后一次访问时间的时间戳
print(os.path.getatime(strvar))			# ==> 1579415457.6072102

# exists() 检测指定的路径是否存在
strvar = r'C:\Users\Alan\Documents\python_basis\1-1.py'
print(os.path.exists(strvar))			# ==> True

# isabs() 检测一个路径是否是绝对路径
print(os.path.isabs(strvar))			# ==> True

# abspath() 将相对路径转化为绝对路径
strvar = r'1-1.py'
print(os.path.abspath(strvar))	# ==> C:\Users\Alan\Documents\python_basis\1-1.py

# 判断一个路径，若不是绝对路径则转换成绝对路径
strvar = r'1-1.py'

import os.path
if not os.path.isabs(strvar):
	res = os.path.abspath(strvar)	
	print(res)		# ==> C:\Users\Alan\Documents\python_basis\1-1.py

    
# 计算一个文件夹里面所有文件的总大小

import os 
pathvar = r'C:\Users\Alan\Documents\python_basis\md'

def function(pathvar):
	sum = 0
	lst = os.listdir(pathvar)

	for i in lst:
		pathnew = os.path.join(pathvar,i)

		if os.path.isfile(pathnew):
			sum += os.path.getsize(pathnew)

		elif os.path.isdir(pathnew):

			sum += function(pathnew)

	return sum

res = function(pathvar)
print(res)
```



## zipfile 压缩模块

```python
# zipfile 压缩模块
import zipfile

# 压缩文件
# 创建 zip 文件
with zipfile.ZipFile('test.zip','w',zipfile.ZIP_DEFLATED) as zf:
	pass
# 添加需要压缩的文件,并关闭
	zf.write('C:/Users/Alan/Documents/python_basis/1-1.py','1.py')
	zf.write('C:/Users/Alan/Documents/python_basis/3-1.py','2.py')
	# 创建一个文件夹，再添加文件
	zf.write('C:/Users/Alan/Documents/python_basis/5-1.py','test_py/3.py')
print(zf)			# ==> <zipfile.ZipFile [closed]>


# 解压文件
# 打开文件
zf = zipfile.ZipFile("test.zip","r")
# 解压所有文件到指定文件夹中
zf.extractall(r"C:/Users/Alan/Documents/python_basis/xf")
# 解压单个文件到指定文件夹中
zf.extract("1.py",r"C:/Users/Alan/Documents/python_basis/xf-1")
# 关闭文件
zf.close()

# 追加文件
# 打开文件
with zipfile.ZipFile('test.zip','a',zipfile.ZIP_DEFLATED) as zf:
# 追加文件
	zf.write('C:/Users/Alan/Documents/python_basis/6-1.py','6.py')	

# 查看文件
with zipfile.ZipFile('test.zip','r') as zf:
	print(zf.namelist())	# ==> ['1.py', '2.py', 'test_py/3.py', '6.py']

```
