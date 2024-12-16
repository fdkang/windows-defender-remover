# ❌️ Windows Defender 移除/禁用工具

<a href="https://github.com/ionuttbara/windows-defender-remover">
    <picture>
        <source media="(prefers-color-scheme: dark)" srcset="https://github.com/drunkwinter/windows-defender-remover/assets/38593134/8072a566-5bf0-4f05-9994-808145406bdc">
        <img alt="Defender Remover" src="https://user-images.githubusercontent.com/79479952/239704528-c017473e-1d2a-4d4a-a215-bf71d137b86a.png">
    </picture>
</a>

## ❓️ 这个应用程序是做什么的？

该应用程序可以移除/禁用 Windows Defender，包括 Windows 安全中心应用、Windows 基于虚拟化的安全性(VBS)、Windows SmartScreen、Windows 安全服务、Windows 网络威胁服务、Windows 文件虚拟化(UAC)、Microsoft Defender 应用防护、Microsoft 驱动程序阻止列表、系统缓解以及 Windows 10 或更高版本中设置应用中的 Windows Defender 页面。

## ❓️ 会移除哪些组件？

### 移除安全组件
    此脚本会移除/禁用以下安全组件：
        - Windows 安全中心支持，包括 Windows 安全中心服务(wscsvc)、Windows 安全服务(SgrmBroker, Sgrm Drivers)，这些是运行 Windows 安全应用所需的。
        - 虚拟化支持
            - Hypervisor 启动(这可以修复基于虚拟化的安全性禁用问题，如果使用 Hyper-V 和/或 WSL(Windows Linux 子系统)、WSA(Windows Android 子系统)会自动启用)
            - LUA(禁用文件虚拟化和用户账户控制，这将使所有应用以管理员权限运行(同时修复旧应用错误))
            - Exploit Guard(漏洞防护)
            - Windows Smart Control
            - 篡改保护(适用于 Windows 11 21H2 或更早版本)
        - SecHealthUI (Windows 安全 UWP 应用)
        - SmartScreen
        - Pluton 支持和 Pluton 服务支持
        - 系统缓解
          - "服务缓解"(在 admx.help 上搜索更多信息，这是一项策略)
          - Spectre 和 Meltdown 缓解(可在旧 Intel CPU 上获得 30% 的性能提升)
        - 设置应用中的 Windows 安全部分

### 移除防病毒组件
    此脚本会强制移除以下防病毒组件：
      - Windows Defender 定义更新列表(这将禁用 Defender 的定义更新，因为它已被移除)
      - Windows Defender SpyNet 遥测
      - 防病毒服务
      - Windows Defender 防病毒过滤器和 Windows Defender rootkit 扫描驱动程序
      - 防病毒扫描任务
      - Shell 关联(右键菜单)
      - 在 Windows 安全应用中隐藏防病毒保护部分

## 📃 使用说明

> [!注意]
> 如果您不确定自己在做什么，建议在运行脚本前创建系统还原点。

