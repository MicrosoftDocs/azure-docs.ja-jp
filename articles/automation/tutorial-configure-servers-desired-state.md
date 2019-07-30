---
title: 目的の状態へのサーバーの構成と Azure Automation での誤差の管理
description: チュートリアル - サーバーの構成を Azure Automation State Configuration で管理する
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 3bcdb667ee649b9bbf32ad33e74e876cdd2b5cbf
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144186"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>目的の状態にサーバーを構成して誤差を管理する

Azure Automation State Configuration を使うと、サーバーの構成を指定し、時間が経過してもサーバーが指定した状態を保つようにすることができます。

> [!div class="checklist"]
> - Azure Automation DSC によって管理する VM をオンボードする
> - Azure Automation に構成をアップロードする
> - 構成をノードの構成としてコンパイルする
> - ノードの構成を管理対象ノードに割り当てる
> - 管理対象ノードの準拠状態を確認する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- Azure Automation アカウント。 Azure Automation 実行アカウントの作成手順については、 [Azure 実行アカウント](automation-sec-configure-azure-runas-account.md)に関するページをご覧ください。
- Windows Server 2008 R2 以降を実行している Azure Resource Manager VM (クラシックではない)。 VM の作成手順については、「 [Azure ポータルで初めての Windows 仮想マシンを作成する](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Azure PowerShell モジュール バージョン 3.6 以降。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/azurerm/install-azurerm-ps)に関するページを参照してください。
- Desired State Configuration (DSC) に関する知識。 DSC については、「[Windows PowerShell Desired State Configuration の概要](https://docs.microsoft.com/powershell/dsc/overview)」をご覧ください。

## <a name="log-in-to-azure"></a>Azure にログインする

`Connect-AzureRmAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>構成を作成して Azure Automation にアップロードする

このチュートリアルでは、IIS を VM に確実にインストールする簡単な DSC 構成を使います。

構成の構成については、「[DSC 構成](/powershell/dsc/configurations)」をご覧ください。

テキスト エディターで次のように入力し、`TestConfig.ps1` としてローカルに保存します。

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

`Import-AzureRmAutomationDscConfiguration` コマンドレットを呼び出して、構成を Automation アカウントにアップロードします。

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>構成をノードの構成としてコンパイルする

ノードに DSC 構成を割り当てるには、先に DSC 構成をノードの構成としてコンパイルする必要があります。

構成のコンパイルについては、「[DSC 構成](/powershell/dsc/configurations)」をご覧ください。

`Start-AzureRmAutomationDscCompilationJob` コマンドレットを呼び出して、`TestConfig` の構成をノード構成にコンパイルします。

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

これにより、`TestConfig.WebServer` という名前のノード構成が Automation アカウントに作成されます。

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>State Configuration によって管理される VM を登録する

Azure Automation State Configuration を使用すると、Azure VM (クラシックと Resource Manager の両方)、オンプレミスの VM、Linux マシン、AWS VM、オンプレミスの物理マシンを管理できます。 このトピックでは、Azure Resource Manager VM を登録する方法のみを説明します。 他の種類のマシンの登録について詳しくは、「[Azure Automation State Configuration による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」をご覧ください。

`Register-AzureRmAutomationDscNode` コマンドレットを呼び出して、VM を Azure Automation State Configuration に登録します。

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

これにより、指定した VM が State Configuration で管理対象ノードとして登録されます。

### <a name="specify-configuration-mode-settings"></a>構成モードの設定を指定する

管理対象ノードとして VM を登録するときに、構成のプロパティも指定することができます。 たとえば、**ConfigurationMode** プロパティの値として `ApplyOnly` を指定することにより、マシンの状態を 1 回だけ適用するように指定できます (DSC は初期チェックの後で構成の適用を試みません)。

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

また、**ConfigurationModeFrequencyMins** プロパティを使うことで、DSC が構成の状態をチェックする頻度も指定できます。

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

管理対象ノードの構成プロパティの設定について詳しくは、「[Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode)」をご覧ください。

DSC 構成の設定について詳しくは、「[ローカル構成マネージャーの構成](/powershell/dsc/metaconfig)」をご覧ください。

## <a name="assign-a-node-configuration-to-a-managed-node"></a>ノードの構成を管理対象ノードに割り当てる

コンパイル済みのノード構成を構成対象の VM 割り当てることができる状態になりました。

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

このコマンドレットは、`TestConfig.WebServer` という名前のノード構成を、登録済みの DSC ノード `DscVm` に割り当てます。
既定では、DSC ノードはノード構成に準拠していることを 30 分ごとにチェックされます。
準拠チェック間隔を変更する方法については、「[ローカル構成マネージャーの構成](/PowerShell/DSC/metaConfig)」をご覧ください。

## <a name="check-the-compliance-status-of-a-managed-node"></a>管理対象ノードの準拠状態を確認する

`Get-AzureRmAutomationDscNodeReport` コマンドレットを呼び出すことで、管理対象ノードの準拠状態に関するレポートを取得できます。

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>サービスからのノードの削除

Azure Automation State Configuration にノードを追加すると、Local Configuration Manager の設定はサービスに登録され、構成とマシンの構成に必要なモジュールがプルされるように設定されます。
サービスからノードを削除することを選択した場合は、Azure portal または Az コマンドレットを使用して削除できます。

> [!NOTE]
> サービスからノードを登録解除しても、Local Configuration Manager 設定のみが設定されるため、ノードはサービスに接続しなくなります。
> これは、現在ノードに適用されている構成には影響しません。
> 現在の構成を削除するには、[PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) を使用するか、ローカル構成ファイルを削除します (これが Linux ノードの唯一のオプションです)。

### <a name="azure-portal"></a>Azure ポータル

Azure Automation から、目次の **[State configuration (DSC)]\(状態の構成 (DSC)\)** をクリックします。
次に **[ノード]** をクリックしてサービスに登録されているノードの一覧を表示します。
削除するノードの名前をクリックします。
開いた [ノード] ビューで、 **[登録解除]** をクリックします。

### <a name="powershell"></a>PowerShell

PowerShell を使用して Azure Automation State Configuration サービスからノードを登録解除するには、コマンドレット [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0) のドキュメントに従ってください。

## <a name="next-steps"></a>次の手順

- 使用を開始するには、「[Azure Automation State Configuration の使用](automation-dsc-getting-started.md)」をご覧ください。
- ノードをオンボードにする方法は、「[Azure Automation State Configuration による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」をご覧ください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てることができるようにする方法の詳細については、「[Azure Automation State Configuration での構成のコンパイル](automation-dsc-compile.md)」をご覧ください。
- PowerShell コマンドレットのリファレンスについては、[Azure Automation State Configuration のコマンドレット](/powershell/module/azurerm.automation/#automation)に関するページをご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- 継続的なデプロイ パイプラインで Azure Automation State Configuration を使う例については、「[Automation State Configuration と Chocolatey を使用した仮想マシンへの継続的なデプロイ](automation-dsc-cd-chocolatey.md)」をご覧ください。
