---
title: Azure PowerShell のサンプル スクリプト - Azure 仮想マシンのバックアップ | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - Azure 仮想マシンのバックアップ
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 03/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 84581dd4dfd1cc993476e0e85f804f32d28e8ab4
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467171"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>PowerShell を使用して、暗号化された Azure 仮想マシンをバックアップする

このスクリプトでは、暗号化された Azure 仮想マシン用に geo 冗長ストレージ (GRS) で Recovery Services コンテナーを作成します。 既定の保護ポリシーはコンテナーに適用されます。 ポリシーによって仮想マシンの毎日のバックアップが生成され、バックアップはそれぞれ 30 日間保持されます。 また、このスクリプトによって、仮想マシンの最初の復旧ポイントがトリガーされ、その復旧ポイントは 365 日間保持されます。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。


| command | メモ | 
|---|---| 
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 | 
| [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | バックアップを格納する Recovery Services コンテナーを作成します。 | 
| [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | Recovery Services コンテナーでバックアップ ストレージのプロパティを設定します。 | 
| [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Recovery Services コンテナーでスケジュール ポリシーと保有ポリシーを使用して、保護ポリシーを作成します。 | 
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | サービス プリンシパルに暗号化キーへのアクセス権を付与するために、Key Vault に対するアクセス許可を設定します。 | 
| [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | 指定した Backup 保護ポリシーで項目のバックアップを有効にします。 | 
| [Set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| 既存の Backup 保護ポリシーを変更します。 | 
| [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | バックアップ スケジュールに関連付けられていない、保護された Azure Backup 項目のバックアップを開始します。 |
| [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Azure Backup ジョブが終了するのを待機します。 | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 | 

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)を参照してください。

