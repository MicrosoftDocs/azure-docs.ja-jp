---
title: PowerShell のサンプル スクリプト - Azure VM のバックアップ
description: この記事では、Azure PowerShell のサンプル スクリプトを使用して Azure 仮想マシンをバックアップする方法について説明します。
ms.topic: sample
ms.date: 03/05/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 6e3af44284acdc96f6f0197c4153c6f7538570d2
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069488"
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
| [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | バックアップを格納する Recovery Services コンテナーを作成します。 |
| [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | Recovery Services コンテナーでバックアップ ストレージのプロパティを設定します。 |
| [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Recovery Services コンテナーでスケジュール ポリシーと保有ポリシーを使用して、保護ポリシーを作成します。 |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | サービス プリンシパルに暗号化キーへのアクセス権を付与するために、Key Vault に対するアクセス許可を設定します。 |
| [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | 指定した Backup 保護ポリシーで項目のバックアップを有効にします。 |
| [Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| 既存の Backup 保護ポリシーを変更します。 |
| [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | バックアップ スケジュールに関連付けられていない、保護された Azure Backup 項目のバックアップを開始します。 |
| [Wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Azure Backup ジョブが終了するのを待機します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/new-azureps-module-az)を参照してください。
