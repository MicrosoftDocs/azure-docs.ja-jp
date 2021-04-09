---
title: リソース グループを管理する - Azure CLI
description: Azure CLI を使用して、Azure Resource Manager 経由でリソース グループを管理します。 リソース グループを作成、一覧表示、および削除する方法を示します。
author: mumian
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: e28b66844eaa0b73c2654175dea2e31d3cd75f5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172098"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Azure CLI を使用した Azure Resource Manager リソース グループの管理

[Azure Resource Manager](overview.md) で Azure CLI を使用して Azure リソース グループを管理する方法について説明します。 Azure リソースの管理については、「[Manage Azure resources by using Azure CLI (Azure CLI を使用した Azure リソースの管理)](manage-resources-cli.md)」をご覧ください。

リソース グループの管理に関するその他の記事は次のとおりです。

- [Azure portal を使用した Azure リソース グループの管理](manage-resources-portal.md)
- [Azure PowerShell を使用した Azure リソース グループの管理](manage-resources-powershell.md)に関する記事

## <a name="what-is-a-resource-group"></a>リソース グループとは

リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 リソース グループには、ソリューションのすべてのリソースか、グループとして管理したいリソースのみを含めることができます。 組織のニーズに合わせてリソースをリソース グループに割り当てる方法を指定してください。 通常は、同じライフサイクルを共有するリソースを同じリソース グループに追加して、グループとして簡単にデプロイ、更新、および削除できるようにします。

リソース グループには、リソースについてのメタデータが格納されます。 そのため、リソース グループの場所を指定するとき、このメタデータが格納される場所を指定することになります。 コンプライアンス上の理由から、データは特定のリージョンに格納されるようにする必要があります。

リソース グループには、リソースについてのメタデータが格納されます。 リソース グループの場所を指定するとき、このメタデータが格納される場所を指定することになります。

## <a name="create-resource-groups"></a>リソース グループを作成する

次の CLI コマンドでは、リソース グループを作成します。

```azurecli-interactive
az group create --name demoResourceGroup --location westus
```

## <a name="list-resource-groups"></a>リソース グループの一覧を表示する

次の CLI スクリプトでは、サブスクリプションのリソース グループを一覧表示します。

```azurecli-interactive
az group list
```

1 つのリソース グループを取得するには、次のスクリプトを使用します。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>リソース グループを削除する

次の CLI スクリプトでは、リソース グループを削除します。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Azure Resource Manager によってリソースの削除の順序が決定される方法の詳細については、「[Azure Resource Manager によるリソース グループの削除](delete-resource-group.md)」を参照してください。

## <a name="deploy-resources-to-an-existing-resource-group"></a>リソースを既存のリソース グループにデプロイする

「[Deploy resources to an existing resource group (リソースを既存のリソース グループにデプロイする)](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group)」をご覧ください。

## <a name="deploy-a-resource-group-and-resources"></a>リソース グループとリソースをデプロイする

