---
title: .NET SDK の Azure Cosmos DB HTTP 408 または要求タイムアウトに関する問題のトラブルシューティング
description: .NET SDK の要求タイムアウト例外を診断して修正する方法について説明します。
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 03/05/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: c8d35f7c666562022f503b2777f30f84193d0231
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102440005"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout-exceptions"></a>Azure Cosmos DB .NET SDK の要求タイムアウト例外を診断してトラブルシューティングする
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 408 エラーは、タイムアウト制限が発生する前に SDK が要求を完了できなかった場合に発生します。

## <a name="customize-the-timeout-on-the-azure-cosmos-db-net-sdk"></a>Azure Cosmos DB .NET SDK でタイムアウトをカスタマイズする

この SDK には、タイムアウトを制御する方法が 2 つあります。いずれもスコープが異なります。

### <a name="requesttimeout"></a>RequestTimeout

`CosmosClientOptions.RequestTimeout` (または、SDK v2 の場合は `ConnectionPolicy.RequestTimeout`) 構成では、個々のネットワーク要求に影響を与えるタイムアウトを設定できます。 ユーザーによって開始された操作は、複数のネットワーク要求にまたがる場合があります (たとえば、調整が発生する可能性があります)。 この構成は、ネットワーク要求ごとに再試行時に適用されます。 このタイムアウトはエンドツーエンドの操作要求タイムアウトではありません。

### <a name="cancellationtoken"></a>CancellationToken

SDK のすべての非同期操作にオプションの CancellationToken パラメーターがあります。 この [CancellationToken](/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) パラメーターは、すべてのネットワーク要求にわたって、操作全体を通して使用されます。 ネットワーク要求の合間にキャンセル トークンが確認され、関連トークンの有効期間が切れている場合、操作が取り消されます。 キャンセル トークンは、操作スコープに予想されるおよそのタイムアウトを定義する目的で使用してください。

> [!NOTE]
> `CancellationToken` パラメーターは、取り消しによって[無効な状態が引き起こされない](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/)ときに、ライブラリによって取り消しが確認されるメカニズムです。 取り消しに定義された時間が経過しても、厳密にその時点で操作がキャンセルされない場合があります。 その代わりに、その時間が経過した後、そうしても安全なときにキャンセルされます。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
次の一覧には、要求タイムアウト例外の既知の原因と解決方法が含まれています。

### <a name="high-cpu-utilization"></a>高い CPU 使用率
高い CPU 使用率は最も一般的なケースです。 最適な待機時間を実現するには、CPU 使用率は 40% ほどである必要があります。 CPU 使用率の (平均ではなく) 最大値を監視するには、間隔として 10 秒を使用します。 CPU スパイクは、1 つのクエリに対して複数の接続を実行する可能性があるクロスパーティション クエリでは、より一般的です。

エラーに `TransportException` 情報が含まれている場合は、`CPU History` も含まれている可能性があります。

```
CPU history: 
(2020-08-28T00:40:09.1769900Z 0.114), 
(2020-08-28T00:40:19.1763818Z 1.732), 
(2020-08-28T00:40:29.1759235Z 0.000), 
(2020-08-28T00:40:39.1763208Z 0.063), 
(2020-08-28T00:40:49.1767057Z 0.648), 
(2020-08-28T00:40:59.1689401Z 0.137), 
CPU count: 8)
```

* CPU 測定値が 70% を超える場合、タイムアウトは CPU の枯渇が原因である可能性があります。 この場合の解決策は、CPU 使用率が高いソースを調査してそれを減らすか、マシンをより大きなリソース サイズにスケーリングすることです。
* CPU 測定が 10 秒ごとに行われていない場合 (たとえば、間隔または測定時間が測定間の時間が長いことを示している場合)、原因はスレッドの枯渇です。 この場合の解決策は、スレッドの枯渇 (ロックされている可能性のあるスレッド) のソースを調査するか、マシンをより大きなリソース サイズにスケーリングすることです。

#### <a name="solution"></a>解決方法:
SDK を使用するクライアント アプリケーションをスケールアップまたはスケールアウトする必要があります。

