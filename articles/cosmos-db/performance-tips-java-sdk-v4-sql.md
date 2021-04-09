---
title: Azure Cosmos DB Java SDK v4 のパフォーマンスに関するヒント
description: Java SDK v4 で Azure Cosmos データベースのパフォーマンスを向上させるためのクライアント構成オプションについて説明します
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 10/13/2020
ms.author: anfeldma
ms.custom: devx-track-java, contperf-fy21q2
ms.openlocfilehash: 8aad9df4720c833a74659b5cd36b7f5aafdf9b60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97631841"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Azure Cosmos DB Java SDK v4 のパフォーマンスに関するヒント
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> 

> [!IMPORTANT]  
> この記事のパフォーマンスに関するヒントは、Azure Cosmos DB Java SDK v4 のみを対象としています。 詳細については、Azure Cosmos DB Java SDK v4 の[リリース ノート](sql-api-sdk-java-v4.md)、[Maven リポジトリ](https://mvnrepository.com/artifact/com.azure/azure-cosmos)、Azure Cosmos DB Java SDK v4 の[トラブルシューティング ガイド](troubleshoot-java-sdk-v4-sql.md)を参照してください。 v4 より前のバージョンを現在使用している場合、v4 へのアップグレードについては、[Azure Cosmos DB Java SDK v4 への移行](migrate-java-v4-sdk.md)ガイドを参照してください。

Azure Cosmos DB は、高速で柔軟性に優れた分散データベースです。待機時間とスループットが保証されており、シームレスにスケーリングできます。 Azure Cosmos DB でデータベースをスケーリングするために、アーキテクチャを大きく変更したり、複雑なコードを記述したりする必要はありません。 スケールアップとスケールダウンは、API 呼び出しか SDK メソッド呼び出しを 1 回行うだけで簡単に実行できます。 ただし、Azure Cosmos DB にはネットワーク呼び出しによってアクセスするため、Azure Cosmos DB Java SDK v4 を使用するときに最高のパフォーマンスを実現するために、クライアント側の最適化を行うことができます。

データベースのパフォーマンスを向上させる場合は、 以下のオプションを検討してください。

## <a name="networking"></a>ネットワーク

* **接続モード:** 直接モードを使用する
<a id="direct-connection"></a>
    
Java SDK の既定の接続モードは直接です。 下の画像のように、*directMode()* または *gatewayMode()* メソッドを利用し、クライアント ビルダーで接続モードを構成できます。 既定の設定でいずれかのモードを構成するには、引数を指定せずにいずれかのメソッドを呼び出します。 それ以外の場合は、構成設定クラス インスタンスを引数として渡します (*directMode()* の場合は *DirectConnectionConfig*、*gatewayMode()* の場合は *GatewayConnectionConfig*)。 さまざまな接続オプションについては、[接続モード](sql-sdk-connection-modes.md)に関する記事を参照してください。
    
### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[非同期](#tab/api-async)

Java SDK V4 (Maven com.azure::azure-cosmos) 非同期 API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientConnectionModeAsync)]

# <a name="sync"></a>[同期](#tab/api-sync)

Java SDK V4 (Maven com.azure::azure-cosmos) 同期 API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientConnectionModeSync)]

--- 

*directMode()* メソッドには、次の理由により、追加のオーバーライドがあります。 データベースやコンテナーの CRUD などのコントロール プレーン操作は、*常に* ゲートウェイ モードを利用します。ユーザーがデータ プレーン操作に直接モードを構成した場合、コントロール プレーン操作では既定のゲートウェイ モード設定が使用されます。 これはほとんどのユーザーに適しています。 ただし、データ プレーン操作の直接モードとコントロール プレーンのゲートウェイ モードパラメーターの調整機能を必要とするユーザーは、次の *directMode()* オーバーライドを使用できます。

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[非同期](#tab/api-async)

Java SDK V4 (Maven com.azure::azure-cosmos) 非同期 API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientDirectOverrideAsync)]

# <a name="sync"></a>[同期](#tab/api-sync)

Java SDK V4 (Maven com.azure::azure-cosmos) 同期 API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientDirectOverrideSync)]

--- 

<a name="collocate-clients"></a>
* **パフォーマンスを確保するために同じ Azure リージョン内にクライアントを併置する**
<a id="same-region"></a>

