---
layout: post
title:  "angr-Solver Engine"
categories: angr
tags:  angr
author: zaipingy
---

* content
{:toc}

导入二进制文件：
```
>>> import angr, monkeyhex
>>> proj = angr.Project('/bin/true')
>>> state = proj.factory.entry_state()
```
angr中的 **bitvector** 用有界整数的语义来解释的话就是**位序列或者位向量**。






构造一个位向量：


```
# 64-bit bitvectors with concrete values 1 and 100
>>> one = state.solver.BVV(1, 64)
>>> one
 <BV64 0x1>
>>> one_hundred = state.solver.BVV(100, 64)
>>> one_hundred
 <BV64 0x64>

# create a 27-bit bitvector with concrete value 9
>>> weird_nine = state.solver.BVV(9, 27)
>>> weird_nine
<BV27 0x9>
```
位向量就只是单纯的一个变量，可以进行运算，但是因为他不想int值那样，所以返回的也还是一个位向量的运算结果，返回的值有两个默认属性，op和args

op就是运算符，args就是运算的值。

```
>>> tree = (x + 1) / (y + 2)
>>> tree
<BV64 (x_9_64 + 0x1) / (y_10_64 + 0x2)>
>>> tree.op
'__div__'
>>> tree.args
(<BV64 x_9_64 + 0x1>, <BV64 y_10_64 + 0x2>)
>>> tree.args[0].op
'__add__'
>>> tree.args[0].args
(<BV64 x_9_64>, <BV64 0x1>)
>>> tree.args[0].args[1].op
'BVV'
>>> tree.args[0].args[1].args
(1, 64)
```
可以有很多层，但是最终层就是位向量。


## 约束求解
angr可以通过state.solver进行约束求解：

```
>>> state.solver.add(x > y)
>>> state.solver.add(y > 2)
>>> state.solver.add(10 > x)
>>> state.solver.eval(x)
4
```
通过add增加约束条件，然后eval输出，答案不一定是4，但是一定会在3-9之间。

下面是一个比较常见的使用方式：

```
# get a fresh state without constraints
>>> state = proj.factory.entry_state()
>>> input = state.solver.BVS('input', 64)
>>> operation = (((input + 4) * 3) >> 1) + input
>>> output = 200
>>> state.solver.add(operation == output)
>>> state.solver.eval(input)
0x3333333333333381
```
通过BVS构造一个位向量，然后operation就是常见的CTF题目中的一些转换，通过add加入限制条件，eval就可以输出最终的结果。

可以通过state.satisfiable()来判断加入的约束条件是否是正确的，如果根本不可能有结果，那就会返回false。

```
>>> state.solver.add(input < 2**32)
>>> state.satisfiable()
False
```
如果强行去求解的话，会返回一个异常信息。

其他类型eval的求解方法：
- solver.eval(expression)  返回一个解
- solver.eval_one(expression)    返回一个解，若多于一个解则发出异常
- solver.eval_upto(expression, n)    返回至多n个解
- solver.eval_atleast(expression, n)    返回至少n个解
- solver.eval_exact(expression, n)    返回n个解，多或者少都会发出异常
- solver.min(expression)    返回可能的最小的解
- solver.max(expression)    返回可能的最大的解

其中可以加入的一些参数：
- extra_constraints    一些外部的约束，元组形式。
- cast_to    输出的类型

例子：
```
state.solver.eval(state.solver.BVV(0x41424344, 32), cast_to=str) will return "ABCD".
```
