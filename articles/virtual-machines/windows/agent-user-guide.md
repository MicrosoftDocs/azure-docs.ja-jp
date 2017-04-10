---
title: "Azure 仮想マシン エージェントの概要 | Microsoft Docs"
description: "Azure 仮想マシン エージェントの概要"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 6cf561b269c4d873a894b47265e39a057cc415c3
ms.openlocfilehash: 24ad2c2d2872f844e32d3fae559683c3d992bd00


---
# <a name="azure-virtual-machine-agent-overview"></a>Azure 仮想マシン エージェントの概要

Microsoft Azure 仮想マシン エージェント (VM エージェント) は、VM と Azure ファブリック コントローラーのやり取りを管理する、セキュリティで保護された簡易プロセスです。 VM エージェントは、Azure 仮想マシン拡張機能の有効化と実行において主要な役割を果たします。 VM 拡張機能は、仮想マシンのデプロイ後の構成 (ソフトウェアのインストールと構成など) を有効にします。 また、仮想マシンの管理者パスワードのリセットなどの回復機能も有効にします。 Azure VM エージェントがないと、仮想マシン拡張機能を実行できません。

このドキュメントでは、Azure 仮想マシン エージェントのインストール、検出、削除について説明します。

## <a name="install-the-vm-agent"></a>[VM エージェントのインストール]

### <a name="azure-gallery-image"></a>Azure ギャラリー イメージ

Azure VM エージェントは、Azure ギャラリー イメージからデプロイされたすべての Windows 仮想マシンに既定でインストールされます。 ポータル、PowerShell、コマンド ライン インターフェイス、Azure Resource Manager テンプレートから Azure ギャラリー イメージをデプロイした場合も、Azure VM エージェントがインストールされます。 

### <a name="manual-installation"></a>手動のインストール

Windows インストーラー パッケージを使用して、手動で Windows VM エージェントをインストールできます。 Azure にデプロイするカスタムな仮想マシン イメージを作成する場合に、手動のインストールが必要になることがあります。 Windows VM エージェントを手動でインストールするには、[Windows Azure VM エージェントのダウンロード](http://go.microsoft.com/fwlink/?LinkID=394789)場所から VM エージェント インストーラーをダウンロードします。 

Windows インストーラー ファイルをダブルクリックすると、VM エージェントをインストールできます。 VM エージェントを自動 (無人) インストールするには、次のコマンドを実行します。

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>VM エージェントの検出

### <a name="powershell"></a>PowerShell

Azure Resource Manager の PowerShell モジュールを使用して、Azure Virtual Machines に関する情報を取得できます。 `Get-AzureRmVM` を実行すると、Azure VM エージェントのプロビジョニング状態を含む、大量の情報が返されます。

```PowerShell
Get-AzureRmVM
```

以下に示すのは、`Get-AzureRmVM` の出力のサブセットに過ぎません。 `ProvisionVMAgent` プロパティが `OSProfile` の入れ子になっていることに注意してください。このプロパティは、VM エージェントが仮想マシンにデプロイされているかどうかを判別するために使用できます。

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : muUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

次のスクリプトを使用すると、仮想マシン名と VM エージェントの状態の正確な一覧を取得できます。

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>手動での検出

Windows Azure VM にログインすると、タスク マネージャーを使用して、実行中のプロセスを調べることができます。 Azure VM エージェントを確認するには、タスク マネージャーを開いて [詳細] タブをクリックし、`WindowsAzureGuestAgent.exe` というプロセス名を探します。 このプロセスが存在する場合は、VM エージェントがインストールされています。

## <a name="upgrade-the-vm-agent"></a>VM エージェントのアップグレード

Windows 用の Azure VM エージェントは自動的にアップグレードされます。 新しい仮想マシンが Azure にデプロイされると、最新の VM エージェントが提供されます。 カスタムな VM イメージの場合は、手動で更新を行って新しい VM エージェントを含める必要があります。


<!--HONumber=Dec16_HO1-->


