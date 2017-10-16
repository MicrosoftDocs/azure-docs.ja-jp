---
title: "目的の状態へのサーバーの構成と Azure Automation での誤差の管理 | Microsoft Docs"
description: "チュートリアル - サーバーの構成を Azure Automation DSC で管理する"
services: automation
documentationcenter: automation
author: eslesar
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: eslesar
ms.custom: 
ms.openlocfilehash: 63a83e35ce29541de578cb264464448fb6ee3e1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>目的の状態にサーバーを構成して誤差を管理する

Azure Automation Desired State Configuration (DSC) を使うと、サーバーの構成を指定し、時間が経過してもサーバーが指定した状態を保つようにすることができます。



> [!div class="checklist"]
> * Azure Automation DSC によって管理する VM をオンボードする
> * Azure Automation に構成をアップロードする
> * 構成をノードの構成としてコンパイルする
> * ノードの構成を管理対象ノードに割り当てる
> * 管理対象ノードの準拠状態を確認する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* Azure Automation アカウント。 Azure Automation 実行アカウントの作成手順については、 [Azure 実行アカウント](automation-sec-configure-azure-runas-account.md)に関するページをご覧ください。
* Windows Server 2008 R2 以降を実行している Azure Resource Manager VM (クラシックではない)。 VM の作成手順については、「 [Azure ポータルで初めての Windows 仮想マシンを作成する](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
* Azure PowerShell モジュール バージョン 3.6 以降。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。
* DSC に関する知識。 DSC については、「[Windows PowerShell Desired State Configuration の概要](https://docs.microsoft.com/powershell/dsc/overview)」をご覧ください。

## <a name="log-in-to-azure"></a>Azure へのログイン

`Login-AzureRmAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Login-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>構成を作成して Azure Automation にアップロードする

このチュートリアルでは、IIS を VM に確実にインストールする簡単な DSC 構成を使います。

構成の構成については、「[DSC 構成](https://docs.microsoft.com/powershell/dsc/configurations)」をご覧ください。

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

構成のコンパイルについては、「[DSC 構成](https://docs.microsoft.com/powershell/dsc/configurations)」をご覧ください。

`Start-AzureRmAutomationDscCompilationJob` コマンドレットを呼び出して、`TestConfig` の構成をノード構成にコンパイルします。

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

これにより、`TestConfig.WebServer` という名前のノード構成が Automation アカウントに作成されます。

## <a name="register-a-vm-to-be-managed-by-dsc"></a>DSC によって管理される VM を登録する

Azure Automation DSC を使用すると、Azure VM (クラシックと Resource Manager の両方)、オンプレミスの VM、Linux マシン、AWS VM、オンプレミスの物理マシンを管理できます。 このトピックでは、Azure Resource Manager VM を登録する方法のみを説明します。
他の種類のマシンの登録について詳しくは、「[Azure Automation DSC による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」をご覧ください。

`Register-AzureRmAutomationDscNode` コマンドレットを呼び出して、VM を Azure Automation DSC に登録します。

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName "MyResourceGroup" -AutomationAccountName "myAutomationAccount" -AzureVMName "DscVm"
```

これにより、指定した VM が Azure Automation アカウントの DSC ノードとして登録されます。

### <a name="specify-configuration-mode-settings"></a>構成モードの設定を指定する

管理対象ノードとして VM を登録するときに、構成のプロパティも指定することができます。
たとえば、**ConfigurationMode** プロパティの値として `ApplyOnly` を指定することにより、マシンの状態を 1 回だけ適用するように指定できます (DSC は初期チェックの後で構成の適用を試みません)。

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationMode 'ApplyOnly'
```

また、**ConfigurationModeFrequencyMins** プロパティを使うことで、DSC が構成の状態をチェックする頻度も指定できます。

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationModeFrequencyMins 60
```

管理対象ノードの構成プロパティの設定について詳しくは、「[Register-AzureRmAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-4.3.1&viewFallbackFrom=azurermps-4.2.0)」をご覧ください。

DSC 構成の設定について詳しくは、「[ローカル構成マネージャーの構成](https://docs.microsoft.com/powershell/dsc/metaconfig)」をご覧ください。

## <a name="assign-a-node-configuration-to-a-managed-node"></a>ノードの構成を管理対象ノードに割り当てる

コンパイル済みのノード構成を構成対象の VM 割り当てることができる状態になりました。

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -Id $node.Id
```

このコマンドレットは、`TestConfig.WebServer` という名前のノード構成を、登録済みの DSC ノード `DscVm` に割り当てます。
既定では、DSC ノードはノード構成に準拠していることを 30 分ごとにチェックされます。
準拠チェック間隔を変更する方法については、「[ローカル構成マネージャーの構成](https://docs.microsoft.com/PowerShell/DSC/metaConfig)」をご覧ください。

## <a name="check-the-compliance-status-of-a-managed-node"></a>管理対象ノードの準拠状態を確認する

`Get-AzureRmAutomationDscNodeReport` コマンドレットを呼び出すことで、DSC ノードの準拠状態に関するレポートを取得できます。

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports Get-Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Id $node.Id

# Display the most recent report
$report[0]
```

## <a name="next-steps"></a>次のステップ

* Azure Automation DSC で管理対象ノードをオンボードにする方法は、「[Azure Automation DSC による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」を参照してください。
* Azure Portal を使って Automation DSC を使う方法については、「[Azure Automation DSC の使用](automation-dsc-getting-started.md)」をご覧ください。
* DSC 構成をコンパイルしてターゲット ノードに割り当てることができるようにする方法の詳細については、「[Azure Automation DSC での構成のコンパイル](automation-dsc-compile.md)」を参照してください。
* Azure Automation DSC の PowerShell コマンドレットのリファレンスについては、「[Azure Automation DSC cmdlets (Azure Automation DSC コマンドレット)](/powershell/module/azurerm.automation/#automation)」を参照してください。
* 料金情報については、[Azure Automation DSC の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページを参照してください。
* 継続的なデプロイ パイプラインで Azure Automation DSC を使う例については、「[Azure Automation DSC と Chocolatey を使用した IaaS VM への継続的なデプロイ](automation-dsc-cd-chocolatey.md)」をご覧ください。