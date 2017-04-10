---
title: "Azure での Windows 用の仮想マシン拡張機能とその機能 | Microsoft Docs"
description: "Azure 仮想マシンに使用できる拡張機能について、提供または改善される内容ごとにまとめて説明します。"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/06/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: d7cb13d751bc9153669ce0d96b42fcda3024f6f0
ms.lasthandoff: 03/08/2017


---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Windows 用の仮想マシン拡張機能とその機能

Azure 仮想マシン拡張機能は、Azure Virtual Machines でのデプロイ後の構成と自動タスクを提供する複数の小さなアプリケーションです。 たとえば、仮想マシンでソフトウェアのインストール、ウイルス対策保護、または Docker 構成が必要な場合、VM 拡張機能を使って、これらのタスクを実行できます。 Azure VM 拡張機能は、Azure CLI、PowerShell、Azure Resource Manager テンプレート、Azure Portal を使って実行できます。 拡張機能は、新しい仮想マシン デプロイにバンドルすることも、既存の任意のシステムに対して実行することもできます。

このドキュメントでは、仮想マシン拡張機能の概要と仮想マシン拡張機能を使う場合の前提条件を示し、仮想マシン拡張機能を検出、管理、削除する方法についてのガイダンスを提供します。 構成がそれぞれ固有の VM 拡張機能が多数あるため、このドキュメントでは一般的な情報を示します。 拡張機能に固有の詳細情報については、それぞれの拡張機能のドキュメントをご覧ください。

## <a name="use-cases-and-samples"></a>ユース ケースとサンプル

多くの異なる Azure VM 拡張機能が使用可能ですが、そのユース ケースはそれぞれ異なります。 ユース ケースの例を次にいくつか示します。

- Windows 用の DSC 拡張機能を使って、仮想マシンに PowerShell Desired State Configuration を適用します。 詳しくは、[Azure Desired State Configuration 拡張機能](virtual-machines-windows-extensions-dsc-overview.md)に関する記事をご覧ください。
- Microsoft Monitoring Agent の VM 拡張機能を使って仮想マシンの監視を構成します。 詳しくは、[「Azure 仮想マシンを Log Analytics に接続する」](../log-analytics/log-analytics-azure-vm-extension.md)をご覧ください。
- Datadog 拡張機能を使って Azure インフラストラクチャの監視を構成します。 詳しくは、[Datadog のブログ](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)をご覧ください。
- Chef を使って、Azure 仮想マシンを構成します。 詳しくは、「[Chef で Azure 仮想マシンのデプロイメントを自動化する](virtual-machines-windows-chef-automation.md)」をご覧ください。

プロセス固有の拡張機能のほか、カスタム スクリプト拡張機能を Windows と Linux の両方の仮想マシンで使用できます。 Windows 用カスタム スクリプト拡張機能では、仮想マシンで実行する任意の PowerShell スクリプトを使用できます。 これは、ネイティブの Azure ツールが提供可能な構成以上の構成を必要とする Azure のデプロイを設計する場合に役立ちます。 詳しくは、[Windows VM のカスタム スクリプト拡張機能](virtual-machines-windows-extensions-customscript.md)に関する記事をご覧ください。

