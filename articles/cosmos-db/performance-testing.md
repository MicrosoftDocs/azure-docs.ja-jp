---
title: Azure Cosmos DB のパフォーマンスとスケールのテスト
description: Azure Cosmos DB のスケーリングとパフォーマンスをテストする方法について説明します。 ハイパフォーマンス アプリケーション シナリオ用の Azure Cosmos DB の機能を評価できます。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 6d2863e39b5f28c56e2b9045513aa83326d8b8c7
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043194"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Azure Cosmos DB のパフォーマンスとスケールのテスト

パフォーマンスとスケールのテストは、アプリケーション開発における重要なステップです。 データベース層は、多くのアプリケーションの全体的なパフォーマンスとスケーラビリティに大きく影響します。 そのため、パフォーマンス テストの重要な構成要素となっています。 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) は、スケーリングの柔軟性とパフォーマンスの確実性を目指した専用設計になっています。 その機能は、ハイパフォーマンスのデータベース層が求められるアプリケーションにとてもよく合います。 

Azure Cosmos DB のワークロードに対するパフォーマンス テストを検討している開発者や、 ハイパフォーマンス アプリケーション用のデータベースとして Azure Cosmos DB の評価を担当する開発者の方は、この記事をご参考ください。 データベースのパフォーマンス テストに主眼を置いた内容となっていますが、運用環境のアプリケーションに該当するベスト プラクティスも含まれています。

この記事を読むと、次の質問に回答できるようになります。 

* Azure Cosmos DB のパフォーマンス テストに使用するサンプル .NET クライアント アプリケーションはどこから入手すればよいか。 
* 開発中のクライアント アプリケーションで Azure Cosmos DB から高水準のスループットを引き出すにはどうすればよいか。

最初に、[Azure Cosmos DB パフォーマンス テスト サンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)からプロジェクトをダウンロードしてコードを入手してください。 

> [!NOTE]
> このアプリケーションの目的は、クライアント マシンの数が少ない場合に Azure Cosmos DB で最良のパフォーマンスを得る方法を示すことです。 このサンプルの目的は、Azure Cosmos DBの (制限なしでスケールできる) ピーク時のスループット容量を達成することではありません。
> 
> 

Azure Cosmos DB のパフォーマンスを向上させるためのクライアント側の構成オプションについては、[Azure Cosmos DB のパフォーマンスに関するヒント](performance-tips.md)に関する記事を参照してください。

## <a name="run-the-performance-testing-application"></a>パフォーマンス テスト アプリケーションの実行
まずは以下の手順に従って、.NET サンプルをコンパイルして実行してみましょう。 ソース コードに目を通して、同様の構成を独自のクライアント アプリケーションに実装することもできます。

**手順 1:**[Azure Cosmos DB パフォーマンス テスト サンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)からプロジェクトをダウンロードするか、GitHub リポジトリをフォークします。

**手順 2:** App.config の EndpointUrl、AuthorizationKey、CollectionThroughput、DocumentTemplate (任意) の設定を変更します。

> [!NOTE]
> 高スループットでコレクションをプロビジョニングすることになるため、事前に[料金に関するページ](https://azure.microsoft.com/pricing/details/cosmos-db/)でコレクションあたりのコストを見積もってください。 Azure Cosmos DB では、ストレージとスループットが別々に時間単位で課金されます。 テスト後に Azure Cosmos DB コレクションのスループットを下げるかコレクションを削除することでコストを節約できます。
> 
> 

**手順 3:** コマンド ラインからコンソール アプリをコンパイルして実行します。 次のような出力結果が表示されます。

    C:\Users\cosmosdb\Desktop\Benchmark>DocumentDBBenchmark.exe
    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://arramacquerymetrics.documents.azure.com:443/
    Collection : db.data at 100000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: -1
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Found collection data with 100000 RU/s
    Starting Inserts with 100 tasks
    Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
    Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
    Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
    Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
    Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
    Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
    Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
    Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
    Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
    Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
    Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
    Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
    Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
    Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
    Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
    Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
    Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
    Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
    Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
    Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.
    Press any key to exit...


**手順 4 (必要に応じて実行):** ツールからレポートされるスループット (RU/s) は、プロビジョニングするコレクションまたは一連のコレクションのスループット以上である必要があります。 そのようになっていない場合は、DegreeOfParallelism を少しずつ増やすと、その境界値に到達しやすくなります。 クライアント アプリのスループットが安定したら、他のクライアント マシンでアプリの複数のインスタンスを開始します。 この手順についてご不明な点がありましたら、askcosmosdb@microsoft.com にメールをお送りいただくか、[Azure Portal](https://portal.azure.com) からサポート チケットを申請してください。

アプリの稼働後は、さまざまな[インデックス作成ポリシー](index-policy.md)と[整合性レベル](consistency-levels.md)を試しながら、スループットと待ち時間への影響を把握することができます。 ソース コードに目を通して、同様の構成を独自のテスト スイートや実稼働アプリケーションに実装することもできます。

## <a name="next-steps"></a>次の手順
この記事では、.NET コンソール アプリを使用して Azure Cosmos DB でパフォーマンスとスケーリングのテストを実行する方法を説明しました。 詳細については、次の記事を参照してください。

* [Azure Cosmos DB パフォーマンス テスト サンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Azure Cosmos DB のパフォーマンスを向上させるクライアント構成オプション](performance-tips.md)
* [Azure Cosmos DB でのサーバー側のパーティション分割](partition-data.md)


