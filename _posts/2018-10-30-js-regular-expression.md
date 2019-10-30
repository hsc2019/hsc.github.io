---
title: JavaScript 正则表达式
date: 2018-10-28 19:35:39
categories:
- 前端
tags:
- js
- 正则表达式
---

# 概述
正则表达式，又称规则表达式。（英语：Regular Expression，在代码中常简写为regex、regexp或RE），计算机科学的一个概念。正则表达式通常被用来检索、替换那些符合某个模式(规则)的文本。本文以 JavaScript 的正则表达式为例，对正则表达式的实际用法进行一个简单的总结。

> * 列举两个可以在线调试的网站
> * [一个正则可视化网站：https://regexper.com/](https://regexper.com/)
> * [一个可视化 JavaScript 调试网站：http://jsbin.com/qutevuwamu/1/edit?js,console](http://jsbin.com/qutevuwamu/1/edit?js,console)

# RegExp 对象

两种方法构造 RegExp 对象

- 字面量 `var reg = /\bis\b /g`
- 构造函数 `var reg = new RegExp('\\bis\\b', 'g'`)

> **注**：在 Linux 中反斜线本身就是特殊字符，所以需要再次转义，故写成 `\\bis\\b` 的格式

# 修饰符

> * **g**: global 全文搜索，不添加的话搜索到第一个匹配停止，添加的话匹配全局
> * **i**: ignore case 忽略大小写，默认大小写敏感
> * **m**: multipl lines 多行搜索

示例

```javascript
'He is a boy. Is he?'.replace(/\bis\b/g, '0')  // expected: "He 0 a boy. Is he?"
'He is a boy. Is he?'.replace(/\bis\b/i, '0')  // expected: "He 0 a boy. 0 he?"
```

# 元字符

- 正则表达式有两种基本字符串类型组成
    - 原义文本字符
    - 元字符
- 元字符是**有特殊含义的非字母字符**
- `* + ? $ ^ . | \ ( ) { } [ }`

元字符

| 字符  | 含义                          |
| ----- | ----------------------------- |
| `\t`  | 水平制表符                    |
| `\v`  | 垂直制表符                    |
| `\n`  | 换行符                        |
| `\r`  | 回车符                        |
| `\0`  | 空字符                        |
| `\f`  | 换页符                        |
| `\cX` | 与 X 对应的控制字符（Ctrl+X） |

# 字符类

- 一般情况下正则表达式一个字符对应字符串的一个字符

- 字符类
    - 我们可以使用元字符`[]`构建一个简单的类
    - 所谓类是指符合某些特性的对象，一个泛指，而不是指某个字符
    - 表达式`[abc]`把字符串 a 或 b 或 c 归为一类，表达式可以匹配这类的字符
- 字符类取反
    - 使用元字符`^`创建**反向类/负向类**
    - 反向类的意思是不属于某类的内容
    - 表达式 `[^abc]` 表示不是字符 a 或 b 或 c 的内容
- 示例

```javascript
'a1b2c3d4'.replace(/[abc]/g, 'X'); //output: X1X2X3d4
'a1b2c3d4'.replace(/[^abc]/g, 'X'); //output: aXbXcXXX
```

# 范围类

> * 正则表达式提供了范围类
> * 可以使用 `[a-z]` 表示**从 a 到 z 的任意字符**
> * 这是个闭区间，包括 a 和 z 本身
> * 在 `[]` 中是可以连写的，例如 `[a-zA-Z]` 表示从 a 到 z 大小写匹配

示例：

```js
'a1b2c3d4'.replace(/[a-z]/g, 'Q'); //output: Q1Q2Q3Q4
'2016-09-12'.replace(/[0-9-]/g, 'A'); //output: AAAAAAAAAA
```

# 预定义类

## 预定义字符

| 字符 | 等价类            | 含义                             |
| ---- | ----------------- | -------------------------------- |
| `.`  | `[^\r\n]`         | 除了回车符和换行符之外的所有字符 |
| `\d` | `[0-9]`           | 数字字符                         |
| `\D` | `[^0-9]`          | 非数字字符                       |
| `\s` | `[\t\n\x0B\f\r]`  | 空白字符                         |
| `\S` | `[^\t\n\x0B\f\r]` | 非空白字符                       |
| `\w` | `[a-zA-Z_0-9]`    | 查询字符串（字母，数字下划线）   |
| `\W` | `[^a-zA-Z_0-9]`   | 非单词字符                       |

## 边界

| 字符 | 含义        |
| ---- | ----------- |
| `^`  | 以 xxx 开头 |
| `$`  | 以 xxx 结束 |
| `\b` | 单词边界    |
| `\B` | 非单词边界  |

示例

```js
// for \b \B
'This is a boy'.replace(/is/g, '0'); //output: Th0 0 a boy
'This is a boy'.replace(/\bis/g, '0'); //output: This 0 a boy
'This is a boy'.replace(/\Bis/g, '0'); //output: Th0 is a boy
```
```js
// for ^
'@123@abc@'.replace(/@./g, 'Q'); //output: Q23Qbc@
'@123@abc@'.replace(/^@./g, 'Q'); //output: Q23@abc@
'@123@abc@'.replace(/.@/g, 'Q'); //output: @23QabcQ
'@123@abc@'.replace(/.@$/g, 'Q'); //output: @123@abQ
```
```js
// for multip line
mulstr="@123\n@456\n@789";
mulstr.replace(/^@\d/g, 'X'); //output:"X23\n@456\n@789"
mulstr.replace(/^@\d/gm, 'X'); //output:"X23\nX456\nX789"
```

# 量词

- 适用场景：希望匹配连续出现多次的内容的字符串

| 字符    | 含义                           |
| ------- | ------------------------------ |
| `?`     | 出现零次或一次（最多出现一次） |
| `+`     | 出现一次或多次（至少出现一次） |
| `*`     | 出现零次或多次（任意次）       |
| `{n}`   | 出现 n 次                      |
| `{n,m}` | 出现 n 到 m 次                 |
| `{n,}`  | 至少出现 n 次                  |

```js
\d{20} //匹配连续出现20次的数字
\d{20}\w\d?\w+\d*\d{3}\w{3,5}\d{3,}
```

# 贪婪模式

> * 正则表达式会尽可能多的匹配，直到匹配失败
> * 非贪婪模式即让正则表达式尽可能少的匹配，即一旦匹配成功就不再匹配了。做法很简单，**在量词后面加上 `?` 即可。**

# 分组

- 问题：匹配字符串 byron 连续出现3次的场景

使用量词：`byron{3}` 并不能完成目标。因为它只会对紧挨着的 `n` 匹配三次，此时，需要使用分组。分组很简单，直接**加上小括号即可**。即 `(byron){3}`

示例：

```js
'a1b2c3d4'.replace(/[a-z]\d{3}/g, 'X') //a1b2c3d4
'a1b2c3d4'.replace(/([a-z]\d){3}/g, 'X') //Xd4
```

## 或（使用竖线表示）

```js
'ByronCaseper'.replace(/Byron|Caseper/g, 'X') //XX
'ByronsperByrCasper'.replace(/Byr(on|Ca)sper/g, 'X') //XX
```

## 反向引用

> * 问题：将2015-12-25 => 12/25/2015
> * 实现方法是使用反向引用，将分组内容使用 `$` 进行捕获，然后再用 `$` 进行表示。

示例
```js
'2015-12-25'.replace(/(\d{4})-(\d{2})-(\d{2})/g, '$1/$2/$3')
// 12/25/2015
```

## 忽略分组

- 不想捕获某些分组，只需要在分组内加上 `?:` 就可以了。例如

```js
(?:Bryon).(ok)
```

## 前瞻

> * 正则表达式从文本头部向尾部开始解析，**文本尾部方向，称之为前**
> * **前瞻**就是再正则表达式匹配到规则的时候，向前检测是否符合断言，后顾/后瞻方向相反

> 注：JavaScript不支持后顾，故不再演示

| 名称     | 正则            | 含义     |
| -------- | --------------- | -------- |
| 正向前瞻 | `exp(?=asset)`  |          |
| 负向前瞻 | `exp(?!asset)`  |          |
| 正向后顾 | `exp(?<=asset)` | JS不支持 |
| 负向后顾 | `exp(?<!asset)` | JS不支持 |

示例：
```js
'a2*34v8'.replace(/\w(?=\d)/g, 'X')
//"X2*X4X8"
'a2*34v8'.replace(/\w(?!\d)/g, 'X')
//"aX*3XvX"
```

# 对象属性

> * global：是否全文搜索，默认 false
> * ignore case：是否大小写敏感，默认 false
> * multiline：多行搜索，默认 false
> * lastIndex：是当前表达式匹配内容的最后一个字符的下一个位置
> * source：正则表达式的文本字符串

# test和exec方法

- RegExp.prototype.test(str)
    - 用于测试字符串参数中是否存在匹配正则表达式模式的字符串
    - 如果存在则返回 true，否则返回 false
- RegExp.prototype.exec(str)
    - 使用正则表达式模式对字符串执行搜索，并将更新全局 RegExp 对象的属性以反映匹配的结果
    - 过没有匹配的文本则返回 null，否则返回一个结果数组
        - index 声明匹配文本的第一个字符的位置
        - input 存放被检索的字符串

> **注**：在 test 方法中，如果匹配全局后有多个结果时，lastindex 保存上次执行结果，并向下迭代，直至没有结果时返回 false，则重新回到首位。此时再次执行，则再次循环上面的步骤。

示例：
```js
var reg1 = /\w/;
var reg1 = /\w/g;
while(reg2.test('ab')) {
    console.log(reg2.lastIndex);
}
/*
1
2
*/
```

## 非全局调用

> * 调用非全局的 RegExp 对象的 `exec()` 方法时，返回数组
> * 第一个元素是与正则表达式相匹配的文本
> * 第二个元素是与 RegExpObject 的第一个子表达式相匹配的文不（如果有的话）
> * 第三个元素是与 RegExp 对的第二个子表达式想匹配的文本（如果有的话），以此类推

```js
var reg3 = /\d(\w)(\w)\d/;
var reg4 = /\d(\w)(\w)\d/g;
var ts = '$1az2bb3cy4dd5ee';

var ret = reg3.exec(ts);

console.log(reg3.lastIndex+'\t'+ret.index+'\t'+ret.toString());
//"0  1 1az2,a,z"
console.log(reg3.lastIndex+'\t'+ret.index+'\t'+ret.toString());
//"0  1 1az2,a,z"

while(ret = reg4.exec(ts)) {
  console.log(reg4.lastIndex + '\t'+ret.index+'\t'+ret.toString());
}
/*
"5  1 1az2,a,z"
"11 7 3cy4,c,y"
*/
```

# 可以使用正则的函数

## String.prototyp.search(reg)

> * `search()` 方法用于检索字符串中指定的字符串，或检索与正则表达式相匹配的子字符串
> * 方法返回一个匹配结果 index，查不到返回-1
> * `search()` 方法不执行全局匹配，它将忽略标志 g，并且总是从字符串开始进行检索

```js
'a1b2c3d4'.search(/1/g); //1
```
## 字符串匹配：String.prototype.match(reg)

- `match()` 方法将检索字符串，以找到一个或多个与 RegExp 匹配的文本
- RegExp 是否具有标志 g 对结果影响很大
- 非全局调用
  - 如果 RegExp 没有标志 g，那么`match()` 方法就只能执行一次匹配
  - 如果没有找到任何匹配，则返回 null
  - 否则返回一个数组，其中存放了了与它找到的匹配文本有关的信息
  - 返回数组的第一个元素存在的是匹配文本，而其他元素存放的是与正则表达式的子表达式匹配的文本处理常规的数组元素之外，返回的数组还含有两个对数属性
  - index 声明匹配文本的其实字符串在字符串的位置
  - input 声明对 stringObject 的引用
- 全局调用
    - 如果 RegExp 具有标志 g，则 match 方法执行全局检索，找到字符串的所有匹配子字符串
        - 没有找到匹配，返回 null
        - 如果找到一个或多个匹配子串，则返回以数组
    - 数组元素中存放的是字符串所有的匹配子串，而且也没有 index 属性或则 input 属性

## 字符串分隔：String.prototype.split(reg)

- 使用 split 方法把字符串分割为字符数组
```js
'a,b,c,d'.split(',');//['a','b','c','d']
```
- 在一些复杂分割情况下我们可以使用正则表达式解决

```js
'a1b2c3d4'.split(/\d/);//['a','b','c','d']
```

## 字符串替换：String.prototype.replace

```js
- String.prototype.replace(str, replaceStr)
- String.prototype.replace(reg, replaceStr)
- String.prototype.replace(reg, function) function有四个参数
    - 匹配字符串
    - 正则表达式分组ner，没有分组则没有该参数
    - 匹配项在字符串中的index
    - 原字符创
```

# 参考文章

[《JavaScript 正则表达式》(https://www.imooc.com/learn/706)](https://www.imooc.com/learn/706)