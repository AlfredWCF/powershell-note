# powershell-note
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
windows Explorer 操作系统，是图形界面shell

## Batch. KiXtart. VBScript. Powershell 并不是第一次自动化方面的尝试。

试想，如果使用GUI工具，添加域账号的场景。添加一个用户需要5分钟，添加100个用户呢？管理员表示压力山大。
powershell之前，可以使用VBScript编写脚本，通过脚本导入编辑好的用户资料（CSV文件），快速创建用户。

那么问题来了，VBScript功能够用，何必开发powershell？
  原因在于，微软各种工具的开发者，需要提前为VBScript预留访问点，用来hook up
  这个问题，被powershell的总架构师effrey Snover称作“最后一公里”

powershell是如何解决这最后一公里的？
  微软继续构建GUI控制台，其所有的功能的调用，改为执行powershell命令。
  the Windows world will start to split into two groups:administrators who can use PowerShell，and those who can’t.

  