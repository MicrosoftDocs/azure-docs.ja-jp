---
title: "DocumentDB のスケールとパフォーマンスのテスト | Microsoft Docs"
description: "Azure DocumentDB のスケールとパフォーマンスのテストを行う方法について説明します。"
keywords: "パフォーマンス テスト"
services: documentdb
author: arramac
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f4c96ebd-f53c-427d-a500-3f28fe7b11d0
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 86bd591c26a58200dab9872e07e6e8bdf2522df9
ms.lasthandoff: 03/29/2017


---
# <a name="performance-and-scale-testing-with-azure-documentdb"></a>Azure DocumentDB のパフォーマンスとスケールのテスト
パフォーマンスとスケールのテストは、アプリケーション開発における重要なステップです。 データベース層は、多くのアプリケーションの全体的なパフォーマンスとスケーラビリティに大きく関係しており、パフォーマンス テストの重要な構成要素となっています。 [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) は、拡張の柔軟性とパフォーマンスの確実性を目指した専用設計になっているため、ハイパフォーマンスのデータベース層が求められるアプリケーションにとてもよく合います。 

DocumentDB のワークロードに対するパフォーマンス テストを検討している開発者や、ハイパフォーマンス アプリケーション用のデータベースとして DocumentDB の評価を担当する開発者の方は、この記事をご参考ください。 データベースのパフォーマンス テストに主眼を置いた内容となっていますが、運用環境のアプリケーションに該当するベスト プラクティスも含まれています。

この記事を読むと、次の質問に回答できるようになります。   

* Azure DocumentDB のパフォーマンス テストに使用するサンプル .NET クライアント アプリケーションはどこから入手すればよいか。 
* 開発中のクライアント アプリケーションで Azure DocumentDB から高水準のスループットを引き出すにはどうすればよいか。

最初に、[DocumentDB Performance Testing サンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)からプロジェクトをダウンロードしてコードを入手してください。 

> [!NOTE]
> このアプリケーションの目的は、クライアント マシンの数が少ない場合に DocumentDB のパフォーマンスを高めるためのベスト プラクティスを示すことです。 サービスのピーク時容量を示すためのものではありません。ピーク キャパシティについては、無制限に拡張することが可能です。
> 
> 

DocumentDB のパフォーマンスを向上させるためのクライアント側の構成オプションについては、 [DocumentDB のパフォーマンスに関するヒント](documentdb-performance-tips.md)に関する記事を参照してください。

## <a name="run-the-performance-testing-application"></a>パフォーマンス テスト アプリケーションの実行
まずは以下の手順に従って、.NET サンプルをコンパイルして実行してみましょう。 ソース コードに目を通して、同様の構成を独自のクライアント アプリケーションに実装することもできます。

**手順 1:** [DocumentDB Performance Testing サンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)からプロジェクトをダウンロードするか、GitHub リポジトリをフォークします。

**手順 2:** App.config で EndpointUrl、AuthorizationKey、CollectionThroughput、DocumentTemplate (任意) の設定を変更します。