Resource Manager テンプレートを使用して、リソース グループを作成し、そのグループにリソースをデプロイできます。 詳しくは、「[リソース グループを作成してリソースをデプロイする](../templates/deploy-to-subscription.md#resource-groups)」をご覧ください。

## <a name="redeploy-when-deployment-fails"></a>デプロイに失敗したときに再デプロイする

この機能は、"*エラー時のロールバック*" とも呼ばれます。 詳細については、「[デプロイに失敗したときに再デプロイする](../templates/rollback-on-error.md)」を参照してください。

## <a name="move-to-another-resource-group-or-subscription"></a>別のリソース グループまたはサブスクリプションに移動する

グループ内のリソースを別のリソース グループに移動できます。 詳細については、「[Move resources (リソースを移動する)](manage-resources-cli.md#move-resources)」をご覧ください。

## <a name="lock-resource-groups"></a>リソース グループをロックする

ロックすることで、組織内の他のユーザーが重要なリソース (Azure サブスクリプション、リソース グループ、リソースなど) を誤って削除または変更することを防ぎます。

次のスクリプトでは、リソース グループをロックして、そのリソース グループを削除できないようにします。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName
```

次のスクリプトでは、リソース グループのすべてのロックを取得します。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName
```

次のスクリプトではロックを削除します。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

詳細については、[「Azure Resource Manager によるリソースのロック」](lock-resources.md)を参照してください。

## <a name="tag-resource-groups"></a>リソース グループにタグを適用する

リソース グループやリソースにタグを適用して、アセットを論理的に整理できます。 詳細については、[タグを使用した Azure リソースの整理](tag-resources.md#azure-cli) に関する記事をご覧ください。

## <a name="export-resource-groups-to-templates"></a>リソース グループをテンプレートにエクスポートする

リソース グループを正常に設定したら、リソース グループの Resource Manager テンプレートを表示できます。 テンプレートのエクスポートには、2 つの利点があります。

- テンプレートに完全なインフラストラクチャが含まれているため、ソリューションの将来のデプロイを自動化します。
- ソリューションを表す JavaScript Object Notation (JSON) を調べることで、テンプレートの構文を確認できます。

リソース グループ内のすべてのリソースをエクスポートするには、[az group export](/cli/azure/group#az_group_export) を使用して、リソース グループ名を指定します。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName
```

このスクリプトでは、コンソールにテンプレートを表示します。 JSON をコピーし、ファイルとして保存します。

リソース グループ内のすべてのリソースをエクスポートする代わりに、どのリソースをエクスポートするかを選択できます。

1 つのリソースをエクスポートするには、そのリソースの ID を渡します。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $resourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az group export --resource-group $resourceGroupName --resource-ids $storageAccount
```

複数のリソースをエクスポートするには、スペースで区切られたリソース ID を渡します。 すべてのリソースをエクスポートするには、この引数を指定したり、"*" を指定したりしないでください。

```azurecli-interactive
az group export --resource-group <resource-group-name> --resource-ids $storageAccount1 $storageAccount2
```

テンプレートをエクスポートするときには、テンプレート内でパラメーターを使用するかどうかを指定できます。 既定では、リソース名用のパラメーターが含められますが、既定値はありません。 そのパラメーターの値は、デプロイ時に渡す必要があります。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "type": "String"
  }
}
```

リソース内では、パラメーターは名前用に使用されます。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

テンプレートをエクスポートするときに `--include-parameter-default-value` パラメーターを使用する場合、テンプレート パラメーターには、現在の値に設定される既定値が含められます。 その既定値を使用することも、異なる値を渡して既定値を上書きすることもできます。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

テンプレートをエクスポートするときに `--skip-resource-name-params` パラメーターを使用する場合、リソース名用のパラメーターはテンプレートに含められません。 代わりに、リソース名はリソースに対して直接、現在の値に設定されます。 この名前はデプロイ時にカスタマイズすることはできません。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

テンプレートのエクスポート機能は、Azure Data Factory リソースのエクスポートをサポートしていません。 Data Factory リソースをエクスポートする方法については、「[Azure Data Factory のデータ ファクトリをコピーまたは複製する](../../data-factory/copy-clone-data-factory.md)」を参照してください。

クラシック デプロイ モデルを使用して作成されたリソースをエクスポートするには、[Resource Manager デプロイ モデルに移行する](../../virtual-machines/migration-classic-resource-manager-overview.md)必要があります。

詳細については、「[Azure portal のテンプレートへの単一および複数リソースのエクスポート](../templates/export-template-portal.md)」を参照してください。

## <a name="manage-access-to-resource-groups"></a>リソース グループへのアクセスを管理する

[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) は、Azure のリソースへのアクセスを管理する方法です。 詳細については、「[Azure CLI を使用して Azure ロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-cli.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- Azure Resource Manager については、「[Azure Resource Manager の概要](overview.md)」を参照してください。
- Resource Manager テンプレートの構文については、「[Azure Resource Manager テンプレートの構造と構文の詳細](../templates/template-syntax.md)」を参照してください。
- テンプレートを開発する方法については、[ステップバイステップのチュートリアル](../index.yml)のページをご覧ください。
- Azure Resource Manager テンプレートのスキーマを表示するには、[テンプレート リファレンス](/azure/templates/)のページをご覧ください。