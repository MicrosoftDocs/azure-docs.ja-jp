---
title: Azure Cosmos DB での変更フィード プロセッサ ライブラリの操作
description: Azure Cosmos DB 変更フィード プロセッサ ライブラリの使用。
author: rimman
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 4074f26cdefd650c1b927293f422623841dfff7d
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71073698"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Azure Cosmos DB の変更フィード プロセッサ 

変更フィード プロセッサは [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) の一部です。 それにより、変更フィードを読み取り、イベント処理を複数のコンシューマーに効率的に分散させるプロセスが簡単になります。

変更フィード プロセッサ ライブラリの主な利点は、変更フィード内のすべてのイベントが確実に "少なくとも 1 回" は配信されるフォールト トレラントな動作です。

## <a name="components-of-the-change-feed-processor"></a>変更フィード プロセッサのコンポーネント

変更フィード プロセッサの実装には、4 つの主要なコンポーネントがあります。 

1. **監視対象コンテナー:** 監視対象コンテナーには、変更フィードの生成元となるデータが含まれています。 監視対象コンテナーに対する挿入と更新が、コンテナーの変更フィードに反映されます。

1. **リース コンテナー:** リース コンテナーは、状態ストレージとして機能し、複数の worker 間での変更フィードの処理を調整します。 リース コンテナーは、監視対象コンテナーと同じアカウントまたは別のアカウントに格納できます。 

1. **ホスト:** ホストは、変更フィード プロセッサを使って変更をリッスンするアプリケーション インスタンスです。 同じリース構成の複数のインスタンスを並列に実行できますが、**インスタンス名**はインスタンスごとに異なっている必要があります。 

1. **デリゲート:** デリゲートは、変更フィード プロセッサによって読み取られる変更の各バッチについて、開発者が行いたいことが定義されているコードです。 

変更フィード プロセッサのこれら 4 要素の連携のしくみについて理解を深めるために、次の図の例を見てみましょう。 監視対象コンテナーでは、ドキュメントが保存され、パーティション キーとして "City" が使われます。 パーティション キーの値は項目を含む範囲内に分散されていることがわかります。 2 つのホスト インスタンスがあり、変更フィード プロセッサでは、コンピューティングの分散を最大にするため、各インスタンスに異なる範囲のパーティション キー値が割り当てられます。 各範囲は並列に読み取られ、その進行状況はリース コンテナー内に他の範囲とは区別して保持されます。

![変更フィード プロセッサの例](./media/change-feed-processor/changefeedprocessor.png)

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
1. 変更がある場合は、それらを**デリゲート**に送信します。
1. デリゲートによる変更の処理が**正常に**完了すると、最後に処理された時点でリース ストアを更新し、#1 に移ります。

## <a name="error-handling"></a>エラー処理

変更フィード プロセッサには、ユーザー コード エラーに対する回復性があります。 つまり、デリゲートの実装にハンドルされない例外がある場合 (ステップ #4)、その特定の変更バッチを処理しているスレッドは停止され、新しいスレッドが作成されます。 新しいスレッドでは、そのパーティション キー値範囲に対してリース ストアで保持されている最新時点が確認され、そこから処理が再開されて、同じ変更バッチがデリゲートに効率的に送信されます。 デリゲートによって変更が正しく処理されるまで、この動作が続けられます。また、デリゲートのコードで例外がスローされると、そのバッチが再試行されるため、変更フィード プロセッサでは "少なくとも 1 回" が保証されます。

## <a name="dynamic-scaling"></a>動的スケーリング

概要で説明したように、変更フィード プロセッサでは、コンピューティングを複数のインスタンスに自動的に分散させることができます。 変更フィード プロセッサを使ってアプリケーションの複数のインスタンスをデプロイし、それを利用することができます。主な要件は以下だけです。

1. すべてのインスタンスのリース コンテナーの構成が同じである必要があります。
1. すべてのインスタンスのワークフロー名が同じである必要があります。
1. 各インスタンスには、異なるインスタンス名が設定されている必要があります (`WithInstanceName`)。

これら 3 つの条件が適用されると、変更フィード プロセッサでは、均等分散アルゴリズムを使って、リース コンテナー内のすべてのリースが実行中のすべてのインスタンスに分散され、コンピューティングが並列化されます。 1 つのリースは一度に 1 つのインスタンスによってのみ所有されるため、インスタンスの最大数はリースの数と同じになります。

インスタンス数は増減する可能性があり、変更フィード プロセッサではそれに従って再配布することで負荷が動的に調整されます。

さらに、スループットまたはストレージの増加に応じて、変更フィード プロセッサでコンテナーを動的に調整できます。 コンテナーが拡張されると、変更フィード プロセッサでは、リースを動的に増やし、既存のインスタンス間で新しいリースを分散することによって、これらのシナリオが透過的に処理されます。

## <a name="change-feed-and-provisioned-throughput"></a>変更フィードとプロビジョニング済みスループット

Cosmos コンテナーとの間のデータ移動では常に RU を消費するため、消費される RU に対して課金されます。 リース コンテナーによって消費される RU に対して課金されます。

## <a name="additional-resources"></a>その他のリソース

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub の使用例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub のその他のサンプル](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>次の手順

以下の記事で、変更フィード プロセッサに関してさらに詳しく知ることができます。

* [変更フィードの概要](change-feed.md)
* [変更フィード プロセッサ ライブラリから移行する方法](how-to-migrate-from-change-feed-library.md)
* [変更フィード推定機能の使用](how-to-use-change-feed-estimator.md)
* [変更フィード プロセッサの開始時刻](how-to-configure-change-feed-start-time.md)
