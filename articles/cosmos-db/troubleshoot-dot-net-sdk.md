---
title: Azure Cosmos DB .NET SDK の使用時の問題を診断しトラブルシューティングする
description: クライアント側のログや他のサード パーティ製ツールなどの機能を使って、.NET SDK 使用時の Azure Cosmos DB の問題を特定、診断、およびトラブルシューティングします。
author: j82w
ms.service: cosmos-db
ms.date: 05/28/2019
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b9511562b81f7ac0c1582897d703f4c5ccb89716
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806399"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Azure Cosmos DB .NET SDK の使用時の問題を診断しトラブルシューティングする
この記事では、Azure Cosmos DB SQL API アカウントで [.NET SDK](sql-api-sdk-dotnet.md) を使用するときの一般的な問題、回避策、診断手順、およびツールについて説明します。
.NET SDK には、Azure Cosmos DB SQL API にアクセスするためのクライアント側の論理表現が用意されています。 この記事では、問題が発生した場合に役立つツールとアプローチについて説明します。

## <a name="checklist-for-troubleshooting-issues"></a>問題をトラブルシューティングするためのチェックリスト:
運用環境にアプリケーションを移行する前に、次のチェックリストを検討してください。 チェックリストを使用すればと、発生する可能性のある一般的な問題のいくつかを防止できます。 問題が発生したときにもすばやく診断できます。

*   最新の [SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md) を使用します。 プレビュー SDK は運用環境で使用しないでください。 これにより、既に修正されている既知の問題の発生が防止されます。
*   [パフォーマンスに関するヒント](performance-tips.md)を確認し、推奨される方法に従います。 これは、スケーリング、待機時間、その他のパフォーマンス上の問題の防止に役立ちます。
*   問題のトラブルシューティングに役立つ SDK のログ機能を有効にします。 ログ機能を有効にするとパフォーマンスに影響することがあるため、問題のトラブルシューティング時にのみ有効にすることをお勧めします。 次のログを有効にできます。
    *   Azure portal を使用して[メトリックを記録します](monitor-accounts.md)。 ポータルのメトリックは Azure Cosmos DB テレメトリを表示し、これは、問題が Azure Cosmos DB に対応したものか、クライアント側から発生したものかを判断する場合に役立ちます。
    *   ポイント操作応答からの[診断文字列](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet)を記録します。
    *   すべてのクエリ応答からの [SQL クエリ メトリック](sql-api-query-metrics.md)を記録します 
    *   [SDK ログ記録]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)の設定に従います

