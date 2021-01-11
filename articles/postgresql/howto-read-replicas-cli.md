---
title: 読み取りレプリカを管理する - Azure CLI、REST API - Azure Database for PostgreSQL - Single Server
description: Azure CLI と REST API から Azure Database for PostgreSQL - Single Server の読み取りレプリカを管理する方法について説明します
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 07/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 491b3ecfc950fa5f76bfe78eec52e81433294c23
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500080"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Azure CLI、REST API から読み取りレプリカを作成および管理する

この記事では、Azure CLI と REST API を使用して Azure Database for PostgreSQL の読み取りレプリカを作成および管理する方法について説明します。 読み取りレプリカの詳細については、[概要](concepts-read-replicas.md)を参照してください。

## <a name="azure-replication-support"></a>Azure レプリケーションのサポート
[読み取りレプリカ](concepts-read-replicas.md)と[論理デコード](concepts-logical.md)はどちらも、情報を Postgres 書き込み先行ログ (WAL) に依存しています。 これらの 2 つの機能には、Postgres とは異なるレベルのログが必要です。 論理デコードには、読み取りレプリカよりも高いレベルのログが必要です。

適切なレベルのログを構成するには、Azure レプリケーション サポート パラメーターを使用します。 Azure レプリケーション サポートには、次の 3 つの設定オプションがあります。

* **オフ** - 最小限の情報を WAL に格納します。 この設定は、ほとんどの Azure Database for PostgreSQL サーバーでは使用できません。  
* **レプリカ** - **[オフ]** よりも冗長です。 これは、[読み取りレプリカ](concepts-read-replicas.md)を機能させるために必要な最小レベルのログです。 ほとんどのサーバーでは、この設定が既定値です。
* **論理** - **[レプリカ]** よりも冗長です。 これは、論理デコードを機能させるための最小レベルのログです。 読み取りレプリカはこの設定でも機能します。

このパラメーターを変更した後、サーバーを再起動する必要があります。 内部的には、このパラメーターによって、Postgres のパラメーター `wal_level`、`max_replication_slots`、および `max_wal_senders` が設定されます。

## <a name="azure-cli"></a>Azure CLI
Azure CLI を使用して、読み取りレプリカを作成して管理できます。

### <a name="prerequisites"></a>前提条件

- [Azure CLI 2.0 のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- マスター サーバーになる [Azure Database for PostgreSQL サーバー](quickstart-create-server-up-azure-cli.md)。


### <a name="prepare-the-master-server"></a>マスター サーバーの準備

1. マスター サーバーの `azure.replication_support` 値を確認します。 読み取りレプリカを機能させるには、少なくともレプリカである必要があります。

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. `azure.replication_support` が少なくともレプリカでない場合は、それを設定します。 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. サーバーを再起動して変更を適用します。

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>読み取りレプリカを作成します

[az postgres server replica create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) コマンドには、次のパラメーターが必要です。

| 設定 | 値の例 | 説明  |
| --- | --- | --- |
| resource-group | myresourcegroup |  レプリカ サーバーが作成されるリソース グループ。  |
| name | mydemoserver-replica | 作成する新しいレプリカ サーバーの名前。 |
| source-server | mydemoserver | レプリケート元の既存のマスター サーバーの名前またはリソース ID。 レプリカとマスターのリソース グループを異なるものにする場合は、リソース ID を使用します。 |

以下の CLI の例では、レプリカはマスターと同じリージョンに作成されます。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

リージョンをまたがる読み取りレプリカを作成するには、`--location` パラメーターを使用します。 次の CLI の例では、米国西部にレプリカを作成します。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> レプリカを作成できるリージョンの詳細については、[読み取りレプリカの概念に関する記事](concepts-read-replicas.md)を参照してください。 

汎用またはメモリ最適化マスター サーバーで `azure.replication_support` パラメーターを **[REPLICA]** に設定しておらず、サーバーを再起動していないと、エラーが返されます。 レプリカを作成する前に、この 2 つの手順を済ませておいてください。

> [!IMPORTANT]
> [読み取りレプリカの概要に関するページの考慮事項セクション](concepts-read-replicas.md#considerations)を確認してください。
>
> マスター サーバーの設定が新しい値に更新される前に、レプリカの設定をそれと同等以上の値に更新します。 このアクションは、レプリカがマスターに対するあらゆる変更に追従できるようにするのに役立ちます。

### <a name="list-replicas"></a>レプリカの一覧表示
マスター サーバーのレプリカの一覧を表示するには、[az postgres server replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) コマンドを使用します。

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>レプリカ サーバーへのレプリケーションを停止します。
マスター サーバーと読み取りレプリカの間のレプリケーションを停止するには、[az postgres server replica stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) コマンドを使用します。

マスター サーバーと読み取りレプリカへのレプリケーションを停止した後、それを元に戻すことはできません。 読み取りレプリカは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。 スタンドアロン サーバーをもう一度レプリカにすることはできません。

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>マスター サーバーまたはレプリカ サーバーの削除
マスター サーバーまたはレプリカ サーバーを削除するには、[az postgres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) コマンドを使用します。

マスター サーバーを削除すると、すべての読み取りレプリカへのレプリケーションが停止されます。 読み取りレプリカは、読み取りと書き込みの両方をサポートするようになったスタンドアロン サーバーになります。

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
[Azure REST API](/rest/api/azure/) を使用して、読み取りレプリカを作成して管理できます。

### <a name="prepare-the-master-server"></a>マスター サーバーの準備

1. マスター サーバーの `azure.replication_support` 値を確認します。 読み取りレプリカを機能させるには、少なくともレプリカである必要があります。

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. `azure.replication_support` が少なくともレプリカでない場合は、それを設定します。

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. 変更を適用するために、[サーバーを再起動](/rest/api/postgresql/servers/restart)します。

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>読み取りレプリカを作成します
[作成 API](/rest/api/postgresql/servers/create) を使用して、読み取りレプリカを作成できます。

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
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
[レプリカ一覧表示 API](/rest/api/postgresql/replicas/listbyserver) を使用して、マスター サーバーのレプリカの一覧を表示できます。

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>レプリカ サーバーへのレプリケーションを停止します。
[更新 API](/rest/api/postgresql/servers/update) を使用して、マスター サーバーと読み取りレプリカの間のレプリケーションを停止できます。

マスター サーバーと読み取りレプリカへのレプリケーションを停止した後、それを元に戻すことはできません。 読み取りレプリカは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。 スタンドアロン サーバーをもう一度レプリカにすることはできません。

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>マスター サーバーまたはレプリカ サーバーの削除
マスター サーバーまたはレプリカ サーバーを削除するには、[削除 API](/rest/api/postgresql/servers/delete) を使用します。

マスター サーバーを削除すると、すべての読み取りレプリカへのレプリケーションが停止されます。 読み取りレプリカは、読み取りと書き込みの両方をサポートするようになったスタンドアロン サーバーになります。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>次のステップ
* [Azure Database for PostgreSQL の読み取りレプリカ](concepts-read-replicas.md)について確認してください。
* [Azure portal で読み取りレプリカを作成および管理する](howto-read-replicas-portal.md)方法を確認する。
