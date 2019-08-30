---
title: Azure Cosmos DB 使用時のクエリの問題を診断し、トラブルシューティングする
description: Azure Cosmos DB の SQL クエリに関する問題を特定、診断、およびトラブルシューティングする方法について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: d0dd9a371c4912cae0e74b214c673c629fc1ff55
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515817"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Azure Cosmos DB のクエリのパフォーマンスをトラブルシューティングする
この記事では、Azure Cosmos DB の SQL クエリに関する問題を特定、診断、およびトラブルシューティングする方法について説明します。 Azure Cosmos DB のクエリのパフォーマンスを最適化するには、次のトラブルシューティング手順に従います。 

## <a name="collocate-clients-in-same-azure-region"></a>クライアントを同じ Azure リージョンに併置する 
最短の待機時間は、プロビジョニングされた Azure Cosmos DB エンドポイントと同じ Azure リージョン内に呼び出し元アプリケーションを配置することによって実現されます。 利用可能なリージョンの一覧については、記事「[Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/#services)」をご覧ください。

## <a name="check-consistency-level"></a>整合性レベルを確認する
[整合性レベル](consistency-levels.md)は、パフォーマンスと料金に影響する可能性があります。 整合性レベルが特定のシナリオに適していることを確認します。 詳細については、[整合性レベルの選択](consistency-levels-choosing.md)に関する記事をご覧ください。

## <a name="log-the-executed-sql-query"></a>実行された SQL クエリをログに記録する 

実行された SQL クエリを、ストレージ アカウントまたは診断ログ テーブルに記録できます。 [診断ログを使用した SQL クエリ ログ](logging.md#turn-on-logging-in-the-azure-portal)を使用すると、難読化されたクエリを任意のストレージ アカウントに記録できます。 これにより、ログを調べて、より高い RU を使用しているクエリを見つけることができます。 後で、アクティビティ ID を使用して、QueryRuntimeStatistics の実際のクエリと一致させることができます。 クエリは、セキュリティ上の目的で難読化されています。クエリ パラメーター名と where 句の値は、実際の名前と値とは異なります。 ストレージ アカウントへのログ記録を使用すると、実行されたクエリの長期保存を維持できます。  

## <a name="log-query-metrics"></a>ログ クエリ メトリック

低速または高コストのクエリをトラブルシューティングするために、`QueryMetrics` を使用します。 

  * `QueryMetrics` を応答に含めるために `FeedOptions.PopulateQueryMetrics = true` を設定します。
  * `QueryMetrics` クラスには、オーバーロードされた `.ToString()` 関数があります。これを呼び出すと、`QueryMetrics` の文字列表現を取得できます。 
  * メトリックを使用すると、特に次の分析情報を得ることができます。 
  
      * クエリ パイプラインの特定のコンポーネントの完了に異常に長い時間がかかっているかどうか (数百ミリ秒以上)。 

          * `TotalExecutionTime` を確認します。
          * クエリの `TotalExecutionTime` がエンドツーエンドの実行時間よりも短い場合、クライアント側またはネットワークで時間がかかっています。 クライアントと Azure リージョンが併置されていることを再度確認します。
      
      * 分析されたドキュメントに誤検出があるかどうか (出力ドキュメント数が、取得されたドキュメント数よりもはるかに少ない場合)。  

          * `Index Utilization` を確認します。
          * `Index Utilization` = (返されたドキュメントの数/読み込まれたドキュメントの数)
          * 返されたドキュメントの数が読み込まれた数よりもはるかに少ない場合、誤検出が分析されます。
          * フィルターを絞り込むことで、取得するドキュメントの数を制限します。  

      * 個々のラウンドトリップがどのように処理されたか (`QueryMetrics` の文字列表現から `Partition Execution Timeline` を確認します)。 
      * クエリで高コストの要求使用量が発生したかどうか。 

詳細については、[SQL クエリの実行メトリックの取得方法](profile-sql-api-query.md)に関する記事をご覧ください。
      
## <a name="tune-query-feed-options-parameters"></a>クエリのフィード オプション パラメーターを調整する 
クエリのパフォーマンスは、要求の[フィード オプション](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) パラメーターを使用して調整できます。 次のオプションを設定してみてください。

  * 最初に `MaxDegreeOfParallelism` を -1 に設定し、その後、異なる値でパフォーマンスを比較します。 
  * 最初に `MaxBufferedItemCount` を -1 に設定し、その後、異なる値でパフォーマンスを比較します。 
  * `MaxItemCount` を -1 に設定します。

異なる値でパフォーマンスを比較するときは、2、4、8、16 などの値を試してください。
 
## <a name="read-all-results-from-continuations"></a>継続からすべての結果を読み取る
すべての結果を取得していないと考えられる場合は、継続を完全にドレインしてください。 つまり、継続トークンによって多くのドキュメントが生成されている間、結果の読み取りを続けます。

完全なドレインは、次のいずれかのパターンで実現できます。

  * 継続が空ではない間、結果の処理を続けます。
  * クエリによって多くの結果が生成される間、処理を続けます。 

    ```csharp
    // using AsDocumentQuery you get access to whether or not the query HasMoreResults
    // If it does, just call ExecuteNextAsync until there are no more results
    // No need to supply a continuation token here as the server keeps track of progress
    var query = client.CreateDocumentQuery<Family>(collectionLink, options).AsDocumentQuery();
    while (query.HasMoreResults)
    {
        foreach (Family family in await query.ExecuteNextAsync())
        {
            families.Add(family);
        }
    }
    ```

## <a name="choose-system-functions-that-utilize-index"></a>インデックスを使用するシステム関数を選択する
式を文字列値の範囲に変換できる場合、インデックスを使用できます。そうでない場合は使用できません。 

インデックスを使用できる文字列関数の一覧を次に示します。 
    
  * STARTSWITH(str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * SUBSTRING(str_expr, num_expr, num_expr) = str_expr, but only if first num_expr is 0 
    
    次にクエリの例をいくつか示します。 
    
    ```sql

    -- If there is a range index on r.name, STARTSWITH will utilize the index while ENDSWITH won't 
    SELECT * 
    FROM c 
    WHERE STARTSWITH(c.name, 'J') AND ENDSWITH(c.name, 'n')

    ```
    
    ```sql

    -- LEFT will utilize the index while RIGHT won't 
    SELECT * 
    FROM c 
    WHERE LEFT(c.name, 2) = 'Jo' AND RIGHT(c.name, 2) = 'hn'

    ```

  * インデックスによって処理されないフィルター (または WHERE 句) でシステム関数は使用しないでください。 このようなシステム関数の例として、Contains、Upper、Lower などがあります。
  * 可能な場合は、パーティション キーでフィルターを使用するクエリを記述します。
  * クエリのパフォーマンスを向上させるために、フィルターで UPPER/LOWER を呼び出さないようにします。 代わりに、挿入時に値の大文字と小文字を正規化します。 それぞれの値について、必要な大文字と小文字を使用した値を挿入するか、元の値と、必要な大文字と小文字を使用した値の両方を挿入します。 

    例:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    この場合、すべて大文字の "JOE" を格納するか、元の値である "Joe" と "JOE" の両方を格納します。 
    
    JSON データの大文字と小文字が正規化されている場合、クエリは次のようになります。
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    2 番目のクエリではパフォーマンスがより向上します。これは、"JOE" と値を比較するために、各値に対して変換を実行する必要がないためです。

システム関数の詳細については、記事「[システム関数](sql-query-system-functions.md)」をご覧ください。

## <a name="check-indexing-policy"></a>インデックス作成ポリシーを確認する
現在の[インデックス作成ポリシー](index-policy.md)が最適であるか確認するには、次のようにします。

  * 読み取りを高速にするために、クエリで使用されるすべての JSON パスがインデックス作成ポリシーに含まれていることを確認します。
  * 書き込みのパフォーマンスを向上させるために、クエリで使用されないパスは除外します。

詳細については、記事「[インデックス作成ポリシーを管理する方法](how-to-manage-indexing-policy.md)」をご覧ください。

## <a name="spatial-data-check-ordering-of-points"></a>空間データ:ポイントの順序を確認する
Polygon 内のポイントは、反時計回りに指定する必要があります。 時計回りに指定された Polygon は、その中の領域を逆にしたものを表します。

## <a name="optimize-join-expressions"></a>JOIN 式を最適化する
`JOIN` 式は、大規模なクロス積に展開できます。 可能な場合は、より絞り込んだフィルターを使用して、より小さい検索領域に対してクエリを実行します。

複数値サブクエリは、`WHERE` 句内のすべてのクロス結合の後ではなく、それぞれの select-many 式の後に述語をプッシュすることによって `JOIN` 式を最適化できます。 詳細な例については、記事「[JOIN 式を最適化する](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions)」をご覧ください。

## <a name="optimize-order-by-expressions"></a>ORDER BY 式を最適化する 
フィールドがスパースである場合、またはインデックス ポリシーに含まれていない場合、`ORDER BY` クエリのパフォーマンスが低下する可能性があります。

  * 時間などのスパース フィールドでは、フィルターを使用して検索領域をできるだけ小さくします。 
  * 単一のプロパティ `ORDER BY` については、インデックス ポリシーにプロパティを含めます。 
  * 複数のプロパティ `ORDER BY` の式については、並べ替えられるフィールドに対して[複合インデックス](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes)を定義します。  

## <a name="many-large-documents-being-loaded-and-processed"></a>多数の大規模ドキュメントの読み込みと処理
クエリに必要な時間と RU は、応答のサイズだけでなく、クエリ処理パイプラインによって実行される処理にも依存します。 時間と RU は、クエリ処理パイプライン全体によって実行される処理量に比例して増加します。 大規模なドキュメントの場合、より多くの処理が実行されるため、大規模なドキュメントを読み込んで処理するには、より多くの時間と RU が必要になります。

## <a name="low-provisioned-throughput"></a>プロビジョニングされているスループットが低い
プロビジョニングされたスループットでワークロードを処理できることを確認します。 影響を受けるコレクションの RU 予算を増やします。

## <a name="try-upgrading-to-the-latest-sdk-version"></a>最新の SDK バージョンにアップグレードする
最新の SDK を確認するには、[SDK のダウンロードとリリース ノート](sql-api-sdk-dotnet.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順
次のドキュメントを参照して、クエリあたりの RU 数を測定する方法を確認し、実行の統計を取得してクエリの調整などを行います。

* [.NET SDK を使用して SQL クエリの実行メトリックを取得する](profile-sql-api-query.md)
* [Azure Cosmos DB を使用したクエリ パフォーマンスのチューニング](sql-api-sql-query-metrics.md)
* [.NET SDK のパフォーマンスに関するヒント](performance-tips.md)
