---
title: 新しいサブスクリプションまたはリソース グループへ Azure VM を移動する
description: Azure Resource Manager を使用して、新しいリソース グループまたはサブスクリプションに仮想マシンを移動します。
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: ad7023f309f1ca948711eaa9bdf3867d2ef7a6f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100104911"
---
# <a name="move-guidance-for-virtual-machines"></a>仮想マシンの移動に関するガイダンス

この記事では、現在サポートされていないシナリオと、バックアップを利用した仮想マシンを移行する手順について説明します。

## <a name="scenarios-not-supported"></a>サポートされていないシナリオ

次のシナリオはまだサポートされていません。

* Standard SKU Load Balancer または Standard SKU パブリック IP を使用した仮想マシン スケール セットを移動することはできません。
* 仮想ネットワーク内のすべてのリソースを移動しない場合、既存の仮想ネットワーク内の仮想マシンを新しいサブスクリプションに移動することはできません。
* プランが添付された Marketplace リソースから作成された仮想マシンは、サブスクリプションの間で移動できません。 考えられる回避策については、「[Marketplace のプランを使用した仮想マシン」](#virtual-machines-with-marketplace-plans)を参照してください。
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

## <a name="virtual-machines-with-marketplace-plans"></a>Marketplace のプランを使用した仮想マシン

プランが添付された Marketplace リソースから作成された仮想マシンは、サブスクリプションの間で移動できません。 この制限を回避するには、現在のサブスクリプションでの仮想マシンのプロビジョニングを解除し、新しいサブスクリプションに再デプロイします。 次の手順により、新しいサブスクリプションで仮想マシンを再作成できます。 ただし、一部のシナリオでは機能しない可能性があります。 そのプランが Marketplace で使用できなくなった場合、これらの手順は機能しなくなります。

1. プランに関する情報をコピーします。

1. OS ディスクを変換先サービスに複製するか、変換元サービスから仮想マシンを削除した後に元のディスクを移動します。

1. 変換先サービスで、ご利用のプランの Marketplace 使用条件に同意します。 次の PowerShell コマンドを実行して、使用条件に同意することができます。

   ```azurepowershell
   Get-AzMarketplaceTerms -Publisher {publisher} -Product {product/offer} -Name {name/SKU} | Set-AzMarketplaceTerms -Accept
   ```

   または、ポータルを使用して、そのプランを使用した仮想マシンの新しいインスタンスを作成することもできます。 新しいサブスクリプションの使用条件に同意した後に、その仮想マシンを削除することができます。

1. 変換先サービスで、PowerShell、CLI、または Azure Resource Manager テンプレートを使用して、複製された OS ディスクから仮想マシンを再作成します。 ディスクに接続されているマーケットプレース プランを含めます。 プランに関する情報は、新しいサブスクリプションで購入したプランと一致している必要があります。

## <a name="virtual-machines-with-azure-backup"></a>Azure Backup を利用した仮想マシン

Azure Backup で構成された仮想マシンを移動するには、その復元ポイントをコンテナーから削除する必要があります。

仮想マシンに対して[論理的な削除](../../../backup/soft-delete-virtual-machines.md)が有効になっている場合は、その復元ポイントが保持されている間は仮想マシンを移動することはできません。 [論理的な削除を無効にする](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)か、復元ポイントの削除後、14 日間経過するまで待ってください。

### <a name="portal"></a>ポータル

1. バックアップを一時的に停止し、バックアップ データを保持します。
2. Azure Backup で構成された仮想マシンを移動するには、次の手順を実行します。

   1. 仮想マシンの場所を探します。
   2. 名前付けパターン `AzureBackupRG_<VM location>_1` を持つリソース グループを探します。 たとえば、名前は *AzureBackupRG_westus2_1* の形式になります。
   3. Azure portal で、 **[非表示の型の表示]** をオンにします。
   4. `AzureBackup_<VM name>_###########` という名前パターンを持つ、**Microsoft.Compute/restorePointCollections** 型のリソースを検索します。
   5. このリソースを削除します。 この操作では、インスタント復旧ポイントのみが削除され、コンテナー内のバックアップされたデータは削除されません。
   6. 削除操作が完了したら、仮想マシンを移動できます。

3. VM をターゲット リソース グループに移動します。
4. バックアップを再開します。

### <a name="powershell"></a>PowerShell

1. 仮想マシンの場所を探します。

1. 名前付けパターンが `AzureBackupRG_<VM location>_1` のリソース グループを探します。 たとえば、`AzureBackupRG_westus2_1` などです。

1. 仮想マシンを 1 つだけ移動する場合は、その仮想マシンの復元ポイント コレクションを取得します。

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -name AzureBackup_<VM name>* -ResourceType Microsoft.Compute/restorePointCollections
   ```

   このリソースを削除します。 この操作では、インスタント復旧ポイントのみが削除され、コンテナー内のバックアップされたデータは削除されません。

   ```azurepowershell-interactive
   Remove-AzResource -ResourceId $restorePointCollection.ResourceId -Force
   ```

1. この場所にある、バックアップを使用した仮想マシンをすべて移動する場合は、その仮想マシンの復元ポイント コレクションを取得します。

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

   各リソースを削除します。 この操作では、インスタント復旧ポイントのみが削除され、コンテナー内のバックアップされたデータは削除されません。

   ```azurepowershell-interactive
   foreach ($restorePoint in $restorePointCollection)
   {
     Remove-AzResource -ResourceId $restorePoint.ResourceId -Force
   }
   ```

### <a name="azure-cli"></a>Azure CLI

1. 仮想マシンの場所を探します。

1. 名前付けパターンが `AzureBackupRG_<VM location>_1` のリソース グループを探します。 たとえば、`AzureBackupRG_westus2_1` などです。

1. 仮想マシンを 1 つだけ移動する場合は、その仮想マシンの復元ポイント コレクションを取得します。

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections --query "[?starts_with(name, 'AzureBackup_<VM name>')].id" --output tsv)
   ```

   このリソースを削除します。 この操作では、インスタント復旧ポイントのみが削除され、コンテナー内のバックアップされたデータは削除されません。

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

1. この場所にある、バックアップを使用した仮想マシンをすべて移動する場合は、その仮想マシンの復元ポイント コレクションを取得します。

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections)
   ```

   各リソースを削除します。 この操作では、インスタント復旧ポイントのみが削除され、コンテナー内のバックアップされたデータは削除されません。

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

## <a name="next-steps"></a>次のステップ

* リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../move-resource-group-and-subscription.md)」を参照してください。

* バックアップのための Recovery Service コンテナーの移動については、「[Recovery Services の制限事項](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)」を参照してください。
