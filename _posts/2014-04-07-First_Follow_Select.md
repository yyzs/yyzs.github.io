---
layout: post
title: "FIRST集、Follow集、SELECT集定义和求法"
description: ""
category: 编译原理
tags: [编译原理,语法分析,FIRST,FOLLOW]
---

### FIRST集

#### 定义

FIRST(α)定义为可以从α推导得到的串的首符号集合，其中α是任意的文法符号串。

FISRT(α) = { a | α ![*次推导][1] a...}

#### 计算

分两种情况，单个文法符号的首符号集和文法符号串的首符号集。

1.  单个文法符号X的首符号集FIRST(X)
    
    1.  如果X是个终结符号，那么FIRST集合就是它自己本身，即FIRST(X) = X;
    2.  如果 X -> ε 是一个产生式， 那么将 ε 加入到FIRST(X)中;
    3.  如果X是个非终结符号，且 X -> Y1Y2Y3...... 是一个产生式，其中Y1，Y2，Y3......既可以是终结符，也可以是非终结符。首先将FIRST(Y1)加入到FIRST(X)中，如果Y1 ![*次推导][1] ε ,那么将FIRST(Y2)加入到FIRST(X)中，如果Y2 ![*次推导][1] ε ,那么将FIRST(Y2)加入到FIRST(X)中。以此类推，如果最后一个依旧能够通过0次或多次推导到ε ，那么将 ε 加入到FIRST(X)中。

2.  文法符号串X1X2X3...Xn的首符号集FIRST(X1X2X3...Xn)
    
    首先将FIRST(X1)中所有非 ε 符号加入到FIRST(X1X2X3...Xn)中。如果 ε 属于 FIRST(X1)， 那么加入FIRST(X2) 中所有非 ε 符号。如果 ε 属于FIRST(X2)，那么加入FIRST(X3) 中所有非 ε 符号。以此类推，最后如果 ε 都在FIRST(Xi)中，那么将 ε 加入到FIRSt(X1X2X3...Xn)中。

来看个例子，加深说明。

对于文法：

<pre>E  -> TE'
E' -> +TE'|&epsilon;
T  -> FT'
T' -> *FT'|&epsilon;
F  -> (E)|id
</pre>

*   FIRST(E) = FIRST(T) = FIRST(F) = { (, id }
    
    E、T、F都不能通过0次或多次推导得到 ε 对应上面单个文法符号首符号集第3条。

*   FIRST(E') = { +, ε }

*   FIRST(T') = { *, ε }
*   FIRST(ε) = {ε}
*   FIRST(id) = { id }
*   FIRST(TE') = FIRST(T) = { (, id }
    
    T 不能通过0次或多次推导得到 ε 所以不需要将FIRST(E')加入到 FIRST(TE')中。

*   FIRST(+TE') = FIRST(+) = { + }

*   FIRST(E'T') = (FIRST(E') - {ε}) ∪ (FIRST(T') - {ε}) ∪ {ε} = { +, *, ε }
    
    首先将FIRST(T')中除了 ε 加入到 FIRST(E'T')中，由于E' -> ε 所以也要将FIRST(T')中除了 ε 加入到 FIRST(E'T')中, 由于 ε 也在 FIRST(T')中，所以将 ε 加入。

### FOLLOW集

#### 定义

对于非终结符号A， FOLLOW(A)定义为可能在某些句型中紧跟在A右边的终结符号的集合。

FOLLOW(A) = { a | S ![*次推导][1] ...Aa... }

#### 计算

1.  将$放到FOllOW(S)中，其中S是开始符号，而$是输入右端的结束标记。
2.  如果存在一个产生式 A -> αBβ, 那么FIRST(β)中除了ε 之外的所有符号都在FOLLOW(B)中。
3.  如果存在一个产生式 A -> αB 或存在产生式 A -> αBβ 且FIRST(β)包含ε，那么FOLLOW(A)中的所有符号都在FOLLOW(B)中。

简单解释，注意到产生式右部的形如"...Aa..."的组合，把a加入到FOLLLOW(A)中;对于形如"...AB..."(B是非终结符)，把FIRST(B)除ε加入到FOLLOW(A)中;对于形如A → ...B的产生式(其中B是非终结符)，FOLLOW(A)中的所有符号都在FOLLOW(B)中。

同样还是拿例子来说明下，文法同上。

1.  FOLLOW(E) = FOLLOW(E') = { ), $ }
    
    E是开始符号，所以将$加入;产生式 F->(E)|id 说明了)加入的原因;对于E‘，产生式 E->TE' ，参照上面第3条可以得到结论，FOLLOW(E)中的所有符号都在FOLLOW(E')中。

2.  FOLLOW(T) = FOLLOW(T') = {+, ), $}
    
    产生式 E'->+TE'|ε 得到结论 将FIRST(E')中除了 ε 都加入，FIRST(E') = { +, ε }, 也就是将‘+’加入;FIRST(E')包含ε,所以将FOLLOW(E')都加入进来,也就是将')','$'加入。

3.  FOLLOW(F) = {+, *, ), $}
    
    产生式 T'->*FT'|ε ,所以将FIRST(T')中除了ε加入，又因为FIRST(T')包含ε，所以将FOLLOW(T')加入。

### SELECT集

#### 定义

给定上下文无关文法的产生式 A→α , A ∈ V<sub>N</sub>, α ∈ V*

*   若α不能推导出ε，则SELECT(A→α) = FIRST(α)
*   若α能推导出ε ，则SELECT(A→α) = FIRST(α) ∪ FOLLOE(A)

SELECT集的作用就是将FIRST集和FOLLOW集合并。

 [1]: /assets/images/CodeCogsEqn1.png
