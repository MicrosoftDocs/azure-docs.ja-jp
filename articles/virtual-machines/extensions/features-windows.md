---
title: Windows 用の Azure VM 拡張機能とその機能
description: Azure 仮想マシンに使用できる拡張機能について、提供または改善される内容ごとにまとめて説明します。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/30/2018
ms.openlocfilehash: b9b10e2ed58a41cee99e7e6ef1c3994035460407
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601862"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Windows 用の仮想マシン拡張機能とその機能

Azure 仮想マシン (VM) 拡張機能は、Azure VM でのデプロイ後の構成と自動タスクを提供する複数の小さなアプリケーションです。 たとえば、仮想マシンでソフトウェアのインストールやウイルス対策保護が必要な場合、あるいは、仮想マシン内でスクリプトを実行するために、VM 拡張機能を使用できます。 Azure VM 拡張機能は、Azure CLI、PowerShell、Azure Resource Manager テンプレート、および Azure Portal を使って実行できます。 拡張機能は、新しい VM の展開にバンドルすることも、既存の任意のシステムに対して実行することもできます。

この記事では、VM 拡張機能の概要と Azure VM 拡張機能を使用する場合の前提条件を示し、VM 拡張機能を検出、管理、および削除する方法についてのガイダンスを提供します。 構成がそれぞれ固有の VM 拡張機能が多数あるため、この記事では一般的な情報を示します。 拡張機能に固有の詳細情報については、それぞれの拡張機能のドキュメントをご覧ください。

 

## <a name="use-cases-and-samples"></a>ユース ケースとサンプル

さまざまな Azure VM 拡張機能が存在しますが、そのユース ケースはそれぞれ異なります。 次に例をいくつか示します。

- Windows 用の DSC 拡張機能を使って、VM に PowerShell Desired State Configuration を適用します。 詳細については、「[Azure Desired State configuration extension](dsc-overview.md)」(Azure Desired State Configuration 拡張機能) を参照してください。
- Log Analytics エージェント VM 拡張機能を使用して VM の監視を構成します。 詳しくは、[Azure VM の Azure Monitor ログへの接続](../../azure-monitor/vm/quick-collect-azurevm.md)に関するページを参照してください。
- Chef を使用して Azure VM を構成します。 詳しくは、「[Chef で Azure VM の展開を自動化する](/azure/developer/chef/windows-vm-configure)」を参照してください。
- Datadog 拡張機能を使って Azure インフラストラクチャの監視を構成します。 詳細については、[Datadog のブログ](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)を参照してください。


