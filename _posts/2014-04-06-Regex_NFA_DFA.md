---
layout: post
title: "词法分析之正则表达式、NFA、DFA之间的转换"
description: ""
category: 编译原理
tags: [编译原理,词法分析,DFA,NFA]
---


词法分析中正则表达式最终是要转换成DFA,而且是最小化的DFA。

步骤：

1. 先把正则表示式转换为NFA
2. 通过“子集构造法”把NFA转换成DFA
3. 通过“分割法”最小化DFA

![][1]
###正则表示式到NFA

####NFA

NFA由以下几个部分组成：

1. 一个有限的状态集合S;
2. 一个输入符号集合&sum; ，即输入字符表;
3. 一个状态转换函数move(S&times;&sum;->S);
4. S的一个状态被指定为开始状态S<sub>0</sub>;
5. S的一个子集F被指定为接受状态集合。

####几条基本规则

1. 对于表达式 &epsilon; 构造下面的NFA：

    ![NFA1][2]

2. 对于表达式 a 构造下面的NFA：

    ![NFA2][3]

3. 对于表达式 a|b 构造下面的NFA：

    假设a和b的NFA分别为N(a)和N(b).
    
    ![NFA4][5]

4. 对于表达式 ab 构造下面的NFA：

    假设a和b的NFA分别为N(a)和N(b).

    ![NFA3][4]

5. 对于表达式 a<sup>\*</sup> 构造下面的NFA：

    ![NFA5][6]

基本上所有的正则表达式通过以上几条规则都可以转换成NFA了，下面来看一个例子：

将正则表达式 (a|b)<sup>\*</sup>abb 转换成NFA

分成几个小步骤：

1. 先分别构造 a 与 b 的NFA;

    ![example-a][7]
    ![example-b][8]

2. 构造 a|b 的NFA;

    ![example-ab][9]

3. 构造 (a|b)<sup>\*</sup> 的NFA;

    ![example-ab1][10]

4. 构造 abb的NFA;

    ![example-abb][11]

5. 构造 (a|b)<sup>\*</sup>abb 的NFA。

    ![example-ababb][12]

###NFA到DFA

**NFA与DFA的区别：** DFA是NFA的一个特例：其中DFA中没有 &epsilon; 跳转;同时对于每个状态s和每个输入符号a，有且仅有一条标记为a的边离开，也就是说状态是唯一的。

**子集构造法** 由NFA构造DFA,主要思想就是将集合变成状态。

输入：一个 NFA N; 输出： 一个接受同样语言的DFA D。

几个概念：

设s表示N的单个状态，而T代表N的一个状态集合。

1. &epsilon;-closure(s)={t|从状态s出发，通过标记为&epsilon;的路径到达的NFA状态集合};
2. &epsilon;-closure(T)={t|从状态s&isin;T出发，通过标记为&epsilon;的路径到达的NFA状态集合};

    简单理解就是，对于每一个s&isin;T，都执行一次&epsilon;-closure(s)，得到的结果是一个状态集合。

3. move(T, a)={t|从s&isin;T出发，经过标记为a的路径到达的NFA集合}。

将NFA转换成DFA就是不断重复上面几个运算的得到的。

先来看看子集构造法算法的简单描述：