### <a name="socket-or-port-availability-might-be-low"></a>ソケットまたはポートの可用性が低下している可能性がある
Azure で実行されている場合、.NET SDK を使用しているクライアントで Azure SNAT (PAT) ポートの枯渇が発生する可能性があります。

#### <a name="solution-1"></a>解決策 1:
Azure VM で実行している場合は、[SNAT ポートの枯渇に関するガイド](troubleshoot-dot-net-sdk.md#snat)を参照してください。

#### <a name="solution-2"></a>解決策 2:
Azure App Service で実行している場合は、[接続エラーのトラブルシューティングのガイド](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause)に従って、[App Service の診断を利用](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html)してください。

#### <a name="solution-3"></a>解決策 3: 
Azure Functions で実行している場合は、必要なすべてのサービス (Azure Cosmos DB を含む) に対してシングルトンまたは静的クライアントを管理するための [Azure Functions の推奨事項](../azure-functions/manage-connections.md#static-clients)に従っていることを確認します。 関数アプリのホスティングの種類とサイズに基づく[サービスの制限](../azure-functions/functions-scale.md#service-limits)を確認します。

#### <a name="solution-4"></a>解決策 4:
HTTP プロキシを使用する場合は、SDK `ConnectionPolicy` で構成されている接続の数をサポートできることを確認します。 そうしないと、接続の問題が発生します。

### <a name="create-multiple-client-instances"></a>複数のクライアント インスタンスの作成
複数のクライアント インスタンスを作成すると、接続の競合とタイムアウトの問題を招くおそれがあります。

#### <a name="solution"></a>解決方法:
[パフォーマンスに関するヒント](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)に従って、プロセス全体で単一の CosmosClient インスタンスを使用します。

### <a name="hot-partition-key"></a>ホット パーティション キー
Azure Cosmos DB は、プロビジョニングされたスループット全体を物理パーティション間で均等に分散します。 ホット パーティションが存在すると、ある物理パーティション上の 1 つ以上の論理パーティション キーによってその物理パーティションのすべての要求ユニット/秒 (RU/秒) が消費されます。 同時に、他の物理パーティション上の RU/秒は未使用のままになります。 症状としては、消費済み RU/秒の合計は、データベースまたはコンテナー上にプロビジョニングされている全体的な RU/秒よりも少なくなりますが、ホット論理パーティション キーに対する要求では、帯域幅調整 (429) が見られます。 [正規化された RU 消費量メトリック](monitor-normalized-request-units.md) を使用して、ワークロードでホット パーティションが発生しているかどうかを確認します。 

#### <a name="solution"></a>解決方法:
要求のボリュームと記憶域を均等に分散する適切なパーティション キーを選択します。 [パーティション キーの変更](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)方法を参照してください。

### <a name="high-degree-of-concurrency"></a>同時実行の程度が高い
アプリケーションで高レベルの同時実行が行われています。これにより、チャネル上で競合が発生する可能性があります。

#### <a name="solution"></a>解決方法:
SDK を使用するクライアント アプリケーションをスケールアップまたはスケールアウトする必要があります。

### <a name="large-requests-or-responses"></a>大量の要求または応答
要求数や応答数が増大すると、同時実行の程度が比較的低い場合でも、チャネル上でヘッドオブライン ブロッキングが発生し、競合が悪化する可能性があります。

#### <a name="solution"></a>解決方法:
SDK を使用するクライアント アプリケーションをスケールアップまたはスケールアウトする必要があります。

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>エラー率が Azure Cosmos DB の SLA に収まっている
アプリケーションでは、一時的なエラーの処理と必要に応じた再試行を実行できる必要があります。 パスの作成時にサービスによって項目が作成されたか否かを知ることはできないため、408 例外は再試行されません。 作成のために同じ項目を再度送信すると、競合例外が発生します。 ユーザー アプリケーションのビジネス ロジックに、競合を処理するためのカスタム ロジックが含まれている場合があります。これにより、既存の項目のあいまいさが解消されたり、作成の再試行と競合したりすることがあります。

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>エラー率が Azure Cosmos DB の SLA に違反している
[Azure サポート](https://aka.ms/azure-support)にお問い合わせください。

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB .NET SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)。
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET v2](performance-tips.md) のパフォーマンス ガイドラインを確認する。