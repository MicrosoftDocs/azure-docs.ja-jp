---
title: Azure Cosmos DB の変更フィード プロセッサ
description: Azure Cosmos DB の変更フィード プロセッサを使用して、変更フィード、変更フィード プロセッサのコンポーネントを読み取る方法について説明します。
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 409b51682700a8b13b2840f171642bdcbee6f6d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340228"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Azure Cosmos DB の変更フィード プロセッサ
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

変更フィード プロセッサは [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) の一部です。 それにより、変更フィードを読み取り、イベント処理を複数のコンシューマーに効率的に分散させるプロセスが簡単になります。

変更フィード プロセッサ ライブラリの主な利点は、変更フィード内のすべてのイベントが確実に "少なくとも 1 回" は配信されるフォールト トレラントな動作です。

## <a name="components-of-the-change-feed-processor"></a>変更フィード プロセッサのコンポーネント

変更フィード プロセッサの実装には、4 つの主要なコンポーネントがあります。

1. **監視対象コンテナー:** 監視対象コンテナーには、変更フィードの生成元となるデータが含まれています。 監視対象コンテナーに対する挿入と更新が、コンテナーの変更フィードに反映されます。

1. **リース コンテナー:** リース コンテナーは、状態ストレージとして機能し、複数の worker 間での変更フィードの処理を調整します。 リース コンテナーは、監視対象コンテナーと同じアカウントまたは別のアカウントに格納できます。

1. **ホスト:** ホストは、変更フィード プロセッサを使って変更をリッスンするアプリケーション インスタンスです。 同じリース構成の複数のインスタンスを並列に実行できますが、**インスタンス名** はインスタンスごとに異なっている必要があります。

1. **デリゲート:** デリゲートは、変更フィード プロセッサによって読み取られる変更の各バッチについて、開発者が行いたいことが定義されているコードです。 

変更フィード プロセッサのこれら 4 要素の連携のしくみについて理解を深めるために、次の図の例を見てみましょう。 監視対象コンテナーでは、ドキュメントが保存され、パーティション キーとして "City" が使われます。 パーティション キーの値は項目を含む範囲内に分散されていることがわかります。 2 つのホスト インスタンスがあり、変更フィード プロセッサでは、コンピューティングの分散を最大にするため、各インスタンスに異なる範囲のパーティション キー値が割り当てられます。 各範囲は並列に読み取られ、その進行状況はリース コンテナー内に他の範囲とは区別して保持されます。

:::image type="content" source="./media/change-feed-processor/changefeedprocessor.png" alt-text="変更フィード プロセッサの例" border="false":::

## <a name="implementing-the-change-feed-processor"></a>変更フィード プロセッサの実装

エントリ ポイントは常に監視対象コンテナーであり、`Container` インスタンスから `GetChangeFeedProcessorBuilder` を呼び出します。

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

1 番目のパラメーターは、このプロセッサの目的を説明する一意の名前です。2番目の名前は、変更を処理するデリゲートの実装です。 

デリゲートの例を次に示します。


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

最後に、このプロセッサ インスタンスの名前を `WithInstanceName` で定義します。これは、`WithLeaseContainer` でリース状態を保持するコンテナーです。

`Build` を呼び出すとプロセッサ インスタンスが提供され、`StartAsync` を呼び出すことによってそれを開始できます。

## <a name="processing-life-cycle"></a>処理のライフ サイクル

ホスト インスタンスの通常のライフ サイクルは次のとおりです。

1. 変更フィードを読み取ります。
1. 変更がない場合は、事前に定義された時間 (ビルダーの `WithPollInterval` でカスタマイズ可能) だけスリープし、#1 に移ります。
1. 変更がある場合は、それらを **デリゲート** に送信します。
1. デリゲートによる変更の処理が **正常に** 完了すると、最後に処理された時点でリース ストアを更新し、#1 に移ります。

## <a name="error-handling"></a>エラー処理

