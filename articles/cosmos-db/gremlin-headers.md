---
title: Azure Cosmos DB Gremlin の応答ヘッダー
description: さらなるトラブルシューティングを可能にするサーバー応答メタデータのリファレンス ドキュメント
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: christopheranderson
ms.author: chrande
ms.openlocfilehash: 0442d21aebe1cf577c50d14a5aeff40bd1f6cd9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98600532"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Azure Cosmos DB Gremlin サーバーの応答ヘッダー
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

この記事では、要求の実行時に Cosmos DB Gremlin サーバーから呼び出し元に返されるヘッダーについて説明します。 これらのヘッダーは、要求パフォーマンスのトラブルシューティング、Cosmos DB サービスとネイティブに統合されるアプリケーションの構築、カスタマー サポートの簡素化に役立ちます。

これらのヘッダーへの依存関係があると、アプリケーションの移植性が他の Gremlin の実装に限定されることに注意してください。 その代わり、Cosmos DB Gremlin との統合はより緊密になります。 これらのヘッダーは TinkerPop 標準ではありません。

## <a name="headers"></a>ヘッダー

| ヘッダー | 種類 | 値の例 | 含まれる場合 | 説明 |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11.3243 | Success and Failure | 部分的な応答メッセージの[要求ユニット (RU/秒または RU)](request-units.md) で使用されているコレクションまたはデータベースのスループットの量。 このヘッダーは、複数のチャンクを持つ要求が継続するたびに存在します。 これには、特定の応答チャンクの料金が反映されます。 1 つの応答チャンクで構成される要求の場合のみ、このヘッダーはトラバーサルの総コストと一致します。 ただし、大半の複雑なトラバーサルでは、この値が表すのは部分的なコストです。 |
| **x-ms-total-request-charge** | double | 423.987 | Success and Failure | 要求全体の[要求ユニット (RU/秒または RU)](request-units.md) で使用されているコレクションまたはデータベースのスループットの量。 このヘッダーは、複数のチャンクを持つ要求が継続するたびに存在します。 これは、要求の開始以降に累積された料金を示します。 最後のチャンクに含まれるこのヘッダーの値が、完了した要求の料金を示します。 |
| **x-ms-server-time-ms** | double | 13.75 | Success and Failure | このヘッダーは、待ち時間のトラブルシューティングのために用意されています。 これは、Cosmos DB Gremlin サーバーで実行と部分的な応答メッセージの生成にかかった時間 (ミリ秒単位) を示します。 このヘッダーの値を使用して、それを要求の待ち時間アプリケーション全体と比較すると、ネットワーク待ち時間のオーバーヘッドを計算できます。 |
| **x-ms-total-server-time-ms** | double | 130.512 | Success and Failure | Cosmos DB Gremlin サーバーでトラバーサル全体の実行にかかった合計時間 (ミリ秒単位)。 このヘッダーは、すべての部分的な応答に含まれます。 これは、要求の開始以降に累積された実行時間を表します。 最後の応答は、総実行時間を示します。 このヘッダーは、遅延の原因としてクライアントとサーバーを区別する際に役立ちます。 クライアントでのトラバーサルの実行時間をこのヘッダーの値と比較できます。 |
| **x-ms-status-code** | long | 200 | Success and Failure | ヘッダーは、要求が完了または終了した内部的な理由を示します。 アプリケーションはこのヘッダーの値を確認して是正措置を講じるよう求められます。 |
| **x-ms-substatus-code** | long | 1003 | 失敗のみ | Cosmos DB は、一元化されたストレージ レイヤーをベースに構築されているマルチモデル データベースです。 このヘッダーには、高可用性スタックの下位レイヤーでエラーが発生した場合のエラーの理由に関する追加の分析情報が含まれます。 アプリケーションは、このヘッダーを格納し、Cosmos DB のカスタマー サポートに問い合わせる際にそれを使用するよう求められます。 このヘッダーの値は、Cosmos DB エンジニアによる迅速なトラブルシューティングに役立ちます。 |
| **x-ms-retry-after-ms** | string (TimeSpan) | "00:00:03.9500000" | 失敗のみ | このヘッダーは、.NET の [TimeSpan](/dotnet/api/system.timespan) 型の文字列表現です。 この値は、プロビジョニングされたスループットの消費によって失敗した要求のみに含まれます。 アプリケーションは、指示された期間の経過後にトラバーサルを再送信する必要があります。 |
| **x-ms-activity-id** | string (Guid) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Success and Failure | ヘッダーには、要求の一意のサーバー側識別子が格納されます。 各要求には、追跡目的でサーバーから一意識別子が割り当てられます。 アプリケーションは、ユーザーがカスタマー サポートに問い合わせる要求についてサーバーから返されたアクティビティ識別子をログに記録する必要があります。 Cosmos DB のサポート担当者は、Cosmos DB サービスのテレメトリ内にあるこれらの識別子によって特定の要求を見つけることができます。 |

