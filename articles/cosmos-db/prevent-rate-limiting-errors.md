---
title: Azure Cosmos DB API for MongoDB の操作のレート制限エラーを回避する。
description: SSR (サーバー側の再試行) 機能を使用して、Azure Cosmos DB API for MongoDB の操作がレート制限エラーに到達しないようにする方法について説明します。
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 1e9062b111c30efa90b98c4ebcee710b1d975a1d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99507932"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Azure Cosmos DB API for MongoDB の操作のレート制限エラーを回避する
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB API for MongoDB の操作は、コレクションのスループット制限 (RU) を超えると、レート制限 (16500/429) エラーで失敗することがあります。 

サーバー側の再試行 (SSR) 機能を有効にし、サーバーでこれらの操作が自動的に再試行されるようにすることができます。 少し時間が経過した後に、アカウント内のすべてのコレクションに対して要求が再試行されます。 この機能は、クライアント アプリケーションでレート制限エラーを処理する方法に代わる便利な方法です。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. Azure Cosmos DB API for MongoDB アカウントに移動します。

1. **[設定]** セクションの下にある **[機能]** ペインにアクセスします。

1. **[Server Side Retry]\(サーバー側の再試行\)** を選択します。

1. **[有効化]** をクリックして、アカウント内のすべてのコレクションに対してこの機能を有効にします。

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Azure Cosmos DB API for MongoDB のサーバー側の再試行機能のスクリーンショット":::

## <a name="use-the-azure-cli"></a>Azure CLI の使用

1. ご利用のアカウントで SSR が既に有効になっているかどうかを確認します。
```bash
az cosmosdb show --name accountname --resource-group resourcegroupname
```
2. ご利用のデータベース アカウント内のすべてのコレクションに対して SSR を **有効にします**。 この変更が有効になるまで、最大 15 分かかる場合があります。
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```
次のコマンドは、機能の一覧から "DisableRateLimitingResponses" を削除することによって、データベース アカウント内のすべてのコレクションに対して SSR を **無効** にします。 この変更が有効になるまで、最大 15 分かかる場合があります。
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo
```

## <a name="frequently-asked-questions"></a>よく寄せられる質問
* 要求はどのように再試行されますか。
    * 60 秒のタイムアウトに達するまで、要求は継続的に (繰り返し) 再試行されます。 タイムアウトに達すると、クライアントに [ExceededTimeLimit 例外 (50)](mongodb-troubleshoot.md) が返されます。
*  SSR の効果を監視するにはどうすればよいですか。
    *  [Cosmos DB メトリック] ウィンドウでは、サーバー側で再試行されたレート制限エラー (429) を確認できます。 SSR が有効になっている場合、これらのエラーはサーバー側で処理され再試行されるため、クライアントには返されないことにご注意ください。 
    *  [Cosmos DB リソース ログ](cosmosdb-monitor-resource-logs.md)内で、"estimatedDelayFromRateLimitingInMilliseconds" を含むログ エントリを検索することができます。
*  SSR を適用すると、整合性レベルに影響がありますか。
    *  SSR を適用しても、要求の整合性に影響はありません。 要求は、レート制限を受けた場合 (429 エラー)、サーバー側で再試行されます。 
*  クライアントに返される可能性のある種類のエラーは、SSR の影響を受けますか。
    *  いいえ。SSR の影響を受けるのは、レート制限エラー (429) のみであり、これらはサーバー側で再試行されます。 この機能を使用すると、クライアント アプリケーション内でレート制限エラーを処理しなくてもよくなります。 [他のエラー](mongodb-troubleshoot.md)はすべて、クライアントに返されます。 

## <a name="next-steps"></a>次の手順

一般的なエラーのトラブルシューティングの詳細については、次の記事を参照してください。

* [Azure Cosmos DB の MongoDB 用 API の一般的な問題のトラブルシューティング](mongodb-troubleshoot.md)
