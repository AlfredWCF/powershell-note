<https://github.com/devops-collective-inc/secrets-of-powershell-remoting/blob/master/SUMMARY.md>

# Working with Endpoints #

In PowerShell terminology, each endpoint is a session configuration, or just a configuration. Each can be configured to offer specific services and capabilities, as well as having specific restrictions and limitations.

## Creating a Custom Endpoint ##

可以利用自定义的配置，完成一下功能：
* 自动加载 scripts 和 modules
* 决定谁可以使用链接该配置
* 使用不同的凭证运行脚本，而不是使用该remoting发起者的 -- [the second hop](https://github.com/devops-collective-inc/secrets-of-powershell-remoting/blob/master/manuscript/accessing-remote-computers.md#the-second-hop)
* 限制remoting发起者使用某些命令

    New-PSSessionConfigurationFile -ModulesToImport molitools -VisibleCmdlets 'Edit-MoliLiveImage' -LanguageMode NoLanguage -SessionType RestrictedRemoteServer  -Path c:\MoliToolsSession.pssc
    
    Register-PSSessionConfiguration -Path C:\MoliToolsSession.pssc -RunAsCredential HENG\wangcf -ShowSecurityDescriptorUI -Name MoliTools

    Enter-PSSession -ComputerName localhost -ConfigurationName MoliTools

