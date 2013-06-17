---
layout: post
disqus_comments: true
title: WinDbg Setup
category: WinDbg
tags:
 - Setup
---

#### 什么时候会用到 WinDbg ####

 -一个 issue 只在客户机上 repro

 -一台干净的机器, 却不想安装 VS 进行调试

 -驱动开发和调试

#### 支持的连接类型 ####

 -Null Modem cable (串口连接)

 -1394 火线

 -虚拟机

 -Hyper-V

#### 调试前须知 ####

Target Machine: 被调试的机器, 上面运行着要被调试的程序.

Host Machine: 运行WinDbg的机器, 操作系统版本不需要与Target Machine 相同.

symbol: 函数的地址, 变量的声明, 对应在源代码中的行号等等.

.pdb file: 代码生成 debug 版本生成, 里面包含symbol的信息. 调试的__应用程序文件__, __源文件__, __.pdb文件版本__ 必须是同一次编译生成的.

#### 搭建调试环境 ####

Target Machine 需要: 

	- Windows XP: 

	 - C:\boot.ini末尾加上一行： `multi(0)disk(0)rdisk(0)partition(1)/WINDOWS="Microsoft Windows XP Professional" /noexecute=optin /fastdetect /debug /debugport=COM1 /baudrate=115200`

	- Windows Vista/Windows 7

 	 - `bcdedit /dbgsettings serial debugport:1 baudrate:115200`

 	 - `bcdedit /debug on`

 	- Win PE

 	 - `bcdedit /store x:\boot\bcd –enum -v` 并记录系统的 GUID

 	 - `bcdedit /store x:\boot\bcd /set {GUID of PE OS} debug on`


VM 需要:

	- Virtual PC: 

	 - Setting -> 选择COM1 -> 勾选 Named Pipe -> 填入: `\\.\pipe\com_1`

	- VM Player:

	 - 打开文件 #VMName#.vmx, 查找 `serial0.present = "TRUE" `, 修改 `serial0.fileType = "pipe"`. 并在其后追加： 

	 `serial0.fileName = "\\.\pipe\com_1"``
	 `serial0.tryNoRxLoss = "TRUE"`
	 `serial0.pipe.endPoint = "server"`

Host Machine:

 打开 WinDbg (可能需要以admin运行), File -> Kernel Debug. 接下来设置
 	- Baudrate: 115200
 	- Type -> Port
 	- Pipe -> checked
 	- Reconnect -> checkd
 	- Resets -> 0
 	-> Port -> \\.\pipe\com_1

#### 设置 symbol 和 src 路径 ####

查看当前设置的 symbol 路径
`.sympath`

微软提供的系统级的 public symbol.
`.sympath SRV*c:\dbgsymbols*http://msdl.microsoft.com/download/symbols`

追加symbol路径
`.sympath+ C:\PrivatePath`

载入符号时查看更多消息
`!sym noisy`

设置源码路径
`.srcpath C:\SourcePath`

载入源码过程时查看更多信息
`.srcnoisy 1`


#### 模块加载相关 ####

重新加载全部模块
`.reload /f`

重新加载一个模块
`.reload /f DllorExe.dll`

查看已加载模块
`lm`

查看一个模块的信息
`!lmi modulename`

#### 断点设置 ####

查看模块内符号
`x <module>!<name>`

设置断点
`bp <module>!<position>`
`bp <address>`
`bp <position>+<offset>`

列出当前断点
`bl`

禁用某个断点
`bd <indexofbl>`

启用某个断点
`be <indexofbl>`


删除某个断点
`bc <indexofbl>`


#### 查看内存和变量 ####

显示变量
`dv`

显示变量类型
`dt <variable>`
结构体展开到几级
`dt -r1 <struct>`

C++语法查看变量
`?? <variable>`

显示内存
`dd/dw/db <addr> <length>`

#### 修改变量值 ####

C++语法改值
`?? <name> = <new-value>`
