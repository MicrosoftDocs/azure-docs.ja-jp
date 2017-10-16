---
title: "Azure クイック スタート - PowerShell で VM をバックアップする | Microsoft Docs"
description: "Azure PowerShell を使用して仮想マシンをバックアップする方法"
services: virtual-machines-windows, azure-backup
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: virtual-machines-windows, azure-backup
ms.devlang: azurecli
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/18/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 3ca0e9d905e23e25b57b46454399ad12e2890d52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Azure PowerShell を使用した Azure の仮想マシンのバックアップ
Azure PowerShell モジュールは、コマンド ラインやスクリプトで Azure リソースを作成および管理するために使用します。 データは、定期的にバックアップすることで保護することができます。 Azure Backup によって、geo 冗長 Recovery コンテナーに保存できる復元ポイントが作成されます。 この記事では、Azure PowerShell モジュールを使用して仮想マシン (VM) をバックアップする方法を説明します。 これらの手順は、[Azure CLI](quick-backup-vm-cli.md) または [Azure ポータル](quick-backup-vm-portal.md)を使用して実行することもできます。

このクイック スタートでは、既存の Azure VM でバックアップを実行できます。 VM を作成する必要がある場合は、[Azure PowerShell を使用して VM を作成](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json)できます。

このクイック スタートには、Azure PowerShell モジュール バージョン 4.4 以降が必要です。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。


## <a name="log-in-to-azure"></a>Azure へのログイン
`Login-AzureRmAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Login-AzureRmAccount
```

Azure Backup を最初に使用するときは、[Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) を使ってサブスクリプション内で Azure Recovery Service プロバイダーを登録する必要があります。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
```


## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーの作成
Recovery Services コンテナーは、Azure VM などの保護された各リソースのバックアップ データを格納する論理コンテナーです。 保護されたリソースのバックアップ ジョブを実行すると、Recovery Services コンテナー内に復元ポイントが作成されます。 次に、この復旧ポイントのいずれかを使用して、データを特定の時点に復元できます。

[New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault) を使用して Recovery Services コンテナーを作成します。 保護する VM と同じリソース グループと場所を指定します。 [サンプル スクリプト](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) を使用して VM を作成した場合、リソース グループの名前は *myResourceGroup*、VM の名前は *myVM* になり、リソースは *WestEurope* に格納されます。

```powershell
New-AzureRmRecoveryServicesVault `
    -ResourceGroupName "myResourceGroup" `
    -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
```

既定では、コンテナーは geo 冗長ストレージ用に設定されます。 さらにデータを保護するために、このストレージの冗長性レベルでは、プライマリ リージョンから数百マイル離れたセカンダリ Azure リージョンにバックアップ データがレプリケートされます。

残りの手順でこのコンテナーを使用するには、コンテナー コンテキストを [AzureRmRecoveryServicesVaultContext](/powershell/module/AzureRM.RecoveryServices/Set-AzureRmRecoveryServicesVaultContext) に設定します。

```powershell
Get-AzureRmRecoveryServicesVault `
    -Name "myRecoveryServicesVault" | Set-AzureRmRecoveryServicesVaultContext
```


## <a name="enable-backup-for-an-azure-vm"></a>Azure VM のバックアップを有効にする
バックアップ ジョブがいつ実行され、復旧ポイントをどのくらいの期間格納するかを定義するポリシーを作成して使用します。 既定の保護ポリシーでは、毎日バックアップ ジョブが実行され、復元ポイントは 30 日間保持されます。 これらの既定ポリシー値を使用して、VM をすばやく保護できます。 まず、既定のポリシーを [Get AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupProtectionPolicy) に設定します。

```powershell
$policy = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "DefaultPolicy"
```

VM のバックアップ保護を有効化するには、[Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/AzureRM.RecoveryServices.Backup/Enable-AzureRmRecoveryServicesBackupProtection) を使用します。 使用するポリシーを指定した後、保護するリソース グループと VM を指定します。

```powershell
Enable-AzureRmRecoveryServicesBackupProtection `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Policy $policy
```


## <a name="start-a-backup-job"></a>バックアップ ジョブの開始
スケジュールされた時刻に既定のポリシーによってジョブが実行されるのを待たず、バックアップを今すぐ開始するには、[Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) を使用します。 この最初のバックアップ ジョブによって、完全な復旧ポイントが作成されます。 初回のバックアップ後のバックアップ ジョブでは、増分復元ポイントが作成されます。 増分復旧ポイントでは、前回のバックアップ以降に行われた変更のみが転送されるため、保存効率と時間効率が向上します。

次のコマンドでは、[Get AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer) を使用してバックアップ データを保持する Recovery Services コンテナー内のコンテナーを指定します。 バックアップする各 VM は、項目として扱われます。 バックアップ ジョブを開始するには、[Get AzureRmRecoveryServicesBackupItem](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupItem) を使用して、VM 項目に関する情報を取得します。

```powershell
$backupcontainer = Get-AzureRmRecoveryServicesBackupContainer `
    -ContainerType "AzureVM" `
    -FriendlyName "myVM"

$item = Get-AzureRmRecoveryServicesBackupItem `
    -Container $backupcontainer `
    -WorkloadType "AzureVM"

Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

この最初のバックアップ ジョブでは完全な復旧ポイントが作成されるため、処理に最大 20 分かかる場合があります。


## <a name="monitor-the-backup-job"></a>バックアップ ジョブの監視
バックアップ ジョブの状態を監視するには、[Get AzureRmRecoveryservicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob) を使用します。

```powershell
Get-AzureRmRecoveryservicesBackupJob
```

出力は次の例のようになります。バックアップ ジョブが **InProgress** であることがわかります。

```
WorkloadName   Operation         Status       StartTime              EndTime                JobID
------------   ---------         ------       ---------              -------                -----
myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
```

バックアップ ジョブの *状態* が *完了* と報告されたら、VM は Recovery Services で保護され、完全な復旧ポイントが格納されています。


## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
VM が不要になったら、VM の保護を無効にし、復旧ポイントと Recovery Services コンテナーを削除した後、リソース グループと関連付けられている VM リソースを削除することができます。 既存の VM を使用した場合は、最後の [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドレットを省略すると、リソース グループと VM をそのままの状態にしておくことができます。

VM のデータを復元する方法を説明するバックアップ チュートリアルに進む場合は、このセクションの手順を省略して、[次のステップ](#next-steps)に進んでください。 

```powershell
Disable-AzureRmRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzureRmRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzureRmRecoveryServicesVault -Vault $vault
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>次のステップ
このクイック スタートでは、Recovery Services コンテナーを作成し、VM の保護を有効にし、最初の復元ポイントを作成しました。 Azure Backup と Recovery Services についてさらに学ぶには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [複数の Azure VM をバックアップする](./tutorial-backup-vm-at-scale.md)