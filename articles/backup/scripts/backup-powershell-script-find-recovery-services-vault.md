---
title: PowerShell スクリプト - ストレージ アカウントのコンテナーを見つける
description: Azure PowerShell スクリプトを使用して、お使いのストレージ アカウントが登録されている Recovery Services コンテナーを見つける方法について説明します。
ms.topic: sample
ms.date: 1/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 40859c1ea05210d27fcdcf33ba9d4f961965ea22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89075700"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>ストレージ アカウントが登録されている Recovery Services コンテナーを見つけるための PowerShell スクリプト

このスクリプトは、お使いのストレージ アカウントが登録されている Recovery Services コンテナーを見つけるのに役立ちます。

## <a name="sample-script"></a>サンプル スクリプト

```powershell
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId
    )

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$vaults = Get-AzRecoveryServicesVault
$found = $false
foreach($vault in $vaults)
{
  Write-Verbose "Checking vault: $($vault.Id)" -Verbose
  
  $containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -FriendlyName $StorageAccountName -ResourceGroupName $ResourceGroupName -VaultId $vault.Id -Status Registered
  
  if($containers -ne $null)
  {
    $found = $True
    Write-Information "Found Storage account $StorageAccountName registered in vault: $($vault.Id)" -InformationAction Continue
    break;
  }
}

if(!$found)
{
     Write-Information "Storage account: $StorageAccountName is not registered in any vault of this subscription" -InformationAction Continue
}
```

## <a name="how-to-execute-the-script"></a>スクリプトを実行する方法

1. 上記のスクリプトを任意の名前でお使いのマシンに保存します。 この例では、*FindRegisteredStorageAccount.ps1* として保存されています。
2. 次のパラメーターを指定して、スクリプトを実行します。

    * **-ResourceGroupName** - ストレージ アカウントのリソース グループ
    * **-StorageAccountName** - ストレージ アカウント名
    * **-SubscriptionID** - ストレージ アカウントが存在するサブスクリプションの ID。

次の例では、*afsaccount* ストレージ アカウントが登録されている Recovery Services コンテナーを見つけます。

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Output

出力には、ストレージ アカウントが登録されている Recovery Services コンテナーの完全なパスが表示されます。 出力例を次に示します。

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>次のステップ

[Azure portal から Azure ファイル共有をバックアップする](../backup-afs.md)方法を学習します
