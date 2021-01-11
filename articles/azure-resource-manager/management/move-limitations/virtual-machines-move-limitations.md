---
title: 新しいサブスクリプションまたはリソース グループへ Azure VM を移動する
description: Azure Resource Manager を使用して、新しいリソース グループまたはサブスクリプションに仮想マシンを移動します。
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: d522eb4a6496bc2cc65b4937a19b9ac5228e7f2b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933241"
---
# <a name="move-guidance-for-virtual-machines"></a>仮想マシンの移動に関するガイダンス

この記事では、現在サポートされていないシナリオと、バックアップを利用した仮想マシンを移行する手順について説明します。

## <a name="scenarios-not-supported"></a>サポートされていないシナリオ

次のシナリオはまだサポートされていません。

* Standard SKU Load Balancer または Standard SKU パブリック IP を使用した仮想マシン スケール セットを移動することはできません。
* プランが添付された Marketplace リソースから作成された仮想マシンは、サブスクリプションの間で移動できません。 現在のサブスクリプションでの仮想マシンをプロビジョニング解除し、新しいサブスクリプションに再デプロイしてください。
* 仮想ネットワーク内のすべてのリソースを移動しない場合、既存の仮想ネットワーク内の仮想マシンを新しいサブスクリプションに移動することはできません。
* 低優先度の仮想マシンと低優先度の仮想マシン スケール セットは、リソース グループまたはサブスクリプション間で移動することはできません。
* 可用性セット内の仮想マシンを個別に移動することはできません。

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

キー コンテナーに統合されている仮想マシンを移動して、[Linux VM の Azure Disk Encryption](../../../virtual-machines/linux/disk-encryption-overview.md) または [Windows VM の Azure Disk Encryption](../../../virtual-machines/windows/disk-encryption-overview.md) を実装することはできません。 VM を移動するには、暗号化を無効にする必要があります。

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1
```

## <a name="virtual-machines-with-azure-backup"></a>Azure Backup を利用した仮想マシン

Azure Backup で構成された仮想マシンを移動するには、その復元ポイントをコンテナーから削除する必要があります。

仮想マシンに対して[論理的な削除](../../../backup/backup-azure-security-feature-cloud.md)が有効になっている場合は、その復元ポイントが保持されている間は仮想マシンを移動することはできません。 [論理的な削除を無効にする](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)か、復元ポイントの削除後、14 日間経過するまで待ってください。

### <a name="portal"></a>ポータル

1. バックアップを一時的に停止し、バックアップ データを保持します。
2. Azure Backup で構成された仮想マシンを移動するには、次の手順を実行します。

   1. 仮想マシンの場所を探します。
   2. 名前付けパターン `AzureBackupRG_<location of your VM>_1` を持つリソース グループを探します。 たとえば、*AzureBackupRG_westus2_1* となります
   3. Azure portal で、 **[非表示の型の表示]** をオンにします。
   4. `AzureBackup_<name of your VM that you're trying to move>_###########` という名前パターンを持つ、**Microsoft.Compute/restorePointCollections** 型のリソースを検索します。
   5. このリソースを削除します。 この操作では、インスタント復旧ポイントのみが削除され、コンテナー内のバックアップされたデータは削除されません。
   6. 削除操作が完了したら、仮想マシンを移動できます。

3. VM をターゲット リソース グループに移動します。
4. バックアップを再開します。

### <a name="powershell"></a>PowerShell

* 仮想マシンの場所を探します。
* 名前付けパターン `AzureBackupRG_<location of your VM>_1` (たとえば、AzureBackupRG_westus2_1) を持つリソース グループを探します
* PowerShell の場合、`Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`コマンドレットを使用します
* 名前付けパターン `AzureBackup_<name of your VM that you're trying to move>_###########` を持つタイプ `Microsoft.Compute/restorePointCollections` のリソースを探します
* このリソースを削除します。 この操作では、インスタント復旧ポイントのみが削除され、コンテナー内のバックアップされたデータは削除されません。

### <a name="azure-cli"></a>Azure CLI

* 仮想マシンの場所を探します。
* 名前付けパターン `AzureBackupRG_<location of your VM>_1` (たとえば、AzureBackupRG_westus2_1) を持つリソース グループを探します
* CLI の場合、`az resource list -g AzureBackupRG_<location of your VM>_1`を使用します
* 名前付けパターン `AzureBackup_<name of your VM that you're trying to move>_###########` を持つタイプ `Microsoft.Compute/restorePointCollections` のリソースを探します
* このリソースを削除します。 この操作では、インスタント復旧ポイントのみが削除され、コンテナー内のバックアップされたデータは削除されません。

## <a name="next-steps"></a>次のステップ

* リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../move-resource-group-and-subscription.md)」を参照してください。

* バックアップのための Recovery Service コンテナーの移動については、「[Recovery Services の制限事項](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)」を参照してください。
