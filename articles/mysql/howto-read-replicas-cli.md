---
title: Azure Database for MySQL の読み取りレプリカの作成と管理 - Azure CLI、REST API
description: この記事では、Azure CLI と REST API を使用して Azure Database for MySQL の読み取りレプリカを設定して管理する方法について説明します
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/14/2019
ms.openlocfilehash: 5bec4e7284e78506372d395bf022055fa31998e3
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993549"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli-and-rest-api"></a>Azure CLI と REST API を使用して Azure Database for MySQL の読み取りレプリカを作成および管理する方法

この記事では、Azure CLI と REST API を使用して Azure Database for MySQL サービスの読み取りレプリカを作成および管理する方法を説明します。 読み取りレプリカの詳細については、[概要](concepts-read-replicas.md)を参照してください。

## <a name="azure-cli"></a>Azure CLI
Azure CLI を使用して、読み取りレプリカを作成して管理できます。

### <a name="prerequisites"></a>前提条件

- [Azure CLI 2.0 のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- マスター サーバーとして使用される [Azure Database for MySQL サーバー ](quickstart-create-mysql-server-database-using-azure-portal.md)。 

> [!IMPORTANT]
> 読み取りレプリカ機能は、汎用とメモリ最適化のどちらかの価格レベルにおける Azure Database for MySQL サーバーにのみ使用可能です。 マスター サーバーがこれらの価格レベルのいずれかを確認します。

### <a name="create-a-read-replica"></a>読み取りレプリカを作成します

読み取りレプリカ サーバーは、次のコマンドを使用して作成できます。

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

`az mysql server replica create` コマンドには、次のパラメーターが必要です。

| Setting | 値の例 | 説明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  レプリカ サーバーを作成するリソース グループ。  |
| 名前 | mydemoreplicaserver | 作成する新しいレプリカ サーバーの名前。 |
| source-server | mydemoserver | レプリケート元の既存のマスター サーバーの名前または ID。 |

リージョンをまたがる読み取りレプリカを作成するには、`--location` パラメーターを使用します。 次の CLI の例では、米国西部にレプリカを作成します。

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> レプリカを作成できるリージョンの詳細については、[読み取りレプリカの概念に関する記事](concepts-read-replicas.md)を参照してください。 

> [!NOTE]
> マスターと同じサーバー構成で、読み取りレプリカが作成されます。 作成された後、レプリカ サーバーの構成を変更できます。 レプリカをマスターと維持できるようにするために、レプリカ サーバーの構成をマスターと同じかそれ以上の値にしておくようお勧めします。


### <a name="list-replicas-for-a-master-server"></a>マスター サーバーのレプリカを一覧表示する

特定のマスター サーバーのすべてのレプリカを表示するには、次のコマンドを実行します。 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

`az mysql server replica list` コマンドには、次のパラメーターが必要です。

| Setting | 値の例 | 説明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  レプリカ サーバーを作成するリソース グループ。  |
| server-name | mydemoserver | マスター サーバーの名前または ID。 |

### <a name="stop-replication-to-a-replica-server"></a>レプリカ サーバーへのレプリケーションを停止します。

> [!IMPORTANT]
> サーバーへのレプリケーションの停止は、元に戻すことができません。 マスターとレプリカの間のレプリケーションを停止すると、取り消すことができません。 レプリカ サーバーはスタンドアロン サーバーになり、読み取りと書き込みをサポートするようになります。 このサーバーをもう一度レプリカにすることはできません。

読み取りレプリカ サーバーへのレプリケーションは、次のコマンドを使用して停止できます。

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

`az mysql server replica stop` コマンドには、次のパラメーターが必要です。

| Setting | 値の例 | 説明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  レプリカ サーバーが存在するリソース グループ。  |
| 名前 | mydemoreplicaserver | レプリケーションを停止するレプリカ サーバーの名前。 |

### <a name="delete-a-replica-server"></a>レプリカ サーバーを削除します

読み取りレプリカ サーバーの削除は、 **[az mysql server delete](/cli/azure/mysql/server)** コマンドを使用して行うことができます。

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>マスター サーバーを削除します

> [!IMPORTANT]
> マスター サーバーを削除すると、すべてのレプリカ サーバーへのレプリケーションを停止し、マスター サーバー自体を削除します。 これでレプリカ サーバーは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。

マスター サーバーを削除するには、 **[az mysql server delete](/cli/azure/mysql/server)** コマンドを実行します。

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```


## <a name="rest-api"></a>REST API
[Azure REST API](/rest/api/azure/) を使用して、読み取りレプリカを作成して管理できます。

### <a name="create-a-read-replica"></a>読み取りレプリカを作成します
作成 API](/rest/api/mysql/servers/create) を使用して、読み取りレプリカを作成できます。

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

汎用またはメモリ最適化マスター サーバーで `azure.replication_support` パラメーターを **[REPLICA]** に設定しておらず、サーバーを再起動していないと、エラーが返されます。 レプリカを作成する前に、この 2 つの手順を済ませておいてください。

レプリカは、マスターと同じコンピューティングとストレージの設定を使用して作成されます。 レプリカが作成されたら、マスター サーバーとは独立にいくつかの設定 (コンピューティング世代、仮想コア、ストレージ、およびバックアップ保持期間) を変更できます。 価格レベルも独立して変更できます (Basic レベルへの変更や Basic レベルからの変更を除く)。


> [!IMPORTANT]
> マスター サーバーの設定が新しい値に更新される前に、レプリカの設定をそれと同等以上の値に更新します。 このアクションは、レプリカがマスターに対するあらゆる変更に追従できるようにするのに役立ちます。

### <a name="list-replicas"></a>レプリカの一覧表示
[レプリカ一覧表示 API](/rest/api/mysql/replicas/listbyserver) を使用して、マスター サーバーのレプリカの一覧を表示できます。

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>レプリカ サーバーへのレプリケーションを停止します。
[更新 API](/rest/api/mysql/servers/update) を使用して、マスター サーバーと読み取りレプリカの間のレプリケーションを停止できます。

マスター サーバーと読み取りレプリカへのレプリケーションを停止した後、それを元に戻すことはできません。 読み取りレプリカは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。 スタンドアロン サーバーをもう一度レプリカにすることはできません。

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

### <a name="delete-a-master-or-replica-server"></a>マスター サーバーまたはレプリカ サーバーの削除
マスター サーバーまたはレプリカ サーバーを削除するには、[削除 API](/rest/api/mysql/servers/delete) を使用します。

マスター サーバーを削除すると、すべての読み取りレプリカへのレプリケーションが停止されます。 読み取りレプリカは、読み取りと書き込みの両方をサポートするようになったスタンドアロン サーバーになります。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>次の手順

- [レプリカの読み取り](concepts-read-replicas.md) の詳細を確認する
