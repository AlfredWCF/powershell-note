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

