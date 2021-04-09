---
title: Cosmos DB 用 Azure Functions トリガーを使用するときの問題のトラブルシューティングを行う
description: Cosmos DB 用 Azure Functions トリガーを使用するときの一般的な問題、回避策、診断手順です
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 12/29/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 1b7b82ea07b7e00d281739011c9c9f83ab4dff73
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97825623"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB 用 Azure Functions トリガーを使用するときの問題の診断とトラブルシューティングを行う
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、[Azure Cosmos 用 Azure Functions トリガー](change-feed-functions.md)を使用するときの一般的な問題、回避策、診断手順について説明します。

## <a name="dependencies"></a>依存関係

Cosmos DB 用 Azure Functions トリガーとバインドは、基になる Azure Functions Runtime に対する拡張機能パッケージに依存します。 これらのパッケージを常に最新の状態にしておいてください。発生する可能性のある問題に対処する新機能や修正プログラムが含まれている場合があります。

* Azure Functions V2 の場合は、[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) をご覧ください。
* Azure Functions V1 の場合は、[Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) をご覧ください。

この記事では、明示的に指定されていない限り、ランタイムに言及するときは常に Azure Functions V2 のことです。

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Azure Cosmos DB SDK を個別に使用する

拡張機能パッケージの重要な機能は、Cosmos DB 用 Azure Functions のトリガーとバインドのサポートを提供することです。 また、[Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md) も含まれており、トリガーやバインドを使わずにプログラムで Azure Cosmos DB とやり取りする場合に便利です。

Azure Cosmos DB SDK を使う場合は、別の NuGet パッケージの参照をプロジェクトに追加しないでください。 代わりに、**Azure Functions の拡張機能パッケージによって SDK の参照が解決されるようにします**。 トリガーおよびバインドとは別に Azure Cosmos DB SDK を使用する