可能な場合は、Azure Cosmos DB を呼び出すすべてのアプリケーションを Azure Cosmos データベースと同じリージョンに配置します。 大ざっぱな比較ですが、Azure Cosmos DB の呼び出しは、同じリージョン内であれば 1 ～ 2 ミリ秒以内で完了するのに対し、米国西部と米国東部との間では待ち時間が 50 ミリ秒を超えます。 要求がクライアントから Azure データセンターの境界まで流れるときに使用されるルートに応じて、この待機時間が要求ごとに異なる可能性があります。 最短の待機時間は、プロビジョニングされた Azure Cosmos DB エンドポイントと同じ Azure リージョン内に呼び出し元アプリケーションを配置することによって実現されます。 使用可能なリージョンの一覧については、「 [Azure のリージョン](https://azure.microsoft.com/regions/#services)」を参照してください。

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Azure Cosmos DB 接続ポリシーの図" border="false":::

マルチリージョンの Azure Cosmos DB アカウントとやり取りするアプリでは、併置されたリージョンに要求が確実に送信されるように、[優先される場所](tutorial-global-distribution-sql-api.md#preferred-locations)を構成する必要があります。

* **待機時間を短縮するために Azure VM で高速ネットワークを有効する**

パフォーマンスを最大限に高めるために、手順に従って Windows ([クリックして手順を参照](../virtual-network/create-vm-accelerated-networking-powershell.md)) または Linux ([クリックして手順を参照](../virtual-network/create-vm-accelerated-networking-cli.md)) Azure VM で高速ネットワークを有効にすることをお勧めします。

高速ネットワークを使用しない場合、Azure VM と他の Azure リソース間を通過する IO は、VM とそのネットワーク カードの間にあるホストと仮想スイッチを介して不必要にルーティングされる可能性があります。 データパスにインラインでホストと仮想スイッチがあると、通信チャネルで待機時間とジッターが増加するだけでなく、VM から CPU サイクルが奪われます。 高速ネットワークを使用すると、VM は中継なしで NIC と直接やり取りします。ホストと仮想スイッチによって処理されていたネットワーク ポリシーの詳細は、NIC のハードウェアで処理されるようになり、ホストと仮想スイッチはバイパスされます。 通常、高速ネットワークを有効にすると、待機時間の短縮とスループットの向上だけでなく、より "*一貫した*" 待機時間と CPU 使用率の削減が期待できます。

制限事項: 高速ネットワークは、VM の OS でサポートされている必要があり、VM が停止され、割り当てが解除されている場合にのみ有効にすることができます。 Azure Resource Manager を使用して VM をデプロイすることはできません。

詳細については、[Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) および [Linux](../virtual-network/create-vm-accelerated-networking-cli.md) の手順を参照してください。

## <a name="sdk-usage"></a>SDK の使用
* **最新の SDK をインストールする**

Azure Cosmos DB SDK は、最適なパフォーマンスを提供するために頻繁に改善されています。 [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) のページを参照して、最新の SDK を確認し、改善点を確認してください。

* **アプリケーションの有効期間中はシングルトン Azure Cosmos DB クライアントを使用する**

各 Azure Cosmos DB クライアント インスタンスはスレッドセーフであり、効率的な接続管理とアドレスのキャッシュが実行されます。 Azure Cosmos DB クライアントによる効率的な接続管理とパフォーマンスの向上を実現するために、アプリケーションの有効期間中は、AppDomain ごとに Azure Cosmos DB クライアントの単一のインスタンスを使用することをお勧めします。

<a id="max-connection"></a>

* **アプリケーションに必要な最低の整合性レベルを使用する**

*CosmosClient* を作成するときに、明示的に設定されていない場合に使用される既定の整合性は "*Session (セッション)* " です。 アプリケーション ロジックで "*Session (セッション)* " 整合性が必要とされない場合は、"*Consistency (整合性)* " を "*Eventual (最終的)* " に設定します。 注: Azure Cosmos DB 変更フィード プロセッサを使用するアプリケーションでは、少なくとも "*Session (セッション)* " 整合性を使用することをお勧めします。

* **非同期 API を使用してプロビジョニングされたスループットを最大化する**

Azure Cosmos DB Java SDK v4 には、同期と非同期の 2 つの API がバンドルされています。 大まかに言うと、非同期 API は SDK 機能を実装し、同期 API は非同期 API のブロッキング呼び出しを行うシン ラッパーです。 これは、非同期のみであった以前の Azure Cosmos DB Async Java SDK v2 や、同期のみで、まったく別の実装を備えていた以前の Azure Cosmos DB Sync Java SDK v2 とは対照的です。 
    
API の選択は、クライアントの初期化中に決定されます。*CosmosAsyncClient* では非同期 API がサポートされ、*CosmosClient* では同期 API がサポートされます。 
    
非同期 API は非ブロッキング IO を実装しており、Azure Cosmos DB に要求を発行するときにスループットを最大化することが目標である場合に最適な選択肢です。 
    
各要求への応答でブロックする API が必要な場合や、同期操作がアプリケーションの主要パラダイムである場合は、同期 API の使用が適切な選択と言えます。 たとえば、スループットが重要でないのであれば、マイクロサービス アプリケーションで Azure Cosmos DB にデータを保持する場合に、同期 API を使用できます。  
    
同期 API では要求の応答時間の増加によってスループットが低下し、非同期 API ではハードウェアの全帯域幅の機能を飽和させる可能性があることに注意してください。 
    
同期 API を使用する場合、地理的な併置によって、より高いより一貫したスループットが得られますが (「[パフォーマンスを確保するために同じ Azure リージョン内にクライアントを併置する](#collocate-clients)」を参照)、非同期 API で達成可能なスループットを超えることはやはり期待できません。

一部のユーザーは、Azure Cosmos DB Java SDK v4 の非同期 API を実装するために使用される Reactive Streams フレームワークである、[Project Reactor](https://projectreactor.io/) に詳しくない場合もあります。 これが懸念される場合は、入門用の「[Reactor pattern guide (Reactor パターン ガイド)](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)」を読んでから、こちらの「[Introduction to Reactive Programming (Reactive プログラミングの概要)](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro)」を読んで理解を深めてください。 非同期インターフェイスで Azure Cosmos DB を既に使用しており、使用していた SDK が Azure Cosmos DB Async Java SDK v2 の場合、[ReactiveX](http://reactivex.io/)/[RxJava](https://github.com/ReactiveX/RxJava) を使い慣れているかもしれませんが、Project Reactor での変更点がわからない可能性があります。 その場合は、「[Reactor vs. RxJava guide (Reactor と RxJava の比較ガイド)](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md)」を参照して理解を深めてください。

次のコード スニペットは、それぞれ非同期 API または同期 API 操作で Azure Cosmos DB クライアントを初期化する方法を示しています。

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[非同期](#tab/api-async)

Java SDK V4 (Maven com.azure::azure-cosmos) 非同期 API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientAsync)]

# <a name="sync"></a>[同期](#tab/api-sync)

Java SDK V4 (Maven com.azure::azure-cosmos) 同期 API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientSync)]

 --- 

* **ConnectionPolicy のチューニング**

既定では、Azure Cosmos DB Java SDK v4 を使用するときに、直接モードの Cosmos DB 要求は TCP 経由で行われます。 内部的には、直接モードで特別なアーキテクチャを使用してネットワーク リソースを動的に管理し、最良のパフォーマンスを実現します。

Azure Cosmos DB Java SDK v4 では、ほとんどのワークロードでデータベースのパフォーマンスを向上させるための最適な選択肢は直接モードです。 

* ***直接モードの概要***

:::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="直接モード アーキテクチャの図" border="false":::

直接モードで使用されるクライアント側のアーキテクチャにより、予測可能なネットワーク使用率と Azure Cosmos DB レプリカへの多重アクセスが可能になります。 上の図は、直接モードで Cosmos DB バックエンドのレプリカにクライアント要求をルーティングする方法を示しています。 直接モード アーキテクチャでは、クライアント側で DB レプリカあたり最大 10 個の **チャネル** が割り当てられます。 チャネルは、要求バッファーが先行する TCP 接続であり、要求の深さは 30 個です。 レプリカに属するチャネルは、レプリカの **サービス エンドポイント** によって、必要に応じて動的に割り当てられます。 ユーザーが直接モードで要求を発行すると、**TransportClient** により、パーティション キーに基づいて要求が適切なサービス エンドポイントにルーティングされます。 **要求キュー** では、サービス エンドポイントの前に要求がバッファリングされます。

* ***直接モード用の構成オプション***

既定以外の直接モードの動作が必要な場合は、*DirectConnectionConfig* インスタンスを作成し、そのプロパティをカスタマイズしてから、Azure Cosmos DB クライアント ビルダーでカスタマイズしたプロパティ インスタンスを *directMode()* メソッドに渡します。

これらの構成設定は、上記で説明した基になる直接モードのアーキテクチャの動作を制御します。

最初の手順として、次の推奨される構成設定を使用します。 これらの *DirectConnectionConfig* オプションは高度な構成設定であり、SDK のパフォーマンスに予期しない影響を与える可能性があります。トレードオフを十分に理解したうえで、どうしても必要な場合を除き、これらを変更しないようにすることをお勧めします。 この特定のトピックで問題が発生した場合は、[Azure Cosmos DB チーム](mailto:CosmosDBPerformanceSupport@service.microsoft.com)にお問い合わせください。

| 構成オプション       | Default   |
| :------------------:       | :-----:   |
| idleConnectionTimeout      | "PT0"     |
| maxConnectionsPerEndpoint  | "130"     |
| connectTimeout             | "PT5S"    |
| idleEndpointTimeout        | "PT1H"    |
| maxRequestsPerConnection   | "30"      |

* **パーティション分割コレクションに対する並列クエリを調整する**

Azure Cosmos DB Java SDK v4 では、パーティション分割コレクションに対してクエリを並列で実行できるようにするために、並列クエリがサポートされています。 詳細については、Azure Cosmos DB Java SDK v4 の操作に関連した[コード サンプル](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)を参照してください。 並列クエリは、シリアル クエリよりもクエリの待機時間とスループットを向上させるように設計されています。

* ***setMaxDegreeOfParallelism を調整する\:***
    
並列クエリは、複数のパーティションに並列にクエリを実行することによって機能します。 ただし、個々のパーティション分割されたコレクションからのデータは、クエリごとに順番に取得されます。 そのため、setMaxDegreeOfParallelism を使ってパーティションの数を設定すると、その他のすべてのシステムの条件が変わらなければ、クエリのパフォーマンスを最大にできる可能性が最大になります。 パーティションの数が不明な場合は、setMaxDegreeOfParallelism を使って大きな数を設定すると、システムが並列処理の最大限度として最小値 (パーティションの数、ユーザー指定の入力) を選びます。

並列クエリが最も有効に機能するのは、クエリに対するデータがすべてのパーティションに均等に分散している場合であることに注意する必要があります。 パーティション分割されたコレクションが、クエリによって返されるすべてまたは大部分のデータがわずかな数のパーティション (最悪の場合は 1 つのパーティション) に集中するように分割されている場合、クエリのパフォーマンスに関してこれらのパーティションがボトルネックになるでしょう。

* ***setMaxBufferedItemCount を調整する\:***
    
並列クエリは、結果の現在のバッチがクライアントによって処理されている間に結果をプリフェッチするように設計されています。 プリフェッチは、クエリの全体的な遅延の削減に役立ちます。 setMaxBufferedItemCount は、プリフェッチされる結果の数を制限します。 setMaxBufferedItemCount を、返される結果の予期される数 (またはそれ以上の数) に設定すると、クエリに対するプリフェッチの効果が最大になります。

プリフェッチは MaxDegreeOfParallelism とは無関係に同じように動作し、すべてのパーティションからのデータに対して単一のバッファーが存在します。

* **クライアント ワークロードをスケールアウトする**

高いスループット レベルでテストを行っている場合、コンピューターが CPU 使用率またはネットワーク使用率の上限に達したことでクライアント アプリケーションがボトルネックになることがあります。 この状態に達しても、クライアント アプリケーションを複数のサーバーにスケールアウトすることで引き続き同じ Azure Cosmos DB アカウントで対応できます。

低待機時間を維持するには、経験則として、特定のサーバーで CPU 使用率が 50% を超えないようにします。

<a id="tune-page-size"></a>

* **パフォーマンスを向上させるために、クエリ/読み取りフィードのページ サイズを調整する**

読み取りフィード機能 (*readItems* など) を使用してドキュメントの一括読み取りを実行するときや、SQL クエリ (*queryItems*) を発行するときに、結果セットが大きすぎる場合は、セグメント化された形式で結果が返されます。 既定では、100 項目または 1 MB (先に達した方) のチャンク単位で結果が返されます。

アプリケーションが Azure Cosmos DB にクエリを発行し、アプリケーションがタスクを完了するためにクエリ結果の完全なセットが必要であるとします。 該当するすべての結果を取得するために必要なネットワーク ラウンド トリップの回数を減らすために、[x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 要求ヘッダー フィールドを調整して、ページ サイズを増やすことができます。 

* 単一パーティション クエリでは、[x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) フィールドの値を -1 (ページ サイズの制限なし) に調整すると、クエリ応答ページの数が最小限に抑えられるので、待機時間が最大限に短縮されます。完全な結果セットが単一ページで返されるか、またはクエリにタイムアウト間隔よりも長い時間がかかる場合は、完全な結果セットが最小限のページ数で返されます。 これにより、要求のラウンド トリップ時間が大幅に節約されます。
    
* クロスパーティション クエリでは、[x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) フィールドを -1 に設定し、ページ サイズの制限を排除すると、ページ サイズを管理できなくなり、SDK が過負荷になるおそれがあります。 クロスパーティションの場合、待機時間を短縮するために、ページ サイズの上限をある程度まで引き上げることをお勧めします。ただし、有限値 (おそらく 1000) を設定してください。 
    
一部のアプリケーションでは、クエリ結果の完全なセットを必要としない場合があります。 ごく少数の結果を表示する必要がある場合は (ユーザー インターフェイスやアプリケーション API が一度に 10 件しか結果を返さない場合など)、読み取りとクエリに使用されるスループットを減らすために、ページ サイズを 10 に減らすこともできます。

REST ヘッダー フィールドを直接変更するのではなく、*byPage* メソッドの優先ページ サイズ引数を設定することもできます。 [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) または *byPage* の優先ページ サイズ引数は、絶対条件ではなく、ページ サイズの上限を設定するだけであることに注意してください。そのため、さまざまな理由から、Azure Cosmos DB から返されるページが優先ページ サイズよりも小さくなる場合があります。 

* **適切なスケジューラを使用する (イベント ループの IO Netty スレッドを盗まない)**

Azure Cosmos DB Java SDK の非同期機能は、[netty](https://netty.io/) 非ブロッキング IO に基づいています。 SDK は、固定数の IO netty イベント ループ スレッド (コンピューターの CPU コアと同じ数) を使って IO 操作を実行します。 API によって返される Flux は、共有 IO イベント ループ netty スレッドの 1 つに結果を出力します。 したがって、共有 IO イベント ループ netty スレッドをブロックしないことが重要です。 IO イベント ループ netty スレッドで CPU を大量に使う処理を行ったり、操作をブロックしたりすると、デッドロックが発生したり、SDK のスループットが大幅に低下したりする可能性があります。

たとえば、次のコードは、イベント ループ IO netty スレッドで CPU を大量に使う処理を実行します。
### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) 非同期 API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNeedsSchedulerAsync)]

結果を受け取った後、結果に対して CPU 負荷の高い操作を実行する場合は、イベント ループ IO netty スレッドでは行わないようにする必要があります。 次に示すように、代わりに独自のスケジューラを用意して、処理を実行するための独自のスレッドを提供できます (`import reactor.core.scheduler.Schedulers` が必要です)。

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) 非同期 API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddSchedulerAsync)]

処理の種類に基づいて、処理に適した既存の Reactor Scheduler を使用する必要があります。 「[``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html)」をご覧ください。

Azure Cosmos DB Java SDK v4 の詳細については、[GitHub の Azure SDK for Java 単一リポジトリの Cosmos DB ディレクトリ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos)を参照してください。

* **アプリケーションのログ設定を最適化する**

さまざまな理由から、高い要求スループットを生成しているスレッドにログを追加することが必要な場合があります。 このスレッドによって生成される要求でコンテナーのプロビジョニングされたスループットを完全に飽和させることが目的である場合は、ログの最適化によってパフォーマンスを大幅に向上させることができます。

* ***非同期ロガーを構成する***

同期ロガーの待機時間は、要求を生成するスレッドの全体的な待機時間の計算に必ず含まれます。 高パフォーマンスのアプリケーション スレッドからログのオーバーヘッドを分離するために、[log4j2](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0) などの非同期ロガーが推奨されます。

* ***netty のログを無効にする***

netty ライブラリのログは量が多いので、CPU コストが増えないようにオフにする必要があります (構成のサインインを抑制するだけでは不十分な場合があります)。 デバッグ モードではない場合は、netty のログを完全に無効にします。 したがって、log4j を使って netty からの ``org.apache.log4j.Category.callAppenders()`` によって発生する追加の CPU コストを削除するには、コードベースに次の行を追加します。

```java
org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
```

 * **OS の開かれるファイルのリソース制限**
 
Red Hat などの一部の Linux システムには、開かれるファイルの数、したがって合計接続数に上限があります。 現在の制限を確認するには、次のコマンドを実行します。

```bash
ulimit -a
```

構成されている接続プール サイズおよび OS によって開かれる他のファイルのために十分なスペースがあるよう、開かれるファイル (nofile) の値は十分な大きさである必要があります。 大きい接続プール サイズに対応できるように変更できます。

limits.conf ファイルを開きます。

```bash
vim /etc/security/limits.conf
```
    
次の行を追加または変更します。

```
* - nofile 100000
```

* **ポイント書き込みでパーティション キーを指定する**

ポイント書き込みのパフォーマンスを向上させるには、次に示すように、ポイント書き込み API 呼び出しで項目のパーティション キーを指定します。

# <a name="async"></a>[非同期](#tab/api-async)

Java SDK V4 (Maven com.azure::azure-cosmos) 非同期 API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNoPKAsync)]