## <a name="status-codes"></a>状態コード

サーバーから返される、`x-ms-status-code` 状態属性に関する最も一般的なコードを下に示します。

| Status | 説明 |
| --- | --- |
| **401** | エラー メッセージ `"Unauthorized: Invalid credentials provided"` は、認証用パスワードが Cosmos DB アカウント キーと一致しない場合に返されます。 Azure portal でお使いの Cosmos DB Gremlin アカウントに移動し、キーが正しいことを確認してください。|
| **404** | 同じエッジまたは頂点の削除と更新を同時に実行しようとする同時実行操作があります。 `"Owner resource does not exist"` というエラー メッセージは、指定されたデータベースまたはコレクションの `/dbs/<database name>/colls/<collection or graph name>` 形式の接続パラメーターに誤りがあることを示しています。|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` 通常、これは、識別子を持つ頂点または辺がグラフに既に存在する場合に発生します。| 
| **412** | 状態コードには、エラー メッセージ `"PreconditionFailedException": One of the specified pre-condition is not met` が併記されます。 このエラーは、エッジまたは頂点の読み取りと、変更後のストアへの書き戻しの間のオプティミスティック同時実行制御違反を示しています。 このエラーが発生する最も一般的な状況は、プロパティの変更 (たとえば `g.V('identifier').property('name','value')`) です。 Gremlin エンジンは頂点を読み取って変更し、書き戻します。 同じ頂点またはエッジを書き込もうとしている別のトラバーサルが並列実行されている場合は、そのいずれかでこのエラーが発生します。 アプリケーションはサーバーにトラバーサルを再度送信する必要があります。| 
| **429** | 要求が調整されたので、**x-ms-retry-after-ms** の値の経過後に再試行する必要があります| 
| **500** | `"NotFoundException: Entity with the specified id does not exist in the system."` を含むエラー メッセージは、データベースやコレクションが同じ名前で再作成されたことを示します。 さまざまな Cosmos DB コンポーネントで変更が反映され、キャッシュが無効になるため、このエラーは 5 分以内に解消されます。 この問題を回避するには、毎回一意のデータベース名とコレクション名を使用します。| 
| **1000** | この状態コードは、サーバーがメッセージの解析に成功したものの実行できなかった場合に返されます。 これは通常、クエリに問題があることを示します。| 
| **1001** | このコードは、サーバーがトラバーサルの実行を完了しても、応答をクライアントにシリアル化できない場合に返されます。 このエラーが発生する可能性があるのは、トラバースによって複雑な結果が生成される場合です。この結果が大きすぎるか、TinkerPop プロトコルの仕様に準拠していません。 アプリケーションは、このエラーが発生したときにトラバーサルを簡略化する必要があります。 | 
| **1003** | トラバーサルが許容されたメモリ制限を超えると、`"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"` が返されます。 トラバーサルごとのメモリ制限は **2 GB** です。| 
| **1004** | この状態コードは、グラフ要求の形式が正しくないことを示します。 要求が間違った形式になるのは、逆シリアル化に失敗した場合、値ではない型が値の型として逆シリアル化されている場合、またはサポートされていない Gremlin 操作が要求された場合です。 アプリケーションは要求を再試行しないでください。これが成功することはありません。 | 
| **1007** | 通常、この状態コードはエラー メッセージ `"Could not process request. Underlying connection has been closed."` と共に返されます。 この状況は、サーバーによって閉じられている接続をクライアント ドライバーが使用しようとした場合に発生する可能性があります。 アプリケーションは、別の接続でトラバーサルを再試行する必要があります。
| **1008** | Cosmos DB Gremlin サーバーは、接続を終了してクラスター内のトラフィックを再調整できます。 クライアント ドライバーは、この状況に対処し、サーバーへの要求の送信にライブ接続のみを使用する必要があります。 場合によっては、接続が閉じられたことをクライアント ドライバーで検出できないことがあります。 アプリケーションでエラー `"Connection is too busy. Please retry after sometime or open more connections."` が発生した場合は、別の接続でトラバーサルを再試行してください。
| **1009** | 割り当てられた時間内に操作が完了せず、サーバーによって取り消されました。 トラバーサルの各ホップで頂点またはエッジをフィルター処理して検索範囲を絞り込むことで、トラバーサルを迅速に実行できるよう最適化してください。 要求のタイムアウトの既定値は **60 秒** です。 |

## <a name="samples"></a>サンプル

1 つの状態属性を読み取る、Gremlin.Net をベースにしたサンプル クライアント アプリケーション:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Gremlin Java クライアントから状態属性を読み取る方法を示す例:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>次の手順
* [Azure Cosmos DB の HTTP 状態コード](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Azure Cosmos DB の一般的な REST 応答ヘッダー](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [TinkerPop Graph Driver Provider の要件]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
