---
title: 在 Windows 上安装 PowerShell
description: 介绍如何在 Windows 上安装 PowerShell
ms.date: 08/06/2018
ms.openlocfilehash: ea5432725f4baea8c688fb8e67482910e2c3981e
ms.sourcegitcommit: b6cf10224eb9f32919a505cdffbe5968241c18a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80374897"
---
# <a name="installing-powershell-on-windows"></a>在 Windows 上安装 PowerShell

有多种方法可以在 Windows 中安装 PowerShell。

## <a name="prerequisites"></a>先决条件

Windows 7 SP1、Server 2008 R2 及更高版本支持最新版 PowerShell。

若要通过 WSMan 启用 PowerShell 远程处理，需要满足以下先决条件：

- 在低于 Windows 10 的 Windows 版本上安装[通用 C 运行时](https://www.microsoft.com/download/details.aspx?id=50410)。 可以通过直接下载或 Windows 更新来获取它。 完全修补的系统已安装此包。
- 在 Windows 7 和 Windows Server 2008 R2 上安装 Windows Management Framework (WMF) 4.0 或更高版本。 有关 WMF 的详细信息，请参阅 [WMF 概述](/powershell/scripting/wmf/overview)。

## <a name="download-the-installer-package"></a>下载安装程序包

若要在 Windows 上安装 PowerShell，请从 GitHub[“发布”][releases]页下载安装包。 向下滚动到“版本”页的“资产”  部分。 由于“资产”  部分可能处于折叠状态，因此可能需要单击展开它。

## <a name="installing-the-msi-package"></a><a id="msi" />安装 MSI 包

MSI 文件如下所示：`PowerShell-<version>-win-<os-arch>.msi`。 例如：

- `PowerShell-7.0.0-win-x64.msi`
- `PowerShell-7.0.0-win-x86.msi`

下载后，双击安装程序并按照提示进行操作。

安装程序在 Windows“开始”菜单中创建一个快捷方式。

- 默认情况下，包安装位置为 `$env:ProgramFiles\PowerShell\<version>`
- 可以通过“开始”菜单或 `$env:ProgramFiles\PowerShell\<version>\pwsh.exe` 启动 PowerShell

> [!NOTE]
> PowerShell 7 安装到新目录，并与 Windows PowerShell 5.1 并行运行。 对于 PowerShell Core 6.x，PowerShell 7 是删除 PowerShell Core 6.x 的就地升级。
>
> - PowerShell 7 安装到 `$env:ProgramFiles\PowerShell\7`
> - `$env:ProgramFiles\PowerShell\7` 文件夹已添加到 `$env:PATH`
> - `$env:ProgramFiles\PowerShell\6` 文件夹已删除
>
> 如果需要与 PowerShell 7 并行运行 PowerShell 6，请使用 [ZIP 安装](#zip)方法重新安装 PowerShell 6。

### <a name="administrative-install-from-the-command-line"></a>通过命令行进行管理安装

可以通过命令行安装 MSI 包，这样管理员能够在没有用户交互的情况下部署包。 MSI 包中有下列控制安装选项的属性：

- **ADD_EXPLORER_CONTEXT_MENU_OPENPOWERSHELL** - 此属性控制向 Windows 资源管理器中的上下文菜单添加“打开 PowerShell”  项的选项。
- **ENABLE_PSREMOTING** - 此属性控制用于在安装过程中启用 PowerShell 远程处理的选项。
- **REGISTER_MANIFEST** - 此属性控制用于注册 Windows 事件日志记录清单的选项。

下面的示例展示了如何在启用所有安装选项的情况下无提示安装 PowerShell。

```powershell
msiexec.exe /package PowerShell-7.0.0-win-x64.msi /quiet ADD_EXPLORER_CONTEXT_MENU_OPENPOWERSHELL=1 ENABLE_PSREMOTING=1 REGISTER_MANIFEST=1
```

有关 `Msiexec.exe` 命令行选项的完整列表，请参阅[命令行选项](/windows/desktop/Msi/command-line-options)。

## <a name="installing-the-msix-package"></a><a id="msix" />安装 MSIX 包

要在 Windows 10 客户端上手动安装 MSIX 包，请从 GitHub [版本][releases]页面下载 MSIX 包。 向下滚动到要安装的版本的“资产”部分。  “资产”部分可能处于折叠状态，因此可能需要单击使其展开。

MSIX 文件类似于 - `PowerShell-<version>-win-<os-arch>.msix`

必须使用 `Add-AppxPackage` cmdlet，才能安装此包。

```powershell
Add-AppxPackage PowerShell-<version>-win-<os-arch>.msix
```

> [!NOTE]
> MSIX 包尚未发布。 发布后，此包将位于 Microsoft Store 和 GitHub[“发布”][releases]页中。

## <a name="installing-the-zip-package"></a><a id="zip" />安装 ZIP 包

提供有 PowerShell 二进制 ZIP 存档，从而支持高级部署方案。 安装 ZIP 存档不会像 MSI 包一样检查先决条件。 为了让使用 WSMan 的远程处理能够正常运行，请确保已满足[先决条件](#prerequisites)。

## <a name="deploying-on-windows-iot"></a>在 Windows IoT 上部署

Windows IoT 随附 Windows PowerShell，可用来部署 PowerShell 7。

1. 在目标设备中创建 `PSSession`

   ```powershell
   Set-Item -Path WSMan:\localhost\Client\TrustedHosts <deviceip>
   $S = New-PSSession -ComputerName <deviceIp> -Credential Administrator
   ```

2. 将 ZIP 包复制到设备

   ```powershell
   # change the destination to however you had partitioned it with sufficient
   # space for the zip and the unzipped contents
   # the path should be local to the device
   Copy-Item .\PowerShell-<version>-win-<os-arch>.zip -Destination u:\users\administrator\Downloads -ToSession $s
   ```

3. 连接到设备并展开存档

   ```powershell
   Enter-PSSession $s
   Set-Location u:\users\administrator\downloads
   Expand-Archive .\PowerShell-<version>-win-<os-arch>.zip
   ```

4. 设置 PowerShell 7 远程处理

   ```powershell
   Set-Location .\PowerShell-<version>-win-<os-arch>
   # Be sure to use the -PowerShellHome parameter otherwise it'll try to create a new
   # endpoint with Windows PowerShell 5.1
   .\Install-PowerShellRemoting.ps1 -PowerShellHome .
   # You'll get an error message and will be disconnected from the device because it has to restart WinRM
   ```

5. 连接到设备上的 PowerShell 7 终结点

   ```powershell
   # Be sure to use the -Configuration parameter.  If you omit it, you will connect to Windows PowerShell 5.1
   Enter-PSSession -ComputerName <deviceIp> -Credential Administrator -Configuration powershell.<version>
   ```

## <a name="deploying-on-nano-server"></a>在 Nano Server 上进行部署

为了更好地理解这些说明，假定 Nano Server 是已运行 PowerShell 版本的“无外设”操作系统。 有关详细信息，请参阅 [Nano Server 映像生成器](/windows-server/get-started/deploy-nano-server)文档。

可以使用两种不同的方法来部署 PowerShell 二进制文件。

1. 脱机 - 安装 Nano Server VHD，并将 zip 文件的内容解压到安装映像中的所选位置。
2. 联机 - 通过 PowerShell 会话传输 zip 文件，并在所需位置中将其解压。

在这两种情况下，都需要 Windows 10 x64 ZIP 版本包。 在 PowerShell 的“管理员”实例中运行命令。

### <a name="offline-deployment-of-powershell"></a>PowerShell 脱机部署

1. 使用常用 zip 实用工具将包解压到已安装的 Nano Server 映像中的目录。
2. 卸载映像并启动。
3. 连接到 Windows PowerShell 的收件箱实例。
4. 按照说明使用[“另一种实例技术”](../learn/remoting/wsman-remoting-in-powershell-core.md#executed-by-another-instance-of-powershell-on-behalf-of-the-instance-that-it-will-register)创建远程处理终结点。

### <a name="online-deployment-of-powershell"></a>PowerShell 联机部署

若要将 PowerShell 部署到 Nano Server，请按照以下步骤操作。

- 连接到 Windows PowerShell 的收件箱实例

  ```powershell
  $session = New-PSSession -ComputerName <Nano Server IP address> -Credential <An Administrator account on the system>
  ```

- 将文件复制到 Nano Server 实例

  ```powershell
  Copy-Item <local PS Core download location>\powershell-<version>-win-x64.zip c:\ -ToSession $session
  ```

- 输入会话

  ```powershell
  Enter-PSSession $session
  ```

- 提取 ZIP 文件

  ```powershell
  # Insert the appropriate version.
  Expand-Archive -Path C:\powershell-<version>-win-x64.zip -DestinationPath "C:\PowerShell_<version>"
  ```

- 如果需要基于 WSMan 的远程处理，请按照说明使用[“另一种实例技术”](../learn/remoting/WSMan-Remoting-in-PowerShell-Core.md#executed-by-another-instance-of-powershell-on-behalf-of-the-instance-that-it-will-register)创建远程处理终结点。

## <a name="install-as-a-net-global-tool"></a>作为 .NET 全局工具安装

如果你已安装 [.NET Core SDK](/dotnet/core/sdk)，则可以轻松地安装 PowerShell 作为 [.NET 全局工具](/dotnet/core/tools/global-tools)。

```
dotnet tool install --global PowerShell
```

dotnet 工具安装程序将 `$env:USERPROFILE\dotnet\tools` 添加到 `$env:PATH` 环境变量中。 不过，当前运行的 shell 没有更新后的 `$env:PATH`。 若要从新 shell 启动 PowerShell，可以键入“`pwsh`”。

## <a name="how-to-create-a-remoting-endpoint"></a>如何创建远程处理终结点

PowerShell 同时支持采用 WSMan 和 SSH 的 PowerShell 远程处理协议 (PSRP)。 有关详细信息，请参阅：

- [在 PowerShell Core 中进行 SSH 远程处理][ssh-remoting]
- [在 PowerShell Core 中进行 WSMan 远程处理][wsman-remoting]

<!-- [download-center]: TODO -->

[releases]: https://github.com/PowerShell/PowerShell/releases
[ssh-remoting]: ../learn/remoting/SSH-Remoting-in-PowerShell-Core.md
[wsman-remoting]: ../learn/remoting/WSMan-Remoting-in-PowerShell-Core.md
[AppVeyor]: https://ci.appveyor.com/project/PowerShell/powershell