1. 从 [Releases](https://github.com/ionuttbara/windows-defender-remover/releases) 下载打包好的脚本
2. 以管理员身份运行 ".exe" 文件
3. 按照显示的说明操作

或者

您可以使用 git

```
git clone https://github.com/ionuttbara/windows-defender-remover.git
cd windows-defender-remover
Script_Run.bat
```

或者

您可以下载完整源代码
1. 从 [Releases](https://github.com/jbara2002/windows-defender-remover/releases) 下载源代码。
2. 从最新版本中选择 **Source Code(.zip)** 文件并下载。
3. 将文件解压到文件夹中并运行 Script_Run.bat。

![cli](https://github.com/drunkwinter/windows-defender-remover/assets/38593134/46007191-0a65-43c2-b451-a993ff90e00e)

如果遇到任何问题，您可以提交 [issue](https://github.com/ionuttbara/windows-defender-remover/issues)。

## 📃 脚本自动化

您可以使用参数移除 Defender。

#### 移除

```PowerShell
# 移除
Defender.Remover.exe /r <# 或 /R #>
```

## 禁用或移除 Windows Defender *应用防护策略*（高级）

如果您在打开应用程序时遇到任何问题（*极少见*）并收到"由于 Device Guard 应用程序无法运行"或"Windows Defender 应用防护已阻止此应用"的消息，您需要从不同位置删除 4 个同名文件。

- 在 EFI 分区中

```PowerShell
Remove-Item -LiteralPath "$((Get-Partition | ? IsSystem).AccessPaths[0])Microsoft\Boot\WiSiPolicy.p7b"
```

- 在代码完整性文件夹中

```PowerShell
Remove-Item -LiteralPath "$env:windir\System32\CodeIntegrity\WiSiPolicy.p7b"
```

- 在 Windows 文件夹中

```PowerShell
Remove-Item -LiteralPath "$env:windir\Boot\EFI\wisipolicy.p7b"
```

- 在 WinSxS 文件夹中

```PowerShell
Remove-Item -Path "$env:windir\WinSxS" -Include *winsipolicy.p7b* -Recurse
```

## 创建禁用 Windows Defender 和服务的 ISO

您可以创建一个已禁用 Windows Defender 和安全服务的 ISO。这很简单，以下是具体步骤：
1. 挂载 ISO 并将其提取到指定位置。
2. 打开 **sources** 文件夹并创建 **$OEM$** 文件夹（这是在 OOBE 中运行 DefenderRemover 所必需的）。
3. 打开 **$OEM$** 文件夹并创建名为 **$$** 的文件夹。
4. 打开 **$$** 文件夹并创建名为 **Panther** 的文件夹。
5. 打开 **Panther** 文件夹。
   路径应该如下所示：
    **%提取的 ISO 位置%\sources\$OEM$\$$\Panther\**
6. 从仓库的 ISO_Maker 文件夹下载 unnatended.xml 文件并将其放入 Panther 文件夹。
7. 将其保存为可启动 ISO（目前脚本无法自动执行此操作，但下一版本将支持）。

## ❓ 常见问题

#### ⭕ 如何在不下载脚本的情况下从 PC 中移除 Windows 安全中心/Windows 安全应用？
将此代码粘贴到 PowerShell 文件中，然后**以管理员身份运行**。
```
$remove_appx = @("SecHealthUI"); $provisioned = get-appxprovisionedpackage -online; $appxpackage = get-appxpackage -allusers; $eol = @()
$store = 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Appx\AppxAllUserStore'
$users = @('S-1-5-18'); if (test-path $store) {$users += $((dir $store -ea 0 |where {$_ -like '*S-1-5-21*'}).PSChildName)}
foreach ($choice in $remove_appx) { if ('' -eq $choice.Trim()) {continue}
  foreach ($appx in $($provisioned |where {$_.PackageName -like "*$choice*"})) {
    $next = !1; foreach ($no in $skip) {if ($appx.PackageName -like "*$no*") {$next = !0}} ; if ($next) {continue}
    $PackageName = $appx.PackageName; $PackageFamilyName = ($appxpackage |where {$_.Name -eq $appx.DisplayName}).PackageFamilyName 
    ni "$store\Deprovisioned\$PackageFamilyName" -force >''; $PackageFamilyName  
    foreach ($sid in $users) {ni "$store\EndOfLife\$sid\$PackageName" -force >''} ; $eol += $PackageName
    dism /online /set-nonremovableapppolicy /packagefamily:$PackageFamilyName /nonremovable:0 >''
    remove-appxprovisionedpackage -packagename $PackageName -online -allusers >''
  }
  foreach ($appx in $($appxpackage |where {$_.PackageFullName -like "*$choice*"})) {
    $next = !1; foreach ($no in $skip) {if ($appx.PackageFullName -like "*$no*") {$next = !0}} ; if ($next) {continue}
    $PackageFullName = $appx.PackageFullName; 
    ni "$store\Deprovisioned\$appx.PackageFamilyName" -force >''; $PackageFullName
    foreach ($sid in $users) {ni "$store\EndOfLife\$sid\$PackageFullName" -force >''} ; $eol += $PackageFullName
    dism /online /set-nonremovableapppolicy /packagefamily:$PackageFamilyName /nonremovable:0 >''
    remove-appxpackage -package $PackageFullName -allusers >''
  }
}
```

#### ⭕ 为什么下载的可执行文件被标记为病毒？

这是误报。

由于创建 ".exe" 文件的方式，一些安全应用会将此应用标记为病毒。通过 **git** 或源代码 .zip 下载将不会被标记为病毒。
从 Defender 12.6.x 开始，某些版本被视为病毒，某些版本则不是（这是我的一个 bug，请不要为此提交问题）。

#### ⭕ 为什么在 Windows 更新后补丁不起作用？

Windows 更新包含一个 ```Intelligence Update```，它会阻止某些操作并修改 Windows Defender/安全策略。
如果脚本对您不起作用，请检查是否安装了 Windows 安全智能更新。如果已安装，请禁用篡改保护，然后重新运行脚本。

#### ⭕ 如何在不从发布版下载可执行文件的情况下使用包移除器？

通过将所需的 ".bat" 文件拖到 PowerRun 可执行文件上，从 cmd 运行它。您必须重启才能使更改生效。

#### ⭕ 如果移除脚本不起作用，如何禁用 VBS

使用此命令禁用并重启。

```
bcdedit /set hypervisorlaunchtype off
```
