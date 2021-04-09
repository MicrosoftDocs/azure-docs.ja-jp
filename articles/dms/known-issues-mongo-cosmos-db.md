---
title: 既知の問題:MongoDB から Azure CosmosDB への移行
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service を使用した MongoDB から Azure Cosmos DB への移行に関する既知の問題と移行の制限事項について説明します。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/27/2020
ms.openlocfilehash: be6d9d3b8a20e11c874234baae26629ce0187e4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91291812"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB から Azure Cosmos DB の MongoDB 用 API への移行に関する既知の問題と移行の制限事項

MongoDB から Azure Cosmos DB の MongoDB 用 API への移行に関する既知の問題と制限については、次のセクションで説明します。

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>間違った SSL 証明書を使用したため、移行が失敗する

* **現象**:この問題は、ユーザーが MongoDB ソース サーバーに接続できない場合に発生します。 すべてのファイアウォール ポートが開いているにもかかわらず、ユーザーは接続できません。

| 原因         | 解像度 |
| ------------- | ------------- |
| Azure Database Migration Service で自己署名証明書を使用すると、SSL 証明書が正しくないために移行が失敗する可能性があります。エラー メッセージには、"検証プロシージャによると、リモート証明書は無効です" と含まれている場合があります。 | CA からの正規の証明書を使用します。  通常、自己署名証明書は内部テストでのみ使用されます。 CA 証明機関から正規の証明書をインストールすると、問題なく Azure Database Migration Service で SSL を使用できるようになります (Cosmos DB の接続には、Mongo API 経由の SSL を使用します)。<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>DMS でマップするデータベースの一覧を取得できない

* **現象**: **[ソースの選択]** ブレードで **Data from Azure Storage** モードを使用しているときに、 **[データベース設定]** ブレードで DB リストを取得できません。

| 原因         | 解像度 |
| ------------- | ------------- |
| ストレージ アカウントの接続文字列に SAS 情報がないため、認証できません。 | Storage Explorer で BLOB コンテナーに SAS を作成し、コンテナー SAS 情報が含まれる URL をソースの詳細接続文字列として使用します。<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>サポートされていないバージョンのデータベースの使用

* **現象**:移行は失敗します。

| 原因         | 解像度 |
| ------------- | ------------- |
| サポートされていないバージョンの MongoDB から Azure Cosmos DB に移行しようとしています。 | 新しいバージョンの MongoDB がリリースされると、Azure Database Migration Service との互換性を確保するためにテストされ、サービスは最新バージョンを適用できるよう、定期的に更新されます。 移行がすぐに必要な場合は、回避策として、データベースまたはコレクションを Azure Storage にエクスポートし、その結果のダンプにソースをポイントすることができます。 Storage Explorer で BLOB コンテナーに SAS を作成してから、コンテナー SAS 情報が含まれる URL をソースの詳細接続文字列として使用します。<br><br> |

## <a name="next-steps"></a>次のステップ

* [DMS を使用して MongoDB を Azure Cosmos DB の MongoDB 用 API にオンラインで移行する](tutorial-mongodb-cosmos-db-online.md)チュートリアルを参照します。
* [DMS を使用して MongoDB を Azure Cosmos DB の MongoDB 用 API にオフラインで移行する](tutorial-mongodb-cosmos-db.md)チュートリアルを参照します。