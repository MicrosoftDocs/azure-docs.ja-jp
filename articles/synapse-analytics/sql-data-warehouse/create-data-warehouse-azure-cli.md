---
title: 'クイックスタート: Azure CLI を使用して Synapse SQL プールを作成する'
description: Azure CLI からサーバーレベルのファイアウォール規則を使用して、Synapse SQL プールをすばやく作成します。
services: synapse-analytics
author: gaursa
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: gaursa
ms.custom: azure-synapse
ms.openlocfilehash: c17bfc06e76bad7730707530001ebeccea539c37
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602266"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-cli"></a>クイックスタート: Azure CLI を使用して Synapse SQL プールを作成する

Azure CLI を使用して、Azure Synapse Analytics で Synapse SQL プール (データ ウェアハウス) を作成します。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="getting-started"></a>作業の開始

次のコマンドを使用して Azure にサインオンし、リソース グループを設定します。

1. ローカル インストールを使用している場合は、[az login](/cli/azure/reference-index#az_login) コマンドを実行して Azure にサインインします。

   ```azurecli
   az login
   ```

1. 必要に応じて、[az account set](/cli/azure/account#az_account_set) コマンドを使用して、お使いのサブスクリプションを選択します。

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. [az group create](/cli/azure/group#az_group_create) コマンドを実行してリソース グループを作成します。

   ```azurecli
   az group create --name myResourceGroup --location WestEurope
   ```

1. [az sql server create](/cli/azure/sql/server#az_sql_server_create) コマンドを使用して、[論理 SQL サーバー](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)を作成します。

   ```azurecli
   az sql server create --resource-group myResourceGroup --name mysqlserver \
      --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
   ```

   サーバーには、ひとまとめにして管理される一連のデータベースが含まれています。

## <a name="configure-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則の構成

[サーバーレベルのファイアウォール規則](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)を作成します。 サーバーレベルのファイアウォール規則を作成すると、SQL Server Management Studio や SQLCMD ユーティリティのような外部アプリケーションから、SQL プール サービスのファイアウォールを介して SQL プールに接続できます。

[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) コマンドを実行して、ファイアウォール規則を作成します。

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --name AllowSome \
   --server mysqlserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

この例では、他の Azure リソースに対してのみファイアウォールを開放しています。 外部から接続できるようにするには、実際の環境に合わせて IP アドレスを変更してください。 すべての IP アドレスを開放するには、開始 IP アドレスとして 0.0.0.0 を、終了アドレスとして 255.255.255.255 を使用します。

> [!NOTE]
> SQL エンドポイントの通信は、ポート 1433 で行われます。 企業ネットワーク内から接続しようとしても、ポート 1433 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 1433 が開放されない限り、サーバーに接続することはできません。
>

## <a name="create-and-manage-your-sql-pool"></a>SQL プールを作成および管理する

SQL プールを作成します。 この例では、サービス目標として DW100c を使用しています。これは、SQL プール用の低コストの開始点です。

> [!NOTE]
> 以前に作成したワークスペースが必要です。 詳細については、「[クイック スタート: Azure CLI を使用して Azure Synapse ワークスペースを作成する](../quickstart-create-workspace-cli.md)」を参照してください。

SQL プールを作成するには、[az synapse sql pool create](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_create) コマンドを使用します。

```azurecli
az synapse sql pool create --resource-group myResourceGroup --name mySampleDataWarehouse \
   --performance-level "DW1000c" --workspace-name testsynapseworkspace
```

パラメーター オプションの詳細については、「[az synapse sql pool](/cli/azure/ext/synapse/synapse/sql/pool)」を参照してください。

[az synapse sql pool list](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_list) コマンドを使用すると、対象の SQL プールを確認できます。

```azurecli
az synapse sql pool list --resource-group myResourceGroup --workspace-name testsynapseworkspace
```

既存のプールを更新するには、[az synapse sql pool update](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_update) コマンドを使用します。

```azurecli
az synapse sql pool update --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

プールを一時停止するには、[az synapse sql pool pause](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_pause) コマンドを使用します。

```azurecli
az synapse sql pool pause --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

一時停止しているプールを開始するには、[az synapse sql pool resume](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_resume) コマンドを使用します。

```azurecli
az synapse sql pool resume --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

既存の SQL プールを削除するには、[az synapse sql pool delete](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_delete) コマンドを使用します。

```azurecli
az synapse sql pool delete --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このコレクションの他のクイック スタート チュートリアルは、このクイック スタートに基づいています。

> [!TIP]
> 引き続きクイック スタート チュートリアルの作業を行う場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 作業する予定がない場合は、[az group delete](/cli/azure/group#az_group_delete) コマンドを使用して、このクイックスタートで作成されたすべてのリソースを削除してください。
>

```azurecli
az group delete --ResourceGroupName MyResourceGroup
```

## <a name="next-steps"></a>次のステップ

ここでは SQL プールを作成し、ファイアウォール規則を作成し、SQL プールに接続しました。 理解をさらに深めるために、[SQL プールへのデータの読み込み](./load-data-from-azure-blob-storage-using-copy.md)に関する記事に進んでください。