---
title: Azure Cosmos DB .NET 変更フィード Processor API、SDK リソース ノート
description: リリース日、提供終了日、.NET Change Feed Processor SDK の各バージョン間の変更など、Change Feed Processor API と SDK に関するあらゆる詳細を提供します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: b1f2efa887fb56d555f354c6d0a3262a7f178e29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577140"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET Change Feed Processor SDK:ダウンロードおよびリリース ノート
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
>
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark コネクタ](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api
> * [REST リソース プロバイダー](/rest/api
> * [SQL](sql-api-query-reference.md)
> * [Bulk Executor - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

|   | リンク  |
|---|---|
|**SDK のダウンロード**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API ドキュメント**|[Change Feed Processor ライブラリ API リファレンス ドキュメント](/dotnet/api/microsoft.azure.documents.changefeedprocessor)|
|**開始するには**|[DocumentDB Change Feed Processor .NET SDK の概要](change-feed.md)|
|**現在サポートされているフレームワーク**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> 変更フィード プロセッサを使用している場合は、変更フィードが SDK に組み込まれている [.NET SDK](change-feed-processor.md) の最新バージョン 3.x を参照してください。 

## <a name="release-notes"></a>リリース ノート

### <a name="v2-builds"></a>v2 のビルド

### <a name="232"></a><a id="2.3.2"></a>2.3.2
* V3 SDK とのリース ストアの互換性が追加されました。これにより、ホット移行パスが有効になります。 アプリケーションを V3 SDK に移行し、状態を失うことなく Change Feed Processor ライブラリに再び移行することができます。

### <a name="231"></a><a id="2.3.1"></a>2.3.1
* パーティションが見つからない場合、またはターゲット レプリカが読み取りセッションで最新の状態になっていない場合、`FeedProcessing.ChangeFeedObserverCloseReason.Unknown` の閉じる理由が `FeedProcessing.IChangeFeedObserver.CloseAsync` に送信される問題を修正しました。 このような場合、`FeedProcessing.ChangeFeedObserverCloseReason.ResourceGone` と `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable` の閉じる理由が使用されるようになりました。
* ターゲット レプリカが読み取りセッションで最新の状態になっていない場合に、変更フィード オブザーバーを閉じるために送信される新しい閉じる理由 `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable` を追加しました。

### <a name="230"></a><a id="2.3.0"></a>2.3.0
* 新しいメソッド `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` と対応するパブリック インターフェイス `ICheckpointPartitionProcessorFactory` が追加されました。 これにより、`IPartitionProcessor` インターフェイスの実装に組み込みのチェックポイント メカニズムを使用できるようになります。 新しいファクトリは既存の `IPartitionProcessorFactory` に似ていますが、`Create` メソッドは `ILeaseCheckpointer` パラメーターも受け取る点が異なります。
* 同じ `ChangeFeedProcessorBuilder` インスタンスに使用できるのは、`ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` または `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` という 2 つのメソッドのいずれかのみです。

### <a name="228"></a><a id="2.2.8"></a>2.2.8
* 安定性と診断能力の向上:
  * 長時間かかっている変更フィード読み取りを検出する機能のサポートを追加しました。 `ChangeFeedProcessorOptions.ChangeFeedTimeout` プロパティによって指定されている値よりも長い時間がかかる場合、以下の手順が行われます。
    * 問題のあるパーティションの変更フィードを読み取る操作は中止されます。
    * 変更フィード プロセッサ インスタンスが問題のあるリースの所有権を破棄します。 破棄されたリースは、同じまたは異なる変更フィード プロセッサ インスタンスによって行われる次のリース取得手順中に取得されます。 このようにして、変更フィードの読み取りがやり直されます。
    * 問題は、ヘルス モニターにレポートされます。 既定のヘルス モニターは、レポートされたすべての問題をトレース ログに送信します。
  * 新しいパブリック プロパティ `ChangeFeedProcessorOptions.ChangeFeedTimeout` を追加しました。 このプロパティの既定値は 10 分です。
  * 新しいパブリック列挙値 `Monitoring.MonitoredOperation.ReadChangeFeed` を追加しました。 `HealthMonitoringRecord.Operation` の値が `Monitoring.MonitoredOperation.ReadChangeFeed` に設定される場合は、正常性の問題が変更フィードの読み取りに関連していることを示しています。

### <a name="227"></a><a id="2.2.7"></a>2.2.7
* ネットワークの問題などにより、すべてのリースの取得にリースの有効期限の間隔よりも長くかかるシナリオの負荷分散戦略を改善しました。
  * このシナリオでは、負荷分散アルゴリズムによって、リースが誤って期限切れと見なされ、アクティブな所有者からリースが奪われていました。 その結果、不必要に多くのリースの再調整がトリガーされる可能性があります。
  * この問題は、このリリースにおいて、所有者が変わっていない期限切れのリースを取得するときに競合を再試行することを避け、期限切れのリースの取得を次回の負荷分散のイテレーションまで延期することで修正されました。

### <a name="226"></a><a id="2.2.6"></a>2.2.6
* オブザーバーの例外の処理が改善されました。
* オブザーバーのエラーに関する情報がより方法になりました。
  * オブザーバーの ProcessChangesAsync によってスローされた例外によりオブザーバーが閉じられると、CloseAsync が ChangeFeedObserverCloseReason.ObserverError に設定された reason パラメーターを受け取るようになりました。
  * オブザーバーでユーザー コード内のエラーを識別するためにトレースが追加されました。

### <a name="225"></a><a id="2.2.5"></a>2.2.5
* 共有データベースのスループットを使用するコレクション内の分割を処理するためのサポートが追加されました。
  * このリリースでは、共有データベースのスループットを使用するコレクション内の分割中に発生する可能性がある、分割によってパーティションのリバランスが発生して子のパーティション キー範囲が 2 つではなく 1 つだけになるという問題が修正されました。 これが発生した場合、Change Feed Processor による古いパーティション キー範囲のリースを削除することができなくなり、新しいリースが作成されない可能性があります。 この問題は今回のリリースで修正されました。

### <a name="224"></a><a id="2.2.4"></a>2.2.4
* 要求の継続トークンからの変更フィードの開始をサポートするための新しいプロパティ ChangeFeedProcessorOptions.StartContinuation が追加されました。 これは、リース コレクションが空であるか、リースに ContinuationToken セットがない場合にのみ使用されます。 ContinuationToken セットがあるリース コレクション内のリースの場合は、ContinuationToken が使用され、ChangeFeedProcessorOptions.StartContinuation は無視されます。

### <a name="223"></a><a id="2.2.3"></a>2.2.3
* カスタム ストアを使用した、パーティションごとの継続トークンの保持のサポートが追加されました。
  * たとえば、カスタム リース ストアには、独自の方法でパーティション分割された Azure Cosmos DB リース コレクションを使用できます。
  * カスタム リース ストアでは、新しい機能拡張ポイント ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) と ILeaseStoreManager パブリック インターフェイスを使用できます。
  * ILeaseManager インターフェイスは複数のロール インターフェイスにリファクタリングされました。
* 小さな破壊的変更: 機能拡張ポイント ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager) は削除されました。代わりに ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) を使用してください。