プロセス固有の拡張機能のほか、カスタム スクリプト拡張機能を Windows と Linux の両方の仮想マシンで使用できます。 Windows 用カスタム スクリプト拡張機能では、VM で実行する任意の PowerShell スクリプトを使用できます。 カスタム スクリプトは、ネイティブの Azure ツールが提供可能な構成以上の構成を必要とする Azure のデプロイを設計する場合に役立ちます。 詳しくは、[Windows VM のカスタム スクリプト拡張機能](custom-script-windows.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

VM 上で拡張機能を処理するには、Azure Windows エージェントがインストールされている必要があります。 一部の個々の拡張機能には、リソースまたは依存関係へのアクセスなどの前提条件があります。

### <a name="azure-vm-agent"></a>Azure VM エージェント

Azure VM エージェントは、Azure VM と Azure ファブリック コントローラーとの相互動作を管理します。 VM エージェントは、Azure VM のデプロイと管理の多くの機能面を担当します (VM 拡張機能の実行など)。 Azure VM エージェントは、Azure Marketplace イメージにプレインストールされており、サポートされているオペレーティング システムに手動でインストールすることができます。 Windows 用 の Azure VM エージェントは、Windows ゲスト エージェントと呼ばれます。

サポートされているオペレーティング システムおよびインストール手順については、「[仮想マシンのエージェントおよび拡張機能について](agent-windows.md)」を参照してください。

#### <a name="supported-agent-versions"></a>サポートされているエージェントのバージョン

最適なエクスペリエンスを提供するために、エージェントの最小バージョンが用意されています。 詳細については、 [こちらの記事](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)を参照してください。

#### <a name="supported-oses"></a>サポート対象の OS

Windows ゲスト エージェントは複数の OS で実行されますが、拡張機能フレームワークでは拡張を行う OS に対して制限があります。 詳細については、 [こちらの記事](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems
)を参照してください。

一部の拡張機能は、すべての OS ではサポートされず、"*エラー コード 51、'OS がサポートされていません'* " が出力される場合があります。 サポートの可否については、個々の拡張機能のドキュメントを確認してください。

#### <a name="network-access"></a>ネットワーク アクセス

拡張機能パッケージは、Azure Storage 拡張機能リポジトリからダウンロードされ、拡張機能ステータスのアップロードが Azure Storage に転記されます。 [サポートされている](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)バージョンのエージェントを使用する場合は、エージェントを使用してエージェント通信用の Azure ファブリック コントローラーに通信をリダイレクトできるので、VM リージョン内の Azure Storage へのアクセスを許可する必要はありません (プライベート IP [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) での特権チャネル経由の HostGAPlugin 機能)。 サポートされていないバージョンのエージェントを使用する場合は、VM からそのリージョン内の Azure Storage への送信アクセスを許可する必要があります。

> [!IMPORTANT]
> ゲスト ファイアウォールまたはプロキシを使用して *168.63.129.16* へのアクセスをブロックした場合、上記とは関係なく、拡張機能はエラーになります。 ポート 80、443、32526 が必要です。

エージェントは、拡張機能パッケージおよびレポート ステータスをダウンロードするためだけに使用できます。 たとえば、拡張機能のインストール時に GitHub からスクリプトをダウンロードする必要がある場合 (カスタム スクリプト)、または Azure Storage へのアクセスが必要な場合 (Azure Backup) は、追加のファイアウォール/ネットワーク セキュリティ グループ ポートが開かれている必要があります。 拡張機能はそれぞれ、独自のアプリケーションになっているため、要件も異なります。 Azure Storage または Azure Active Directory へのアクセスを必要とする拡張機能の場合は、Storage または AzureActiveDirectory の [Azure NSG サービス タグ](../../virtual-network/network-security-groups-overview.md#service-tags)を使用してアクセスを許可できます。

Windows ゲスト エージェントには、エージェントのトラフィック要求をリダイレクトするためのプロキシ サーバーのサポートはありません。つまり、Windows ゲスト エージェントでは、カスタムプロキシに依存して (ある場合)、IP 168.63.129.16 経由でインターネット上またはホスト上のリソースにアクセスできます。

## <a name="discover-vm-extensions"></a>VM 拡張機能の検出

Azure VM と共に、多くのさまざまな VM 拡張機能を使用できます。 完全な一覧を表示するには、[Get-AzVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage) を使用します。 次の例では、*WestUS* の場所で利用できるすべての拡張機能が表示されています。

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>VM 拡張機能の実行

Azure VM 拡張機能は既存の VM で実行できます。これは、構成の変更や、デプロイ済みの VM での接続の回復が必要な場合に便利です。 また、VM 拡張機能を Azure Resource Manager テンプレートのデプロイにバンドルすることもできます。 Resource Manager テンプレートで拡張機能を使用すると、Azure VM をデプロイし、デプロイ後の操作を行わずに構成できます。

既存の VM に対して拡張機能を実行するには、次の方法を使用します。

### <a name="powershell"></a>PowerShell

個々の拡張機能を実行するための PowerShell コマンドがいくつか存在します。 一覧を表示するには、[Get-command](/powershell/module/microsoft.powershell.core/get-command) を使用して *拡張機能* をフィルター処理します。

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

次のような出力が表示されます。

```powershell
CommandType     Name                                          Version    Source
-----------     ----                                          -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      Az.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      Az.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      Az.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      Az.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      Az.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      Az.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      Az.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      Az.Compute
```

次の例では、カスタム スクリプト拡張機能を使って、ターゲットの仮想マシン上に GitHub リポジトリからスクリプトをダウンロードし、スクリプトを実行します。 カスタム スクリプト拡張機能について詳しくは、[カスタム スクリプト拡張機能の概要](custom-script-windows.md)に関する記事をご覧ください。

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

次の例では、VM アクセス拡張機能を使って、Windows VM の管理パスワードを一時パスワードにリセットします。 VM アクセス拡張機能について詳しくは、[Windows VM でのリモート デスクトップ サービスのリセット](/troubleshoot/azure/virtual-machines/reset-rdp)に関する記事をご覧ください。 これを実行した後は、最初のログイン時にパスワードをリセットします。

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

`Set-AzVMExtension` コマンドを使って、任意の VM 拡張機能を開始できます。 詳細については、[Set-AzVMExtension のリファレンス](/powershell/module/az.compute/set-azvmextension)を参照してください。


### <a name="azure-portal"></a>Azure portal

VM 拡張機能は、Azure Portal から既存の VM に適用できます。 ポータルで VM を選択し、 **[拡張機能]** を選択してから、 **[追加]** を選択します。 利用可能な拡張機能の一覧から目的の拡張機能を選択し、ウィザードの手順に従います。

次の例は、Azure Portal からの Microsoft Antimalware 拡張機能のインストールを示しています。

![antimalware 拡張機能のインストール](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート

VM 拡張機能を Azure Resource Manager テンプレートに追加し、テンプレートのデプロイを使用して実行できます。 テンプレートを使用して拡張機能をデプロイする場合は、完全に構成された Azure デプロイを作成できます。 たとえば、次の JSON は、負荷分散された一連の VM と Azure SQL Database をデプロイし、各 VM に .NET Core アプリケーションをインストールする Resource Manager テンプレートからの抜粋です。 VM 拡張機能はソフトウェアのインストールに対応します。

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
    "typeHandlerVersion": "1.9",
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

Resource Manager テンプレートの作成の詳細については、[Windows VM 拡張機能を使った Azure Resource Manager テンプレートの作成](../windows/template-description.md#extensions)に関するページを参照してください。

## <a name="secure-vm-extension-data"></a>VM 拡張機能のデータの保護

VM 拡張機能の実行時には、資格情報、ストレージ アカウント名、ストレージ アカウント アクセス キーなどの機密情報を含めなければならない場合があります。 多くの VM 拡張機能には、データを暗号化し、ターゲットの VM 内でのみ暗号化を解除する保護された構成が含まれます。 拡張機能には、それぞれ固有の保護された構成スキーマがあります。スキーマについては、拡張機能のドキュメントで詳しく説明されています。

次の例は、Windows 用カスタム スクリプト拡張機能のインスタンスを示しています。 実行するコマンドには、一連の資格情報が含まれています。 この例では、実行するコマンドは暗号化されません。

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
    "typeHandlerVersion": "1.9",
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

**command to execute** プロパティを **protected** 構成に移動すると、次の例に示すように実行文字列が保護されます。

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
    "typeHandlerVersion": "1.9",
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

拡張機能を使用する Azure IaaS VM の証明書コンソールには、**_Windows Azure CRP Certificate Generator_** という件名の証明書が表示されることがあります。 クラシック RDFE VM の場合、証明書の件名は **_Windows Azure Service Management for Extensions_** になります。

これらの証明書により、拡張機能によって使用される保護された設定 (パスワードやその他の資格情報) の転送中、VM とそのホストの間の通信がセキュリティで保護されます。 証明書は Azure ファブリック コントローラーによって作られ、VM エージェントに渡されます。 VM を毎日起動し、停止する場合、ファブリック コントローラーによって新しい証明書が作成されることがあります。 証明書はコンピューターの個人用証明書ストアに保存されます。 これらの証明書は削除できます。 必要に応じて、VM エージェントにより証明書が再作成されます。

### <a name="how-do-agents-and-extensions-get-updated"></a>エージェントと拡張機能を更新する方法

エージェントと拡張機能は、同じ更新メカニズムを共有します。 一部の更新プログラムでは、追加のファイアウォール規則を必要としません。

更新プログラムが利用できる場合で、拡張機能への変更があり、次のような他の VM モデルが変更されるときは、更新プログラムは VM にのみインストールされます。

- データ ディスク
- 拡張機能
- ブート診断コンテナー
- ゲスト OS のシークレット
- VM サイズ
- ネットワーク プロファイル

別のリージョンの VM は異なるバージョン上に搭載できるので、パブリッシャーは別のタイミングで、更新プログラムをリージョンで使用できるようにします。

#### <a name="listing-extensions-deployed-to-a-vm"></a>VM にデプロイされる拡張機能の一覧

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>エージェントの更新プログラム

Windows ゲスト エージェントには *拡張機能処理コード* のみが含まれ、*Windows プロビジョニング コード* は分離されています。 Windows ゲスト エージェントは、アンインストールできます。 Window ゲスト エージェントの自動更新を無効にすることはできません。

*拡張機能処理コード* は、Azure ファブリックとの通信を担い、インストール、ステータスのレポート、個々の拡張機能の更新と削除などの VM 拡張機能の操作を処理します。 更新プログラムには、*拡張機能処理コード* に対するセキュリティ修正プログラム、バグ修正プログラム、および拡張機能が含まれます。

実行しているバージョンを確認するには、[インストールされている Windows ゲスト エージェントの検出](agent-windows.md#detect-the-vm-agent)に関するページを参照してください。

#### <a name="extension-updates"></a>拡張機能の更新プログラム

拡張機能の更新プログラムが使用可能な場合、Windows ゲスト エージェントではこれをダウンロードして、拡張機能をアップグレードします。 拡張機能の自動更新プログラムは、"*マイナー*" または "*修正プログラム*" のどちらかです。 拡張機能の "*マイナー*" 更新プログラムは、拡張機能をプロビジョニングするときに、選択または除外できます。 次の例では、*autoUpgradeMinorVersion": true,'* を使って、Resource Manager テンプレートのマイナー バージョンを自動でアップグレードする方法を示しています。

```json
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
```

最新のマイナー リリースのバグ修正プログラムを取得するには、拡張機能のデプロイで常に自動更新を選択することを強くお勧めします。 セキュリティまたは主要なバグの修正を提供する修正プログラムは、除外できません。

### <a name="how-to-identify-extension-updates"></a>拡張機能の更新プログラムを識別する方法

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>拡張機能が autoUpgradeMinorVersion を使って VM 上に設定されているかどうかを識別する

拡張機能が 'autoUpgradeMinorVersion' を使ってプロビジョニングされたかどうかは、VM モデルから判断できます。 確認するには、[Get-AzVm](/powershell/module/az.compute/get-azvm) を使用し、リソース グループと VM の名前を次のように指定します。

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

次の例の出力は、*autoUpgradeMinorVersion* が *true* に設定されていることを示しています。

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>autoUpgradeMinorVersion が行われたタイミングを識別する

拡張機能への更新がいつ行われたかを判断するには、*C:\WindowsAzure\Logs\WaAppAgent.log* で VM 上のエージェント ログを確認します。

次の例では、VM に "*Microsoft.Compute.CustomScriptExtension 1.8*" をインストールしました。 修正プログラムはバージョン "*1.9*" で利用できました。

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>エージェントのアクセス許可

タスクを実行するには、エージェントが "*ローカル システム*" として実行される必要があります。

## <a name="troubleshoot-vm-extensions"></a>VM 拡張機能のトラブルシューティング

各 VM 拡張機能には、固有のトラブルシューティング手順が存在する場合があります。 たとえば、カスタム スクリプト拡張機能を使用する場合、スクリプト実行の詳細は、拡張機能が実行された VM のローカルに保存されます。 拡張機能に固有のトラブルシューティング手順は、拡張機能のドキュメントで詳しく説明されています。

次のトラブルシューティング手順は、すべての VM 張機能に適用されます。

1. Windows ゲスト エージェント ログをチェックするには、*C:\WindowsAzure\Logs\WaAppAgent.log* で拡張機能がプロビジョニングされたときのアクティビティを確認します

2. *C:\WindowsAzure\Logs\Plugins\\<extensionName>* の実際の拡張機能ログで詳細を確認します

3. エラーコードや既知の問題などについて、拡張機能固有のドキュメントのトラブルシューティングのセクションを確認します。

4. システム ログを確認します。 排他的なパッケージ マネージャーのアクセスを必要とする別のアプリケーションのインストールが長時間実行されている場合など、拡張機能に干渉する可能性のある他の操作をチェックします。

### <a name="common-reasons-for-extension-failures"></a>拡張機能のエラーの一般的な理由

1. 拡張機能の実行には 20 分かかります (例外は CustomScript 拡張機能、Chef、および DSC であり、これらは 90 分かかります)。 デプロイにかかる時間がこれを超過する場合、タイムアウトとしてマークされます。 これは、拡張機能がプロビジョニングを試行しているときの、VM のリソース低減や他の VM 構成/起動タスクによる大量のリソース消費に起因する可能性があります。

2. 最小限の前提条件が満たされていない。 一部の拡張機能では、HPC イメージなど、VM SKU 上に依存関係があります。 拡張機能は、Azure Storage やパブリック サービスとの通信など、特定のネットワーク アクセス要件を必要とする場合があります。 その他の例としては、パッケージ リポジトリへのアクセス、ディスク領域の不足、セキュリティ上の制限事項が考えられます。

3. 排他的なパッケージ マネージャーのアクセス。 一部のケースでは、長時間にわたる VM 構成の実行が拡張機能のインストールと競合し、どちらもパッケージ マネージャーへの排他的なアクセスを必要とする状況になる場合があります。

### <a name="view-extension-status"></a>拡張機能の状態表示

VM に対して VM 拡張機能が実行されたら、[Get-AzVM](/powershell/module/az.compute/get-azvm) を使用して拡張機能の状態を返します。 *Substatuses[0]* は、拡張機能のプロビジョニングに成功したことを示しており、VM には正常にデプロイされたが VM 内での拡張機能の実行はエラーになったことが *Substatuses[1]* でわかります。

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

出力は次の例のようになります。

```powershell
Extensions[0]           :
  Name                  : CustomScriptExtension
  Type                  : Microsoft.Compute.CustomScriptExtension
  TypeHandlerVersion    : 1.9
  Substatuses[0]        :
    Code                : ComponentStatus/StdOut/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : Windows PowerShell \nCopyright (C) Microsoft Corporation. All rights reserved.\n
  Substatuses[1]        :
    Code                : ComponentStatus/StdErr/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : The argument 'cseTest%20Scriptparam1.ps1' to the -File parameter does not exist. Provide the path to an existing '.ps1' file as an argument to the

-File parameter.
  Statuses[0]           :
    Code                : ProvisioningState/failed/-196608
    Level               : Error
    DisplayStatus       : Provisioning failed
    Message             : Finished executing command
```

拡張機能の実行の状態は、Azure Portal で確認することもできます。 拡張機能の状態を表示するには、VM を選択し、 **[拡張機能]** を選択して、目的の拡張機能を選択します。

### <a name="rerun-vm-extensions"></a>VM 拡張機能の再実行

VM 拡張機能の再実行が必要な場合があります。 拡張機能を再実行するには、その拡張機能を削除し、その後任意の実行方法で拡張機能を再実行します。 拡張機能を削除するには、[Remove-AzVMExtension](/powershell/module/az.compute/remove-azvmextension) を次のように使用します。

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

また、次の手順を使用して、Azure Portal で拡張機能を削除することもできます。

1. VM を選択します。
2. **[拡張機能]** を選択します。
3. 目的の拡張機能を選択します。
4. **[アンインストール]** を選択します。

## <a name="common-vm-extensions-reference"></a>一般的な VM 拡張機能のリファレンス
| 拡張機能の名前 | 説明 | 詳細情報 |
| --- | --- | --- |
| Windows でのカスタムのスクリプト拡張機能 |Azure 仮想マシンに対してスクリプトを実行します。 |[Windows でのカスタムのスクリプト拡張機能](custom-script-windows.md) |
| Windows での DSC 拡張機能 |PowerShell DSC (必要な状態の構成) 拡張機能 |[Windows での DSC 拡張機能](dsc-overview.md) |
| Azure Diagnostics 拡張機能 |Azure Diagnostics を管理します |[Azure Diagnostics 拡張機能](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM アクセス拡張機能 |ユーザーと資格情報を管理します。 |[Linux 用 VM アクセス拡張機能](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>次のステップ

VM 拡張機能の詳細については、「[Azure 仮想マシンの拡張機能と機能の概要](overview.md)」をご覧ください。