<pre>
一开始，&epsilon;-closure(s0)是Dstates中唯一的状态，且未被标记;
while(在Dstates中有未被标记的状态T) {
    给T加上标记;
    for(每个输入符号a) {
        U = &epsilon;-closure((move(T, a));
        if (U不在Dstates中)
            将U加入到Dstates中，且不加标记;
        Dtran[T, a] = U;
    }
}
</pre>

上面这段描述是龙书上的算法描述，我的理解就是，刚开始时候转换表中只有开始符号s0的&epsilon;-closure(s0)。如果转换表中还有未被标记的状态T，这是先标记上，然后对于每个输入符号a，先计算move(T, a)得到一个状态集合A，然后计算&epsilon;-closure(A)得到状态U,如果U不在转换表中，那么将状态U加入到转换表中，如果转换表中有U，那么就不需加入。反复循环，直到转换表中的状态都被标记过。

来个例子，加深理解。

####举个栗子

将接受语言 (a|b)<sup>\*</sup>abb 的NFA转换成DFA。

NFA如图： ![example-ababb][12]

NFA中开始状态是0, 那么一开始转换表中只有T = &epsilon;-closure(0) = {0, 1, 2, 4, 7}。这里T中的状态只能从0出发，只经过标记为&epsilon;的路径到达的状态，这里就是0, 1, 2, 4, 7。**注意：**路径可以是不包含边的，所以状态0也是可以从自身出发经过标记为&epsilon;的路径到达的状态。

NFA的输入字母表为{a, b}。先把T标记，表示以及处理了这个状态。下面开始处理T这个状态。计算A = move(T, a) = {3, 8}，这里T的状态0, 1, 2, 4, 7中只有2和7状态能够通过标记为a的路径到达3和8,其余都没有a边转换，所以move(T, a) = {3, 8}。同理 move(T, b) = {5}。计算U = &epsilon;-closure(A) = {1, 2, 3, 4, 6, 7, 8}。这里计算&epsilon;-closure(A)就是计算&epsilon;-closure(s),s为状态集合A中的状态，这里s就分别为3和8。所以&epsilon;-closure(A) = &epsilon;-closure(3) &cup; &epsilon;-closure(8)。U = {1, 2, 3, 4, 6, 7, 8}没有在转换表中，这时将U加入到转换表中。然后计算 U = &epsilon;-closure(move(T, b)) = {1, 2, 4, 5, 6, 7}, 也没有在转换表中，将其加入。到这里转换表中一个状态T以及处理完成，然后接下来处理剩下的未被标记的状态，方法同时。

最终，转换表中的状态都被标记上，也就代表所以的操作以及完成。子集构造法完成，接下来就根据刚完成的转换表画出DFA。

来看看完成后的状态转换表。转换表中状态集合用单独符号表示，如状态集合{0,1,2,4,7}就用Ａ来表示。

<table>
    <tr>
        <td>Dstates</td>
        <td>DFA状态</td>
        <td>a</td>
        <td>b</td>
    </tr>
    <tr>
        <td>{0,1,2,4,7}</td>
        <td>A</td>
        <td>{1,2,3,4,6,7,8} B</td>
        <td>{1,2,4,5,6,7} C</td>
    </tr>
    <tr>
        <td>{1,2,3,4,6,7,8}</td>
        <td>B</td>
        <td>{1,2,3,4,6,7,8} B</td>
        <td>{1,2,4,5,6,7,9} D</td>
    </tr>
    <tr>
        <td>{1,2,4,5,6,7}</td>
        <td>C</td>
        <td>{1,2,3,4,6,7,8} B</td>
        <td>{1,2,4,5,6,7} C</td>
    </tr>
    <tr>
        <td>{1,2,4,5,6,7,9}</td>
        <td>D</td>
        <td>{1,2,3,4,6,7,8} B</td>
        <td>{1,2,4,5,6,7,10} E</td>
    </tr>
    <tr>
        <td>{1,2,4,5,6,7,10}</td>
        <td>E</td>
        <td>{1,2,3,4,6,7,8} B</td>
        <td>{1,2,4,5,6,7} C</td>
    </tr>
</table>

从表中可以看出状态Ａ通过ａ边跳转可以到达状态Ｂ，通过ｂ边跳转可以到达状态Ｃ。转换后的DFA中开始符号是Ａ，包含NFA状态10的E状态是唯一的接受状态。

根据这个转换表可以得到DFA。

![DFA][13]

PS: 包含NFA接受状态的DFA状态都是DFA接受状态。也就是说，比如NFA中有接受状态a, b，那么DFA中包含ａ或ｂ的状态的状态集合都是接受状态。

###最小化DFA

最小化DFA的含义：

1. 没有对于的状态(死状态);
2. 没有两个状态是互相等价(不可区分)

基本思想：首先将状态划分为接受状态和非接受状态两组，然后逐步将这个划分精细化，最后得到一个不可再细化的状态集划分，每个状态子集做为一个状态。

等价状态必须满足两个条件：

1. 一致性条件——状态ｓ和状态ｔ必须同时为接受状态或非接受状态；
2. 蔓延性条件——对于所有的输入符号，状态ｓ和状态ｔ必须转移到等价的状态中。

同样来个例子来说明：

####举个栗子

DFA: ![DFA][14]

1. 将状态划分为两个组，接受状态一组 S1 = {C, D, E, F}，非接受状态一组 S2 = {S, A, B}, 现在是两组{S, A, B} {C, D, E, F};
2. 判断{S, A, B}是否可划分;

    S经过a到达A，在S2中;A经过a到达C，在S1中;B经过a到的A，在S2中。所以 {S, A, B}可以在划分了{S, B} {A}。现在是三组{S, B} {A} {C, D, E, F};


3. 判断{S, B}是否可以划分;

    S经过b到达B，B经过b到达D，所以S与B不是等价的，可以再划分。现在 {S} {A} {B} {C, D, E, F};

4. 判断{C, D, E, F}是否可以划分;

    {C, D, E, F}经过a和b到达的状态都属于{C, D, E, F}，所以不可在划分。{C, D, E, F}是等价的，所以可以用{C}来代替{C, D, E, F}

最小化后的DFA：

![DFA][15]

[1]: /assets/images/pic3.png
[2]: /assets/images/NFA1.png
[3]: /assets/images/NFA2.png
[4]: /assets/images/NFA3.jpeg
[5]: /assets/images/NFA4.jpeg
[6]: /assets/images/NFA5.jpeg
[7]: /assets/images/example_a.png
[8]: /assets/images/example_b.png
[9]: /assets/images/example_ab.png
[10]: /assets/images/example_ab1.png
[11]: /assets/images/example_abb.png
[12]: /assets/images/example_ababb.png
[13]: /assets/images/DFA.png
[14]: /assets/images/DFA1.jpeg
[15]: /assets/images/DFA2.jpeg