### <a name="222"></a><a id="2.2.2"></a>2.2.2
* このリリースでは、監視対象コレクション内の分割の処理中とパーティション リース コレクションの使用中に発生する問題が解決されます。 分割パーティションのリースの処理時に、そのパーティションに対応するリースを削除できません。 この問題は今回のリリースで修正されました。

### <a name="221"></a><a id="2.2.1"></a>2.2.1
* 複数の書き込みリージョンがあるアカウントと新しいセッション トークン形式の固定見積もり計算。

### <a name="220"></a><a id="2.2.0"></a>2.2.0
* パーティション分割されたリース コレクションのサポートが追加されました。 パーティション キーは /id として定義する必要があります。
* マイナーな破壊的変更: IChangeFeedDocumentClient インターフェイスと ChangeFeedDocumentClient クラスのメソッドが RequestOptions パラメーターと CancellationToken パラメーターを含むように変更されました。 IChangeFeedDocumentClient は、ドキュメント クライアントのカスタム実装を提供して変更フィード プロセッサで使用できるようにする高度な拡張ポイントです。たとえば、DocumentClient を修飾し、それに対するすべての呼び出しをインターセプトして、追加のトレースやエラー処理などを行います。この更新プログラムでは、IChangeFeedDocumentClient を実装するコードを変更して、新しいパラメーターを実装に含める必要があります。
* マイナーな診断機能の向上。

