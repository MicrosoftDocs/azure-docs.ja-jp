---
title: 検索インデックス作成のための Azure SQL マネージド インスタンスの接続 - Azure Search
description: Azure Search のインデクサーから SQL マネージド インスタンスへの接続を許可するためにパブリック エンドポイントを有効にします。
author: vl8163264128
manager: briansmi
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: victliu
ms.openlocfilehash: ae947e85f600b3bc380898ad5820239aa823794d
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228986"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-managed-instance"></a>Azure Search インデクサーから SQL マネージド インスタンスへの接続を構成する
[インデクサーを使用した Azure Search への Azure SQL Database の接続](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)に関するページに示されているように、**SQL マネージド インスタンス**に対するインデクサーの作成は、Azure Search によってパブリック エンドポイント経由でサポートされています。

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>パブリック エンドポイントを使用して Azure SQL マネージド インスタンスを作成する
**[パブリック エンドポイントの有効化]** オプションが選択された状態で SQL マネージド インスタンスを作成します。

   ![[パブリック エンドポイントの有効化]](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "[パブリック エンドポイントの有効化]")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Azure SQL マネージド インスタンスのパブリック エンドポイントを有効にする
**[セキュリティ]**  >  **[仮想ネットワーク]**  >  **[パブリック エンドポイント]**  >  **[有効化]** によって、既存の SQL マネージド インスタンスでパブリック エンドポイントを有効にすることもできます。

   ![[パブリック エンドポイントの有効化]](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "[パブリック エンドポイントの有効化]")

## <a name="verify-nsg-rules"></a>NSG 規則を確認する
ネットワーク セキュリティ グループに、Azure サービスからの接続を許可する正しい**受信セキュリティ規則**が設定されていることを確認します。

   ![NSG 受信セキュリティ規則](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG 受信セキュリティ規則")

## <a name="get-public-endpoint-connection-string"></a>パブリック エンドポイントの接続文字列を取得する
**パブリック エンドポイント**の接続文字列 (ポート 1433 ではなく、ポート 3342) を使用していることを確認します。

   ![パブリック エンドポイントの接続文字列](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "パブリック エンドポイントの接続文字列")

## <a name="next-steps"></a>次の手順
これで構成が完了したため、ポータルまたは REST API のどちらかを使用して、Azure Search インデクサーのデータ ソースとして SQL マネージド インスタンスを指定できるようになりました。 詳細については、「 [インデクサーを使用した Azure Search への Azure SQL Database の接続](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) 」を参照してください。
