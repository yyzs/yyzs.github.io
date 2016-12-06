---
layout: post
title: "编译原理—自顶向下的语法分析"
description: ""
category: 编译原理
tags: [编译原理,语法分析]
---

从文法的开始符号出发，从顶部开始构造语法分析树。

考虑如何根据当前的输入单词符号唯一地确定选用哪条产生式替换相应的非终结符，如何构建一颗相应的语法树。

当选用自顶向下的语法分析时，首先要判断所给的文法是不是LL(1)文法。

对于给定的LL(1)文法的句子，可以用确定的分析方法进行分析，这种程序称为预测分析器。

一个分析表驱动的预测分析器的模型：

*   控制程序
*   预测分析表M
*   栈
*   输入
*   输出

控制程序考虑栈顶符号X和当前的输入符号a。如果X是个非终结符号，分析器查询分析表M中的条目M [X, a]来选择一个X产生式。

#### 预测分析表的建立

预测分析表是一个二维矩阵M[A, a]。其中A是终结符号，a是非终结符号或$符号。

矩阵中的元素是产生式，如果 a ∈ SELECT(A → α)，则把A → α 放入 M[A, a]。

SELECT(A → α)的含义前面的文章有所介绍。

考虑文法G：

*   E → TE'
*   E' → +TE'|ε
*   T → FT'
*   T' → *FT'|ε
*   F → (E)|id

SELECT集

*   SELECT(E → TE') = { (, id }
*   SELECT(E' → +TE') = { + }
*   SELECT(E' → ε) = { ε, ), $ }
*   SELECT(T → FT') = { (, id }
*   SELECT(T' → *FT') = { * }
*   SELECT(T' → ε) = { ε, +, ), $ }
*   SELECT(F → (E)) = { ( }
*   SELECT(F → id) = { id }

建立分析表：

先看第一条 SELECT(E → TE') = { (, id } ， ( ∈ SELECT(E → TE') ，那么M[E, (]中填入 SELECT(E → TE') ; id ∈ SELECT(E → TE') ，那么M[E, id][]中填入 SELECT(E → TE') 。

很简单，简单点说就是把 SELECT(A → α) 中的元素a，与A对应，得到M[A, a][]，在M[A, a]中填入 SELECT(A → α) 。

当把所有的SELECT集合都处理完，就的到了分析表，如下：

<table>
  <tr>
    <td>
    </td>
    
    <td>
      id
    </td>
    
    <td>
      +
    </td>
    
    <td>
      *
    </td>
    
    <td>
      (
    </td>
    
    <td>
      )
    </td>
    
    <td>
      $
    </td>
  </tr>
  
  <tr>
    <td>
      E
    </td>
    
    <td>
      E → TE'
    </td>
    
    <td>
    </td>
    
    <td>
    </td>
    
    <td>
      E → TE'
    </td>
    
    <td>
    </td>
    
    <td>
    </td>
  </tr>
  
  <tr>
    <td>
      E'
    </td>
    
    <td>
    </td>
    
    <td>
      E' → +TE'
    </td>
    
    <td>
    </td>
    
    <td>
    </td>
    
    <td>
      E' → ε
    </td>
    
    <td>
      E' → ε
    </td>
  </tr>
  
  <tr>
    <td>
      T
    </td>
    
    <td>
      T → FT'
    </td>
    
    <td>
    </td>
    
    <td>
    </td>
    
    <td>
      T → FT'
    </td>
    
    <td>
    </td>
    
    <td>
    </td>
  </tr>
  
  <tr>
    <td>
      T'
    </td>
    
    <td>
    </td>
    
    <td>
      T' → ε
    </td>
    
    <td>
      T' → *FT'
    </td>
    
    <td>
    </td>
    
    <td>
      T' → ε
    </td>
    
    <td>
      T' → ε
    </td>
  </tr>
  
  <tr>
    <td>
      F
    </td>
    
    <td>
      F → id
    </td>
    
    <td>
    </td>
    
    <td>
    </td>
    
    <td>
      F → (E)
    </td>
    
    <td>
    </td>
    
    <td>
    </td>
  </tr>
</table>

后面的语法分析算法就需要用到这张表。

#### 表驱动的预测语法分析

输入：一个串w， 文法G的预测分析表M

输出：如果w在L(G)中，输出w的一个最左推导，否则给出一个错误提示。

初始状态：输入缓冲区为`w$`，栈顶为开始符号S，它的下面为$。

算法：

<pre>设置ip指想w的第一个符号，其中ip是输入指针;
令 X 为栈顶符号;
while(X != $)   /* 栈非空 */
{
    if (X 等于ip所指向的符号a) 执行栈的弹出操作，将ip指针后移一位;
    else if (X 是一个终结符号) error();
    else if (M[X, a] 是一个报错条目 ) error();
    else if (M[X, a] = X &rarr; Y1Y2Y3...Yk)
    {
        输出产生式 X &rarr; Y1Y2Y3...Yk;
        弹出栈顶符号;
        将Yk,Yk-1...Y3,Y2,Y1压入栈，其中Y1为栈顶。
    }
    X = 栈定符号;
}
</pre>

算法其实并不是很复杂，主要是要得到文法G的预测分析表。

下面来看个例子，用上面的到的预测分析表来处理`id + id * id`。

开始状态，栈中的元素为`$, E`(约定右边为栈顶)，栈顶元素为E，输入缓存区为`id + id * id$`。

1.  ip 指向 id, X 为E。M[E, id] = E → TE', 所以输出产生式 E → TE' ，同时，弹出E，把E'，T入栈。操作结束时栈中元素为`$E'T`，输入缓存区`id + id * id$`。

2.  ip 指向 id, X 为T。M[T, id] = T → FT', 所以输出产生式 T → FT' ，同时，弹出T，把T'，F入栈。操作结束时栈中元素为`$E'T'F`，输入缓存区`id + id * id$`。

3.  ip 指向 id, X 为F。M[F, id] = F → id, 所以输出产生式 F → id ，同时，弹出F，把id入栈。操作结束时栈中元素为`$E'T'id`，输入缓存区`id + id * id$`。

4.  ip 指向 id, X 为id。X 等于ip所指向的符号，都为id, 所以弹出X，ip指针后移一位。操作结束时栈中元素为`$E'T'`，输入缓存区`+ id * id$`。

......

就是这样循环，知道X为$，循环结束。

![分析过程][1]

循环结束后，输出的缓存区的内容为：

<pre>E &rarr; TE'
T &rarr; FT'
F &rarr; id
T' &rarr; &epsilon;
E' &rarr; +TE'
T &rarr; FT'
F &rarr; id
T' &rarr; *FT'
F &rarr; id
T' &rarr; &epsilon;
E' &rarr; &epsilon;
</pre>

可以根据输出缓存区的内容构建出一棵语法树。

![语法树][2]

 [1]: /assets/images/table.png
 [2]: /assets/images/tree.png