VM 拡張機能がエンドツーエンドのアプリケーションのデプロイで使われる例については、「[Azure Virtual Machines へのアプリケーションのデプロイを自動化する](virtual-machines-windows-dotnet-core-1-landing.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

仮想マシン拡張機能には、それぞれ独自の前提条件があります。 たとえば、Docker VM 拡張機能には、サポートされている Linux ディストリビューションの前提条件があります。 個々の拡張機能の要件については、その拡張機能のドキュメントで詳しく説明されています。

### <a name="azure-vm-agent"></a>Azure VM エージェント
Azure VM エージェントは、Azure 仮想マシンと Azure ファブリック コントローラーとの相互動作を管理します。 VM エージェントは、Azure Virtual Machines のデプロイと管理の多くの機能面を担当します (VM 拡張機能の実行など)。 Azure VM エージェントは、Azure Marketplace イメージにプレインストールされており、サポートされているオペレーティング システムにインストールすることができます。

サポートされているオペレーティング システムとインストール手順については、[Azure 仮想マシンのエージェント](virtual-machines-windows-agent-user-guide.md)に関する記事をご覧ください。

## <a name="discover-vm-extensions"></a>VM 拡張機能の検出
Azure Virtual Machines と共に、さまざまな VM 拡張機能を使用できます。 完全な一覧を表示するには、Azure Resource Manager の PowerShell モジュールを使って、次のコマンドを実行します。 このコマンドの実行時に、目的の場所を指定してください。

```powershell
Get-AzureRmVmImagePublisher -Location WestUS | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>VM 拡張機能の実行

Azure 仮想マシン拡張機能は既存の仮想マシンで実行できます。これは、構成の変更や、デプロイ済みの VM での接続の回復が必要な場合に便利です。 また、VM 拡張機能を Azure Resource Manager テンプレートのデプロイにバンドルすることもできます。 Resource Manager テンプレートで拡張機能を使うと、Azure Virtual Machines をデプロイし、デプロイ後の操作を必要とすることなく構成できます。

既存の仮想マシンに対して拡張機能を実行するには、次の方法を使用します。

### <a name="powershell"></a>PowerShell

個々の拡張機能を実行するための PowerShell コマンドがいくつか存在します。 一覧を表示するには、次の PowerShell コマンドを実行します。

```powershell
get-command Set-AzureRM*Extension* -Module AzureRM.Compute
```

次のような出力が表示されます。

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    2.2.0      AzureRM.Compute
```

次の例では、カスタム スクリプト拡張機能を使って、ターゲットの仮想マシン上に GitHub リポジトリからスクリプトをダウンロードし、スクリプトを実行します。 カスタム スクリプト拡張機能について詳しくは、[カスタム スクリプト拡張機能の概要](virtual-machines-windows-extensions-customscript.md)に関する記事をご覧ください。

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

この例では、VM アクセス拡張機能を使って、Windows 仮想マシンの管理パスワードをリセットします。 VM アクセス拡張機能について詳しくは、[Windows VM でのリモート デスクトップ サービスのリセット](virtual-machines-windows-reset-rdp.md)に関する記事をご覧ください。

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

`Set-AzureRmVMExtension` コマンドを使って、任意の VM 拡張機能を開始できます。 詳しくは、[Set-AzureRmVMExtension のリファレンス](https://msdn.microsoft.com/en-us/library/mt603745.aspx)に関する記事をご覧ください。


### <a name="azure-portal"></a>Azure Portal

VM 拡張機能は、Azure Portal から既存の仮想マシンに適用できます。 そのためには、使う仮想マシンを選び、**[拡張機能]** を選択して **[追加]** をクリックします。 これで、使用可能な拡張機能の一覧が表示されます。 必要な拡張機能を選び、ウィザードの手順に従います。

次のイメージは、Azure Portal からの Microsoft Antimalware 拡張機能のインストールを示しています。

![antimalware 拡張機能のインストール](./media/virtual-machines-windows-extensions-features/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure リソース マネージャーのテンプレート

VM 拡張機能を Azure Resource Manager テンプレートに追加し、テンプレートのデプロイを使って実行できます。 テンプレートを使った拡張機能のデプロイは、完全に構成された Azure デプロイの作成に役立ちます。 たとえば、次の JSON は、負荷分散された一連の仮想マシンと Azure SQL データベースをデプロイし、各 VM に .NET Core アプリケーションをインストールする Resource Manager テンプレートからの抜粋です。 VM 拡張機能はソフトウェアのインストールに対応します。

詳しくは、[Resource Manager テンプレート](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)全体をご覧ください。

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

詳しくは、「[Windows VM 拡張機能を使用した Azure Resource Manager テンプレートの作成](virtual-machines-windows-extensions-authoring-templates.md)」をご覧ください。

## <a name="secure-vm-extension-data"></a>VM 拡張機能のデータの保護

VM 拡張機能の実行時には、資格情報、ストレージ アカウント名、ストレージ アカウント アクセス キーなどの機密情報を含めなければならない場合があります。 多くの VM 拡張機能には、データを暗号化し、ターゲットの仮想マシン内でのみ暗号化を解除する保護された構成が含まれます。 拡張機能には、それぞれ固有の保護された構成スキーマがあります。スキーマについては、拡張機能のドキュメントで詳しく説明されています。

次の例は、Windows 用カスタム スクリプト拡張機能のインスタンスを示しています。 実行するコマンドに一連の資格情報が含まれていることにご注意ください。 この例では、実行するコマンドは暗号化されません。


```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

**command to execute** プロパティを **protected** 構成に移動すると、実行文字列が保護されます。

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

## <a name="troubleshoot-vm-extensions"></a>VM 拡張機能のトラブルシューティング

各 VM 拡張機能には、固有のトラブルシューティング手順が存在する場合があります。 たとえば、カスタム スクリプト拡張機能を使う場合、スクリプト実行の詳細は、拡張機能が実行された仮想マシンのローカルに保存されます。 拡張機能に固有のトラブルシューティング手順は、拡張機能のドキュメントで詳しく説明されています。

次のトラブルシューティング手順は、すべての仮想マシン拡張機能に適用されます。

### <a name="view-extension-status"></a>拡張機能の状態表示

仮想マシンに対して仮想マシン拡張機能を実行した後で、次の PowerShell CLI コマンドを使って、拡張機能の状態を返します。 次のコマンドでは、パラメーター名を独自の値に置き換えてください。 `Name` パラメーターは、実行時に拡張機能に指定された名前になります。

```PowerShell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

出力は次のようになります。

```json
ResourceGroupName       : myResourceGroup
VMName                  : myVM
Name                    : myExtensionName
Location                : westus
Etag                    : null
Publisher               : Microsoft.Azure.Extensions
ExtensionType           : DockerExtension
TypeHandlerVersion      : 1.0
Id                      : /subscriptions/mySubscriptionIS/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/myExtensionName
PublicSettings          :
ProtectedSettings       :
ProvisioningState       : Succeeded
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : False
ForceUpdateTag          :
```

拡張機能の実行の状態は、Azure Portal で確認することもできます。 拡張機能の状態を表示するには、仮想マシンを選び、**[拡張機能]** を選択して目的の拡張機能を選択します。

### <a name="rerun-vm-extensions"></a>VM 拡張機能の再実行

仮想マシン拡張機能の再実行が必要な場合があります。 そのためには、拡張機能を削除し、任意の実行方法で拡張機能を再実行します。 拡張機能を削除するには、Azure PowerShell モジュールを使って次のコマンドを実行します。 次のコマンドでは、パラメーター名を独自の値に置き換えてください。

```powershell
Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Azure Portalを使って拡張機能を削除することもできます。 そのためには、次の手順を実行します。

1. 仮想マシンを選びます。
2. **[拡張機能]** を選択します。
3. 目的の拡張機能を選びます。
4. **[アンインストール]** を選択します。

## <a name="common-vm-extensions-reference"></a>一般的な VM 拡張機能のリファレンス
| 拡張機能の名前 | 説明 | 詳細情報 |
| --- | --- | --- |
| Windows でのカスタムのスクリプト拡張機能 |Azure 仮想マシンに対してスクリプトを実行します |[Windows でのカスタムのスクリプト拡張機能](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Windows での DSC 拡張機能 |PowerShell DSC (必要な状態の構成) 拡張機能 |[Windows での DSC 拡張機能](virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure 診断拡張機能 |Azure 診断を管理します |[Azure 診断拡張機能](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM アクセス拡張機能 |ユーザーと資格情報を管理します |[Linux 用 VM アクセス拡張機能](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

