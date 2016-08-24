---
layout: post
title: "JS正则表达式CheetSheet"
categories: 编程语言 cheetsheets
tags: 正则表达式
---

今天仔细研读了一下 ES2015 标准中的正则表达式部分，作此留念，方便查阅。
ES2015 和 ES5.1 在正则上的区别是: ES5.1 不支持 "\u{HH..H}"。
保留符号: `^`, `$`, `\`, `.`, `*`, `+`, `?`, `(`, `)`, `[`, `]`, `{`, `}`, `|`,`/`

<table class="table table-hover" >
    <tbody>
<tr><td colspan="2" style="text-align:center;font-weight:bold">元字符</td></tr>
<tr><td>.</td><td>除换行之外的字符</td></tr>
<tr><td>非保留符号</td><td>匹配此字符</td></tr>
<tr><td>\保留符号</td><td>匹配保留符号</td></tr>
<tr><td>\d</td><td>匹配任意数字[0-9]</td></tr>
<tr><td>\D</td><td>匹配任意非数字[^0-9]</td></tr>
<tr><td>\s</td><td>匹配任何空白字符[\f\n\r\t\v]</td></tr>
<tr><td>\S</td><td>匹配任何非空白字符[^\f\n\r\t\v]</td></tr>
<tr><td>\w</td><td>匹配任何单词字符[A-Za-z0-9_]</td></tr>
<tr><td>\W</td><td>匹配任何非单词字符 </td></tr>
<tr><td>abc | xyz</td><td>匹配 abc 或 xyz</td></tr>
<tr><td>[xyz]</td><td>其中的一个字符</td></tr>
<tr><td>[^xyz]</td><td>非其中的字符</td></tr>
<tr><td>\f</td><td>匹配"\u000C",换页符</td></tr>
<tr><td>\n</td><td>匹配"\u000A"，换行</td></tr>
<tr><td>\r</td><td>匹配"\u000D"，回车</td></tr>
<tr><td>\t</td><td>匹配"\u0009"，制表</td></tr>
<tr><td>\v</td><td>匹配"\u000B"，垂直制表符</td></tr>
<tr><td>\0</td><td>匹配"\u0000"，NULL</td></tr>
<tr><td>\xHH</td><td> H 表示一位16进制数字，匹配"\xHH"</td></tr>
<tr><td>\uHHHH</td><td>匹配"\uHHHH"</td></tr>
<tr><td>\u{任意个H}</td><td>匹配"\u{HH…H}"</td></tr>
<tr><td>\cx</td><td> Ctrl-x（x是任意字母,不区分大小写)<br /> (a-z分别表示"0x01"-"0x1A")</td></tr>
<tr><td colspan="2" style="text-align:center;font-weight:bold">捕获</td></tr>
<tr><td>（表达式）</td><td>捕获(编号从1开始)+改变优先级</td></tr>
<tr><td>（?:表达式）</td><td>改变优先级，不捕获</td></tr>
<tr><td>\编号</td><td>前面捕获的值[注1]</td></tr>
<tr><td colspan="2" style="text-align:center;font-weight:bold">匹配位置</td></tr>
<tr><td>^</td><td>起始</td></tr>
<tr><td>$</td><td>结束</td></tr>
<tr><td>\b</td><td>单词边界</td></tr>
<tr><td>\B</td><td>非单词边界</td></tr>
<tr><td>(?=表达式）</td><td>后面匹配"表达式"的位置</td></tr>
<tr><td>(?!表达式)</td><td>后面不匹配"表达式"的位置</td></tr>
<tr><td colspan="2" style="text-align:center;font-weight:bold">量词</td></tr>
<tr><td>?</td><td>匹配前面的子表达式 0 次或 1 次(尽可能多)</td></tr>
<tr><td>*</td><td>匹配前面的子表达式任意次（尽可能多）</td></tr>
<tr><td>+</td><td>匹配前面的子表达式 1 次或多次（尽可能多）</td></tr>
<tr><td>{n}</td><td>匹配子表达式 n 次</td></tr>
<tr><td>{n,}</td><td>匹配子表达式大于等于 n次（尽可能多）</td></tr>
<tr><td>{n,m}</td><td>最少匹配 n 次且最多匹配 m 次（尽可能多）</td></tr>
<tr><td>??</td><td>匹配前面的子表达式 0 次或 1 次(尽可能少)</td></tr>
<tr><td>*?</td><td>匹配前面的子表达式任意次(尽可能少)</td></tr>
<tr><td>+?</td><td>匹配前面的子表达式 1 次或多次(尽可能少)</td></tr>
<tr><td>{n}?</td><td>匹配子表达式 n 次(跟贪婪模式一样)</td></tr>
<tr><td>{n,}?</td><td>匹配子表达式大于等于 n次(尽可能少)</td></tr>
<tr><td>{n,m}?</td><td>最少匹配 n 次且最多匹配 m 次(尽可能少)</td></tr>
    </tbody>
</table>
注1: 在未捕获到值之前`\数字`会被大部分值JS引擎解释八进制数对应的值，比如`\1(a)\1` 会匹配 `"\x01aa"`，`\11`匹配"\x09"。但是ES标准里面要求这种情况要抛出异常。另外，`\数字`中`\` 后所有的数字作为一起，比如`\11`和`(\1)1`不等价,`\11`匹配捕获到的第11个值。


##### 方括号中的改变

1. `^` 作为第一个字符具有特殊意义
1. `-` 只有其前后都有字符才有特殊意义
1. 保留字符仅剩 `\`，`-`，`]`
1. 不能使用 `\B` 和捕获的值 `\编号`
1. `\b` 匹配 `"\u0008"`（退格）

