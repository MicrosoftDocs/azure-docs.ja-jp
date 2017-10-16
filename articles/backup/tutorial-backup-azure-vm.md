---
title: "Azure での Azure VM の大規模なバックアップ | Microsoft Docs"
description: "このチュートリアルでは、複数の Azure 仮想マシンを Recovery Services コンテナーにバックアップする方法について説明します。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "仮想マシン バックアップ; 仮想マシンのバックアップ; バックアップとディザスター リカバリー"
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: 
ms.openlocfilehash: db4e1392acaeb2431d29a851113b7bc5a6dc1e9d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-azure-virtual-machines-in-azure-at-scale"></a>Azure での Azure 仮想マシンの大規模なバックアップ

このチュートリアルでは、Azure 仮想マシンを Recovery Services コンテナーにバックアップする方法について説明します。 仮想マシンをバックアップするための作業のほとんどは、準備です。 仮想マシンをバックアップ (または保護) する前に、[前提条件](backup-azure-arm-vms-prepare.md)をすべて満たして、VM を保護するための環境を準備する必要があります。 

> [!IMPORTANT]
> このチュートリアルでは、リソース グループと Azure 仮想マシンを既に作成していることを前提としています。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

[Recovery Services コンテナー](backup-azure-recovery-services-vault-overview.md)は、バックアップされている項目の復旧ポイントを保持するコンテナーです。 Recovery Services コンテナーは、Azure リソース グループの一部としてデプロイし、管理できる Azure リソースです。 このチュートリアルでは、保護されている仮想マシンと同じリソース グループに Recovery Services コンテナーを作成します。


Azure Backup を最初に使用するときは、ご使用のサブスクリプションで Azure Recovery Service プロバイダーを登録する必要があります。 ご使用のサブスクリプションでプロバイダーが既に登録されている場合は、次の手順に進みます。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
```

**New-AzureRmRecoveryServicesVault** を使用して Recovery Services コンテナーを作成します。 バックアップを作成する仮想マシンを構成するときに使用するリソース グループ名と保存先を必ず指定します。 

```powershell
New-AzureRmRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
```

Azure Backup コマンドレットの多くは、入力として Recovery Services コンテナー オブジェクトを必要としています。 このため、Backup Recovery Services コンテナー オブジェクトを変数に格納すると便利です。 次に **Set-AzureRmRecoveryServicesBackupProperties** を使用して、**-BackupStorageRedundancy** オプションを [geo 冗長ストレージ (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) に設定します。 

```powershell
$vault1 = Get-AzureRmRecoveryServicesVault –Name myRSVault
Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```

## <a name="back-up-azure-virtual-machines"></a>Azure 仮想マシンのバックアップ

初期バックアップを実行するには、コンテナーのコンテキストを設定する必要があります。 コンテナーのコンテキストとは、コンテナーで保護されるデータの種類です。 Recovery Services コンテナーの作成時に、既定の保護およびアイテム保持ポリシーも付属しています。 既定の保護ポリシーは、毎日指定した時刻にバックアップ ジョブをトリガーします。 既定のアイテム保持ポリシーは、毎日の復旧ポイントを 30 日間保持します。 このチュートリアルでは、既定のポリシーをそのまま使用します。 

**[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** を使用して、コンテナーのコンテキストを設定します。 コンテナーのコンテキストを設定すると、後続のすべてのコマンドレットに適用されます。 

```powershell
Get-AzureRmRecoveryServicesVault -Name myRSVault | Set-AzureRmRecoveryServicesVaultContext
```

**[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** を使用して、バックアップ ジョブをトリガーします。 バックアップ ジョブによって、復旧ポイントが作成されます。 初回バックアップの場合は、復旧ポイントは完全バックアップです。 以後のバックアップでは、増分コピーを作成します。

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

## <a name="delete-the-recovery-services-vault"></a>Recovery Services コンテナーを削除する

Recovery Services コンテナーを削除するには、まずコンテナーのすべての復旧ポイントを削除し、コンテナーの登録を解除する必要があります。 次のコマンドは、この手順を詳しく説明しています。 


```powershell
$Cont = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzureRmRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzureRmRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzureRmRecoveryServicesVault -Vault $vault1
```

## <a name="troubleshooting-errors"></a>エラーのトラブルシューティング
仮想マシンのバックアップ中に問題が発生した場合は、[Azure 仮想マシンのバックアップのトラブルシューティングに関する記事](backup-azure-vms-troubleshoot.md)をご覧ください。

## <a name="next-steps"></a>次のステップ
これでお使いの仮想マシンが保護されました。管理タスクおよび仮想マシンを復旧ポイントから復元する方法の詳細については、次の記事をご覧ください。

* バックアップ ポリシーを変更する場合は、「[AzureRM.RecoveryServices.Backup コマンドレットを使って仮想マシンをバックアップする](backup-azure-vms-automation.md#create-a-protection-policy)」をご覧ください。
* [仮想マシンの管理と監視](backup-azure-manage-vms.md)
* [仮想マシンの復元](backup-azure-arm-restore-vms.md)
