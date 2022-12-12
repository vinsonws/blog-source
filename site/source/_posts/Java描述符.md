---
layout:   post
author:   vinsonws
title:    Java描述符
date:     2022-10-04 16:21:36
updated:  2022-10-04 17:00:12
cover:    false  
tags: 
  - Java
  - Java 类文件
  - Java Class
plugins:
  - indent
---
Java class 描述符分为 Field 描述符和 Method 描述符。

## Field（域）

> 在 [Java Language and Virtual Machine Specifications](https://docs.oracle.com/javase/specs/) 中将 void 这种类型定义为额外的 VoidDescriptor（V），种子方法描述符的返回值使用，故不属于Field。

有 BaseType、ObjectType、ArrayType 三种类型。

### BaseType

BaseType 是 Field 的基本类型，它是下面类型中的一种。

| Type            | 描述符   |
| --------------- | ----------- |
| byte            | B           |
| char            | C           |
| double          | D           |
| float           | F           |
| int             | I           |
| long            | J           |
| short           | S           |
| boolean         | Z           |

**例子：**

int i 中，i 描述符就为 I

### ObjectType

即为引用

| Type            | 描述符   |
| --------------- | ----------- |
| reference       | L *classname* |

**例子：**

Object o，o 描述符就为 Ljava/lang/Object

### ArrayType

| Type            | 描述符   |
| --------------- | ----------- |
| reference array | [           |

例子：

double[][][] array，o 描述符就为 [[[D

## Method（方法）

### 描述符格式

> ( {ParameterDescriptor} ) ReturnDescriptor

其中 ParameterDescriptor 指入参，类型为 FieldType。而 ReturnDescriptor 指返回值，类型可能为 FieldType 和 VoidDescriptor（V）

**例子：**

```class
(IDLjava/lang/Thread;)Ljava/lang/Object;
```
