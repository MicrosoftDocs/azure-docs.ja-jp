---
title: Azure Cosmos DB .NET SDK の使用時の問題を診断しトラブルシューティングする
description: クライアント側のログや他のサード パーティ製ツールなどの機能を使って、.NET SDK 使用時の Azure Cosmos DB の問題を特定、診断、およびトラブルシューティングします。
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 03/05/2021
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 1f7548b355353eb77419f4d1760b40ba02eeddda
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442198"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Azure Cosmos DB .NET SDK の使用時の問題を診断しトラブルシューティングする
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

この記事では、Azure Cosmos DB SQL API アカウントで [.NET SDK](sql-api-sdk-dotnet.md) を使用するときの一般的な問題、回避策、診断手順、およびツールについて説明します。
.NET SDK には、Azure Cosmos DB SQL API にアクセスするためのクライアント側の論理表現が用意されています。 この記事では、問題が発生した場合に役立つツールとアプローチについて説明します。

## <a name="checklist-for-troubleshooting-issues"></a>問題をトラブルシューティングするためのチェックリスト

運用環境にアプリケーションを移行する前に、次のチェックリストを検討してください。 チェックリストを使用すればと、発生する可能性のある一般的な問題のいくつかを防止できます。 問題が発生したときにもすばやく診断できます。

*    最新の [SDK](sql-api-sdk-dotnet-standard.md) を使用します。 プレビュー SDK は運用環境で使用しないでください。 これにより、既に修正されている既知の問題の発生が防止されます。
*    [パフォーマンスに関するヒント](performance-tips.md)を確認し、推奨される方法に従います。 これは、スケーリング、待機時間、その他のパフォーマンス上の問題の防止に役立ちます。
*    問題のトラブルシューティングに役立つ SDK のログ機能を有効にします。 ログ機能を有効にするとパフォーマンスに影響することがあるため、問題のトラブルシューティング時にのみ有効にすることをお勧めします。 次のログを有効にできます。
*    Azure portal を使用して[メトリックを記録します](./monitor-cosmos-db.md)。 ポータルのメトリックは Azure Cosmos DB テレメトリを表示します。これは、問題が Azure Cosmos DB に対応したものか、クライアント側から発生したものかを判断する場合に役立ちます。
*    ポイント操作応答から、V2 SDK では[診断文字列](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring)、V3 SDK では[診断](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics)をログに記録します。
*    すべてのクエリ応答からの [SQL クエリ メトリック](sql-api-query-metrics.md)を記録します 
*    [SDK ログ記録]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)の設定に従います

