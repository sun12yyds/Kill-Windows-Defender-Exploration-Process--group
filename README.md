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

# 禁用实时保护
```
Set-MpPreference -DisableRealtimeMonitoring $true
```

# 禁用 Windows Defender 服务
```
Set-Service -Name WinDefend -StartupType Disabled
```

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
3.Chapter 3
# 现在进行关闭操作（同样方法写出了第一个bat脚本）：
```
@echo off
:: 以管理员身份运行此脚本
:: 检查是否以管理员身份运行
net session >nul 2>&1
if %errorLevel% == 0 (
    echo 正在禁用 Windows Defender 实时保护...
) else (
    echo 请以管理员身份运行此脚本！
    pause
    exit /b
)

:: 禁用实时保护
powershell -Command "Set-MpPreference -DisableRealtimeMonitoring $true"

:: 禁用 Windows Defender 服务
sc config WinDefend start= disabled
sc stop WinDefend

:: 通过注册表禁用 Windows Defender
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v DisableAntiSpyware /t REG_DWORD /d 1 /f

:: 禁用 Windows Defender 实时保护注册表项
reg add "HKLM\SOFTWARE\Microsoft\Windows Defender\Real-Time Protection" /v DisableRealtimeMonitoring /t REG_DWORD /d 1 /f

echo Windows Defender 实时保护已永久禁用。
pause
```

# 所对应的恢复方法：
```
@echo off
:: 以管理员身份运行此脚本
:: 检查是否以管理员身份运行
net session >nul 2>&1
if %errorLevel% == 0 (
    echo 正在启用 Windows Defender 实时保护...
) else (
    echo 请以管理员身份运行此脚本！
    pause
    exit /b
)

:: 启用实时保护
powershell -Command "Set-MpPreference -DisableRealtimeMonitoring $false"

:: 启用 Windows Defender 服务
sc config WinDefend start= auto
sc start WinDefend

:: 删除注册表中的禁用项
reg delete "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v DisableAntiSpyware /f
reg delete "HKLM\SOFTWARE\Microsoft\Windows Defender\Real-Time Protection" /v DisableRealtimeMonitoring /f

echo Windows Defender 实时保护已重新启用。
pause
```

# 单局限性极大！
可查看下一张节