### <a name="210"></a><a id="2.1.0"></a>2.1.0
* 新しい API、Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync() が追加されました。 これを使用して、パーティションごとの見積作業を取得できます。
* Microsoft.Azure.DocumentDB SDK 2.0 をサポートします。 Microsoft.Azure.DocumentDB 2.0 以降が必要です。

### <a name="206"></a><a id="2.0.6"></a>2.0.6
* v1 との互換性を保つために、ChangeFeedEventHost.HostName パブリック プロパティが追加されました。

### <a name="205"></a><a id="2.0.5"></a>2.0.5
* パーティション分割中に発生する競合状態を修正しました。 競合状態は、リースを取得し、パーティション分割中にすぐにそれを失うことで、競合の発生につながる可能性があります。 この競合状態に関する問題がこのリリースで修正されました。

### <a name="204"></a><a id="2.0.4"></a>2.0.4
* GA SDK

### <a name="203-prerelease"></a><a id="2.0.3-prerelease"></a>2.0.3-prerelease
* 次の問題が修正されました。
  * パーティション分割が発生すると、分割前に修正されたドキュメントの処理が重複する可能性があります。
  * リース コレクションにリースが存在しない場合、GetEstimatedRemainingWork API は 0 を返しました。

* 次の例外が公開されました。 IPartitionProcessor を実装する拡張機能では、次の例外をスローすることができます。
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException。 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException。 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException。
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException。 

### <a name="202-prerelease"></a><a id="2.0.2-prerelease"></a>2.0.2-prerelease
* マイナーな API の変更:
  * 古いとマークされた ChangeFeedProcessorOptions.IsAutoCheckpointEnabled を削除しました。

### <a name="201-prerelease"></a><a id="2.0.1-prerelease"></a>2.0.1-prerelease
* 安定性の向上:
  * リース ストアの初期化の処理が改善しました。 リース ストアが空の場合、プロセッサの 1 つのインスタンスだけがそれを初期化でき、他は待機します。
  * リースの更新または解放の安定性と効率が向上しました。 1 つのパーティションのリースの更新と解放は、他のパーティションの更新から独立しています。 v1 では、すべてのパーティションに対して順番に行われていました。
* 新しい v2 API:
  * プロセッサーの柔軟な構築に対応したビルダー パターン: ChangeFeedProcessorBuilder クラス。
    * 任意の組み合わせのパラメーターを取得できます。
    * 監視用の DocumentClient インスタンスとリース コレクションの両方またはいずれかを取得できます (v1 では利用できません)。
  * IChangeFeedObserver.ProcessChangesAsync で CancellationToken を取得できるようになりました。
  * IRemainingWorkEstimator - 残存する作業見積もりツールは、プロセッサとは個別に使用できます。
  * 新しい拡張性ポイント:
    * IParitionLoadBalancingStrategy - プロセッサのインスタンス間でパーティションのカスタム負荷分散に使用します。
    * ILease、ILeaseManager - カスタムのリース管理に使用します。
    * IPartitionProcessor - パーティション上にあるカスタム処理の変更に使用します。
