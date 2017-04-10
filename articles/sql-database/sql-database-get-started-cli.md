---
title: "Azure CLI: SQL データベースの作成 | Microsoft Docs"
description: "Azure CLI を使用して、SQL Database の論理サーバー、サーバーレベルのファイアウォール規則、およびデータベースを作成する方法について説明します。"
keywords: "SQL データベース チュートリアル, SQL データベースの作成"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 3d642447526c5562ae3bfad5e4a4592e33e766aa
ms.lasthandoff: 03/25/2017

---

# <a name="create-a-single-azure-sql-database-using-the-azure-cli"></a>Azure CLI を使用して単一の Azure SQL データベースを作成する

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このガイドでは、Azure CLI を使用して Azure SQL データベースを [Azure SQL Database 論理サーバー](sql-database-features.md)内の [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)にデプロイする方法について詳しく説明します。

開始する前に、Azure CLI がインストールされていることを確認してください。 詳細については、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)を参照してください。 

## <a name="log-in-to-azure"></a>Azure へのログイン

[az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) コマンドで [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 次の例では、`myResourceGroup` という名前のリソース グループを `westeurope` の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-a-logical-server"></a>論理サーバーの作成

[az sql server create](/cli/azure/sql/server#create) コマンドで [Azure SQL Database 論理サーバー](sql-database-features.md)を作成します。 論理サーバーには、ひとまとめにして管理される一連のデータベースが含まれています。 次の例では、管理者ログイン `ServerAdmin` とパスワード `ChangeYourAdminPassword1` を使用し、ランダムに名前を付けたサーバーをリソース グループ内に作成しています。 これらの定義済みの値は、必要に応じて別の値に置き換えてください。

```azurecli
servername=server-$RANDOM
az sql server create --name $servername --resource-group myResourceGroup --location westeurope \
    --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
```

## <a name="configure-a-server-firewall-rule"></a>サーバーのファイアウォール規則の構成

[az sql server firewall create](/cli/azure/sql/server/firewall#create) コマンドで [Azure SQL Database サーバーレベルのファイアウォール規則](sql-database-firewall-configure.md)を作成します。 サーバーレベルのファイアウォール規則を作成すると、SQL Server Management Studio や SQLCMD ユーティリティのような外部アプリケーションから、SQL Database サービスのファイアウォールを介して SQL データベースに接続できます。 次の例では、定義済みのアドレス範囲に対するファイアウォール規則が作成されます。この例でのアドレス範囲は、IP アドレスの範囲として可能な全範囲です。 これらの定義済みの値は、自分の外部 IP アドレスや IP アドレス範囲に置き換えてください。 

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="create-a-database-in-the-server-with-sample-data"></a>サンプル データを使用したサーバーのデータベースの作成

[az sql db create](/cli/azure/sql/db#create) コマンドで [S0 パフォーマンス レベル](sql-database-service-tiers.md)のデータベースをサーバーに作成します。 次の例では、`mySampleDatabase` というデータベースを作成し、このデータベースに AdventureWorksLT のサンプル データを読み込みます。 必要に応じて、これらの定義済みの値を置き換えてください (このコレクションの他のクイック スタートは、このクイック スタートの値に基づいています)。

```azurecli
az sql db create --resource-group myResourceGroup --server $servername \
    --name mySampleDatabase --sample-name AdventureWorksLT --service-objective S0
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このコレクションの他のクイック スタートは、このクイック スタートに基づいています。 引き続きクイック スタートまたはチュートリアルの作業を行う場合は、このクイック スタートで作成したリソースをクリーンアップしないでください。 作業する予定がない場合は、次のコマンドを使用して、このクイック スタートで作成したすべてのリソースを削除してください。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

- SQL Server Management Studio を使用して接続とクエリを実行するには、[SSMS を使用した接続とクエリ](sql-database-connect-query-ssms.md)に関するページを参照してください。
- Visual Studio を使用して接続を行うには、[Visual Studio を使用した接続とクエリ](sql-database-connect-query.md)に関するページを参照してください。
- SQL Database の技術的な概要については、[SQL Database サービスの説明](sql-database-technical-overview.md)に関するページを参照してください。

