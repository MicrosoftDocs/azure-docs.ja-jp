---
title: Azure Cosmos DB の Mongo DB 用 API の一般的なエラーのトラブルシューティング
description: このドキュメントでは、Azure Cosmos DB の MongoDB 用 API で発生する一般的な問題のトラブルシューティング方法について説明します。
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: lbosq
ms.openlocfilehash: f75374fc88923a0f131d513bebf0ffe1feeca359
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076767"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API の一般的な問題のトラブルシューティング

次の記事では、Azure Cosmos DB の MongoDB 用 API を使用するデータベースの一般的なエラーと解決策について説明します。

>[!Note]
> Azure Cosmos DB では、MongoDB エンジンをホストしません。 MongoDB [ワイヤ プロトコル バージョン 3.6](mongodb-feature-support-36.md) の実装と、[ワイヤ プロトコル バージョン 3.2](mongodb-feature-support.md) 用のレガシ サポートを提供します。そのため、これらのエラーの一部は、Azure Cosmos DB の MongoDB 用 API でのみ見つかります。 

## <a name="common-errors-and-solutions"></a>一般的なエラーと解決

| エラー               | コード  | 説明  | 解決策  |
|---------------------|-------|--------------|-----------|
| ExceededTimeLimit   | 50 | 要求が実行のタイムアウトである 60 秒を超えました。 | このエラーには多くの原因が考えられます。 原因の 1 つは、現在割り当てられている要求ユニットの容量が、要求を完了するのに十分ではない場合です。 これは、そのコレクションまたはデータベースの要求ユニットを増やすことによって解決できます。 それ以外の場合は、大きな要求を小さい要求に分割することで、このエラーを回避できます。 |
| TooManyRequests     | 16500 | 使用された要求ユニットの合計数が、コレクションのプロビジョニング済みの要求ユニット レートを超えたために調整されました。 | Azure portal からコンテナーまたはコンテナーのセットに割り当てられているスループットをスケーリングすることを検討してください。または、操作を再試行できます。 |
| ExceededMemoryLimit | 16501 | マルチテナント サービスとしての操作が、クライアントのメモリ配分を超えました。 | より制限の厳しいクエリ条件によって操作のスコープを減らすか、[Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) からサポートに連絡してください。 例: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 指定された order by 項目に対応するインデックスのパスが除外されます。order by クエリには提供元にすることができる対応する複合インデックスがありません。 | 2 | クエリは、インデックスが設定されていないフィールドに対して並べ替えを要求します。 | 実行しようとしている並べ替えクエリの一致するインデックス (または複合インデックス) を作成します。 |
| MongoDB ワイヤ バージョンの問題 | - | 古いバージョンの MongoDB ドライバーは、Azure Cosmos アカウントの名前を接続文字列から検出できません。 | Cosmos DB の MongoDB 用 API の接続文字列用の末尾に *appName=@**accountName**@* を追加します。***accountName*** は Cosmos DB アカウント名です。 |

## <a name="next-steps"></a>次のステップ

- Azure Cosmos DB の MongoDB 用 API と共に [Studio 3T を使用する](mongodb-mongochef.md)方法を学習します。
- Azure Cosmos DB の MongoDB 用 API と共に [Robo 3T を使用する](mongodb-robomongo.md)方法を学びます。
- Azure Cosmos DB の MongoDB 用 API を使用した MongoDB の[サンプル](mongodb-samples.md)を調査します。

