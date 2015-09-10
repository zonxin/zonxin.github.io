---
layout: post
title: "正则表达式CheetSheet"
categories: 编程语言 CheetSheets
tags: 正则表达式
---

正则表达式教程，参见[正则表达式30分钟入门](http://deerchao.net/tutorials/regex/regex-1.htm)

<table class="table table-hover" >
    <thead><tr>
        <th>作用</th><th>Basic Regx</th><th>Extenheh Regx</th><th>Python Regx</th><th>Perl Regx</th>
    </tr></thead>
    <tbody>
<tr><td>转义</td><td>\</td><td>\</td><td>\</td><td>\</td></tr>
<tr><td>任意单个字符</td><td>.</td><td>.</td><td>.</td><td>.</td></tr>
<tr><td>行首</td><td>^</td><td>^</td><td>^</td><td>^</td></tr>
<tr><td>行尾</td><td>$</td><td>$</td><td>$</td><td>$</td></tr>
<tr><td>表达式</td><td>\(\)</td><td>()</td><td>()</td><td>()</td></tr>
<tr><td>x 或 y</td><td>\|</td><td>|</td><td>|</td><td>|</td></tr>
<tr><td>其中的一个字符</td><td>[xyz]</td><td>[xyz]</td><td>[xyz]</td><td>[xyz]</td></tr>
<tr><td>非其中的字符</td><td>[^xyz]</td><td>[^xyz]</td><td>[^xyz]</td><td>[^xyz]</td></tr>
<tr><td>捕获匹配的引用</td><td>不支持</td><td>\num</td><td>\num</td><td>不支持</td></tr>
<tr><td>单词边界</td><td>\b</td><td>\b</td><td>\b</td><td>\b</td></tr>
<tr><td>非单词边界</td><td>\B</td><td>\B</td><td>\B</td><td>\B</td></tr>
<tr><td>单词边界（起始）</td><td>\<</td><td>\<</td><td>不支持</td><td>不支持</td></tr>
<tr><td>单词边界（结束）</td><td>\></td><td>\></td><td>不支持</td><td>不支持</td></tr>
<tr><td>任何单词字符[A-Za-z0-9_]</td><td>\w</td><td>\w</td><td>\w</td><td>\w</td></tr>
<tr><td>任何非单词字符 </td><td>\W</td><td>\W</td><td>\W</td><td>\W</td></tr>
<tr><td>任意一个数字字符</td><td>不支持</td><td>不支持</td><td>\d</td><td>\d</td></tr>
<tr><td>非数字字符</td><td>不支持</td><td>不支持</td><td>\D</td><td>\D</td></tr>
<tr><td>任何空白字符[\f\n\r\t\v]</td><td>不支持</td><td>不支持</td><td>\s</td><td>\s</td></tr>
<tr><td>任何非空白字符[^\f\n\r\t\v]</td><td>不支持</td><td>不支持</td><td>\S</td><td>\S</td></tr>
<tr><td>换行符</td><td>不支持</td><td>不支持</td><td>\n</td><td>\n</td></tr>
<tr><td>回车符</td><td>不支持</td><td>不支持</td><td>\r</td><td>\r</td></tr>
<tr><td>换页符</td><td>不支持</td><td>不支持</td><td>\f</td><td>\f</td></tr>
<tr><td>横向制表符</td><td>不支持</td><td>不支持</td><td>\t</td><td>\t</td></tr>
<tr><td>垂直制表符</td><td>不支持</td><td>不支持</td><td>\v</td><td>\v</td></tr>
<tr><td>Ctrl-x(x是任意字母)</td><td>不支持</td><td>不支持</td><td>不支持</td><td>\cx</td></tr>
<tr><td>0xdd对应的字符</td><td>不支持</td><td>不支持</td><td>不支持</td><td>\xdd</td></tr>
<tr><td colspan="0" style="text-align:center;font-weight:bold">POSIX字符集</td></tr>
<tr><td>任何字母或数字</td><td>[:alnum:]</td><td>[:alnum:]</td><td>[:alnum:]</td><td>[:alnum:]</td></tr>
<tr><td>任何字母</td><td>[:alpha:]</td><td>[:alpha:]</td><td>[:alpha:]</td><td>[:alpha:]</td></tr>
<tr><td>任何数字</td><td>[:digit:]</td><td>[:digit:]</td><td>[:digit:]</td><td>[:digit:]</td></tr>
<tr><td>任何小写字母</td><td>[:lower:]</td><td>[:lower:]</td><td>[:lower:]</td><td>[:lower:]</td></tr>
<tr><td>任何大写字母</td><td>[:upper:]</td><td>[:upper:]</td><td>[:upper:]</td><td>[:upper:]</td></tr>
<tr><td>任何空白字符</td><td>[:space:]</td><td>[:space:]</td><td>[:space:]</td><td>[:space:]</td></tr>
<tr><td>空格和制表符（横向和纵向）</td><td>[:blank:]</td><td>[:blank:]</td><td>[:blank:]</td><td>[:blank:]</td></tr>
<tr><td>任何可以看得见的且可以打印的字符</td><td>[:graph:]</td><td>[:graph:]</td><td>[:graph:]</td><td>[:graph:]</td></tr>
<tr><td>任何可以打印的字符</td><td>[:print:]</td><td>[:print:]</td><td>[:print:]</td><td>[:print:]</td></tr>
<tr><td>任何控制字符</td><td>[:cntrl:]</td><td>[:cntrl:]</td><td>[:cntrl:]</td><td>[:cntrl:]</td></tr>
<tr><td>任何标点符号</td><td>[:punct:]</td><td>[:punct:]</td><td>[:punct:]</td><td>[:punct:]</td></tr>
<tr><td>任何十六进制数[0-9A-Fa-f]</td><td>[:xdigit:]</td><td>[:xdigit:]</td><td>[:xdigit:]</td><td>[:xdigit:]</td></tr>
<tr><td colspan="0" style="text-align:center;font-weight:bold">量词</td></tr>
<tr><td>非贪婪模式</td><td>不支持</td><td>不支持</td><td>不支持</td><td>不支持</td></tr>
<tr><td>匹配前面的子表达式 0 次或 1 次</td><td>\?</td><td>？</td><td>？</td><td>？</td></tr>
<tr><td>匹配前面的子表达式 0 次或多次</td><td>*</td><td>*</td><td>*</td><td>*</td></tr>
<tr><td>匹配前面的子表达式 1 次或多次</td><td>\+</td><td>+</td><td>+</td><td>+</td></tr>
<tr><td>n 必须是一个 0 或者正整数，匹配子表达式 n 次</td><td>\{n\}</td><td>{n}</td><td>{n}</td><td>{n}</td></tr>
<tr><td>匹配子表达式大于等于 n次</td><td>\{n,\}</td><td>{n,}</td><td>{n,}</td><td>{n,}</td></tr>
<tr><td>最少匹配 n 次且最多匹配 m 次 </td><td>\{n,m\}</td><td>{n,m}</td><td>{n,m}</td><td>{n,m}</td></tr>
    </tbody>
</table>