この記事の[一般的な問題と対処法](#common-issues-workarounds)のセクションを確認します。

アクティブに監視されている [GitHub の問題セクション](https://github.com/Azure/azure-cosmos-dotnet-v2/issues)を調べます。 回避策が既に提出済みの同様の問題がないか確認します。 ソリューションが見つからなかった場合は、GitHub の問題を提出してください。 緊急の問題のサポートのティックを開くことができます。


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>一般的な問題と対処法

### <a name="general-suggestions"></a>一般的な推奨事項
* 可能なときはいつでも、Azure Cosmos DB アカウントと同じ Azure リージョンでアプリを実行します。 
* クライアント マシン上のリソースが不足しているため、接続/可用性の問題に遭遇することがあります。 Azure Cosmos DB クライアントを実行しているノードの CPU 使用率を監視し、高負荷で実行している場合にはスケール アップ/アウトを実行することをお勧めします。

### <a name="check-the-portal-metrics"></a>ポータルのメトリックを確認する
[ポータルのメトリック](./monitor-cosmos-db.md)を確認すると、クライアント側の問題であるか、サービスに問題があるかの判断に役立ちます。 たとえば、要求が調整されていることを意味する、高いレートのレート制限された要求 (HTTP 状態コード 429) がメトリックに含まれていれば、"[要求率が大きすぎる](troubleshoot-request-rate-too-large.md)" に関するセクションを確認してください。 

## <a name="retry-logic"></a>再試行ロジック <a id="retry-logics"></a>
SDK での再試行が可能な場合、すべての IO エラーで Cosmos DB SDK は、失敗した操作の再試行を試みます。 エラーが発生した場合に再試行を行うことをお勧めしますが、特に書き込みエラーの処理と再試行は必要です。 再試行ロジックが継続的に改善されているため、最新の SDK を使用することをお勧めします。

1. 読み取りおよびクエリ IO エラーは、エンド ユーザーにはわからないまま SDK によって再試行されます。
2. 書き込み (Create、Upsert、Replace、Delete) はべき等では "なく"、したがって SDK は必ずしも無条件に、失敗した書き込み操作を再試行できるわけではありません。 エラーを処理して再試行するには、ユーザーのアプリケーション ロジックが必要です。
3. [SDK の可用性のトラブルシューティング](troubleshoot-sdk-availability.md)に関するページでは、複数リージョンの Cosmos DB アカウントの再試行について説明しています。

## <a name="common-error-status-codes"></a>一般的なエラー状態コード <a id="error-codes"></a>

| 状態コード | 説明 | 
|----------|-------------|
| 400 | 無効な要求 (エラー メッセージによって異なります)| 
| 401 | [許可されていません](troubleshoot-unauthorized.md) | 
| 403 | [Forbidden](troubleshoot-forbidden.md) |
| 404 | [リソースが見つかりません](troubleshoot-not-found.md) |
| 408 | [要求がタイムアウトしました](troubleshoot-dot-net-sdk-request-timeout.md) |
| 409 | 失敗の競合は、書き込み操作でリソースに指定された ID が既存のリソースにより取得された場合に発生します。 この問題を解決するには、リソースに別の ID を使用します。同じパーティション キー値が含まれるすべてのドキュメント内で、ID が一意であるためです。 |
| 410 | 例外が発生しました (SLA に違反しない一時的な失敗) |
| 412 | サーバーで利用できるバージョンとは異なる eTag が操作によって指定される場合、前提条件エラーが発生します。 これは、オプティミスティック同時実行制御エラーです。 リソースの最新バージョンを読み取り、要求の eTag を更新した後、要求を再試行してください。
| 413 | [要求のエンティティが大きすぎます](concepts-limits.md#per-item-limits) |
| 429 | [要求が多すぎます](troubleshoot-request-rate-too-large.md) |
| 449 | 書き込み操作でのみ発生する一時的なエラーであり、安全に再試行できます |
| 500 | 予期しないサービス エラーのため、操作に失敗しました。 サポートにお問い合せください。 [Azure サポートの問題](https://aka.ms/azure-support)の提出に関するページを参照してください。 |
| 503 | [サービスを利用できません](troubleshoot-service-unavailable.md) | 

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT (PAT) ポート不足

[パブリック IP アドレスを使用しない Azure Virtual Machines](../load-balancer/load-balancer-outbound-connections.md) にアプリをデプロイした場合、既定では、[Azure SNAT ポート](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)によって VM 外の任意のエンドポイントへの接続が確立されます。 VM から Azure Cosmos DB エンドポイントへの許可される接続の数は、[Azure SNAT 構成](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)によって制限されます。 このような状況では、接続の調整、接続の終了、または上記の[要求タイムアウト](troubleshoot-dot-net-sdk-request-timeout.md)が発生する可能性があります。

 Azure SNAT ポートが使用されるのは、プライベート IP アドレスを持つ VM がパブリック IP アドレスに接続しようとしている場合に限られます。 Azure SNAT の制限を回避するには、次の 2 つの回避策があります (アプリケーション全体で 1 つのクライアント インスタンスを既に使用している場合)。

* Azure Virtual Machines 仮想ネットワークのサブネットに Azure Cosmos DB サービス エンドポイントを追加します。 詳細については、[Azure 仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)に関するページを参照してください。 

    サービス エンドポイントが有効になると、要求はパブリック IP から Azure Cosmos DB に送信されなくなります。 代わりに、仮想ネットワークとサブネット ID が送信されます。 この変更により、パブリック IP のみが許可された場合はファイアウォール ドロップが発生することがあります。 ファイアウォールを使用している場合、サービス エンドポイントを有効にするときに、[Virtual Network ACL](/previous-versions/azure/virtual-network/virtual-networks-acl) を使用してファイアウォールにサブネットを追加します。
* [Azure VM にパブリック IP](../load-balancer/troubleshoot-outbound-connection.md#assignilpip) を割り当てます。

### <a name="high-network-latency"></a><a name="high-network-latency"></a>長いネットワーク待ち時間
長いネットワーク待ち時間を識別するには、V2 SDK では[診断文字列](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring)、V3 SDK では[診断](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics)を使用します。

[タイムアウト](troubleshoot-dot-net-sdk-request-timeout.md)がない場合、待ち時間が長いことが明らかな単一要求が診断に表示されます。

# <a name="v3-sdk"></a>[V3 SDK](#tab/diagnostics-v3)

診断は `ResponseMessage`、`ItemResponse`、`FeedResponse` または`CosmosException` から `Diagnostics` プロパティによって取得できます。

```csharp
ItemResponse<MyItem> response = await container.CreateItemAsync<MyItem>(item);
Console.WriteLine(response.Diagnostics.ToString());
```

診断のネットワーク インタラクションの例:

```json
{
    "name": "Microsoft.Azure.Documents.ServerStoreModel Transport Request",
    "id": "0e026cca-15d3-4cf6-bb07-48be02e1e82e",
    "component": "Transport",
    "start time": "12: 58: 20: 032",
    "duration in milliseconds": 1638.5957
}
```

`duration in milliseconds` は待ち時間を示すことになります。

# <a name="v2-sdk"></a>[V2 SDK](#tab/diagnostics-v2)

`RequestDiagnosticsString` プロパティを利用し、[直接モード](sql-sdk-connection-modes.md)でクライアントが構成されるとき、診断が利用できます。

```csharp
ResourceResponse<Document> response = await client.ReadDocumentAsync(documentLink, new RequestOptions() { PartitionKey = new PartitionKey(partitionKey) });
Console.WriteLine(response.RequestDiagnosticsString);
```

また、待ち時間は `ResponseTime` と `RequestStartTime` の差に基づきます。

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```
--- 

この待ち時間には、以下のような複数の原因が考えられます。

* アプリケーションが Azure Cosmos DB アカウントと同じリージョンで実行されていない。
* [PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) または [ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) 構成が不正確であり、アプリが現在実行されている別のリージョンに接続しようとしている。
* 大量のトラフィックが原因で、ネットワーク インターフェイスにボトルネックが発生している可能性がある。 アプリケーションが Azure Virtual Machines で実行されている場合は、次のような回避策が考えられます。
    * [高速ネットワークを有効にした仮想マシン](../virtual-network/create-vm-accelerated-networking-powershell.md)を使用することを検討します。
    * [既存の仮想マシンで高速ネットワーク](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)を有効にします。
    * [よりハイエンドな仮想マシン](../virtual-machines/sizes.md)を使用することを検討します。

### <a name="common-query-issues"></a>一般的なクエリの問題

[クエリ メトリック](sql-api-query-metrics.md)は、クエリがほとんどの時間を費やしている箇所を特定する場合に役立ちます。 クエリ メトリックから、バックエンドとクライアントでどれだけの時間が費やされているかを確認できます。 [クエリ パフォーマンスのトラブルシューティング](troubleshoot-query-performance.md)の詳細をご覧ください。

* バックエンド クエリが短時間で返され、クライアント上で多くの時間が費やされた場合は、マシン上の負荷を調べてください。 十分なリソースがなく、応答の処理にリソースが利用できるようになるまで SDK が待機している可能性があります。
* バックエンド クエリが低速である場合は、[クエリを最適化](troubleshoot-query-performance.md)して、現在の[インデックス作成ポリシー](index-overview.md)を調べてみてください

    > [!NOTE]
    > パフォーマンス向上のため、Windows 64 ビットのホスト処理をお勧めします。 SQL SDK には、ローカル環境でクエリを解析して最適化するためのネイティブ ServiceInterop.dll が含まれています。 ServiceInterop.dll は、Windows x64 プラットフォームでのみサポートされています。 Linux および ServiceInterop.dll を使用できない他のサポート対象外プラットフォームでは、最適化されたクエリを取得するために、ゲートウェイに対して追加のネットワーク呼び出しが行われます。

エラー `Unable to load DLL 'Microsoft.Azure.Cosmos.ServiceInterop.dll' or one of its dependencies:` が発生したとき、Windows を使用している場合、最新の Windows バージョンにアップグレードしてください。

## <a name="next-steps"></a>次のステップ

* [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET V2](performance-tips.md) のパフォーマンス ガイドラインを確認する
* [リアクター ベースの Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) について学習する

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list