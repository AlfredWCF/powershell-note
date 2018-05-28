# 写在前面
powershell learn note

出版于Manning Publications，相关的Month of Lunches系列还有好多

[Learn Windows PowerShell in a Month of Lunches, Second Edition](https://www.manning.com/books/learn-windows-powershell-in-a-month-of-lunches-second-edition)

[Learn PowerShell Toolmaking in a Month of Lunches](https://www.manning.com/books/learn-powershell-toolmaking-in-a-month-of-lunches)

学习路线<https://jdhitsolutions.com/blog/essential-powershell-resources/>
相关书籍<https://jdhitsolutions.com/blog/books-and-training/>

其它一些资料
[powershell deep dives](http://pdf.th7.cn/down/files/1312/PowerShell%20Deep%20Dives.pdf)  [微软在线文档](https://docs.microsoft.com/en-us/powershell/scripting/powershell-scripting?view=powershell-5.1)

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
  原因在于，微软各种工具的开发者，需要提前为VBScript预留访问点，用来hook up。
  这个问题，被powershell的总架构师effrey Snover称作“最后一公里”

powershell是如何解决这最后一公里的？
  微软继续构建GUI控制台，其所有工具的所有功能的调用，改为执行工具的powershell命令。
  the Windows world will start to split into two groups:administrators who can use PowerShell，and those who can’t.

开始之前，请确保使用admin权限运行powershell ISE

> Ctrl + C 退出命令

*************************************

# 强大的帮助系统

在探索学习方面，虽然没有GUIs那样显而易见的强大，powershell的帮助系统却也极尽全力的为用户提供了强有力的保证。

* 处理某个问题该用哪个命令，如何使用？
* 遇到error怎么处理？
* 如何组合使用不同的命令实现复杂功能？

获取关于event log 的相关命令

        Get-Help -Name *log*
        Get-Help *event*

Get-Help 显示关于**powershell命令**的 help topics,几乎每个命令都包含help topics
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
* 参数有别名 (get-command get-eventlog | select -ExpandProperty parameters).ComputerName.Aliases
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

在PSDrive中进行导航时，问号（?）星号（*）可以作为通配符使用。在文件系统中，这是没有问题的，因为文件/文件夹的命名中不允许出现问号和星号。

若在别的provider中使用特殊符号时不想被解释成通配符，使用-LiteralPath参数

*******************************************************

# Pipeline

与传统管道命令的不同在于，PowerShell管道中传递的是对象

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

伪命令:
    
    CommandA | CommandB

管道是如何传递参数的： pipeline parameter binding
* pipeline input ByValue
* pipeline input ByPropertyName
* ...

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

点语法，简化了展开属性的步骤

    Get-Service -computerName (Get-ADComputer -filter * -searchBase "ou=domain controllers,dc=company,dc=pri").Name

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

* Format-Table

合理使用-autoSize，-property，-groupBy，-wrap等参数，调整输出格式

* Format-List
* Format-Wide
*  ...

### 自定义列和list entries

    Get-Process | Format-Table name,@{n='VM(MB)';e={$_.vm / 1MB -as [int]}} -AutoSize

    Get-Process | Format-Table name,@{n='VM(MB)';e={$_.vm};formatstring='F2';align='left'} -AutoSize

不同于Select-Ojbect命令中，hash表达式中还可以添加[formatstring](https://docs.microsoft.com/en-us/dotnet/standard/base-types/formatting-types)、align参数。

## OutPut格式化的数据

格式化不是目的，output才是

    Get-Process | Format-Wide | Out-Host

    Out-Default
    Out-File
    Out-GridView
    Out-Host
    Out-Null
    Out-Printer
    Out-String

out-default 等同于 out-host。

Out-GridView比较特殊，不会经过formatting system。且不能与Format-命令连用。


**********************************************************************************************

# 过滤和比较 -- Filtering and comparisions

区别 filter left & filter right

    # 左侧过滤
    Get-Service -Name a*
    # 右侧过滤
    Get-Service | Where-Object -filter {$PSItem.Status -eq 'running'}

尽可能的使用左侧过滤，减少后续命令执行时的工作量。

## 比较运算符

* -eq(相等) 例如：数值比较 5 -eq 5 ；字符串比较 'hello' -eq 'hello'
* -ne(不相等)
* -ge(大于或等于) and -le(小于或等于)
* -gt(大于) and -lt(小于)

比较运算符运用到字符串比较时，大小写不敏感。

若想在比较字符串时，进行大小写匹配，使用一组特别的运算符：

* -ceq
* -cne
* -cgt and -clt
* -cge and -cle

其它用于字符串比较的运算符：

* -like -notlike 可使用通配符
* -clike -cnotlike
* -match -notmatch 使用正则表达式
* -cmatch -cnotmatch

布尔运算符：

* -and -or -not

其它运算符参考 Get-Help about_Comparison_Operators

## 管道中过滤对象

    Get-Service | Where-Object -filter {$PSItem.Status -eq 'running'}

之前的右侧过滤的例子中，通过比较服务的状态，过滤出所有状态为running的服务。
查看管道中的对象都有哪些属性，使用Get-Member命令，例如：
    
    Get-Service | Get-Member

更复杂一些的，也属于左侧过滤：

    Get-Process | Where-Object -FilterScript { $PSItem.Name -notlike 'powershell*'} | Sort-Object -Property vm -Descending | Select-Object -First 10 | Measure-Object -Property vm -sum

一直强调的尽可能的左侧过滤，当运用在远程执行命令的时候（remoting），显得尤其重要。因为越多的对象需要传输，就会占用越多的带宽。


**********************************************************************************************************

# Remote control

注：在6.0版本引入，可以通过[SSH](https://docs.microsoft.com/en-us/powershell/scripting/core-powershell/ssh-remoting-in-powershell-core?view=powershell-6)实现跨平台Remoting调用。

不同于其它使用Telnet 或者 SSH的远程技术，Powershell使用的是新的基于http(s)的通信协议 Web Services for Management（WS-MAN）。
WinRM（Windows Remote Management）服务则是基于WS-MAN的实现。WinRM服务随PowerShell一同安装，且默认开启（Windows Server 2012以上操作系统）

原理：远程机器执行完命令，将Output序列化成XML，通过http(s)传输给客户端，客户端反序列化后获得结果对象。

## WinRM概览

远程机器上，powershell在WinRM中注册成endpoint。当WinRM接收到Remoting traffic时，根据其标识，找到对应的endpoint，并将此远程控制命令路由给对应的程序。
也就是说，WinRM只管分发，而不仅仅是为powershell remoting服务。

powershell把在WinRM中注册的endpoint，叫做session configurations

运行 Enable-PSRemoting 命令，即可完成包含WinRM在内的配置。当有许多台机器需要开启powershell remoting时，可以使用GPO（Group Policy object）

Get-Help about_Remote_Troubleshooting 获取更多信息。

## one-to-one remoting

    # 连接域环境中的机器
    Enter-PSSession -ComputerName server2
    # 使用凭据连接到开放互联网上的特定服务器，需要添加TruntedHosts配置（Set-Item -Path WSMan:\localhost\Client\TrustedHosts -Value '172.20.123.220[,another host]'）
    Enter-PSSession -ComputerName 172.20.123.220 -Credential 'DESKTOP-AAKT5OA\Jack Chen'

还可通过ssl连接到服务器，更多信息参考[Access remote computers](https://github.com/devops-collective-inc/secrets-of-powershell-remoting/blob/master/manuscript/accessing-remote-computers.md)

需要注意：

* 进行remoting时，powershell profile script不会被执行
* 用户依然受限于远程计算机的执行策略，比如远程计算机上 Get-ExecutionPolicy后结果为 Restricted。那么远程计算机上此时限制脚本运行。

## 使用 Invoke-Command 进行一对多远程控制

该命令默认的最大并发数为32，若调用时，目标计算机超过32台机器，剩下的则会进入队列，顺序执行。当然，这个并发限制可以修改。

    Invoke-Command -command { dir } -computerName (Get-ADComputer -filter * -searchBase "ou=Sales,dc=company,dc=pri" | Select-Object -expand Name )

Get-ADComputer命令只有在Windows Server 2008或者装有Remote Server Administration Tools的Win7以上版本才能使用。

-SessionOption参数可以指定remoting时的选项

关于remoting的更多信息，查阅<https://github.com/devops-collective-inc/secrets-of-powershell-remoting>


**********************************************************************************************************


# WMI（Windows Management Instrumentation）

这里只讲Powershell中如何使用WMI，以便来完成PowerShell鞭长莫及的事情。

    Compare-Object -DifferenceObject (Get-CimInstance -Namespace root/CIMV2 -ClassName Win32_Service | Select-Object Name)  -ReferenceObject (Get-Service | Select-Object Name) -Property name -IncludeEqual

其实，随着powershell的不断更新优化，需要使用WMI的场景越来越少。尽管powershell命令的底层有可能依然是通过WMI来实现的。

## namespace & classname

WMI 通过 命名空间 和 类名 来组织所有Component（组件）

* 命名空间下包含特定类别的产品或技术
* 类名指管理组件（component）

通过namespace和classname可以拿到所有的管理组件，并且对其执行各种方法调用。
看上去很美好，事实却并非如此。否则，也就没有powershell什么事儿了。

## 一些坏消息

关于WMI的编程标准有不少，但各个产品组如何实现class，以及是否提供文档，完全处于失控状态。

当IIS team在实现 IIS6 时，为各种元素定义了并行的class 例如：

一个网站的实现分多个类，其中一个包含只读属性；另一个类包含可写属性。尤其是当缺乏文档时，对使用者来说，简直就是受罪。

> A website, for example, could be represented by one class that had the typical read-only properties, but also by a second class that had writable properties you could change.

微软没有规定必须使用WMI，必须暴露所有组件。这与开篇说的：

> 微软继续构建GUI控制台，其所有工具的所有功能的调用，改为执行工具的powershell命令。

刚好是背道而驰。

## WMI or CIM cmdlets

* WMI cmdlets 使用RPCs（Remote Procedure calls）协议
* CIM cmdlets 使用WS-MAN协议

微软不会继续投资WMI cmdlets，鼓励使用CIM cmdlets

    gwmi -class win32_bios -computer localhost |
        format-table `
         @{l='ComputerName';e={$_.__SERVER}},
         @{l='BIOSSerial';e={$_.SerialNumber}},
         @{l='OSBuild';e={
           gwmi -class win32_operatingsystem -comp $_.__SERVER |
           select-object -expand BuildNumber}
         } -autosize

    Invoke-Command -ScriptBlock { Get-CimInstance -ClassName win32_process } -ComputerName 172.20.123.220 -Credential 'DESKTOP-AAKT5OA\Jack Chen'

推荐阅读：《PowerShell and WMI》 by Richard Siddaway


*************************************************************************************************************************************

# 多任务 Multitasking with background jobs

## 异步执行

* 在后台执行命令时，若需要交互输入。看不到输入请求，命令执行将终止。
* 异步执行命令，无法直接看到错误信息
* 无法直接看到执行结果，需要取回

## Local job（本地后台任务）

    start-job -ScriptBlock { dir }

虽然本地job，在本地运行。但需要启用Remoting

某些命令具有-asjob 参数

    Get-Command -ParameterName asjob

    Get-WmiObject -Class win32_process -ComputerName 172.20.123.220 -Credential 'DESKTOP-AAKT5OA\Jack Chen' -AsJob

    Invoke-Command -ScriptBlock { Get-Process } -ComputerName 172.20.123.220 -Credential 'DESKTOP-AAKT5OA\Jack Chen' -AsJob

Invoke-Command与其它命令的不同之处在于。它所指定的命令，是在各远程计算机上并行执行的。这样，可以合理利用资源，提高性能。

## 获取任务结果

    # 所有Job
    Get-Job | Format-List

    # 获取结果
    Receive-Job -Id 18

获取结果，需要指定某个任务（或子任务）。并且，不可重复获取（可以使用-keep参数，持续保存结果）。
Get-Job中的HasMoreData属性，表明了是否存有结果。而ChildJobs属性，显示了子任务的个数（每个Top job 至少有一个）。

## Scheduled Jobs

延伸阅读[Scheduled Jobs 和 Scheduled Tasks](https://blogs.technet.microsoft.com/heyscriptingguy/2013/11/23/using-scheduled-tasks-and-scheduled-jobs-in-powershell/)

    Register-ScheduledJob -Name DailyProcList2 -ScriptBlock { Get-Process } -Trigger (New-JobTrigger -Daily -At 2am) -ScheduledJobOption (New-ScheduledJobOption -WakeToRun -RunElevated)

    Get-Command -Module psscheduledjob

ScheduledJob 会生成 普通的job

？？？ win10 ise中测试，不触发job


*******************************************************************************************************************************************************


# Working with many objects，one at a time

## “batch” cmdlets

    get-service | stop-service -passthru

cmdlet命令本身具有批处理功能，并且推荐使用这种方式处理mass management

## WMI 对象

    # 之前的调用方式
    Get-WmiObject -ClassName Win32_NetworkAdapterConfiguration | Invoke-WmiMethod -Name enabledhcp
    # 现在使用点语法调用
    (Get-WmiObject -ClassName Win32_NetworkAdapterConfiguration).enabledhcp()

统一使用Invoke-WmiMethod方法（或点语法）调用WMI对象的方法。

## 枚举对象

    commandA | foreach-object -process { ... }
    # % 代表 foreach-object
    gwmi win32_service -fi "name = 'BITS'" | % {$_.change($null,$null,$null,$null,$null,$null,$null,"P@ssw0rd") }

## 条条大路通罗马

    Get-Service -name *B* | Stop-Service
    Get-Service -name *B* | ForEach-Object { $_.Stop() }
    Get-WmiObject Win32_Service -filter "name LIKE '%B%' | Invoke-WmiMethod -name StopService
    Get-WmiObject Win32_Service -filter "name LIKE '%B%' | ForEach-Object { $_.StopService() }
    Stop-Service -name *B*


*****************************************************************************************************************************************************************


# 关于安全

自从比尔盖茨提出了 “Trustworthy Computing Initiative”， 每个产品组都会配置有一个软件安全专家。
powershell从设计开始，就考虑了安全性因素。

* powershell不处理任何permission，所有的执行权限，都依赖于运行powershell所使用的账户
* execution policy 和 code signing 防止用户因被骗，而运行破坏性指令
* 可以通过Set-ExecutionPolicy或GPO修改执行权限
* 脚本签名有效追踪来源，判断可靠性
* 脚本后缀为 .ps1 双击时，默认打开编辑器，不执行脚本，防止误操作
* shell中键入脚本文件名时，不执行脚本文件，防止 hijacking


*******************************************************************************************************************************************************************


# Variables

    $var = “Server1”

* 无需声明
* 弱类型
* ${My Variable} --变量名可包含空格
* 双引号解析 $var = 'hello'; Write-Host "${var} world"
* 转义符号为backtick(`)  $computerName = 'SERVER2'; Write-Host "`$computerName`ncontains`n$computername"

## deal with array   
    
    $computers = 'db1', 'db2'
    Write-Host $computers[0]
    Write-Host $computers[-1]

automatic unrolling 自动展开。对数组调用方法/属性时，若不存在该方法，则调用其数组元素的方法。

    $computers.toupper()

## subexpression

    $services = Get-Service
    out-host "The first name is $($services[0].name)"

双引号中可以解析变量，但若要调用方法，则需要用到subexpression  

$(...) 括号中的表达式会被当成脚本执行

## 声明变量类型

    PS C:\> $number = Read-Host "Enter a number"
    Enter a number: 100

    PS C:\> $number * 10
    100100100100100100100100100100

未指定类型时，输入被默认为字符串，未达到预期效果。

可以通过指定类型来解决上述问题：

    PS C:\> [int]$number = Read-Host "Enter a number"
    Enter a number: 100

    PS C:\> $number | gm


    TypeName:System.Int32

常用的类型有：

* [int]
* [single] or [double]
* [string]
* [char]
* [xml]
* [adsi]


************************************************************************************************************************


# Input and output

## read-host

用来获取用户输入，console host中若想给用户弹对话框，则用下面命令。

    [void][System.Reflection.Assembly]::LoadWithPartialName('Microsoft.VisualBasic')

    $computername = [Microsoft.VisualBasic.Interaction]::InputBox('Enter a computer name','Computer Name','localhost')

## write-host

因为其它命令的存在，这个命令很少用到。只是在某些特殊的场合，例如，用特别的颜色显示信息，来引起注意。

* Write-Debug
* Write-Error
* Write-Information
* Write-Verbose
* Write-Warning

这些命令是否输出到host是可以配置的，例如$DebugPreference控制是否输出调试信息。

## write-output

区别write-output 与 write-host：

    Write-Output 'Hello' | Where-Object { $PSItem.length -gt 10 }

    Write-Host 'Hello' | Where-Object { $PSItem.length -gt 10 }

write-output 是把对象输出到管道。而write-host是把对象输出到host的标准输出设备。

当你在powershell 命令行输入‘Hello’时，默认自动执行write-output命令，将对象输出到管道；进而执行out-default；最终执行out-host命令。


*******************************************************************************************************************************


# Session

通过Session，复用远程连接

    Get-PSSession -Name 'db1' | Enter-PSSession

