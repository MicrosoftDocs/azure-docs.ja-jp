---
title: Azure Cosmos DB の MongoDB 用 API についてよく寄せられる質問
description: Azure Cosmos DB の MongoDB 用 API についてよく寄せられる質問とその回答を示します
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 08899018d03209dab09f61d4dd74feceee03b246
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019018"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API についてよく寄せられる質問
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB の MongoDB 用 API は、コミュニティでサポートされる MongoDB 用の既存の SDK とドライバーを使用して、アプリケーションがネイティブの Azure Cosmos データベース エンジンと簡単かつ透過的に通信できるようにするワイヤプロトコル互換レイヤーです。 開発者は、既存の MongoDB ツールチェーンとスキルを使って、Azure Cosmos DB を活用するアプリケーションを構築できるようになりました。 これにより、マルチリージョン書き込みレプリケーションによるグローバル配信、自動インデックス作成、バックアップ メンテナンス、利用料金に基づくサービス レベル アグリーメント (SLA) など、Azure Cosmos DB の独自の機能によるメリットが得られます。

## <a name="how-do-i-connect-to-my-database"></a>データベースに接続する方法は?

Azure Cosmos DB の MongoDB 用 API で Cosmo データベースに接続する最も簡単な方法は、[Azure Portal](https://portal.azure.com) を使用することです。 アカウントに移動し、左側のナビゲーション メニューで **[クイック スタート]** をクリックします。 クイック スタートは、コード スニペットを取得してデータベースに接続するための最善の方法です。

Azure Cosmos DB では、厳密なセキュリティ要件と基準が適用されます。 Azure Cosmos DB アカウントには TLS 経由の認証およびセキュリティで保護された通信が必要なため、必ず TLSv1.2 を使用してください。

詳細については、[Azure Cosmos DB の MongoDB 用 API で Cosmo データベースに接続する](connect-mongodb-account.md)方法に関するページを参照してください。

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API を使用しているときのエラー コード

Azure Cosmos DB の MongoDB 用 API には、一般的な MongoDB エラー コードのほかに、次のような独自のエラー コードがあります。 これらについては、[トラブルシューティング ガイド](mongodb-troubleshoot.md)に関する記事を参照してください。

## <a name="supported-drivers"></a>サポートされているドライバー

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB 向けの Simba ドライバーは Azure Cosmos DB の MongoDB 用 API で使用できますか。

はい。Simba の Mongo ODBC ドライバーは Azure Cosmos DB の MongoDB 用 API で使用できます

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB の MongoDB 用 API を使用して .NET Web アプリを構築する](create-mongodb-dotnet.md)
* [Azure Cosmos DB の MongoDB API と Java を使ってコンソール アプリを作成する](create-mongodb-java.md)