さらに、[Azure Cosmos DB SDK クライアント](./sql-api-sdk-dotnet-core.md)の独自のインスタンスを手動で作成する場合は、[シングルトン パターン アプローチを使用](../azure-functions/manage-connections.md#documentclient-code-example-c)して、クライアントのインスタンスを 1 つだけ使用するパターンに従う必要があります。 このプロセスにより、操作でのソケットの問題の可能性を回避できます。

## <a name="common-scenarios-and-workarounds"></a>一般的なシナリオと回避策

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>コレクションが存在しないというエラー メッセージで Azure 関数が失敗する

Azure Function が次のエラー メッセージで失敗する: "Either the source collection 'collection-name' (in database 'database-name') or the lease collection 'collection2-name' (in database 'database2-name') does not exist. Both collections must exist before the listener starts. To automatically create the lease collection, set 'CreateLeaseCollectionIfNotExists' to 'true'" (ソース コレクション 'コレクション名' (データベース 'データベース名' 内) またはリース コレクション 'コレクション名 2' (データベース 'データベース名 2' 内) が存在しません。リスナーが開始する前に、両方のコレクションが存在している必要があります。リース コレクションを自動的に作成するには、'CreateLeaseCollectionIfNotExists' を 'true' に設定します)

これは、トリガーが機能するために必要な Azure Cosmos コンテナーのいずれか一方または両方が存在しないか、または Azure 関数でアクセスできないことを意味します。 構成に基づき、**トリガーが探している Azure Cosmos データベースとコンテナーがエラー自体で示されます**。

1. `ConnectionStringSetting` 属性を確認し、それが **Azure 関数アプリ内に存在する設定を参照している** ことを確認します。 この属性の値は、接続文字列自体ではなく、構成設定の名前になっている必要があります。
2. Azure Cosmos アカウントに `databaseName` と `collectionName` が存在していることを確認します。 値の自動置換を使用している場合は (`%settingName%` パターンを使用)、設定の名前が自分の Azure 関数アプリに存在することを確認します。
3. `LeaseCollectionName/leaseCollectionName` を指定しないと、既定値の "leases" になります。 そのようなコンテナーが存在することを確認します。 必要に応じて、トリガーの `CreateLeaseCollectionIfNotExists` 属性を `true` 設定し、自動的にそれを作成することができます。
4. [Azure Cosmos アカウントのファイアウォール構成](how-to-configure-firewall.md)を調べて、Azure 関数をブロックしていないことを確認します。

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure 関数の開始が、"Shared throughput collection should have a partition key" (共有スループット コレクションにはパーティション キーが必要です) で失敗する

以前のバージョンの Azure Cosmos DB 拡張機能では、[共有スループット データベース](./set-throughput.md#set-throughput-on-a-database)内で作成されたリース コンテナーの使用はサポートされていませんでした。 この問題を解決するには、[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) 拡張機能を最新バージョンに更新します。

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Azure 関数は、"PartitionKey must be supplied for this operation" (この操作には PartitionKey を指定する必要があります) で開始できません。

このエラーは、古い[拡張機能の依存関係](#dependencies)を持つパーティション分割されたリース コレクションが現在使用されていることを示しています。 利用可能な最新バージョンにアップグレードしてください。 現在 Azure Functions V1 で実行している場合は、Azure Functions V2 にアップグレードする必要があります。

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure 関数の開始が、"The lease collection, if partitioned, must have partition key equal to id" (リース コレクションがパーティション分割されている場合は、パーティション キーと ID が同じである必要があります) で失敗する

このエラーは、現在のリース コンテナーがパーティション分割されているのに、パーティション キーのパスが `/id` ではないことを意味します。 この問題を解決するには、パーティション キーを `/id` としてリース コンテナーを作り直す必要があります。

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>"Value cannot be null. Parameter name: o" (値を null にすることはできません。パラメーター名: o) と、トリガーを実行しようとすると Azure Functions ログに記録される

Azure portal を使用していて、トリガーを使用する Azure 関数を調べているときに画面の **[実行]** ボタンを選択しようとすると、この問題が発生します。 トリガーを開始するために [実行] を選択する必要はなく、Azure 関数をデプロイすると自動的に開始されます。 Azure portal で Azure 関数のログ ストリームを確認したい場合は、単に監視対象のコンテナーに移動して新しい項目をいくつか挿入すると、トリガーの実行が自動的に表示されます。

### <a name="my-changes-take-too-long-to-be-received"></a>変更の受信に時間がかかりすぎる

このシナリオには可能性のある原因が複数あり、それらのすべてを確認する必要があります。

1. Azure 関数は、Azure Cosmos アカウントと同じリージョンにデプロイされていますか。 ネットワーク待機時間を最適にするには、Azure 関数と Azure Cosmos アカウントの両方を、同じ Azure リージョンに併置する必要があります。
2. Azure Cosmos コンテナーでの変更の発生は、連続的ですか、それとも散発的ですか。
後者の場合、変更が格納されてから Azure 関数がそれを取得するまでの間に、若干の遅延がある可能性があります。 これは、トリガーは、Azure Cosmos コンテナー内の変更を調べて、読み取る必要のある保留中の変更がないことを確認すると、RU の消費が増えるのを避けるため、新しい変更を確認する前に、構成可能な時間 (既定では 5 秒) だけスリープ状態になるという、内部的な動作のためです。 このスリープ時間は、トリガーの[構成](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)の `FeedPollDelay/feedPollDelay` の設定を使用して構成できます (値はミリ秒単位)。
3. お使いの Azure Cosmos コンテナーが、[レート制限されている](./request-units.md)可能性があります。
4. トリガーの `PreferredLocations` 属性を使用して Azure リージョンのコンマ区切りリストを指定し、ユーザー設定の優先接続順序を定義することができます。

### <a name="some-changes-are-repeated-in-my-trigger"></a>一部の変更がトリガーで繰り返される

"変更" の概念は、ドキュメントに対する 1 つの操作です。 同じドキュメントに対して複数のイベントが受信される最も一般的なシナリオは、次のとおりです。
* アカウントで、最終的な整合性が使用されている。 変更フィードを最終的な整合性レベルで使用している場合は、後続の変更フィードの読み取り操作の間に重複するイベントが発生する可能性があります (1 つの読み取り操作の最後のイベントが、次の操作の最初のものとして表示されます)。
* ドキュメントが更新されている。 変更フィードには、同じドキュメントに対して複数の操作を含めることができます。そのドキュメントが更新を受信している場合は、複数のイベント (更新ごとに 1 つ) を取得できます。 同じドキュメントに対する異なる操作を区別する簡単な方法の 1 つは、[各変更の `_lsn` プロパティ](change-feed.md#change-feed-and-_etag-_lsn-or-_ts)を追跡することです。 それらが一致しない場合、これらは同じドキュメントに対する異なる変更です。
* `id` のみでドキュメントを識別する場合、ドキュメントの一意の識別子は `id` とそのパーティション キーであることに注意してください (同じ `id` を持つが、異なるパーティション キーを持つ 2 つのドキュメントが存在する可能性があります)。

### <a name="some-changes-are-missing-in-my-trigger"></a>一部の変更がトリガーで失われる

Azure Cosmos コンテナーで発生した変更の一部が Azure Function によって取得されない場合、または、一部の変更のコピー中にコピー先で変更が失われた場合は、次の手順に従ってください。

Azure 関数では、多くの場合、受け取った変更の処理が行われ、必要に応じて結果が別の宛先に送信されることがあります。 失われた変更を調査するときは、その宛先ではなく、(Azure 関数の開始時に) **インジェスト ポイントで受信されている変更を測定する** 必要があります。

一部の変更が宛先で失われる場合、変更を受け取った後の Azure 関数での実行の間に何らかのエラーが発生していることを意味している可能性があります。

このシナリオでの最善の対応策は、コードに `try/catch` ブロックを追加し、変更を処理している可能性があるループ内で、項目の特定のサブセットに対する障害を検出して、それらを適切に処理することです (さらに分析するために別のストレージに送信するか、再試行します)。

> [!NOTE]
> 既定の Cosmos DB 用 Azure Functions トリガーでは、コードの実行中にハンドルされない例外が発生した場合、変更のバッチの再試行は行われません。 つまり、宛先に変更が到達しなかった理由は、それらを処理していないためです。

コピー先が別の Cosmos コンテナーであり、項目をコピーするために Upsert 操作を実行している場合は、**監視対象のコンテナーとコピー先コンテナーの両方でパーティション キー定義が同じであることを確認します**。 この構成の違いが原因で、Upsert 操作で、複数のソース項目がコピー先で 1 つの項目として保存されている可能性があります。

一部の変更がトリガーによってまったく受信されない場合、最もよくあるシナリオは、**別の Azure 関数が実行されている** ことです。 **まったく同じ構成** (監視対象コンテナーとリース コンテナーが同じ) を持つ別の Azure 関数が、Azure にデプロイされているか、または開発者のコンピューター上でローカルに実行されていて、この Azure 関数により、自分の Azure 関数で処理されるはずの変更のサブセットが盗まれている可能性があります。

さらに、実行している Azure 関数アプリ インスタンスの数がわかっている場合は、そのシナリオを検証できます。 リース コンテナーを調べて、その中のリース項目の数を数えた場合、`Owner` プロパティの異なる値の数は、関数アプリのインスタンスの数と等しくなっている必要があります。 既知の Azure 関数アプリ インスタンスの数より所有者の数が多い場合、余分な所有者が変更を "盗んでいる" ことを意味します。

このような状況を回避する簡単な方法は、`LeaseCollectionPrefix/leaseCollectionPrefix` を新規または別の値で関数に適用するか、または新しいリース コンテナーでテストします。

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>再起動してコンテナーにあるすべての項目を最初から再処理する必要がある 
コンテナーにあるすべての項目を最初から再処理するには、次の手順を実行します。
1. 現在実行中の場合は、Azure 関数を停止します。 
1. リース コレクション内のドキュメントを削除します (または、空になるようにリース コレクションを削除して再作成します)。
1. 関数内の [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger 属性を true に設定します。 
1. Azure 関数を再起動します。 これですべての変更が最初から読み取られ、処理されるようになります。 

[StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) を true に設定すると、現在の時刻ではなく、コレクションの履歴の最初から変更の読み取りを開始するように、Azure 関数が指示されます。 これは、リースがまだ作成されていないとき (つまり、リース コレクションにドキュメントがあるとき) にのみ機能します。 既に作成されているリースがある場合は、このプロパティを true に設定しても影響はありません。このシナリオでは、関数を停止して再起動すると、リース コレクションで定義されている最後のチェックポイントから読み取りが開始されます。 最初から再処理するには、上記の手順 1 から 4 に従います。  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>バインディングを実行するには、IReadOnlyList\<Document> または JArray を使用する必要があります。

このエラーは、Azure Functions プロジェクト (または任意の参照されているプロジェクト) に、[Azure Functions Cosmos DB 拡張機能](./troubleshoot-changefeed-functions.md#dependencies)で提供されているバージョンとは異なるバージョンの Azure Cosmos DB SDK への手動による NuGet の参照が含まれている場合に発生します。

この状況を回避するには、追加された手動による NuGet の参照を削除し、Azure Cosmos DB SDK の参照で Azure Functions Cosmos DB 拡張機能パッケージを介して解決されるようにします。

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>変更を検出するための Azure 関数のポーリング間隔を変更する

前の「[変更の受信に時間がかかりすぎる](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)」で説明したように、Azure 関数は、(高い RU 消費を避けるため) 新しい変更を確認する前に、構成可能な時間 (既定では 5 秒) だけスリープ状態になります。 このスリープ時間は、トリガーの[構成](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)の `FeedPollDelay/feedPollDelay` の設定を使用して構成できます (値はミリ秒単位)。

## <a name="next-steps"></a>次のステップ

* [Azure Functions の監視を有効にする](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK のトラブルシューティング](./troubleshoot-dot-net-sdk.md)
