---
title: Azure 仮想マシン エージェントの概要 | Microsoft Docs
description: Azure 仮想マシン エージェントの概要
services: virtual-machines-windows
documentationcenter: virtual-machines
author: axayjo
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2019
ms.author: akjosh
ms.openlocfilehash: 7c163dd48e53a3116d58cb94988f2822ddede5e5
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169127"
---
# <a name="azure-virtual-machine-agent-overview"></a>Azure 仮想マシン エージェントの概要
Microsoft Azure 仮想マシン エージェント (VM エージェント) は、仮想マシン (VM) と Azure ファブリック コントローラーのやり取りを管理する、セキュリティで保護された簡易プロセスです。 VM エージェントは、Azure 仮想マシン拡張機能の有効化と実行において主要な役割を果たします。 VM 拡張機能は、VM のデプロイ後の構成 (ソフトウェアのインストールと構成など) を有効にします。 VM 拡張機能は、VM の管理者パスワードのリセットなどの回復機能も有効にします。 Azure VM エージェントがないと、VM 拡張機能を実行できません。

この記事では、Azure 仮想マシン エージェントのインストール、検出、削除について説明します。

## <a name="install-the-vm-agent"></a>VM エージェントのインストール

### <a name="azure-marketplace-image"></a>Azure Marketplace イメージ

Azure VM エージェントは、Azure Marketplace イメージからデプロイされたすべての Windows VM に既定でインストールされます。 ポータル、PowerShell、コマンド ライン インターフェイス、Azure Resource Manager テンプレートから Azure Marketplace イメージをデプロイしても、Azure VM エージェントがインストールされます。

Windows Guest Agent Package は 2 つの部分に分かれています。

- プロビジョニング エージェント (PA)
- Windows ゲスト エージェント (WinGA)

VM を起動するには、VM に PA がインストールされている必要がありますが、WinGA はインストール不要です。 VM のデプロイ時に、WinGA をインストールしないことを選択できます。 次の例は、Azure Resource Manager テンプレートを使って *provisionVmAgent* オプションを選択する方法を示しています。

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

エージェントがインストールされていない場合、Azure Backup や Azure Security など、Azure の一部のサービスを使用できません。 これらのサービスでは、拡張機能をインストールする必要があります。 WinGA なしで VM をデプロイした場合は、最新バージョンのエージェントを後からインストールできます。

### <a name="manual-installation"></a>手動のインストール
Windows インストーラー パッケージを使用して、手動で Windows VM エージェントをインストールできます。 Azure にデプロイされるカスタム VM イメージを作成するときには、手動でのインストールが必要な場合があります。 手動で Windows VM エージェントをインストールするには、[VM エージェント インストーラーをダウンロードします](https://go.microsoft.com/fwlink/?LinkID=394789)。 VM エージェントは、Windows Server 2008 R2 以降でサポートされます。

Windows インストーラー ファイルをダブルクリックすると、VM エージェントをインストールできます。 VM エージェントを自動 (無人) インストールするには、次のコマンドを実行します。

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

### <a name="prerequisites"></a>前提条件
Windows VM エージェントでは、.Net Framework 4.0 を使用して、少なくとも Windows Server 2008 R2 (64 ビット) を実行する必要があります。

## <a name="detect-the-vm-agent"></a>VM エージェントの検出

### <a name="powershell"></a>PowerShell

Azure Resource Manager の PowerShell モジュールを使用して、Azure VM に関する情報を取得できます。 VM に関する情報 (Azure VM エージェントのプロビジョニング状態など) を表示するには、[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) を使用します。

```powershell
Get-AzVM
```

次の圧縮された出力例は、*OSProfile* の内部で入れ子になった *ProvisionVMAgent* プロパティを示しています。 このプロパティを使用すると、VM に VM エージェントかデプロイされているかどうかを判定できます。

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

次のスクリプトを使用すると、VM 名と VM エージェントの状態の簡潔な一覧が返されます。

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>手動での検出

Windows VM にログインすると、タスク マネージャーを使用して、実行中のプロセスを調べることができます。 Azure VM エージェントを確認するには、タスク マネージャーを開いて *[詳細]* タブをクリックし、**WindowsAzureGuestAgent.exe** というプロセス名を探します。 このプロセスが存在する場合は、VM エージェントがインストールされています。


## <a name="upgrade-the-vm-agent"></a>VM エージェントのアップグレード
Windows 用の Azure VM エージェントは自動的にアップグレードされます。 新しい VM が Azure にデプロイされると、VM のプロビジョニング時に最新の VM エージェントが提供されます。 カスタム VM イメージの場合は、新しい VM エージェントを含めるために、イメージ作成時に手動で更新する必要があります。


## <a name="next-steps"></a>次の手順
VM 拡張機能の詳細については、「[Azure 仮想マシンの拡張機能と機能の概要](overview.md)」をご覧ください。