> [!NOTE]
> 高スループットでコレクションをプロビジョニングすることになるので、事前に[料金に関するページ](https://azure.microsoft.com/pricing/details/documentdb/)でコレクションあたりのコストを見積もってください。 DocumentDB では、記憶域とスループットが別々に時間単位で課金されます。そのためテスト後に DocumentDB コレクションのスループットを下げるかコレクションを削除することでコストを節約できます。
> 
> 

**手順 3:** コマンド ラインからコンソール アプリをコンパイルして実行します。 次のような出力結果が表示されます。

    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://docdb-scale-demo.documents.azure.com:443/
    Collection : db.testdata at 50000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: 500
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Creating database db
    Creating collection testdata
    Creating metric collection metrics
    Retrying after sleeping for 00:03:34.1720000
    Starting Inserts with 500 tasks
    Inserted 661 docs @ 656 writes/s, 6860 RU/s (18B max monthly 1KB reads)
    Inserted 6505 docs @ 2668 writes/s, 27962 RU/s (72B max monthly 1KB reads)
    Inserted 11756 docs @ 3240 writes/s, 33957 RU/s (88B max monthly 1KB reads)
    Inserted 17076 docs @ 3590 writes/s, 37627 RU/s (98B max monthly 1KB reads)
    Inserted 22106 docs @ 3748 writes/s, 39281 RU/s (102B max monthly 1KB reads)
    Inserted 28430 docs @ 3902 writes/s, 40897 RU/s (106B max monthly 1KB reads)
    Inserted 33492 docs @ 3928 writes/s, 41168 RU/s (107B max monthly 1KB reads)
    Inserted 38392 docs @ 3963 writes/s, 41528 RU/s (108B max monthly 1KB reads)
    Inserted 43371 docs @ 4012 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 48477 docs @ 4035 writes/s, 42282 RU/s (110B max monthly 1KB reads)
    Inserted 53845 docs @ 4088 writes/s, 42845 RU/s (111B max monthly 1KB reads)
    Inserted 59267 docs @ 4138 writes/s, 43364 RU/s (112B max monthly 1KB reads)
    Inserted 64703 docs @ 4197 writes/s, 43981 RU/s (114B max monthly 1KB reads)
    Inserted 70428 docs @ 4216 writes/s, 44181 RU/s (115B max monthly 1KB reads)
    Inserted 75868 docs @ 4247 writes/s, 44505 RU/s (115B max monthly 1KB reads)
    Inserted 81571 docs @ 4280 writes/s, 44852 RU/s (116B max monthly 1KB reads)
    Inserted 86271 docs @ 4273 writes/s, 44783 RU/s (116B max monthly 1KB reads)
    Inserted 91993 docs @ 4299 writes/s, 45056 RU/s (117B max monthly 1KB reads)
    Inserted 97469 docs @ 4292 writes/s, 44984 RU/s (117B max monthly 1KB reads)
    Inserted 99736 docs @ 4192 writes/s, 43930 RU/s (114B max monthly 1KB reads)
    Inserted 99997 docs @ 4013 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 100000 docs @ 3846 writes/s, 40304 RU/s (104B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 100000 docs @ 3834 writes/s, 40180 RU/s (104B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.


**手順 4 (必要に応じて実行):** ツールからレポートされるスループット (RU/s) は、プロビジョニングするコレクションのスループット以上である必要があります。 そのようになっていない場合は、DegreeOfParallelism を少しずつ増やすと、その境界値に到達しやすくなります。 クライアント アプリのスループットが横ばいになった場合は、その複数のインスタンスを同じマシンまたは異なるマシンで起動すれば、それら複数のインスタンスで、プロビジョニングするスループットに到達させることができます。 この手順について不明な点がある場合は、askdocdb@microsoft.com にメールをお送りいただくか、[Azure Portal](https://portal.azure.com) からサポート チケットを申請してください。

アプリの稼働後は、さまざまな[インデックス作成ポリシー](documentdb-indexing-policies.md)と[一貫性レベル](documentdb-consistency-levels.md)を試しながら、スループットや待機時間への影響を把握することができます。 ソース コードに目を通して、同様の構成を独自のテスト スイートや実稼働アプリケーションに実装することもできます。

## <a name="next-steps"></a>次のステップ
この記事では、.NET コンソール アプリを使用して DocumentDB でパフォーマンスとスケールのテストを実行する方法を説明しました。 DocumentDB の操作について詳しくは、以下のリンクを参照してください。

* [DocumentDB パフォーマンス テスト サンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [DocumentDB のパフォーマンスを向上させるクライアント構成オプション](documentdb-performance-tips.md)
* [DocumentDB でのサーバー側のパーティション分割](documentdb-partition-data.md)
* [DocumentDB のコレクションとパフォーマンス レベル](documentdb-performance-levels.md)
* [MSDN の DocumentDB .NET SDK に関するドキュメント](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [DocumentDB .NET のサンプル](https://github.com/Azure/azure-documentdb-net)
* [パフォーマンスに関するヒントについての DocumentDB ブログ](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)


