---
layout: post
disqus_comments: true
title: Learning Bash Note - Chapter 4
category: Bash
tags:
 - Basic
---

shell 脚本: 包含 shell 命令的文件

执行脚本的方式: 

 - `source 脚本名`，在当前会话中运行
 
 - `chmod a+x scriptname` + `./scriptname`，在‘子shell’中运行

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 

脚本运行方式：

 - `source script`: 当前会话中运行
 
 - `./script`: 子shell中运行，并且当前shell等待执行其运行结束
 
 - `./script &`: 子shell中运行，并且当前shell仍然有控制权

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 

函数: 

function functionname

{
    
    shell commands

}

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 

试着用 `source function.sh` 和 `./function.sh` 分别运行此脚本function.sh,并查看输出.

#!/bin/bash

function ls {
	#unset -f ls
	ls ~/ > ~/fromfunc.txt
}

declare -f > before.txt

ls

declare -f > after.txt

ls

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

shell查找资源顺序, 包含脚本:

1. alias

2. keyword

3. function

4. 内置命令

5. 脚本和可执行程序, 按 PATH 环境变量中列出的目录对其进行搜索

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

查看一个命令(脚本，函数)的类型:

`type` 

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

shell 变量内部存储字符串, 用`$`可以引用到

`hatter=mad`
`echo "$hatter"`

注意:

`$var` 实际是 `${var}`的缩写, 所以要打印 UID后面加一个下划线：

`echo $UID_` 会找 UID_这个变量

正确的做法应该是 `echo ${UID}_`

__区分语言间特性的方式就是比较其变量的功能__

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

位置参数

`$n`: 第 n 个参数
`$0`: 脚本名
`$*`: 以 `IFS`分隔的单行所有参数 `"$1 $2 $3"`
`$@`: `"$1" "$2" "$3"`
`$#`: 参数个数

脚本的`$n`是传递给脚本的参数

函数的`$n`是传递给函数的参数

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

变量的作用域是：从其定义以后开始生效

如果函数内部想要定义一个函数作用域外的同名变量(从而不影响到外面变量)，需要用 `local`

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

字符串操作符

测试是否定义: `${varname:-word}`

 - 若 varname 存在且非 `null`, 返回其值
 - 若 varname 不存在或者`null`, 返回 `word`
 
赋值: `${varname:=word}` __位置参数和特殊参数不能这么设置__

 - 若 varname 存在且非 `null`, 返回其值
 - 若 varname 不存在或者为 `null`, 将 varname 设置为 `word`,并返回其值
 
错误检查: `${varname:?message}`

 - 若 varname 存在且非 `null`, 返回其值
 - 若 varname 不存在或者为 `null`, 打印 `varname: message`，若省略 `message`则产生默认信息 `parameter null or not set`
 
测试变量是否存在: `${varname:+word}`

 - 若 varname 存在且非`null`, 返回 `word`
 - 若 varname 不存在或者为 `null`, 返回 `null`
 
返回字符串一部分: `${varname:offset}` 或 `￥{varname:offset:length}`

 - 若 `offset > 0`, 从第 0 个字符开始计算
 - 若 `offset < 0`, 从末尾开始计算
 - 若 `varname` 为 `@`, `length` 则为从参数 `offset`开始的位置参数的数目
 
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

模式和模式匹配

`${variable#pattern}`: 若可匹配变量值开头, 删除最短匹配, 返回其余

`${variable##pattern}`: 若可匹配变量值开头, 删除最长匹配, 返回其余

`${variable%pattern}`: 若可匹配变量值结尾, 删除最短匹配, 返回其余

`${variable%%pattern}`: 若可匹配变量值结尾, 删除最长匹配, 返回其余

`${variable/pattern/string}${variable//pattern/string}`: 将匹配模式的最长部分替换为string. 第一种格式中只有第一个匹配被替换, 第二个格式中, 所有匹配均被替换. 

   - 如果模式以 `#`开头, 则必须匹配 `variable` 的开头
   
   - 如果模式以 `%`开头, 则必须匹配 `variable` 的结尾
   
   - 如果 `string` 为 null, 匹配部分被删除
   
   - 如果 `variable` 为 `@` 或者 `*`, 操作被一次应用于每个位置参数并扩展为结果列表

记法: #总是在数字前 #1 #2 #3 %总在数字后 1% 100%

例子: 

path=/home/cam/book/long.file.name

Expression                   Result

${path##/*/}                      long.file.name

${path#/*/}              cam/book/long.file.name

$path              /home/cam/book/long.file.name

${path%.*}         /home/cam/book/long.file

${path%%.*}        /home/cam/book/long

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

让变量有值的三种方式:

  - 赋值语句
  
  - 命令行参数
  
  - __命令替换__
  
命令替换： `$(UNIX command)`, 命令结果返回为表达式的值, 可嵌套.