変更フィード プロセッサには、ユーザー コード エラーに対する回復性があります。 つまり、デリゲートの実装にハンドルされない例外がある場合 (ステップ #4)、その特定の変更バッチを処理しているスレッドは停止され、新しいスレッドが作成されます。 新しいスレッドでは、そのパーティション キー値範囲に対してリース ストアで保持されている最新時点が確認され、そこから処理が再開されて、同じ変更バッチがデリゲートに効率的に送信されます。 デリゲートによって変更が正しく処理されるまで、この動作が続けられます。また、デリゲートのコードで例外がスローされると、そのバッチが再試行されるため、変更フィード プロセッサでは "少なくとも 1 回" が保証されます。

変更フィード プロセッサが同じバッチの変更を継続的に再試行して "行き詰まる" ことがないように、例外が発生した場合に、ドキュメントに書き込むためのデリゲート コードのロジックを配信不能キューに追加する必要があります。 このように設計することで、今後の変更の処理を継続しながら、未処理の変更を追跡できます。 配信不能キューは、別の Cosmos コンテナーである可能性があります。 問題になるのは、データ ストア自体ではなく、ただ未処理の変更が永続化されることだけです。

さらに、[変更フィード推定機能](how-to-use-change-feed-estimator.md)を使用して、変更フィードを読み取る変更フィード プロセッサ インスタンスの進行状況を監視できます。 この推定を使用すると、CPU、メモリ、ネットワーク帯域幅などの使用可能なリソースによって、変更フィード プロセッサが "停止している" あるいは遅れているかどうかを把握できます。

## <a name="deployment-unit"></a>展開単位

変更フィード プロセッサの 1 つの展開単位は、同じ `processorName` とリース コンテナーの構成を備えた 1 つまたは複数のインスタンスで成り立っています。 変更に対してそれぞれに異なるビジネス フローを備えた展開単位を多数保持することができ、各展開単位は 1 つまたは複数のインスタンスで構成されています。 

たとえば、コンテナーに変更が発生するたびに、外部 API をトリガーする 1 つの展開単位があるとします。 別の展開単位では、変更が発生するたびに、リアルタイムでデータを移動することができます。 監視対象のコンテナーで変更が発生すると、すべての展開単位に対して通知が行われます。

## <a name="dynamic-scaling"></a>動的スケーリング

前述したように、1 つの展開単位内には、1 つまたは複数のインスタンスを保持できます。 展開単位内でコンピューティングの分散を利用するには、次の要件のみが重要になります。

1. すべてのインスタンスのリース コンテナーの構成が同じである必要があります。
1. すべてのインスタンスが、同じ `processorName` を保持している必要があります。
1. 各インスタンスには、異なるインスタンス名が設定されている必要があります (`WithInstanceName`)。

これら 3 つの条件が適用されると、変更フィード プロセッサでは均等分散アルゴリズムを使って、リース コンテナー内のすべてのリースが該当の展開単位に含まれる実行中の全インスタンスに分散されて、コンピューティングが並列化されます。 1 つのリースは一度に 1 つのインスタンスによってのみ所有されるため、インスタンスの最大数はリースの数と同じになります。

インスタンス数は増減する可能性があり、変更フィード プロセッサではそれに従って再配布することで負荷が動的に調整されます。

さらに、スループットまたはストレージの増加に応じて、変更フィード プロセッサでコンテナーを動的に調整できます。 コンテナーが拡張されると、変更フィード プロセッサでは、リースを動的に増やし、既存のインスタンス間で新しいリースを分散することによって、これらのシナリオが透過的に処理されます。

## <a name="change-feed-and-provisioned-throughput"></a>変更フィードとプロビジョニング済みスループット

監視対象コンテナーで変更フィード読み取り操作を行うと、RU が使用されます。 

リース コンテナーに対する操作では、RU が使用されます。 同じリース コンテナーを使用しているインスタンスの数が多いほど、潜在的な RU の消費量が多くなります。 インスタンスの数をスケール調整および増分する場合は、リース コンテナーでの RU の消費量を必ず監視してください。

## <a name="starting-time"></a>開始時刻

既定では、変更フィード プロセッサは、最初に開始したときに、リース コンテナーを初期化し、その[処理のライフ サイクル](#processing-life-cycle)を開始します。 変更フィード プロセッサが初めて初期化される前に監視対象コンテナー内で発生した変更が検出されることはありません。

### <a name="reading-from-a-previous-date-and-time"></a>以前の日時からの読み取り

`DateTime` のインスタンスを `WithStartTime` ビルダー拡張機能に渡すことで、**特定の日時** 以降の変更を読み取るよう変更フィード プロセッサを初期化することができます。

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

変更フィード プロセッサは、その特定の日時に対して初期化され、それ以降に発生した変更の読み取りを開始します。

### <a name="reading-from-the-beginning"></a>最初からの読み取り

データの移行やコンテナーの履歴全体の分析など、他のシナリオでは、**そのコンテナーの有効期間の最初** から変更フィードを読み取る必要があります。 これを行うために、ビルダー拡張機能で `WithStartTime` を使用できますが、`DateTime.MinValue.ToUniversalTime()` を渡すと、次のように `DateTime` の最小値の UTC 表現が生成されます。

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

この変更フィード プロセッサは初期化され、コンテナーの有効期間の最初から変更の読み取りを開始します。

> [!NOTE]
> これらのカスタマイズ オプションは、変更フィード プロセッサの開始時点を設定するためだけに機能します。 リース コンテナーが初めて初期化された後、それらを変更しても影響はありません。

## <a name="where-to-host-the-change-feed-processor"></a>変更フィード プロセッサをホストする場所

変更フィード プロセッサは、長時間実行されるプロセスまたはタスクをサポートする任意のプラットフォームでホストできます。

* 継続的に実行される [Azure WebJob](/learn/modules/run-web-app-background-task-with-webjobs/)。
* [Azure Virtual Machine](/azure/architecture/best-practices/background-jobs#azure-virtual-machines) 内のプロセス
* [Azure Kubernetes Service](/azure/architecture/best-practices/background-jobs#azure-kubernetes-service) でのバックグラウンド ジョブ
* [ASP.NET ホステッド サービス](/aspnet/core/fundamentals/host/hosted-services)。

変更フィード プロセッサは有効期間が短い環境で実行できますが、その状態がリース コンテナーによって維持されるため、これらの環境の開始および停止サイクルでは、通知の受信に遅延が加えられます (環境が開始されるたびにプロセッサを開始するオーバーヘッドが生じるため)。

## <a name="additional-resources"></a>その他のリソース

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub でサンプルアプリケーションを完成させる](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)
* [GitHub でのその他の使用例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [変更フィード プロセッサの Cosmos DB ワークショップ ラボ](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html#consume-cosmos-db-change-feed-via-the-change-feed-processor)

## <a name="next-steps"></a>次のステップ

以下の記事で、変更フィード プロセッサに関してさらに詳しく知ることができます。

* [変更フィードの概要](change-feed.md)
* [変更フィードのプル モデル](change-feed-pull-model.md)
* [変更フィード プロセッサ ライブラリから移行する方法](how-to-migrate-from-change-feed-library.md)
* [変更フィード推定機能の使用](how-to-use-change-feed-estimator.md)
* [変更フィード プロセッサの開始時刻](#starting-time)