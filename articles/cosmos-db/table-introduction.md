---
title: "Azure Cosmos DB の Table API の概要 | Microsoft Docs"
description: "人気のある OSS MongoDB API と Azure Cosmos DB を使用して、大量のキー値データを低遅延で格納およびクエリする方法を説明します。"
services: cosmos-db
author: bhanupr
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/29/2017
ms.author: arramac
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 3ccc3b176df2f0a5d864554a74508292d272bd5a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/30/2017

---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Azure Cosmos DB の概要: Table API

[Azure Cosmos DB](introduction.md) は、Azure Table Storage サービス向けに作成されたアプリケーションの中でも、プレミアムな機能 ([ターンキー グローバル分散](distribute-data-globally.md)、世界規模での[専用スループット](partition-data.md)、99 パーセンタイルで 10 ミリ秒未満の待ち時間、高可用性保証、[自動セカンダリ インデックス作成](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)など) を必要とするアプリケーションのための Table API (プレビュー) を提供します。 このようなアプリケーションについては、Table API を使って Azure Cosmos DB に移行させることで、コードに変更を加えることなく、プレミアムな機能を活用できるようになります。

まずは以下のビデオを見ることをお勧めします。この動画では、Azure Cosmos DB の Table API の導入方法を Aravind Ramachandran が説明しています。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Table-API-for-Azure-Cosmos-DB/player]
> 
> 

## <a name="premium-and-standard-table-apis"></a>Premium および標準の Table API
現在 Azure Table Storage を使用している場合、Azure Cosmos DB の "Premium テーブル" プレビューに移行することにより次のメリットが得られます。

|  | Azure Table Storage | Azure Cosmos DB: Table Storage (プレビュー) |
| --- | --- | --- |
| 待機時間 | 高速だが、待機時間の上限はなし | 読み取りと書き込みの待ち時間は数ミリ秒であり、世界中のどこでもあらゆるスケールで 99 パーセンタイルの 10 ms 未満の読み取り待ち時間と 15 ms 未満の書き込み待ち時間でサポート |
| スループット | 可変スループット モデル。 テーブルには、20,000 操作/秒の拡張性の制限あり | SLA によって保証された、[テーブルごとの専用の予約済みスループット](request-units.md)を備えた高い拡張性。 アカウントにはスループットの上限がなく、テーブルあたり 10,000, 000 操作/秒以上に対応 |
| グローバル分散 | HA のために 1 つの読み取り可能なセカンダリ読み取りリージョンをオプションで備えた単一リージョン。 フェールオーバーを開始できない | 1 から 30 以上の地域にわたる[ターンキー グローバル分散](distribute-data-globally.md)。世界中のどこでもいつでも[自動および手動フェールオーバー](regional-failover.md)をサポート |
| インデックス作成 | PartitionKey と RowKey のプライマリ インデックスのみ。 セカンダリ インデックスなし | すべてのプロパティに対する自動および完全なインデックス作成。インデックス管理なし |
| クエリ | クエリの実行では、プライマリ キーにインデックスを使用し、それ以外の場合はスキャンする。 | クエリは、クエリ時間の短縮のためにプロパティの自動インデックス作成を利用できる。 Azure Cosmos DB のデータベース エンジンは、集計、地理空間、および並べ替えをサポート可能。 |
| 整合性 | プライマリ リージョン内では強力、セカンダリ リージョンでは成り行き次第 | アプリケーションのニーズに基づいて、可用性、待ち時間、スループット、および整合性のトレードオフを行う[明確に定義された 5 つの整合性レベル](consistency-levels.md) |
| 価格 | ストレージ最適化  | スループット最適化 |
| SLA | 99.9% の可用性 | 単一のリージョン内では 99.99% の可用性、さらにリージョンを追加して可用性を高めることが可能。 一般公開に対する[業界トップレベルの包括的な SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) |

## <a name="how-to-get-started"></a>ファースト ステップ

[Azure Portal](https://portal.azure.com) で Azure Cosmos DB アカウントを作成し、「[.NET を使用した Table API のクイック スタート](create-table-dotnet.md)」から開始します。 

## <a name="next-steps"></a>次のステップ

使用し始めるためのいくつかのヒントを次に示します。
* [Table API を使用した .NET アプリケーションの構築](create-table-dotnet.md)
* [.NET での Table API を使用した開発](tutorial-develop-table-dotnet.md)
* [Table API を使用してテーブル データのクエリを実行する方法](tutorial-query-table.md)
* [Table API を使用して Azure Cosmos DB グローバル分散をセットアップする方法](tutorial-global-distribution-table.md)
* [Azure Cosmos DB Table API SDK for .NET](table-sdk-dotnet.md)

