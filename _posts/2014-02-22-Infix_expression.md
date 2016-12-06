---
layout: post
title: "中缀表达式转换成后缀表达式"
description: "中缀表达式转换成后缀表达式"
category: 数据结构与算法
tags: [数据结构, 算法]
---

##目的
将中缀表达式转换为后缀表达式。例如`a*b+c+(d+e*f)*g`转化为`ab*c+def*+g*-`。

##转换算法
从左向右扫描，每一个字符：

1. 若当前字符为数字字符或小数点时，将其放入输出队列中;
2. 若当前字符为左括号"("，则将其压入栈中（不需要与栈顶元素进行比较），在不遇到匹配的右括号")"时，绝不从栈中移走左括号;
3. 若当前字符为操作符时，则将该操作符与栈顶元素进行比较：
    1. 若操作符的优先级大于栈顶元素的优先级，则将该操作符压入栈;
    2. 若操作符的优先级小于或等于栈顶元素的优先级时，取出栈顶元素放入输出队列，反复执行直到栈顶元素的优先级小于当前操作符的优先级。
4. 重复上述操作，直到字符串的末尾，将栈中的元素弹出放入输出队列，知道栈为空。

##实现
算法中需要判断，当前字符是否是括号字符，这个简单，代码如下:

```c
int isBrackets (char c)
{
    if (c == '(' || c == ')')
        return 1;
    else
        return 0;
}
```

同时还需要一个函数来判断操作符的优先级

```c
int getPriority (char c)
{
    switch(c)
    {
        case '+':
        case '-':
            return 0;
        case '*':
        case '/':
            return 1;
        case '(':
        case ')':
            return -1;  /* 将括号的优先级设为最低的原因是，左括号只有遇到右括号才会弹出 */
    }
}
```

下面的是转换代码

```c
void ChangeToPostfix(queue<char> &str, stack<char> &op, queue<char> &out)
{
    while (!str.empty())
    {
        char c = str.front();
        str.pop();
        if ((c >= '0' && c <= '9') || c == '.') /* 规则1：如果是数字或小数点，加入到输出队列中 */
            out.push(c);
        else
            check(c, op, out);  /* 规则3：如果是操作符，check函数处理 */
    }
    while (!op.empty()) /* 规则4：字符串处理结束后，将栈中的元素弹出加入输出队列中 */
    {
        char c = op.top();
        op.pop();
        out.push(c);
    }
}
```
单独把字符是操作符的处理作为一个函数

```c
void check (char c, stack<char> &op, queue<char> &out)
{
    if (op.empty()) { /* 如果栈op为空，即op中没有操作符，则把操作符c压入 */
        op.push(c);
        return;
    }
    if (isBrackets()) {   /* 规则2：如果操作符是括号的话 */
        if (c == '(')
            op.push(c);
        else {
            while (op.top() != '(') {   / *弹出所有元素直到遇到左括号 */
                char ch = op.top();
                op.pop();
                out.push(ch);
            }
        }
    }
    else {   /* 操作符不是括号 */
        char ch = op.top(); /* 取栈顶元素，与当前操作符比较优先级 */
        if (getPriority(c) <= getPriority(ch)) {  /* 规则3.2：c的优先级小于栈顶元素ch的优先级 */
            op.pop();
            out.push(ch);
            check(c, op, out);
        }
        else {
            op.push(c); /* 规则3.1：c的优先级比栈顶元素ch的优先级大，压入栈 */
        }
    }
}
```
###参考
* [利用栈实现计算器功能](http://blog.csdn.net/mvpsendoh/article/details/6440835)
