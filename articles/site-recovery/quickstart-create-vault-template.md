---
title: Azure Resource Manager テンプレートを使用して Azure Recovery Services コンテナーを作成するためのクイックスタート。
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure Recovery Services コンテナーを作成する方法について説明します。
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/29/2020
ms.openlocfilehash: 62bfe71c397367282c696eecdf55ab9c900c69a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92745879"
---
# <a name="quickstart-create-a-recovery-services-vault-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して Recovery Services コンテナーを作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Recovery Services コンテナーを設定する方法について説明します。 [Azure Site Recovery](site-recovery-overview.md) サービスは、計画された停止や計画外の停止の際にビジネス アプリがオンライン状態を保てるよう、ビジネス継続性とディザスター リカバリー (BCDR) 戦略に貢献します。 Site Recovery は、レプリケーション、フェールオーバー、フェールバックなど、オンプレミスのマシンと Azure Virtual Machines (VM) のディザスター リカバリーを管理します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

アクティブな Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/)からのものです。

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json":::

テンプレートでは、次の 2 つの Azure リソースが定義されています。

- [Microsoft.RecoveryServices コンテナー](/azure/templates/microsoft.recoveryservices/vaults): コンテナーを作成します。
- [Microsoft.RecoveryServices/vaults/backupstorageconfig](/rest/api/backup/backupresourcestorageconfigs): コンテナーのバックアップ冗長設定を構成します。

テンプレートには、コンテナーのバックアップ構成のためのオプション パラメーターが含まれます。 ストレージ冗長設定は、ローカル冗長ストレージ (LRS) または geo 冗長ストレージ (GRS) です。 詳細については、「[ストレージ冗長性の設定](../backup/backup-create-rs-vault.md#set-storage-redundancy)」を参照してください。

その他の Azure Recovery Services テンプレートについては、「[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular)」を参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

テンプレートをデプロイするには、**サブスクリプション**、**リソース グループ**、**コンテナー名** が必要です。

1. Azure にサインインしてテンプレートを開くには、 **[Azure へのデプロイ]** イメージを選択します。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. 次の値を選択または入力します。

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="Recovery Services コンテナーを作成するためのテンプレート。":::

   - **サブスクリプション**: Azure サブスクリプションを選択します。
   - **リソース グループ**: 既存のグループを選択するか、 **[新規作成]** を選択してグループを追加します。
   - **場所**: 既定でリソース グループの場所になります。リソース グループの選択後は利用できなくなります。
   - **Vault Name**:コンテナーの名前を指定します。
   - **Change Storage Type (ストレージの種類の変更)** : 既定値は **false** です。 **true** は、コンテナーのストレージの種類を変更する必要がある場合のみ選択してください。
   - **Vault Storage Type (コンテナー ストレージの種類)** : 既定値は **GloballyRedundant** です。 ストレージの種類が **true** に設定されている場合は、 **[LocallyRedundant]** を選択します。
   - **場所**: `[resourceGroup().location]` 関数によって、既定でリソース グループの場所が選択されます。 場所を変更するには、「**westus**」などの値を入力してください。
   - **[上記の使用条件に同意する]** のチェック ボックスをオンにします。

1. コンテナーのデプロイを開始するには、 **[購入]** ボタンを選択します。 デプロイの成功後、通知が表示されます。

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="コンテナーのデプロイに成功。":::

## <a name="validate-the-deployment"></a>デプロイの検証

コンテナーが作成されたことを確認するには、Azure CLI または Azure PowerShell を使用します。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the vault name:" &&
read vaultName &&
az backup vault show --name $vaultName --resource-group $resourceGroupName &&
az backup vault backup-properties show --name $vaultName --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
$vaultName = Read-Host -Prompt "Enter the vault name"
$vaultBackupConfig = Get-AzRecoveryServicesVault -Name $vaultName
Get-AzRecoveryServicesVault -ResourceGroupName $resouceGroupName -Name $vaultName
Get-AzRecoveryServicesBackupProperty -Vault $vaultBackupConfig
Write-Host "Press [ENTER] to continue..."
```

---

次の出力は、コンテナーの情報を抜粋したものです。

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
"id": "/subscriptions/<Subscription Id>/resourceGroups/myResourceGroup
         /providers/Microsoft.RecoveryServices/vaults/myVault"
"location": "eastus"
"name": "myVault"
"resourceGroup": "myResourceGroup"

"storageModelType": "GeoRedundant"
"storageType": "GeoRedundant"
"type": "Microsoft.RecoveryServices/vaults/backupstorageconfig"
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : myVault
Type              : Microsoft.RecoveryServices/vaults
Location          : eastus
ResourceGroupName : myResourceGroup
SubscriptionId    : <Subscription Id>

BackupStorageRedundancy
-----------------------
GeoRedundant
```

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

新しいリソースを使用する予定がある場合は、対処は必要ありません。 それ以外の場合は、このクイックスタートで作成したリソース グループとコンテナーを削除してください。 リソース グループとそのリソースを削除するには、Azure CLI または Azure PowerShell を使用します。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resouceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Recovery Services コンテナーを作成しました。 ディザスター リカバリーの詳細については、次のクイックスタート記事に進んでください。

> [!div class="nextstepaction"]
> [ディザスター リカバリーを設定する](azure-to-azure-quickstart.md)
