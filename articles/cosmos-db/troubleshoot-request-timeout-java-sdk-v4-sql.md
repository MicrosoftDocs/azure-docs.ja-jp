---
title: Java v4 SDK の Azure Cosmos DB HTTP 408 または要求タイムアウトに関する問題のトラブルシューティング
description: Java v4 SDK を使用して Java SDK の要求タイムアウト例外を診断して修正する方法について説明します。
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: a805300ac62d0627c9b06188c9764a6887947afe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94411288"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-request-timeout-exceptions"></a>Azure Cosmos DB Java v4 SDK の要求タイムアウト例外を診断してトラブルシューティングする
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 408 エラーは、タイムアウト制限が発生する前に SDK が要求を完了できなかった場合に発生します。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
次の一覧には、要求タイムアウト例外の既知の原因と解決方法が含まれています。

### <a name="existing-issues"></a>既存の問題
要求がより長い期間停止しているか、タイムアウトがより頻繁に発生していると表示されている場合は、Java v4 SDK を最新バージョンにアップグレードしてください。 注: 4.7.0 以降のバージョンの使用を強くお勧めします。 詳細については、[Java v4 SDK リリース ノート](sql-api-sdk-java-v4.md)に関する記事を参照してください。

### <a name="high-cpu-utilization"></a>高い CPU 使用率
高い CPU 使用率は最も一般的なケースです。 最適な待機時間を実現するには、CPU 使用率は 40% ほどである必要があります。 CPU 使用率の (平均ではなく) 最大値を監視するには、間隔として 10 秒を使用します。 CPU スパイクは、1 つのクエリに対して複数の接続を実行する可能性があるクロスパーティション クエリでは、より一般的です。

#### <a name="solution"></a>解答:
SDK を使用するクライアント アプリケーションをスケールアップまたはスケールアウトする必要があります。

### <a name="connection-throttling"></a>接続の帯域幅調整
接続の帯域幅調整は、ホスト マシンの接続制限、または Azure SNAT (PAT) ポート不足のいずれかが原因で発生します。

### <a name="connection-limit-on-a-host-machine"></a>ホスト マシンの接続制限
一部の Linux システム (Red Hat など) では、開くファイルの最大数に上限があります。 Linux のソケットはファイルとして実装されるため、この数は接続の合計数も制限します。 次のコマンドを実行します。

```bash
ulimit -a
```

#### <a name="solution"></a>解決方法:
開くことができる最大ファイル数 ("nofile" で指定) は、10,000 個以上にする必要があります。 詳細については、Azure Cosmos DB Java SDK v4 の[パフォーマンスに関するヒント](performance-tips-java-sdk-v4-sql.md)を参照してください。

### <a name="socket-or-port-availability-might-be-low"></a>ソケットまたはポートの可用性が低下している可能性がある
Azure で実行されている場合、Java SDK を使用しているクライアントで Azure SNAT (PAT) ポートの枯渇が発生する可能性があります。

#### <a name="solution-1"></a>解決策 1:
Azure VM で実行している場合は、[SNAT ポートの枯渇に関するガイド](troubleshoot-java-sdk-v4-sql.md#snat)を参照してください。

#### <a name="solution-2"></a>解決策 2:
Azure App Service で実行している場合は、[接続エラーのトラブルシューティングのガイド](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause)に従って、[App Service の診断を利用](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html)してください。

#### <a name="solution-3"></a>解決策 3: 
Azure Functions で実行している場合は、必要なすべてのサービス (Azure Cosmos DB を含む) に対してシングルトンまたは静的クライアントを管理するための [Azure Functions の推奨事項](../azure-functions/manage-connections.md#static-clients)に従っていることを確認します。 関数アプリのホスティングの種類とサイズに基づく[サービスの制限](../azure-functions/functions-scale.md#service-limits)を確認します。

#### <a name="solution-4"></a>解決策 4:
HTTP プロキシを使用する場合は、SDK `GatewayConnectionConfig` で構成されている接続の数をサポートできることを確認します。 そうしないと、接続の問題が発生します。

### <a name="create-multiple-client-instances"></a>複数のクライアント インスタンスの作成
複数のクライアント インスタンスを作成すると、接続の競合とタイムアウトの問題を招くおそれがあります。

#### <a name="solution-1"></a>解決策 1:
[パフォーマンスに関するヒント](performance-tips-java-sdk-v4-sql.md#sdk-usage)に従って、アプリケーション全体で単一の CosmosClient インスタンスを使用します。

#### <a name="solution-2"></a>解決策 2:
シングルトン CosmosClient をアプリケーションに含めることができない場合は、CosmosClient のこの API `connectionSharingAcrossClientsEnabled(true)` を通じて、複数の Cosmos クライアントにわたって接続の共有を使用することをお勧めします。 同じ JVM に複数の Cosmos アカウントと対話する Cosmos Client のインスタンスが複数あるときは、これを有効にすると、Cosmos Client のインスタンス間で、実行できる場合に直接モードで接続の共有を実行できます。 このオプションを設定すると、最初にインスタンス化されたクライアントの接続構成 (ソケット タイムアウト構成、アイドル タイムアウト構成など) が、他のすべてのクライアント インスタンスに使用されることに注意してください。

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
* Azure Cosmos DB Java v4 SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-java-sdk-v4-sql.md)。
* [Java v4](performance-tips-java-sdk-v4-sql.md) のパフォーマンス ガイドラインを確認する。
