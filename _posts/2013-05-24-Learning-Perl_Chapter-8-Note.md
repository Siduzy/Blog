---
layout: post
disqus_comments: true
title: 正则表达式匹配
category: Regex
tags:
 - Basic
---

#### 用 m// 进行匹配 ####

--- 

匹配时`//` 是 `m//` 的简写，可以选定其他定界符。既可以：
    
 - 选择成对的界定符：() <> {} []

 - 也可以选择不成对的界定符：`m,fred`, `m!fred!`
 
应该选择模式中不会出现的字符作为定界符，如：

`/http:\/\//` 匹配起始的 `http://` 就不如使用 `m%http://%` 易读。

#### 模式匹配修饰符 ####

--- 


`/i`：忽略大小写

`/s`：令 `.` 匹配换行符

`/x`：在模式中加入空白符 

`/m`：多行匹配

修饰符可以组合使用。

#### 字符解释方式 ####

--- 


`/a`：ANSI

`/u`：UNICODE

`/l`：Local

#### 锚位 ####

--- 


`\A`：匹配字符串的绝对开头

`\z`：匹配字符串的绝对结尾，抹掉换行符

`\Z`：匹配字符串的结尾，后面允许出现换行。

`\^`：行首匹配

`\$`：行末匹配

匹配空行：`/\A\s*\Z/`
如果没有`/m`，`^`和`$` 的行为和`\A` `\z`一样



`\b`：单词边界，个数一定是偶数

`\B`：非单词锚位，可以匹配所有 `\b`不能匹配的位置

#### 绑定操作符 ####

--- 


绑定操作符： =~

使得模式匹配对象不再是 `$_`，而是等号左边的变量

#### 模式中的内插 ####

--- 


`my $what = "larry";`

`if (/\A($what)/) {`

`   #...`
`}`

#### 捕获变量 ####

--- 


在模式中用 `()` 括起来，对应小括号的次序，捕获的变量为 `$1`，`$2`， `$3`...
这使得perl拥有提取字符串特定部分的能力。

注意：__捕获变量通常能活到下次匹配成功为止，所以，失败的匹配不会改变上次成功匹配时捕获的内容，而成功的匹配会将它们的值重置。__
解决方法：__用 `if` 判断捕获成功与否__

#### 不捕获模式 ###

--- 


有时侯括号仅是用来分组，而不是用来捕获，这时要使得分组的括号失效，不参与捕获，方法是：

`/(?:)`

#### 命名捕获 ####

--- 


若日后要修改模式中的捕获情况，增加一个捕获或者减少 一个，那么会影响到 `$1` `$2` 的值。所以要用到命名捕获：

模式中取名：`(?<LABEL> PATTEN)`

捕获中使用：`$+{LABEL}`

#### 自动捕获变量 ####

--- 


$` 或 ${^PREMATCH}：$& 匹配前的内容
$& 或 ${^MATCH}：整个patten所匹配的内容，不同于 $1
$' 或 ${^POSTMATCH}：$& 匹配后的内容

#### 通用量词 ####

--- 


`{m,n}`：最少出现m次，最多出现n次
`{m,}`：最少出现m次，最多出现无限次

`{0,}`：出现0次或者多次，等同于 `*`
`{0,1}`：最多出现一次，等同于 `？`
`{1,}`：最少出现一次，等同于 `+`

#### 优先级 ####

--- 


1. ()
2. 量词 包括 `*` `+` `?` 以及通用量词
3. 锚位和序列（彼此相邻的条目, `abc`）
4. `|`
5. 单独的字符，字符集（`[abc]`），反引用