# <a name="sync"></a>[同期](#tab/api-sync)

Java SDK V4 (Maven com.azure::azure-cosmos) 同期 API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceNoPKSync)]

--- 

項目インスタンスだけを指定した場合は次のようになります。

# <a name="async"></a>[非同期](#tab/api-async)

Java SDK V4 (Maven com.azure::azure-cosmos) 非同期 API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddPKAsync)]

# <a name="sync"></a>[同期](#tab/api-sync)

Java SDK V4 (Maven com.azure::azure-cosmos) 同期 API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceAddPKSync)]

--- 

後者はサポートされていますが、アプリケーションに待機時間が追加されます。SDK が項目を解析し、パーティション キーを抽出する必要があります。

## <a name="indexing-policy"></a>インデックス作成ポリシー
 
* **インデックス作成から未使用のパスを除外して書き込みを高速化する**

Azure Cosmos DB のインデックス作成ポリシーでは、パスのインデックス作成 (setIncludedPaths および setExcludedPaths) を使って、インデックス作成に含める/除外するドキュメント パスを指定できます。 インデックス作成コストはインデックス付きの一意のパスの数に直接関係するため、パスのインデックス作成を使用すると、クエリ パターンが事前にわかっているシナリオで書き込みパフォーマンスが向上し、インデックス ストレージを削減できます。 たとえば、次のコードは、ワイルドカード "*" を使用して、ドキュメントのセクション全体 (サブツリーとも呼ばれる) をインデックス作成から追加および除外する方法を示しています。

### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

詳細については、[Azure Cosmos DB インデックス作成ポリシー](index-policy.md)に関するページをご覧ください。

## <a name="throughput"></a>スループット
<a id="measure-rus"></a>

* **測定と調整によって 1 秒あたりの要求ユニットの使用量を削減する**

Azure Cosmos DB には、UDF、ストアド プロシージャ、トリガーを使ったリレーショナル クエリや階層クエリなど、さまざまなデータベース操作が用意されています。これらの操作はすべて、データベース コレクション内のドキュメントに対して実行できます。 これらの操作のそれぞれに関連付けられたコストは、操作を完了するために必要な CPU、IO、およびメモリに応じて異なります。 ハードウェア リソースの管理について考える代わりに、各種のデータベース操作を実行しアプリケーション要求を処理するのに必要なリソースに関する単一の測定単位として要求単位 (RU) を考えることができます。

コンテナーごとに設定された[要求ユニット](request-units.md)の数に基づいて、スループットをプロビジョニングします。 要求単位の消費は、1 秒あたりのレートとして評価されます。 コンテナーのプロビジョニング済み要求ユニット レートを超過したアプリケーションは、レートがそのコンテナーにプロビジョニングされているレベルを下回るまで制限されます。 アプリケーションでより高いスループットが必要になった場合は、追加の要求ユニットをプロビジョニングしてスループットを増やすことができます。

