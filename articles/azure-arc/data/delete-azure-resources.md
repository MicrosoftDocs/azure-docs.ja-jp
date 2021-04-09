---
title: Azure からのリソースの削除
description: Azure からのリソースの削除
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 60c5ddcc67db6e4a0649458cfbd5c2949aa9a32a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202044"
---
# <a name="delete-resources-from-azure"></a>Azure からのリソースの削除

> [!NOTE]
>  この記事に記載されているリソースを削除するオプションは元に戻せません。

> [!NOTE]
>  現在、Azure Arc 対応データ サービス用に提供されている接続モードは間接接続モードのみであるため、Kubernetes からインスタンスを削除しても Azure から削除されることはなく、Azure からインスタンスを削除しても Kubernetes から削除されることはありません。  現時点では、リソースの削除は 2 段階のプロセスであり、今後改善される予定です。  将来的には、Kubernetes が信頼できるソースとなり、Azure はそれを反映するように更新されます。

場合によっては、Azure Resource Manager (ARM) で Azure Arc 対応 データ サービスのリソースを手動で削除する必要があります。  これらのリソースは、次のいずれかのオプションを使用して削除できます。

- [Azure からのリソースの削除](#delete-resources-from-azure)
  - [リソース グループ全体の削除](#delete-an-entire-resource-group)
  - [リソース グループ内の特定のリソースの削除](#delete-specific-resources-in-the-resource-group)
  - [Azure CLI を使用したリソースの削除](#delete-resources-using-the-azure-cli)
    - [Azure CLI を使用した SQL マネージド インスタンス リソースの削除](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [Azure CLI を使用した PostgreSQL Hyperscale サーバー グループ リソースの削除](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Azure CLI を使用した Azure Arc データ コントローラー リソースの削除](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Azure CLI を使用したリソース グループの削除](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>リソース グループ全体の削除
Azure Arc 対応データ サービスに特定の専用リソース グループを使用していて、そのリソース グループ内の *すべてのもの* を削除したい場合、リソース グループを削除すれば、その中のすべてのものが削除され ます。  

Azure portal でリソース グループを削除するには、次の操作を行います。

- Azure Arc 対応データ サービスのリソースが作成されている Azure portal のリソース グループを参照します。
- **[リソース グループの削除]** ボタンをクリックします。
- リソース グループ名を入力して削除を確認し、 **[削除]** をクリックします。

## <a name="delete-specific-resources-in-the-resource-group"></a>リソース グループ内の特定のリソースの削除

Azure portal でリソース グループ内の特定の Azure Arc 対応データ サービスのリソースを削除するには、次の操作を行います。

- Azure Arc 対応データ サービスのリソースが作成されている Azure portal のリソース グループを参照します。
- 削除するリソースをすべて選択します。
- [削除] ボタンをクリックします。
- 「はい」と入力して削除を確認し、 **[削除]** をクリックします。

## <a name="delete-resources-using-the-azure-cli"></a>Azure CLI を使用したリソースの削除

Azure CLI を使用して、特定の Azure Arc 対応データ サービスのリソースを削除できます。

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>Azure CLI を使用した SQL マネージド インスタンス リソースの削除

Azure CLI を使用して Azure から SQL マネージド インスタンスのリソースを削除するには、以下のコマンドのプレースホルダー値を置き換えて、そのコマンドを実行します。

```azurecli
az resource delete --name <sql instance name> --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>Azure CLI を使用した PostgreSQL Hyperscale サーバー グループ リソースの削除

Azure CLI を使用して Azure から PostgreSQL Hyperscale サーバー グループのリソースを削除するには、以下のコマンドのプレースホルダー値を置き換えて、そのコマンドを実行します。

```azurecli
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureArcData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureArcData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Azure CLI を使用した Azure Arc データ コントローラー リソースの削除

> [!NOTE]
> Azure Arc データ コントローラーを削除する前に、それによって管理されているデータベース インスタンスのリソースをすべて削除する必要があります。

Azure CLI を使用して Azure から Azure Arc データ コントローラーを削除するには、以下のコマンドのプレースホルダー値を置き換えて、そのコマンドを実行します。

```azurecli
az resource delete --name <data controller name> --resource-type Microsoft.AzureArcData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureArcData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Azure CLI を使用したリソース グループの削除

Azure CLI を使用して、[リソース グループを削除する](../../azure-resource-manager/management/delete-resource-group.md)こともできます。