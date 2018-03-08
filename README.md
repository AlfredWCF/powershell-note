# 写在前面
powershell learn note

出版于Manning Publications，相关的Month of Lunches系列还有好多
[Learn Windows PowerShell in a Month of Lunches, Second Edition](https://www.manning.com/books/learn-windows-powershell-in-a-month-of-lunches-second-edition)
[Learn PowerShell Toolmaking in a Month of Lunches](https://www.manning.com/books/learn-powershell-toolmaking-in-a-month-of-lunches)

其它一些资料
[powershell deep dives]http://pdf.th7.cn/down/files/1312/PowerShell%20Deep%20Dives.pdf

## Command Line Interface shell ，即CLI shell
powershell 本质上是一种shell（壳程序），区别与core（核心程序）。具体一点儿，是一种可以运行命令行工具(command-line utilities)的command-line shell(命令行壳程序)。
作为一款优秀的shell，powershell具有脚本的能力

## Graphical User Interface shell 即 GUI shell
windows Explorer 操作系统，是图形界面shell。
图形界面的优点在于，可以很容易的探索学习

## Batch. KiXtart. VBScript. Powershell 并不是第一次自动化方面的尝试。

试想，如果使用GUI工具，添加域账号的场景。添加一个用户需要5分钟，添加100个用户呢？管理员表示压力山大。
powershell之前，可以使用VBScript编写脚本，通过脚本导入编辑好的用户资料（CSV文件），快速创建用户。

那么问题来了，VBScript功能够用，何必开发powershell？
  原因在于，微软各种工具的开发者，需要提前为VBScript预留访问点，用来hook up
  这个问题，被powershell的总架构师effrey Snover称作“最后一公里”

powershell是如何解决这最后一公里的？
  微软继续构建GUI控制台，其所有的功能的调用，改为执行powershell命令。
  the Windows world will start to split into two groups:administrators who can use PowerShell，and those who can’t.

开始之前，请确保使用admin权限运行powershell ISE

> Ctrl + C 退出命令

*************************************

# 强大的帮助系统

在探索学习方面，虽然没有GUIs那样显而易见的强大，powershell的帮助系统为用户提供了强有力的保证。

* 处理某个问题该用哪个命令，如何使用？
* 遇到error怎么处理？
* 如何组合使用不同的命令实现复杂功能？

获取关于event log 的相关命令
    Get-Help -Name *log*
    Get-Help *event*

Get-Help 搜索的是命令的 help topics,几乎每个命令都包含help topics
Get-Command 搜索的是命令本身,并且可以使用-verb -noun -type 指定动词、名词、命令类型(cmdlet/func)

从结果列表中，找到可能会用到的命令，进而查看命令帮助
    Get-Help Get-EventLog

名称
    Get-EventLog
    
摘要
    Gets the events in an event log, or a list of the event logs, on the local or remote computers.

语法
    Get-EventLog [-LogName] <String> [[-InstanceId] <Int64[]>] [-After <DateTime>] [-AsBaseObject] [-Before <DateTime>] [-ComputerName <String[]>] [-Ent
    ryType {Error | Information | FailureAudit | SuccessAudit | Warning}] [-Index <Int32[]>] [-Message <String>] [-Newest <Int32>] [-Source <String[]>] 
    [-UserName <String[]>] [<CommonParameters>]
    
    Get-EventLog [-AsString] [-ComputerName <String[]>] [-List] [<CommonParameters>]

## 解读帮助内容

关于语法：

* 一个命令可能有多个参数集，调用不同的参数集，会产生不同的行为。
* 每两个参数集之间，至少有一组互斥的参数，但可能有共同的参数。
* 当调用命令时，指定的参数不足以区分参数集时，powershell通常选择帮助列表中的第一个参数集来执行。
* 各参数集都会包含通用参数

关于参数：

* [中括号]中的内容为可选项，未在中括号中的参数为强制参数
* 可省略参数名（直接填写参数value）的参数，叫做位置参数。因其出场率非常高，享受优待

脚本写入文件时，不要使用位置参数、以及参数缩写。以便提高其它人员的可读性。

* switches 参数不需要参数值（value）
* <尖括号>中规定了参数类型

******************************

# 撇开脚本，说说命令(command)

命名惯例：

* cmdlet 是PowerShell中的命令行工具,基于.net框架编写
* function 与cmdlet功能类似，不同之处在于，使用PowerShell自己的脚本语言编写
* workflow 区别于前两者，是特定环境中（PowerShell workflow系统）的function
* application 是各种外部可执行文件（多为.exe），例如 ping ipconfig
* command 是前面所有的统称

> cmdlet 遵循 动词-名词（verb-noun）的命名方式
> 不是所有的动词，都是真正意义上的动词，例如 New-  Where-

进阶玩法：

* 使用别名
* 使用 Export-Alias Import-Alias 定制专属别名体系
* **尽量避免**使用自定义别名，会给他人以及后期使用上带来诸多不便
* 参数可截断
* 参数有别名 (get-command get-eventlog | select -ExpandProperty parameters).computername.alias
* 参数名可省略 (位置参数)

可以 Show-Command <cmdlet>，调用图形界面填写参数，生成调用参数，避免语法错误。当遇到**不知所以**的error时(大多是语法错误)，可以先用Show-Command 命令生成完整的调用参数，重新运行命令。

PowerShell 在后台运行Cmd.exe 所以支持例如ping ipconfig的外部工具

*********************************************

# Providers

Get-PSProvider

PSProvider 是PowerShell中的适配器，将数据存储（Data Storage）以磁盘驱动器（Disk Drive）的形式展现出来。

不同的PSProvider提供不同的能力，最常见的:

* ShouldProcess 该PSProvider可使用-WhatIf -Confim参数进行测试
* Filder 该PSProvider中的cmdlet支持-Filter参数
* Credentials 连接数据存储时，需要提供凭证 -credentials
* Transactions 支持事务处理

通过PSProvider创建并以磁盘驱动器形式暴露出来的Disk Driver 叫做PS Drive
通过各种cmdlets操作PS Drive中的数据。Get-Item[Property] Move-Item[Property] Copy-Item[Property] New-Item[Property] Rename-Item[Property]等等。这些cmdlets基本上都**带有item字样**。

-Path vs -LiteralPath:
在PSDrive中进行导航时，问号（?）星号（*）可以作为通配符使用。在文件系统中，这是没有问题的，因为文件/文件夹的命名中不允许出现问号和星号。使用参数 -LiteralPath，其中的特殊字符则不会被解释成通配符

*******************************************************

# Pipeline




