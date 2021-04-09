---
title: 読み取りレプリカの管理 - Azure CLI、REST API - Azure Database for MySQL
description: Azure CLI または REST API を使用して Azure Database for MySQL の読み取りレプリカを設定し、管理する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1a5bc9638e2e6eeff8f2176247f579b64beede90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540214"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli-and-rest-api"></a>Azure CLI と REST API を使用して Azure Database for MySQL の読み取りレプリカを作成および管理する方法

この記事では、Azure CLI と REST API を使用して Azure Database for MySQL サービスの読み取りレプリカを作成および管理する方法を説明します。 読み取りレプリカの詳細については、[概要](concepts-read-replicas.md)を参照してください。

## <a name="azure-cli"></a>Azure CLI
Azure CLI を使用して、読み取りレプリカを作成して管理できます。

### <a name="prerequisites"></a>前提条件

- [Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)
- ソース サーバーとして使用される [Azure Database for MySQL サーバー](quickstart-create-mysql-server-database-using-azure-portal.md)。 

> [!IMPORTANT]
> 読み取りレプリカ機能は、汎用とメモリ最適化のどちらかの価格レベルにおける Azure Database for MySQL サーバーにのみ使用可能です。 ソース サーバーがこれらの価格レベルのいずれであるかを確認します。

### <a name="create-a-read-replica"></a>読み取りレプリカを作成します

> [!IMPORTANT]
> 既存のレプリカがないソースのレプリカを作成すると、ソースは最初に、レプリケーションの準備をするために再起動します。 これを考慮して、これらの操作はオフピーク期間中に実行してください。

読み取りレプリカ サーバーは、次のコマンドを使用して作成できます。

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

`az mysql server replica create` コマンドには、次のパラメーターが必要です。

| 設定 | 値の例 | 説明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  レプリカ サーバーを作成するリソース グループ。  |
| name | mydemoreplicaserver | 作成する新しいレプリカ サーバーの名前。 |
| source-server | mydemoserver | レプリケート元の既存のソース サーバーの名前または ID。 |

リージョンをまたがる読み取りレプリカを作成するには、`--location` パラメーターを使用します。 次の CLI の例では、米国西部にレプリカを作成します。

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> レプリカを作成できるリージョンの詳細については、[読み取りレプリカの概念に関する記事](concepts-read-replicas.md)を参照してください。 

> [!NOTE]
> マスターと同じサーバー構成で、読み取りレプリカが作成されます。 作成された後、レプリカ サーバーの構成を変更できます。 レプリカをマスターと確実に維持できるようにするために、レプリカ サーバーの構成をソースと同じかそれ以上の値にしておくことをお勧めします。


### <a name="list-replicas-for-a-source-server"></a>ソース サーバーのレプリカを一覧表示する

特定のソース サーバーのレプリカをすべて表示するには、次のコマンドを実行します。 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

`az mysql server replica list` コマンドには、次のパラメーターが必要です。

| 設定 | 値の例 | 説明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  レプリカ サーバーを作成するリソース グループ。  |
| server-name | mydemoserver | ソース サーバーの名前または ID。 |

### <a name="stop-replication-to-a-replica-server"></a>レプリカ サーバーへのレプリケーションを停止します。

> [!IMPORTANT]
> サーバーへのレプリケーションの停止は、元に戻すことができません。 ソースとレプリカの間のレプリケーションを停止すると、元に戻すことはできません。 レプリカ サーバーはスタンドアロン サーバーになり、読み取りと書き込みをサポートするようになります。 このサーバーをもう一度レプリカにすることはできません。

読み取りレプリカ サーバーへのレプリケーションは、次のコマンドを使用して停止できます。

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

`az mysql server replica stop` コマンドには、次のパラメーターが必要です。

| 設定 | 値の例 | 説明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  レプリカ サーバーが存在するリソース グループ。  |
| name | mydemoreplicaserver | レプリケーションを停止するレプリカ サーバーの名前。 |

### <a name="delete-a-replica-server"></a>レプリカ サーバーを削除します

読み取りレプリカ サーバーの削除は、 **[az mysql server delete](/cli/azure/mysql/server)** コマンドを使用して行うことができます。

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>ソース サーバーの削除

> [!IMPORTANT]
> ソース サーバーを削除すると、すべてのレプリカ サーバーへのレプリケーションを停止し、ソース サーバー自体を削除します。 これでレプリカ サーバーは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。

ソース サーバーを削除するには、 **[az mysql server delete](/cli/azure/mysql/server)** コマンドを実行します。

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```


## <a name="rest-api"></a>REST API
[Azure REST API](/rest/api/azure/) を使用して、読み取りレプリカを作成して管理できます。

### <a name="create-a-read-replica"></a>読み取りレプリカを作成します
[作成 API](/rest/api/mysql/servers/create) を使用して、読み取りレプリカを作成できます。

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> レプリカを作成できるリージョンの詳細については、[読み取りレプリカの概念に関する記事](concepts-read-replicas.md)を参照してください。 

汎用またはメモリ最適化ソース サーバーで `azure.replication_support` パラメーターを **[レプリカ]** に設定しておらず、サーバーを再起動していない場合は、エラーを受け取ります。 レプリカを作成する前に、この 2 つの手順を済ませておいてください。

レプリカは、マスターと同じコンピューティングとストレージの設定を使用して作成されます。 レプリカが作成されたら、ソース サーバーとは独立していくつかの設定 (コンピューティング世代、仮想コア、ストレージ、バックアップ保持期間) を変更できます。 価格レベルも独立して変更できます (Basic レベルへの変更や Basic レベルからの変更を除く)。


> [!IMPORTANT]
> ソース サーバーの設定が新しい値に更新される前に、レプリカの設定をそれと同等以上の値に更新します。 このアクションは、レプリカがマスターに対するあらゆる変更に追従できるようにするのに役立ちます。

### <a name="list-replicas"></a>レプリカの一覧表示
[レプリカ一覧表示 API](/rest/api/mysql/replicas/listbyserver) を使用して、ソース サーバーのレプリカの一覧を表示できます。

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>レプリカ サーバーへのレプリケーションを停止します。
[更新 API](/rest/api/mysql/servers/update) を使用して、ソース サーバーと読み取りレプリカの間のレプリケーションを停止できます。

ソース サーバーと読み取りレプリカへのレプリケーションを停止した後、それを元に戻すことはできません。 読み取りレプリカは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。 スタンドアロン サーバーをもう一度レプリカにすることはできません。

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-source-or-replica-server"></a>ソースまたはレプリカ サーバーの削除
ソースまたはレプリカ サーバーを削除するには、[削除 API](/rest/api/mysql/servers/delete) を使用します。

ソース サーバーを削除すると、すべての読み取りレプリカへのレプリケーションが停止されます。 読み取りレプリカは、読み取りと書き込みの両方をサポートするようになったスタンドアロン サーバーになります。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>次のステップ

- [レプリカの読み取り](concepts-read-replicas.md) の詳細を確認する