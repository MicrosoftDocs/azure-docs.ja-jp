---
title: Azure Cosmos DB でクエリを実行するために要求ユニットとコストを最適化する
description: クエリの要求ユニット使用量を評価し、パフォーマンスとコストの観点からクエリを最適化する方法について説明します。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 80c9cd91efd14e3d4b4214bde089f73692568f76
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57840190"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Azure Cosmos DB でのクエリ コストを最適化する

Azure Cosmos DB では、コンテナー内の項目を操作するリレーショナル クエリや階層クエリなど、豊富なデータベース操作が提供されます。 これらの操作のそれぞれに関連付けられたコストは、操作を完了するために必要な CPU、IO、およびメモリに応じて異なります。 ハードウェア リソースについて考えて管理する代わりに、要求ユニット (RU) を、さまざまなデータベース操作を実行して要求を処理するのに必要なリソースの 1 つの測定単位として考えることができます。 この記事では、クエリの要求ユニット使用量を評価し、パフォーマンスとコストの観点からクエリを最適化する方法について説明します。 

Azure Cosmos DB でのクエリは、通常、以下のようにスループットの観点から、最も実行時間が短く、効率的なものから、実行時間が長く、効率性の低いものの順に並べられます。  

* 1 つのパーティション キーと項目キーでの取得操作。

* 1 つのパーティション キー内にフィルター句を含むクエリ。

* 任意のプロパティに対して等値または範囲フィルター句を使用しないクエリ。

* フィルターを使用しないクエリ。

1 つ以上のパーティションからデータを読み取るクエリでは、待機時間がより長くなり、より多くの数の要求ユニットが使用されます。 各パーティションにはすべてのプロパティに対する自動インデックス作成機能があるため、インデックスからクエリを効率的に実行できます。 並行処理オプションを使うことで、より迅速に複数のパーティションを使用するクエリを実行できます。 パーティション分割とパーティション キーの詳細については、「[Azure Cosmos DB のパーティション分割](partitioning-overview.md)」をご覧ください。

## <a name="evaluate-request-unit-charge-for-a-query"></a>クエリの要求ユニット使用量を評価する

Azure Cosmos コンテナーでデータを格納したら、Azure ポータルのデータ エクスプローラーを使用して、クエリを構築して実行することができます。 また、データ エクスプローラーを使用して、クエリのコストを取得することもできます。 この方法では、ご利用のシステムでサポートされる一般的なクエリと操作に関する実際の使用量を把握できます。

また、SDK を使用して、クエリのコストをプログラムで取得することもできます。 作成、更新、削除などの操作のオーバーヘッドを測定するには、REST API を使用するときに `x-ms-request-charge` ヘッダーを確認します。 .NET または Java SDK を使用する場合、`RequestCharge` プロパティは要求使用量を取得するためのプロパティと同等であり、このプロパティは ResourceResponse または FeedResponse 内にあります。

```csharp
// Measure the performance (request units) of writes 
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument); 

Console.WriteLine("Insert of an item consumed {0} request units", response.RequestCharge); 

// Measure the performance (request units) of queries 
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery(); 

while (queryable.HasMoreResults) 
     { 
          FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
          Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge); 
     }
```

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>クエリの要求ユニット使用量に影響する要因

クエリの要求ユニットはさまざまな要因に依存します。 たとえば、読み込まれる/返される Azure Cosmos 項目の数、インデックスに対する参照の数、クエリのコンパイル時間などの詳細です。 Azure Cosmos DB では、同じデータに対して実行される同じクエリによって、繰り返しの実行でも常に同じ数の要求ユニットが使用されることが保証されます。 クエリ実行メトリックを使用するクエリ プロファイルでは、要求ユニットがどのように使用されたかを把握できます。  

場合によっては、一連の 200 個および 429 個の応答、およびページングされたクエリの実行での変化する要求ユニットが表示されます。これは、クエリが利用可能な RU に基づいて、できるだけ早く実行されるためです。 サーバーとクライアントの間の複数のページ/ラウンド トリップに分割されるクエリの実行が表示される場合もあります。 たとえば、10,000 項目が複数のページとして返され、それぞれ、そのページに対して行われた計算に基づいて課金される場合があります。 これらのページ全体で合計するときに、クエリ全体の場合と同じ数の RU を取得する必要があります。  

## <a name="metrics-for-troubleshooting"></a>トラブルシューティング用のメトリック

パフォーマンス、クエリで使用されるスループット、ユーザー定義関数 (UDF) は、ほとんどの場合、関数本体によって異なります。 UDF で費やされるクエリの実行時間と、使用される RU の数を確認する最も簡単な方法は、クエリ メトリックを有効にすることです。 .NET SDK を使用する場合に、SDK によって返されるクエリ メトリックのサンプルを以下に示します。

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

## <a name="best-practices-to-cost-optimize-queries"></a>コストのクエリを最適化する場合のベスト プラクティス 

コストのクエリを最適化するときに、次のベスト プラクティスについて検討してください。

* **複数のエンティティ型を併置する**

   1 つまたは少数のコンテナー内に複数のエンティティ型を併置してみます。 この方法では、価格の観点からだけでなく、クエリの実行やトランザクションについても利点が得られます。 クエリのスコープは 1 つのコンテナーとなります。ストアド プロシージャ/トリガーを介した複数レコードに対するアトミック トランザクションのスコープは、1 つのコンテナー内のパーティション キーとなります。 同じコンテナー内のエンティティーを併置することで、ネットワークのラウンド トリップ数を減らして、レコード全体の関係を解決することができます。 したがって、エンド ツー エンドのパフォーマンスが向上し、大規模なデータセットの複数のレコードに対するアトミック トランザクションが有効になり、その結果、コストが低くなります。 ご自分のシナリオでは 1 つまたは少数のコンテナー内に複数のエンティティ型を併置するのが難しい場合は (通常は、既存のアプリケーションを移行し、コードを変更したくないため)、データベース レベルでのスループットのプロビジョニングを検討する必要があります。  

* **測定と調整によって 1 秒あたりの要求ユニットの使用量を削減する**

   クエリの複雑さは、操作で使用される要求ユニット (RU) の数に影響します。 述語の数、述語の特性、UDF の数、ソース データ セットのサイズ。 これらすべての要因がクエリ操作のコストに影響します。 

   要求ヘッダーで返される要求使用量は、特定のクエリのコストを示します。 たとえば、クエリで 1 KB の項目が 1000 個返された場合、操作のコストは 1000 となります。 そのため、後続の要求をレート制限する前に、サーバーは 1 秒以内にこのような要求を 2 つだけ受け付けます。 詳細については、[要求ユニット](request-units.md)に関する記事と要求ユニット計算ツールのページを参照してください。 

## <a name="next-steps"></a>次の手順

次は、先に進み、以下の各記事で Azure Cosmos DB でのコストの最適化の詳細について学習することができます。

* [Azure Cosmos の料金体系](how-pricing-works.md)の詳細について学習します
* [開発とテストのための最適化](optimize-dev-test.md)の詳細について学習します
* [Azure Cosmos DB の課金内容の確認](understand-your-bill.md)の詳細について学習します
* [スループット コストの最適化](optimize-cost-throughput.md)の詳細について学習します
* [ストレージ コストの最適化](optimize-cost-storage.md)の詳細について学習します
* [読み取りと書き込みのコストの最適化](optimize-cost-reads-writes.md)の詳細について学習します
* [複数リージョンの Azure Cosmos アカウント コストの最適化](optimize-cost-regions.md)の詳細について学習します
* [Azure Cosmos DB の予約容量](cosmos-db-reserved-capacity.md)の詳細について学習します

