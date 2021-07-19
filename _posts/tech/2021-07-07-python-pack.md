---
layout: post
title: Python中的pack和unpack
category: 技术
tags: pyhton，unpack，pack
---

> 导语：在看代码过程中，遇到unpack(">H",data)不明其用法，故做记录。主要参考如下博客
>
> 参考博客：[https://sanyuesha.com/2018/03/10/why-pack-unpack/](https://sanyuesha.com/2018/03/10/why-pack-unpack/)

#### 1 背景知识

把计算机的内存看做是一个很大的字节数组，一个字节包含8bit信息，可以表示0-255的无符号整型，以及-128-127的有符号整型。当存储一个大于8bit的值到内存时，这个值常常会被切分成多个8bit的segment存储在一个连续的内存空间，一个segment一个字节。有些处理器会把高位存储在这个节点数组的头部，把低位储存在尾部，这种处理方式叫Big-endian,有的处理器则相反，低位存储在头部，高位储存在尾部，称之为Little-endian。

##### 例子 

假设一个寄存器想要存储0x12345678到内存中，big-endian和little-endian分别存储到内存1000的地址表示如下

| addree | big-endian | little-endian |
| ------ | ---------- | ------------- |
| 1000   | 0x12       | 0x78          |
| 1001   | 0x34       | 0x56          |
| 1002   | 0x56       | 0x34          |
| 1003   | 0x78       | 0x12          |

而Python中字节在机器中储存的字节顺序用字母表示如下：

| Character | Byte order             | Size     | Aligment |
| --------- | ---------------------- | -------- | -------- |
| `@`       | native                 | native   | native   |
| `=`       | native                 | standard | none     |
| `<`       | little-endian          | standard | none     |
| `>`       | big-endian             | standard | none     |
| `!`       | network (= big-endian) | standard | none     |

**计算机存储character的方法与存储number类似，通过一定的编码格式进行编码，如unicode，然后以字节的方式存储**

#### 2 python中的struct模块

Python中提供了三个pack与unpack相关的函数

```python
struct.pack(fmt, v1, v2) # 把不同的变量v1,v2打包在一起，成为一个字节字符串
struct.unpack(fmt, string) # 将字节字符串解包为变量string
struct.calcsize(fmt) # 计算按照格式fmt打包的结果有多少个字节
```

##### 2.1 pack操作

该操作必须接受一个template string以及需要进行pack一组的数据，这意味这pack处理操作定长的数据

```python
import struct
fmt = "2I3sI"
a = struct.pack(fmt, 12, 34, bytes("abc", encoding='utf-8'), 56)
b = struct.unpack(fmt, a)
print(b)
(12, 34, ‘abc’, 56)
```

上面代码将两个整数12和34，一个字符串“abc”和一个整数46一起打包成一个字符字节流，然后再解包。其中打包格式中明确指出了打包的长度：`“2I”`表明起始是两个`unsigned int`，`3s`表明长度为4的字符串，最后一个`“I”`表示最后紧跟一个`unsigned int`。完整的Python pack操作支持的数据类型见下表。

```
注意：拼接字符串时，要将其转换成某种编码格式
```

| Format | C Type               | Python type        | Standard size | Notes    |
| :----: | -------------------- | ------------------ | ------------- | -------- |
|  `x`   | pad byte             | no value           |               |          |
|  `c`   | `char`               | string of length 1 | 1             |          |
|  `b`   | `signed char`        | integer            | 1             | (3)      |
|  `B`   | `unsigned char`      | integer            | 1             | (3)      |
|  `?`   | `_Bool`              | bool               | 1             | (1)      |
|  `h`   | `short`              | integer            | 2             | (3)      |
|  `H`   | `unsigned short`     | integer            | 2             | (3)      |
|  `i`   | `int`                | integer            | 4             | (3)      |
|  `I`   | `unsigned int`       | integer            | 4             | (3)      |
|  `l`   | `long`               | integer            | 4             | (3)      |
|  `L`   | `unsigned long`      | integer            | 4             | (3)      |
|  `q`   | `long long`          | integer            | 8             | (2), (3) |
|  `Q`   | `unsigned long long` | integer            | 8             | (2), (3) |
|  `f`   | `float`              | float              | 4             | (4)      |
|  `d`   | `double`             | float              | 8             | (4)      |
|  `s`   | `char[]`             | string             |               |          |
|  `p`   | `char[]`             | string             |               |          |
|  `P`   | `void *`             | integer            |               | (5), (3) |

##### 2.2 计算字节大小

```python
fmt = "2I3sI"
print(struct.calcsize(fmt)) # 16
```

可以看到上面的三个整型加一个3个字符的字符串一共占用了16个字节。这是因为struct在打包过程中，根据特定类型的要求，必须进行字节对齐。由于默认的unsigned int型占用四个字节，因此要在字符串的位置进行4个字节对齐，因此即使是3个字符的字符串也要占用4个字节。

以下模式不需要字节对齐

```python
print(struct.calcsize("2Is")) # 9
print(struct.calcsize("I")) # 4
print(struct.calcsize("2I")) # 8
print(struct.calcsize("2IsI")) # 16
print(struct.calcsize("sI")) # 8
print(struct.calcsize("s")) # 1
```

由于单字符出现在两个整型之后，不需要进行字节对齐，所以输出结果是9。

##### 2.3 unpack操作

对于该操作而言，只要fmt对应的字节数和字节字符串string的字节数一致，就可以成功的进行解析，否则unpack函数将抛出异常。例如可以使用如下的fmt解析出a

```
fmt = "2I2sI"
c = struct.unpack(fmt, a)
print(struct.calcsize(fmt))
print(c) # 16 (12, 34, "ab", 56)
```

##### 2.4 扩展--打包/解包变长字符串

对于变长字符串在处理的时候可以把字符串的长度当成数据的内容一起打包

```python
s = byte(s)
data = struct.pack("I%ds" %(len(s),), len(s), s)
```

上述代码把字符s的长度打包成内容，可以在进行内容读取的时候直接读取。

```
# 解包变长字符串
int_size = struct.calcsize("I")
(i, ), data = struct.unpack("I", data[:int_size]), data[int_size:]
```

解包变长字符时首先解包内容的长度，在根据内容的长度解包数据