クエリの複雑さは、操作で消費される要求ユニット数に影響します。 述語の数、述語の特性、UDF 数、ソース データ セットのサイズのすべてがクエリ操作のコストに影響します。

操作 (作成、更新、または削除) のオーバーヘッドを測定するには、[x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) ヘッダーを調べて、これらの操作で使われる要求ユニット数を測定します。 ResourceResponse\<T> または FeedResponse\<T> で同等の RequestCharge プロパティを確認することもできます。

# <a name="async"></a>[非同期](#tab/api-async)

Java SDK V4 (Maven com.azure::azure-cosmos) 非同期 API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceRequestChargeAsync)]

# <a name="sync"></a>[同期](#tab/api-sync)

Java SDK V4 (Maven com.azure::azure-cosmos) 同期 API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceRequestChargeSync)]

--- 

このヘッダーで返される要求の使用量は、プロビジョニングしたスループットの一部です。 たとえば、2000 RU/秒がプロビジョニングされていて、上記のクエリが 1 KB のドキュメントを 1000 個返した場合、この操作のコストは 1000 になります。 そのため、後続の要求をレート制限する前に、サーバーは 1 秒以内にこのような要求を 2 つだけ受け付けます。 詳細については、[要求ユニット](request-units.md)に関する記事および[要求ユニット計算ツール](https://www.documentdb.com/capacityplanner)のページを参照してください。

<a id="429"></a>
* **レート制限と大きすぎる要求レートに対処する**

クライアントがアカウントの予約済みスループットを超えようとしても、サーバーでパフォーマンスの低下が発生することはなく、予約済みのレベルを超えてスループット容量が使用されることもありません。 サーバーはいち早く RequestRateTooLarge (HTTP 状態コード 429) で要求を終了させ、要求を再試行するまでにユーザーが待機しなければならない時間 (ミリ秒) を示す [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) ヘッダーを返します。

```xml
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

SDK はすべてこの応答を暗黙的にキャッチし、サーバーが指定した retry-after ヘッダーを優先して要求を再試行します。 アカウントに複数のクライアントが同時アクセスしている状況でなければ、次回の再試行は成功します。

複数のクライアントが要求レートを常に上回った状態で累積的に動作している場合、現在クライアントによって内部的に 9 に設定されている既定の再試行回数では不十分な可能性があります。この場合、クライアントは状態コード 429 を含む *CosmosClientException* をアプリケーションにスローします。 既定の再試行回数は、ConnectionPolicy インスタンスで setRetryOptions を使って変更できます。 既定では、要求レートを超えて要求が続行されている場合に、30 秒の累積待機時間を過ぎると、状態コード 429 を含む *CosmosClientException* が返されます。 これは、現在の再試行回数が最大再試行回数 (既定値の 9 またはユーザー定義の値) より少ない場合でも発生します。

自動再試行動作により、ほとんどのアプリケーションの回復性とユーザービリティが向上しますが、パフォーマンス ベンチマークの実行時 (特に待機時間の測定時) に問題が生じることがあります。 実験でサーバー スロットルが発生し、クライアント SDK によって警告なしに再試行が行われると、クライアントが監視する待機時間が急増します。 パフォーマンスの実験中に待機時間が急増するのを回避するには、各操作で返される使用量を測定し、予約済みの要求レートを下回った状態で要求が行われていることを確認します。 詳細については、 [要求ユニット](request-units.md)に関する記事を参照してください。

* **スループットを向上させるためにサイズの小さいドキュメントに合わせて設計する**

特定の操作の要求の使用量 (要求処理コスト) は、ドキュメントのサイズに直接関係します。 サイズの大きいドキュメントの操作は、サイズの小さいドキュメントの操作よりもコストがかかります。 項目のサイズを最大 1 KB 程度にするようにアプリケーションとワークフローを設計するのが理想的です。 待機時間の影響を受けやすいアプリケーションでは、サイズの大きい項目は避ける必要があります。数 MB のドキュメントはアプリケーションの速度を低下させます。

## <a name="next-steps"></a>次のステップ

スケーリングと高パフォーマンスのためのアプリケーションの設計について詳しくは、「[Azure Cosmos DB でのパーティション分割とスケーリング](partitioning-overview.md)」をご覧ください。