この記事の[一般的な問題と対処法](#common-issues-workarounds)のセクションを確認します。

アクティブに監視されている [GitHub の問題セクション](https://github.com/Azure/azure-cosmos-dotnet-v2/issues)を調べます。 回避策が既に提出済みの同様の問題がないか確認します。 ソリューションが見つからなかった場合は、GitHub の問題を提出してください。 緊急の問題のサポートのティックを開くことができます。


## <a name="common-issues-workarounds"></a>一般的な問題と対処法

### <a name="general-suggestions"></a>一般的な推奨事項
* 可能なときはいつでも、Azure Cosmos DB アカウントと同じ Azure リージョンでアプリを実行します。 
* クライアント マシン上のリソースが不足しているため、接続/可用性の問題に遭遇することがあります。 Azure Cosmos DB クライアントを実行しているノードの CPU 使用率を監視し、高負荷で実行している場合にはスケール アップ/アウトを実行することをお勧めします。

### <a name="check-the-portal-metrics"></a>ポータルのメトリックを確認する
[ポータルのメトリック](monitor-accounts.md)を確認すると、クライアント側の問題であるか、サービスに問題があるかの判断に役立ちます。 たとえば、要求が調整されていることを意味する、高いレートのレート制限された要求 (HTTP 状態コード 429) がメトリックに含まれていれば、[要求率が大きすぎる] セクションを確認してください。 

### <a name="request-timeouts"></a>要求タイムアウト
RequestTimeout は、通常ダイレクト/TCP を使用するときに発生しますが、ゲートウェイ モードで起きることがあります。 以下は、一般的な既知の原因と、問題の解決方法に関する推奨事項です。

* CPU 使用率が高くなっているため、待ち時間または要求タイムアウト、あるいはその両方が発生しています。 顧客がホスト マシンをスケールアップしてリソースを増やすか、複数のマシンに負荷を分散できます。
* ソケット・ポートの可用性が低下している可能性があります。 2\.0 バージョン以前にリリースされた .NET SDK を使用している場合、Azure で実行しているクライアントで、[Azure SNAT (PAT) ポート不足]が発生することがあります。 これが、常に最新の SDK バージョンを実行することをお勧めする理由のひとつです。
* 複数の DocumentClient インスタンスを作成すると、接続の競合およびタイムアウトの問題を招くおそれがあります。 [パフォーマンスに関するヒント](performance-tips.md)に従って、プロセス全体で単一の DocumentClient インスタンスを使用します。
* コレクションのプロビジョニングが不十分であり、バックエンドが要求を調整し、クライアントがこれを呼び出し側に表面化させることなく内部的に再試行するので、ユーザーは待ち時間または要求タイムアウトの増大を経験することがあります。 [ポータルのメトリック](monitor-accounts.md)を確認します。
* Azure Cosmos DB は、プロビジョニングされたスループット全体を物理パーティション間で均等に分散します。 ポータルのメトリックを調べて、ワークロードでホットな[パーティション キー](partition-data.md)が発生しているかどうかを確認します。 これにより、消費済みスループットの集計 (RU/秒) がプロビジョニングされた RU より低く表示されますが、単一パーティションの消費済みスループット (RU/秒) はプロビジョニングされたスループットを超過します。 
* さらに、2.0 SDK は、ダイレクト/TCP 接続にチャネル セマンティクスを追加します。 同時に複数の要求に 1 つの TCP 接続が使用されます。 これは特定の場合に 2 つの問題を招くことがあります。
    * 同時実行の程度が高くなると、チャネル上で競合が発生することがあります。
    * 要求数や応答数が増大すると、同時実行の程度が比較的低い場合でも、チャネル上でヘッドオブライン ブロッキングが発生し、競合が悪化する可能性があります。
    * これらの 2 つのカテゴリのどちらかに当てはまる場合 (または高い CPU 使用率が疑われる場合)、以下の軽減策が考えられます。
        * アプリケーションのスケール アップ/アウトを試してください。
        * さらに、[トレース リスナー](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md)を通じて SDK ログをキャプチャし、詳細を取得できます。

### <a name="connection-throttling"></a>接続の帯域幅調整
接続の帯域幅調整は、ホスト マシンの接続制限が原因で発生します。 2\.0 以前では、Azure で実行しているクライアントに、[Azure SNAT (PAT) ポート不足]が発生する可能性があります。

### <a name="snat"></a>Azure SNAT (PAT) ポート不足

パブリック IP アドレスを使わずにアプリを Azure Virtual Machines にデプロイした場合、既定では [Azure SNAT ポート](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)によって VM 外の任意のエンドポイントへの接続が確立されます。 VM から Azure Cosmos DB エンドポイントへの許可される接続の数は、[Azure SNAT 構成](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)によって制限されます。

 Azure SNAT ポートが使用されるのは、VM がプライベート IP アドレスを持ち、VM からのプロセスが、パブリック IP アドレスに接続しようとしている場合に限られます。 Azure SNAT の制限を回避するには次の 2 つの回避策があります。

* Azure Virtual Machines 仮想ネットワークのサブネットに Azure Cosmos DB サービス エンドポイントを追加します。 詳細については、[Azure 仮想ネットワーク サービス エンドポイント](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)に関するページを参照してください。 

    サービス エンドポイントが有効になると、要求はパブリック IP から Azure Cosmos DB に送信されなくなります。 代わりに、仮想ネットワークとサブネット ID が送信されます。 この変更により、パブリック IP のみが許可された場合はファイアウォール ドロップが発生することがあります。 ファイアウォールを使用している場合、サービス エンドポイントを有効にするときに、[Virtual Network ACL](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl) を使用してファイアウォールにサブネットを追加します。
* Azure VM にパブリック IP を割り当てます。

### <a name="http-proxy"></a>HTTP プロキシ
HTTP プロキシを使用する場合は、SDK `ConnectionPolicy` で構成されている接続の数をサポートできることを確認します。
できない場合、接続の問題が発生します。

### 要求率が大きすぎる<a name="request-rate-too-large"></a>
「要求率が大きすぎる」またはエラー コード 429 は、消費済みスループット (RU/秒) がプロビジョニング スループットを超過したので、要求が調整されていることを示します。 SDK は、指定された[再試行ポリシー](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)に基づいて要求を自動的に再試行します。 このエラーが頻繁に発生する場合、コレクションでスループットを増やすことを検討してください。 [ポータルのメトリック](use-metrics.md)を調べて、429 エラーが発生しているかどうかを確認してください。 [パーティション キー](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)を調べて、ストレージおよび要求ボリュームが均等に分散されていることを確認してください。 

### <a name="slow-query-performance"></a>低速なクエリ パフォーマンス
[クエリ メトリック](sql-api-query-metrics.md)は、クエリがほとんどの時間を費やしている箇所を特定する場合に役立ちます。 クエリ メトリックから、バックエンドとクライアントでどれだけの時間が費やされているかを確認できます。
* バックエンド クエリが短時間で返され、クライアント上で多くの時間が費やされた場合は、マシン上の負荷を調べてください。 十分なリソースがなく、応答の処理にリソースが利用できるようになるまで SDK が待機している可能性があります。
* バックエンド クエリが低速である場合、[クエリを最適化](optimize-cost-queries.md)して、現在の[インデックス作成ポリシー](index-overview.md)を調べてみてください 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[要求率が大きすぎる]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) ポート不足]: #snat
[Production check list]: #production-check-list


