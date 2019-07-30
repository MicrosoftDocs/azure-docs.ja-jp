---
title: スロットルされた要求に関するガイダンス
description: Azure Resource Graph への要求のスロットルを回避するための適切なクエリの作成について説明します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c644d230846d9c644c3845348431eef36c8279c8
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276564"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Azure Resource Graph のスロットルされた要求に関するガイダンス

Azure Resource Graph データのプログラムによる頻繁な使用を作成するときは、スロットリングがクエリの結果にどのように影響するかを検討する必要があります。 データの要求方法を変更することで、ユーザーと組織でのスロットルが回避され、Azure リソースに関するタイムリーなデータのフローを維持することができます。

この記事では、Azure Resource Graph でのクエリの作成に関連する 4 つの領域とパターンについて説明します。

- スロットリング ヘッダーを理解する
- クエリのバッチ処理
- クエリの時間差処理
- 改ページ位置の自動修正の影響

## <a name="understand-throttling-headers"></a>スロットリング ヘッダーを理解する

Azure Resource Graph では、タイム ウィンドウに基づいて各ユーザーにクォータ数が割り当てられます。 たとえば、ユーザーは、5 秒間のウィンドウごとに最大 15 のクエリをスロットルなしで送信できます。 クォータ値は多数の要因によって決定され、変更される可能性があります。

すべてのクエリ応答で、Azure Resource Graph によって、2 つのスロットリング ヘッダーが追加されます。

- `x-ms-user-quota-remaining` (int):ユーザーの残りリソース クォータ。 この値はクエリ カウントにマップされます。
- `x-ms-user-quota-resets-after` (hh:mm:ss):ユーザーのクォータ消費量がリセットされるまでの期間。

ヘッダーの働きを示すために、ヘッダーと、値 `x-ms-user-quota-remaining: 10` と `x-ms-user-quota-resets-after: 00:00:03` が含まれているクエリ応答を検討してみましょう。

- 次の 3 秒以内に、最大 10 個のクエリをスロットルなしで送信できます。
- 3 秒後に、`x-ms-user-quota-remaining` と `x-ms-user-quota-resets-after` の値が、それぞれ `15` と `00:00:05` にリセットされます。

これらのヘッダーを使用したクエリ要求の "_バックオフ_" の例については、「[並行で処理されるクエリ](#query-in-parallel)」を参照してください。

## <a name="batching-queries"></a>クエリのバッチ処理

サブスクリプション、リソース グループ、または個々のリソースによるクエリのバッチ処理は、クエリの並列化よりも効率的です。 大規模なクエリのクォータ コストは、多くの場合、多数の小規模なターゲット クエリのクォータ コストよりも小さくなります。 バッチ サイズを _300_ 未満にすることが推奨されています。

- 最適化が不足しているアプローチの例

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- 最適化されたバッチ処理アプローチの例 1

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int batchSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / batchSize; ++i)
  {
      var currSubscriptionBatch = subscriptionIds.Skip(i * batchSize).Take(batchSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionBatch,
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- 最適化されたバッチ処理アプローチの例 2

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int batchSize = 100;
  for (var i = 0; i <= resourceIds.Count / batchSize; ++i)
  {
      var resourceIdBatch = string.Join(",",
          resourceIds.Skip(i * batchSize).Take(batchSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>クエリの時間差処理

スロットリングの実行方法のため、クエリを時間差で実行することをお勧めします。 つまり、60 のクエリを同時に送信する代わりに、クエリを 4 つの 5 秒間のウィンドウで時間差で送信します。

- 時間差を使用しないクエリのスケジュール

  | クエリ数         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | 期間 (秒) | 0-5 | 5-10 | 10-15 | 15-20 |

- 時間差を使用するクエリのスケジュール

  | クエリ数         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | 期間 (秒) | 0-5 | 5-10 | 10-15 | 15-20 |

次に示すのは、Azure Resource Graph をクエリするときのスロットリング ヘッダーに関する例です。

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>並行で処理されるクエリ

並列処理よりもバッチ処理が推奨されますが、クエリを簡単にバッチ処理できない場合があります。 このような場合は、複数のクエリを並列で送信することによって、Azure Resource Graph をクエリします。 このようなシナリオで、スロットリング ヘッダーに基づいて "_バックオフ_" する方法の例を次に示します。

```csharp
IEnumerable<IEnumerable<string>> queryBatches = /* Batches of queries  */
// Run batches in parallel.
await Task.WhenAll(queryBatches.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>改ページ位置の自動修正

Azure Resource Graph では、単一のクエリ応答で最大 1,000 のエントリが返されるため、探している完全なデータセットを取得するには、クエリの[改ページ調整](./work-with-data.md#paging-results)が必要な場合があります。 ただし、一部の Azure Resource Graph クライアントでは、他とは異なる改ページ位置の自動修正処理が行われます。

- C# SDK

  ResourceGraph SDK を使用する場合は、前のクエリ応答から返されるスキップ トークンを次の改ページ調整されるクエリに渡すことで、改ページ位置の自動修正を処理する必要があります。 この設計が意味しているのは、すべての改ページ調整された呼び出しの結果を収集し、それらを最後に結合する必要があることです。 この場合、改ページ調整された各クエリの送信によって、1 つのクエリ クォータが消費されます。

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Azure CLI/Azure PowerShell

  Azure CLI または Azure PowerShell の使用では、Azure Resource Graph に対するクエリは、自動的に最大 5,000 のエントリをフェッチするように改ページ調整されます。 クエリの結果では、すべての改ページ調整された呼び出しが結合されたエントリの一覧が返されます。 この場合、クエリの結果に含まれるエントリ数によっては、単一の改ページ調整されたクエリで、複数のクエリのクォータが消費される可能性があります。 たとえば、次の例では、クエリの 1 回の実行で、最大 5 のクエリのクォータが消費される可能性があります。

  ```azurecli-interactive
  az graph query -q 'project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>スロットルが解消されない場合

上記の推奨事項を実行してもスロットルされる場合は、チームにお問い合わせください ([resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com))。

次の詳細情報を提供してください。

- スロットリング制限が高い具体的なユース ケースとビジネス推進ニーズ。
- アクセスするリソースの数。 単一のクエリで返されるエントリの数。
- 関心のあるリソースの種類。
- お使いのクエリ パターン。 Y 秒あたり X 回のクエリなど。

## <a name="next-steps"></a>次の手順

- [初歩的なクエリ](../samples/starter.md)で使用されている言語を確認します。
- [高度なクエリ](../samples/advanced.md)で高度な使用方法を確認します。
- [リソースの探索](explore-resources.md)について学習します。