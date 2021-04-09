---
title: 検索インデックス作成のための Azure SQL Managed Instance の接続
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 上のインデクサーから SQL マネージド インスタンスへの接続を許可するために、パブリック エンドポイントを有効にします。
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9e8625724f67caac99ae799674f9db9399e11ad8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89294256"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Azure Cognitive Search インデクサーから SQL マネージド インスタンスへの接続を構成する

[インデクサーを使用した Azure Cognitive Search への Azure SQL Database の接続](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)に関するページに示されているように、**SQL マネージド インスタンス** に対するインデクサーの作成は、Azure Cognitive Search によってパブリック エンドポイント経由でサポートされています。

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>パブリック エンドポイントを使用して Azure SQL Managed Instance を作成する
**[パブリック エンドポイントの有効化]** オプションが選択された状態で SQL マネージド インスタンスを作成します。

   ![パブリック エンドポイントを有効にする](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "パブリック エンドポイントの有効化")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Azure SQL Managed Instance のパブリック エンドポイントを有効にする
**[セキュリティ]**  >  **[仮想ネットワーク]**  >  **[パブリック エンドポイント]**  >  **[有効化]** によって、既存の SQL マネージド インスタンスでパブリック エンドポイントを有効にすることもできます。

   ![マネージド インスタンス VNET を使用してパブリック エンドポイントを有効にする](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "パブリック エンドポイントの有効化")

## <a name="verify-nsg-rules"></a>NSG 規則を確認する
ネットワーク セキュリティ グループに、Azure サービスからの接続を許可する正しい **受信セキュリティ規則** が設定されていることを確認します。

   ![NSG インバウンド セキュリティ規則](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG インバウンド セキュリティ規則")

> [!NOTE]
> インデクサーでは、データを読み取るために、パブリック エンドポイントを使用して SQL マネージド インスタンスを構成することが引き続き必要となります。
> ただし、現在の規則 (`public_endpoint_inbound`) を次の 2 つの規則に置き換えることによって、そのパブリック エンドポイントへの受信アクセスを制限することもできます。
>
> * `AzureCognitiveSearch` [サービス タグ](../virtual-network/service-tags-overview.md#available-service-tags) ("SOURCE" = `AzureCognitiveSearch`、"NAME" = `cognitive_search_inbound`) からの受信アクセスを許可する
>
> * 検索サービスの IP アドレスからの受信アクセスを許可します。これは、完全修飾ドメイン名 (`<your-search-service-name>.search.windows.net` など) を ping することで取得できます。 ("SOURCE" = `IP address`、"NAME" = `search_service_inbound`)
>
> これらの 2 つの規則それぞれについて、"PORT" = `3342`、"PROTOCOL" = `TCP`、"DESTINATION" = `Any`、"ACTION" = `Allow` と設定します

## <a name="get-public-endpoint-connection-string"></a>パブリック エンドポイントの接続文字列を取得する
**パブリック エンドポイント** の接続文字列 (ポート 1433 ではなく、ポート 3342) を使用していることを確認します。

   ![パブリック エンドポイントの接続文字列](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "パブリック エンドポイントの接続文字列")

## <a name="next-steps"></a>次のステップ
これで構成が完了したため、ポータルまたは REST API のどちらかを使用して、Azure Cognitive Search インデクサーのデータ ソースとして SQL マネージド インスタンスを指定できるようになりました。 詳細については、[インデクサーを使用した Azure Cognitive Search への Azure SQL Database の接続](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)に関する記事をご覧ください。