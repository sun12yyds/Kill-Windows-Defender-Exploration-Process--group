# Kill Windows Defender Exploration Process group文章合集
基于前一仓库的合计，具体链接🔗：![GO](https://github.com/sun12yyds/Kill-Windows-Defender-Exploration-Process)
# 文章合集🎲：
1.Chapter 1
```
禁用实时保护（保存powwershell运行）
Set-MpPreference -DisableRealtimeMonitoring $true
```
2.Chapter 2
```
基于第一章的研究发现的新成果：
发现了以下东西---
1.禁用 Windows Defender 服务
2.禁用 Windows Defender 通过组策略
3.禁用 Windows Defender 通过注册表
4.注意：均是用了：powershell

代码：
```
# 以管理员身份运行此脚本

# 禁用实时保护
Set-MpPreference -DisableRealtimeMonitoring $true

# 禁用 Windows Defender 服务
Set-Service -Name WinDefend -StartupType Disabled

# 禁用 Windows Defender 通过组策略
```
$registryPath = "HKLM:\SOFTWARE\Policies\Microsoft\Windows Defender"
$name = "DisableAntiSpyware"
$value = 1

if (-not (Test-Path $registryPath)) {
    New-Item -Path $registryPath -Force | Out-Null
}

New-ItemProperty -Path $registryPath -Name $name -Value $value -PropertyType DWORD -Force | Out-Null

# 禁用 Windows Defender 通过注册表
$registryPath = "HKLM:\SOFTWARE\Microsoft\Windows Defender"
$name = "DisableAntiSpyware"
$value = 1

if (-not (Test-Path $registryPath)) {
    New-Item -Path $registryPath -Force | Out-Null
}

New-ItemProperty -Path $registryPath -Name $name -Value $value -PropertyType DWORD -Force | Out-Null
```

```
