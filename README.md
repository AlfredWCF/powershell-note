# 写在前面
powershell learn note

出版于Manning Publications，相关的Month of Lunches系列还有好多
[Learn Windows PowerShell in a Month of Lunches, Second Edition](https://www.manning.com/books/learn-windows-powershell-in-a-month-of-lunches-second-edition)
[Learn PowerShell Toolmaking in a Month of Lunches](https://www.manning.com/books/learn-powershell-toolmaking-in-a-month-of-lunches)

学习路线<https://jdhitsolutions.com/blog/essential-powershell-resources/>
相关书籍<https://jdhitsolutions.com/blog/books-and-training/>

其它一些资料
[powershell deep dives](http://pdf.th7.cn/down/files/1312/PowerShell%20Deep%20Dives.pdf)

## Command Line Interface shell ，即CLI shell
powershell 本质上是一种shell（壳程序），区别于core（核心程序）。具体一点儿，是一种可以运行命令行工具(command-line utilities)的command-line shell(命令行壳程序)。
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

与传统管道命令的不同在于，PowerShell管道中传递的对象

    Get-Process | Get-Member
    Get-Process | Export-Csv proc.csv

结合管道命令使用Export-命令，将特定信息导出到文件。
从而，可以持久化一个快照；与别人共享快照；或者在之后的某个时间点查看快照；

## 对比文件【对象】

导出的文件，结合使用Compare-Object命令，可以实现文件对比的功能，即Diff。Diff同样也是Compare-Object的别名。
本质上是对比两个对象。
例如：

    Compare-Object -ReferenceObject (Import-Clixml .\reference.xml) -DifferenceObject (Get-Process) -Property name

可以制作各种baseline，例如：
* services
* processes
* 系统配置
* 用户组
……等等
基于这些baseline管理各台机器

使用export-* 命令，导出文件时。其实是结合使用了ConvertTo- 以及 Out-File 命令

    Get-Service | ConvertTo-Html | Out-File services.html

## 影响等级（Are you sure ？）

Pipeline可以连接各种操作

    Get-Process -Name notepad++ | Stop-Process

系统级别的命令，内部会对其定义一个影响等级。对应的，Power Shell有一个设置：

    $ConfirmPreference

当命令的影响等级等于或者高于当前配置时，shell会询问“Are you sure？”。你也可以强制Power Shell这么做：

    Get-Service | Stop-Service -confirm

类似的参数 -whatif 可以用在所有适用于 -confirm 的场景。

*********************************************************************

# 扩展命令


类似于MMC（Microsoft Management Console）。当添加管理工具时，例如 Exchange Server、SqlServer Management Studio 等，这些工具的PowerShell扩展同时也被添加进来。
并且，极有可能会添加一个 特定产品的管理Shell

## 扩展：snap-ins

PSSnapin 由一个或多个DLL文件，以及一个写有配置、帮助文本的XML文件组成。
而且，PSSnapin越来越远离我们的生活。微软团队致力发展power shell扩展的另一种形式module

## 扩展：modules

modules扩展不需要像snap-ins一样 注册，Power shell去特定的位置寻找modules。
路径地址存于环境变量中：

    get-content env:PSModulePath

PSModulePath如此重要。原因在于，借助于PSModulePath，Power shell可以自动发现所有的modules，并且在用用户调用某个命令时，自动加载该module。

不同扩展中的重名命令，后加载的覆盖前者。命名新扩展中的命令时，应该为其添加前缀，避免重名。例如：get-ADUser，中的AD代表Activedirectory。
否则，需要键入 "扩展名\命令"。

## profile script -- 预加载扩展

添加文件$Home\Documents\WindowsPowerShell\profile.ps1
文件中的命令会被预先执行，例如：

    Add-PSSnapin SqlServerCmdletSnapin100
    cd c:\
    Write-Host 'hello ...[from moli administrator]'

保存好文件后，再次打开Power shell时，会加载SqlServerCmdletSnapin100插件，并且输出：

    hello ...[from moli administrator]
    PS C:\> 

注意需要设置脚本执行权限：
    Set-ExecutionPolicy -ExecutionPolicy Unrestricted

******************************************************************************************

# Pipeline，deeper

CommandA | CommandB

管道是如何传递参数的： pipeline parameter binding
* pipeline input ByValue
* pipeline input ByPropertyName
* 

## Pipleline input ByValue

一个cmdlet，只允许存在一个参数通过ByValue的形式，接受管道传参。之前用到的，就是这种形式

    Get-Process -Name notepad++ | Stop-Process

## Pipeline input ByPropertyName

当尝试通过ByValue的方式传输管道参数失败时，Power shell 会改用第二种方式 Pipeline input ByPropertyName

通过这种方式绑定参数，问题在于，它会匹配所有CommandA中的属性以及CommandB中的参数。
比如，CommandA输出的对象包含有Name<String>和Value<String>参数时，便可以调用：

    CommandA | new-alias

因为new-alias的参数中，恰好包含同名同类型的ByPropertyName参数：

    -Name <String>
        Specifies the new alias. You can use any alphanumeric characters in an alias, but the first character cannot be a number.

        必需?        true
        位置?        0
        默认值        None
        是否接受管道输入?  True (ByPropertyName)
        是否接受通配符?   false
    ……
    ……

    -Value <String>
        Specifies the name of the cmdlet or command element that is being aliased.

        必需?        true
        位置?        1
        默认值        None
        是否接受管道输入?  True (ByPropertyName)
        是否接受通配符?   false

## 通过自定义属性，进行管道参数绑定
当之前的两种管道参数绑定的形式，都不能满足需求时，可以使用hash表达式自定义参数，**注意命令中的各种写法**：

    Get-Process TeamViewer | Select-Object -Property @{name='A';expression='ID'},@{label='B';e={$psitem.ProcessName}},@{n='C';e={$_.workingSet}}

        A B                 C
        - -                 -
        1520 TeamViewer 33640448

将上例中的A B C 参数，替换成CommandB中需要通过管道绑定的参数名即可

## 还可以使用括号，或者点语法 $PSItem.Property

Get-WmiObject 命令的-ComputerName参数，不支持管道绑定。可以使用括号的方式，传参：

    Get-WmiObject -class Win32_BIOS -ComputerName (Get-Content .\computers.txt)

更复杂一点儿的，需要展开某个属性，来满足指定参数的要求，注意-expand的用法：

    Get-Service -computerName (Get-ADComputer -filter * -searchBase "ou=domain controllers,dc=company,dc=pri" | Select-Object -expand name)

************************************************************************************

# Formatting -- 格式化 OutPut

多数情况下，Power shell默认的输出格式，可以满足我们的需求。

## default formatting

管道中的对象，没有特别指定的话，最终都会调用Out-Default命令。
所有Out-命令，无法直接处理管道中的对象。它们会求助于格式化系统，然后按照Formating System返回的的指令输出内容。

* formatting rule
随着Power shell一同安装的.format.ps1xml文件中，内置了各种对象的格式化规则。

规定了，以table还是list的方式显示，显示哪些属性，每一列的宽度等等

* second formatting rule
.format.ps1xml文件中找不到对应的格式化指令的，继而看，是否存在default display property set。例如types.ps1xml中的DefaultDisplayPropertySet定义。

规定了显示对象的哪些属性。

* the third formatting rule
如果要显示的属性少于（包含）4个，则显示table；如果多于四个，则显示list

## 用户如何控制格式化

