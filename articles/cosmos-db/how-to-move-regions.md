---
title: Azure Cosmos DB アカウントを別のリージョンに移動する
description: Azure Cosmos DB アカウントを別のリージョンに移動する方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 29b5c5d3cf55cd11fe505c0d9ab9b894dc2ad267
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342030"
---
# <a name="move-an-azure-cosmos-db-account-to-another-region"></a>Azure Cosmos DB アカウントを別のリージョンに移動する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、次の方法について説明します。

- Azure Cosmos DB でデータがレプリケートされるリージョンを移動する。
- アカウント (Azure Resource Manager) のメタデータとデータを一方のリージョンから他方に移行する。

## <a name="move-data-from-one-region-to-another"></a>一方のリージョンから他方にデータを移動する

Azure Cosmos DB はデータ レプリケーションをネイティブでサポートしているため、一方のリージョンから他方にデータを移動するのは簡単です。 これを行うには、Azure portal、Azure PowerShell、または Azure CLI を使用します。 これには、次の手順が含まれます。

1. 新しいリージョンをアカウントに追加します。

    Azure Cosmos DB アカウントに新しいリージョンを追加するには、[Azure Cosmos DB アカウントに対するリージョンの追加/削除](how-to-manage-database-account.md#addremove-regions-from-your-database-account)に関するページを参照してください。

1. 新しいリージョンへの手動フェールオーバーを実行します。

    削除するリージョンが、現時点でアカウントの書き込みリージョンである場合は、前の手順で追加した新しいリージョンへのフェールオーバーを開始する必要があります。 これは、ダウンタイムなしの操作です。 複数リージョンのアカウントで読み取りリージョンを移動する場合は、この手順は省略できます。 
    
    フェールオーバーを開始するには、「[Azure Cosmos アカウントでの手動フェールオーバーの実行](how-to-manage-database-account.md#manual-failover)」を参照してください。

1. 元のリージョンを削除します。

    Azure Cosmos DB アカウントからリージョンを削除するには、[Azure Cosmos DB アカウントに対するリージョンの追加/削除](how-to-manage-database-account.md#addremove-regions-from-your-database-account)に関するページを参照してください。

## <a name="migrate-azure-cosmos-db-account-metadata"></a>Azure Cosmos DB アカウントのメタデータを移行する

Azure Cosmos DB は、一方のリージョンから他方へのアカウント メタデータの移行をネイティブではサポートしていません。 アカウント メタデータと顧客データの両方を一方のリージョンから他方に移行するには、目的のリージョンに新しいアカウントを作成し、データを手動でコピーする必要があります。 

ダウンタイムがほぼゼロの SQL API の移行を行うには、[変更フィード](change-feed.md)を使用するか、それを使用するツールを使う必要があります。 MongoDB API、Cassandra API、または別の API を移行する場合、あるいはアカウント間でデータを移行するためのオプションの詳細について確認する場合は、「[オンプレミスまたはクラウドのデータを Azure Cosmos DB に移行するためのオプション](cosmosdb-migrationchoices.md)」を参照してください。 

次の手順では、SQL API とそのデータの Azure Cosmos DB アカウントを一方のリージョンから他方に移行する方法について説明します。

1. 目的のリージョンに新しい Azure Cosmos DB アカウントを作成します。

    Azure portal、PowerShell、または Azure CLI を使用して新しいアカウントを作成するには、[Azure Cosmos DB アカウントの作成](how-to-manage-database-account.md#create-an-account)に関するページを参照してください。

1. 新しいデータベースとコンテナーを作成します。

    新しいデータベースとコンテナーを作成するには、「[Azure Cosmos コンテナーを作成する](how-to-create-container.md)」を参照してください。

1. Azure Cosmos DB Live Data Migrator ツールを使用してデータを移行します。

    ダウンタイムがほぼゼロのデータ移行を行うには、[Azure Cosmos DB Live Data Migrator ツール](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)に関するページを参照してください。

1. アプリケーション接続文字列を更新します。

    Live Data Migrator ツールを実行したまま、アプリケーションの新しいデプロイで接続情報を更新します。 アプリケーションのエンドポイントとキーは、Azure portal から取得できます。

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Azure portal でのアクセス制御、NoSQL データベースのセキュリティのデモ。":::

1. 新しいアプリケーションに要求をリダイレクトします。

    新しいアプリケーションが Azure Cosmos DB に接続された後、新しいデプロイにクライアント要求をリダイレクトできます。

1. 不要になったリソースを削除します。

    これで、要求が新しいインスタンスに完全にリダイレクトされるようになったため、古い Azure Cosmos DB アカウントと Live Data Migrator ツールを削除できます。

## <a name="next-steps"></a>次の手順

Azure Cosmos アカウント、データベース、コンテナーの管理方法に関する詳細と例については、次の記事を参照してください。

* [Azure Cosmos アカウントを管理する](how-to-manage-database-account.md)
* [Azure Cosmos DB の変更フィード](change-feed.md)