* ログの記録 - [LibLog](https://github.com/damianh/LibLog) ライブラリを使用します。
* v1 API との 100% の下位互換性
* 新しいコード ベース。
* [SQL .NET SDK](sql-api-sdk-dotnet.md) バージョン 1.21.1 以降と互換性があります。

### <a name="v1-builds"></a>v1 のビルド

### <a name="133"></a><a id="1.3.3"></a>1.3.3
* ログが追加されました。
* 保留中の作業見積もりを複数回呼び出すときの DocumentClient のリークを修正しました。

### <a name="132"></a><a id="1.3.2"></a>1.3.2
* 保留中の作業見積もりを修正しました。

### <a name="131"></a><a id="1.3.1"></a>1.3.1
* 安定性が向上しました。
  * 一部のパーティションでオブザーバーの停止につながる可能性がある取り消されたタスクの問題を処理するために修正しました。
* 手動チェックポイント処理をサポートします。
* [SQL .NET SDK](sql-api-sdk-dotnet.md) バージョン 1.21 以降と互換性があります。

### <a name="120"></a><a id="1.2.0"></a>1.2.0
* .NET Standard 2.0 のサポートを追加します。 このパッケージで `netstandard2.0`と`net451` フレームワーク モニカーがサポートされるようになりました。
* [SQL .NET SDK](sql-api-sdk-dotnet.md) バージョン 1.17.0 以降と互換性があります。
* [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) バージョン 1.5.1 以降と互換性があります。

### <a name="111"></a><a id="1.1.1"></a>1.1.1
* 変更フィードが空であるか保留中の作業がない場合の、残っている作業の推定量の計算に関する問題を修正しました。
* [SQL .NET SDK](sql-api-sdk-dotnet.md) バージョン 1.13.2 以降と互換性があります。

### <a name="110"></a><a id="1.1.0"></a>1.1.0
* Change Feed に処理が残っている作業の推定量を取得するメソッドが追加されました。
* [SQL .NET SDK](sql-api-sdk-dotnet.md) バージョン 1.13.2 以降と互換性があります。

### <a name="100"></a><a id="1.0.0"></a>1.0.0
* GA SDK
* [SQL .NET SDK](sql-api-sdk-dotnet.md) バージョン 1.14.1 以降と互換性があります。

## <a name="release--retirement-dates"></a>リリース日と提供終了日

Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月** 前に通知します。 新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。

> [!WARNING]
> 2022 年 8 月 31 日以降、Azure Cosmos DB .NET または .NET Core SDK for SQL API のバージョン 1.x に対するバグ修正、新機能の追加、サポートの提供は行われません。 アップグレードしない場合でも、SDK バージョン 1.x から送信される要求は、引き続き Azure Cosmos DB サービスによって処理されます。

<br/>

| Version | リリース日 | 提供終了日 |
| --- | --- | --- |
| [2.3.2](#2.3.2) |2020 年 8 月 11 日 |--- |
| [2.3.1](#2.3.1) |2020 年 7 月 30 日 |--- |
| [2.3.0](#2.3.0) |2020 年 4 月 2 日 |--- |
| [2.2.8](#2.2.8) |2019 年 10 月 28 日 |--- |
| [2.2.7](#2.2.7) |2019 年 5 月 14 日 |--- |
| [2.2.6](#2.2.6) |2019 年 1 月 29 日 |--- |
| [2.2.5](#2.2.5) |2018 年 12 月 13 日 |--- |
| [2.2.4](#2.2.4) |2018 年 11 月 29 日 |--- |
| [2.2.3](#2.2.3) |2018 年 11 月 19 日 |--- |
| [2.2.2](#2.2.2) |2018 年 10 月 31 日 |--- |
| [2.2.1](#2.2.1) |2018 年 10 月 24 日 |--- |
| [1.3.3](#1.3.3) |2018 年 5 月 8 日 |--- |
| [1.3.2](#1.3.2) |2018 年 4 月 18 日 |--- |
| [1.3.1](#1.3.1) |2018 年 3 月 13 日 |--- |
| [1.2.0](#1.2.0) |2017 年 10 月 31 日 |--- |
| [1.1.1](#1.1.1) |2017 年 8 月 29 日 |--- |
| [1.1.0](#1.1.0) |2017 年 8 月 13 日 |--- |
| [1.0.0](#1.0.0) |2017 年 7 月 7 日 |--- |

## <a name="faq"></a>よく寄せられる質問

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>関連項目

Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページを参照してください。
