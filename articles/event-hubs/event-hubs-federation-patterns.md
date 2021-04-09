---
title: イベント レプリケーション タスクのパターン - Azure Event Hubs | Microsoft Docs
description: この記事では、特定のイベント レプリケーション タスクのパターンを実装するための詳細なガイダンスを提供します
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 438964c228f060dede93abf582c9504b698db8b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934613"
---
# <a name="event-replication-tasks-patterns"></a>イベント レプリケーション タスクのパターン

[フェデレーションの概要](event-hubs-federation-overview.md)と[レプリケーター関数の概要](event-hubs-federation-replicator-functions.md)に関するページでは、レプリケーション タスクの原理と基本要素について説明しています。この記事を読み進める前にこれらについてよく理解しておくことをお勧めします。

この記事では、概要セクションで強調されているいくつかのパターンの実装ガイダンスについて詳しく説明します。

## <a name="replication"></a>レプリケーション

レプリケーション パターンでは、あるイベント ハブから次のものへ、またはイベント ハブから、Service Bus キューなどの他の宛先にイベントをコピーします。 イベントは、イベント ペイロードを変更することなく転送されます。

このパターンの実装は、「[Event Hubs 間のイベント レプリケーション](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy)」および 「[Event Hubs と Service Bus 間のイベント レプリケーション](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus)」のサンプル、および Apache Kafka ブローカーから Event Hubs にデータをレプリケートする特定のケースについては、「[Event Hubs で Apache Kafka MirrorMaker を使用する](event-hubs-kafka-mirror-maker-tutorial.md)」のチュートリアルに記載されています。

### <a name="streams-and-order-preservation"></a>ストリームと順序の維持

Azure Functions または Azure Stream Analytics を使用するレプリケーションの目的は、ソース イベント ハブからターゲット イベント ハブへの完全に 1 対 1 のクローンの作成を保証することではありませんが、アプリケーションで必要なイベントの相対順序を維持することに重点を置いています。 アプリケーションにより、同じパーティション キーを持つ関連するイベントをグループ化することでこのことが伝達され、[Event Hubs により、同じパーティション キーを持つメッセージが同じパーティション内に順番に配置されます](event-hubs-features.md#partitions)。

> [!IMPORTANT] 
> "オフセット" 情報はイベント ハブごとに一意であり、同じイベントのオフセットはイベント ハブ インスタンス間で異なります。 コピーされたイベント ストリームで位置を見つけるには、時間ベースのオフセットを使用して、[伝達されたサービスによって割り当てられたメタデータ](#service-assigned-metadata)を参照します。
>
> 時間ベースのオフセットでは、特定の時点でレシーバーを開始します。
> - *EventPosition.FromStart()* - 保持されているすべてのデータを再度読み取ります。
> - *EventPosition.FromEnd()* - 接続の時点からすべての新しいデータを読み取ります。
> - *EventPosition.FromEnqueuedTime(dateTime)* - 指定した日付と時刻から始まるすべてのデータ。
>
> EventProcessor では、EventProcessorOptions で InitialOffsetProvider を使用して位置を設定します。 他のレシーバー API では、位置がコンストラクター経由で渡されます。 


事前に構築されたレプリケーション関数のヘルパー (Azure Functions ベースのガイダンスで使用される[サンプルとして提供](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication)) により、ソース パーティションから取得された同じパーティション キーを持つイベント ストリームが、同じパーティション キーで元のストリームのバッチとしてターゲット イベント ハブに送信されることが保証されます。

ソースおよびターゲット イベント ハブのパーティション数が同一であれば、ターゲット内のすべてのストリームが、ソースの場合と同じパーティションにマップされます。
パーティション数が異なる場合 (以下に示す別のパターンのいくつかでは重要)、マッピングは異なりますが、ストリームは常に順序どおりにまとめられます。

異なるストリームに属するイベント、またはターゲット パーティションのパーティション キーがない独立したイベントの相対順序は、常にソース パーティションとは異なる場合があります。

### <a name="service-assigned-metadata"></a>サービスによって割り当てられたメタデータ

ソース イベント ハブから取得されたイベントのサービスによって割り当てられたメタデータ、元のエンキュー時刻、シーケンス番号、およびオフセットは、ターゲット イベント ハブのサービスによって割り当てられた新しい値に置き換えられますが、[ヘルパー関数](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) (サンプルで提供されているレプリケーション タスク) では、元の値は次のユーザー プロパティに保持されます: `repl-enqueue-time` (ISO8601 文字列型)、`repl-sequence`、`repl-offset`。

これらのプロパティは文字列型であり、それぞれの元のプロパティの文字列化された値が含まれています。 複数回イベントが転送される場合は、直接のソースのサービスによって割り当てられたメタデータが既存のプロパティに追加され、値はセミコロンで区切られます。

### <a name="failover"></a>[フェールオーバー]

ディザスター リカバリーのためにレプリケーションを使用する場合、Event Hubs サービスのリージョンの可用性イベントから、あるいはネットワークの中断から保護するために、このような障害シナリオでは、あるイベント ハブから次のものへのフェールオーバーを実行する必要があります。これにより、プロデューサーやコンシューマーに対して、セカンダリ エンドポイントを使用するように指示されます。

すべてのフェールオーバー シナリオでは、名前空間の必須要素が構造的に同一であることが前提となります。これは、Event Hubs とコンシューマー グループは同じ名前で、共有アクセス署名規則やロールベースのアクセス制御規則が同じ方法で設定されることを意味します。 [名前空間の移動に関するガイダンス](move-across-regions.md)に従い、クリーンアップ手順を省略して、セカンダリ名前空間を作成 (および更新) することができます。

プロデューサーとコンシューマーを強制的に切り替えるには、参照にどの名前空間を使用するかに関する情報をアクセスと更新が容易な場所で利用可能にする必要があります。 プロデューサーまたはコンシューマーは頻繁に発生するまたは永続的なエラーを検出した場合、その場所を確認し、それらの構成を調整する必要があります。 その構成を共有するためのさまざまな方法がありますが、ここでは次の 2 つを取り上げます: DNS とファイル共有。

#### <a name="dns-based-failover-configuration"></a>DNS ベースのフェールオーバー構成

1 つの候補として、制御する DNS で DNS SRV レコードの情報を保持し、それぞれのイベント ハブ エンドポイントを指す方法があります。 

> [!IMPORTANT] 
> Event Hubs では、そのエンドポイントを CNAME レコードで直接エイリアス化することが許可されないことに注意してください。つまり、IP アドレス情報を直接解決するのではなく、エンドポイント アドレスに対して回復力のある参照メカニズムとして DNS を使用します。

ドメイン `example.com` を所有しているとします。アプリケーションのゾーンは `test.example.com` です。 2 つの代替の Event Hubs に対して、ここでさらに入れ子になった 2 つのゾーンおよびそれぞれに SRV レコードを作成します。

SRV レコードには、一般的な規則に従って、先頭に `_azure_eventhubs._amqp` が付けられ、2 つのエンドポイント レコードが保持されます。1 つはポート 5671 の AMQP-over-TLS 用で、もう 1 つはポート 443 の AMQP-over-WebSockets 用です。これらの両方が、ゾーンに対応する名前空間の Event Hubs エンドポイントを指します。

| ゾーン                   | SRV レコード                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `eh1.test.example.com` | **`_azure_servicebus._amqp.eh1.test.example.com`**<br>`1 1 5671 eh1-test-example-com.servicebus.windows.net`<br>`2 2 443 eh1-test-example-com.servicebus.windows.net` |
| `eh2.test.example.com` | **`_azure_servicebus._amqp.eh2.test.example.com`**<br>`1 1 5671 eh2-test-example-com.servicebus.windows.net`<br>`2 2 443 eh2-test-example-com.servicebus.windows.net` |

次に、アプリケーションのゾーンで、プライマリ イベント ハブに対応する下位ゾーンを指す CNAME エントリを作成します。

| CNAME レコード                | エイリアス                    |
| --------------------------- | ------------------------ |
| `eventhub.test.example.com` | `test1.test.example.com` |

その後、明示的な CNAME および SRV レコードに対するクエリの実行を許可する DNS クライアントを使用して (Java および .NET の組み込みクライアントで許可されるのは、IP アドレスへの名前のシンプルな解決のみ)、目的のエンドポイントを解決できます。 たとえば、[DnsClient.NET](https://dnsclient.michaco.net/) の場合、参照関数は次のようになります。

```C#
static string GetEventHubName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_eventhub._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

この関数からは、上記のように CNAME で現在エイリアス化されているゾーンのポート 5671 に登録されているターゲット ホスト名が返されます。

フェールオーバーを実行するには、CNAME レコードを編集し、代替ゾーンを指定する必要があります。

DNS (具体的には [Azure DNS](../dns/dns-overview.md)) を使用する利点は、Azure DNS の情報がグローバルにレプリケートされるため、単一リージョンの障害に対する回復性があることです。

この手順は、[Event Hubs Geo-DR](event-hubs-geo-dr.md) の場合と似ていますが、ユーザーが自分で完全に制御でき、アクティブ/アクティブ シナリオでも機能します。

#### <a name="file-share-based-failover-configuration"></a>ファイル共有ベースのフェールオーバー構成

エンドポイント情報の共有に DNS を使用する最もシンプルな代替方法は、プライマリ エンドポイントの名前をプレーンテキスト ファイルに格納し、障害に対して堅牢で、引き続き更新を許可するインフラストラクチャからファイルを提供することです。

グローバル対応でコンテンツ レプリケーションが可能な高可用性 Web サイト インフラストラクチャを既に実行している場合は、このようなファイルをそこに追加し、切り替えが必要な場合はファイルを再発行します。

> [!CAUTION]
> エンドポイント名は、シークレットを含む完全な接続文字列ではなく、この方法でのみ公開する必要があります。

#### <a name="extra-considerations-for-failing-over-consumers"></a>コンシューマーのフェールオーバーに関する追加の考慮事項

イベント ハブ コンシューマーの場合、フェールオーバー戦略に関するその他の考慮事項は、イベント プロセッサのニーズによって異なります。

バックアップ データから、データベースを含むシステムを再構築する必要がある障害が発生し、そのデータベースが直接、またはイベント ハブに保持されているイベントからの中間処理を介してフィードされる場合は、バックアップを復元してから、元のシステムが破棄された時点からではなく、データベースのバックアップが作成された時点からシステムへのイベントの再生を開始する必要があります。

障害がシステムのスライスにのみ影響する場合や、実際には到達できなくなったイベント ハブが 1 つだけの場合は、処理が中断されたのとほぼ同じ位置からイベントの処理を続行するのが一般的です。

いずれかのシナリオを実現し、それぞれの Azure SDK のイベント プロセッサを使用するには、[新しいチェックポイント ストアを作成](event-processor-balance-partition-load.md#checkpointing)し、処理を再開する "_タイムスタンプ_" に基づいて、初期のパーティション位置を指定します。

切り替え元のイベント ハブのチェックポイント ストアに引き続きアクセスできる場合は、既に処理されているイベントをスキップし、最後に中断した場所から正確に再開するのに、前述の[伝達されたメタデータ](#service-assigned-metadata)が役立ちます。

## <a name="merge"></a>マージする

マージ パターンには、1 つのターゲットを指す 1 つまたは複数のレプリケーション タスクがあります。また、通常のプロデューサーと同時に、同じターゲットにイベントが送信される場合もあります。

これらのパターンの種類は次のとおりです。

- 2 つ以上のレプリケーション関数で、別々のソースからイベントを同時に取得し、それらを同じターゲットに送信する。
- もう 1 つのレプリケーション関数で、ソースからイベントを取得するが、ターゲットもプロデューサーによって直接使用される。
- パターンは前述のものだが、2 つ以上のイベント ハブ間でミラー化されるため、イベントの生成場所に関係なく、それらのイベント ハブには同じストリームが含まれる。

最初の 2 つのパターンの違いはわずかですが、単純なレプリケーション タスクとは異なります。

最後のシナリオでは、既にレプリケートされているイベントが再度レプリケートされないようにする必要があります。 この手法については、[EventHubToEventHubMerge](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/code/EventHubMerge) サンプルで説明されています。

## <a name="editor"></a>エディター

エディター パターンは[レプリケーション](#replication) パターンに基づいていますが、メッセージは転送される前に変更されます。 

そのような変更の例を以下に示します。

- "**_コード変換_**" - _Apache Avro_ 形式または何らかの独自のシリアル化形式を使用してエンコードされたソースからイベント コンテンツ ("本文" または "ペイロード" ともいう) が到着したものの、ターゲットを所有しているシステムではコンテンツが _JSON_ エンコードされることを想定している場合、コード変換レプリケーション タスクでは、まず _Apache Avro_ からメモリ内オブジェクト グラフにペイロードを逆シリアル化してから、そのグラフを、転送されるイベントのために _JSON_ 形式にシリアル化します。 また、コード変換には **コンテンツの圧縮** とその解除のタスクも含まれます。
- "**_変換_**" - 構造化データを含むイベントは、ダウンストリーム コンシューマーによる使用をより容易にするために、そのデータをリシェイプする必要がある場合があります。 これには、入れ子構造のフラット化、余分なデータ要素の排除、特定のスキーマに正確に適合させるためのペイロードのリシェイプなどの作業が含まれる場合があります。
- "**_バッチ処理_**" - ソースからバッチでイベント (1 回の転送で複数のイベント) が受信される場合がありますが、ターゲットに 1 つずつ転送する必要があります。その逆の場合も同様です。 そのため、タスクで 1 つの入力イベントの転送に基づいて複数のイベントが転送されたり、一連のイベントがまとめて転送されたりする場合があります。
- "**_検証_**" - 外部ソースからのイベント データでは多くの場合、転送する前に一連の規則に準拠しているかどうかを確認する必要があります。 これらの規則はスキーマまたはコードを使用して表すことができます。 準拠していないことが検出されたイベントは、ログに記録された問題と共に削除されることもあれば、特別なターゲット宛先に転送されてさらに処理されることもあります。
- "**_エンリッチメント_**" - 一部のソースから送信されるイベント データは、ターゲット システムで使用できるようにするためにさらにコンテキストでのエンリッチメントが必要な場合があります。 これには、参照データを検索し、そのデータをイベントと共に埋め込んだり、レプリケーション タスクで認識されてはいるもののイベントには含まれていないソースに関する情報を追加したりする作業が含まれる場合があります。
- "**_フィルター処理_**" - ソースから到着する一部のイベントは、何らかの規則に基づいてターゲットから除外する必要がある場合があります。 フィルターにより、規則に基づいてイベントがテストされ、その規則と一致しない場合はイベントが削除されます。 特定の条件を観察し、同じ値の後続のイベントを削除することによって重複イベントをフィルター処理することは、フィルター処理の 1 つの形式です。
- "**_暗号化_**" - レプリケーション タスクでは、ソースから到着するコンテンツの暗号化を解除したり、ターゲットに転送されるコンテンツを暗号化したりする必要がある場合があります。また、イベントで伝達される署名を基準にコンテンツやメタデータの整合性を確認したり、そのような署名を添付したりする必要がある場合があります。
- "**_構成証明_**" - レプリケーション タスクでは、特定のチャネルを介して、あるいは特定の時刻にイベントが受信されたことを証明するイベントに、デジタル署名で保護される可能性のあるメタデータを添付する場合があります。
- "**_チェーン_**" - レプリケーション タスクでは、ストリームの整合性が保護され、欠落しているイベントを検出できるように、イベントのストリームに署名を適用する場合があります。

通常、変換、バッチ処理、およびエンリッチメントのパターンは [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) ジョブで実装することをお勧めします。 

これらのパターンはすべて Azure Functions を使用して実装でき、[Event Hubs トリガー](../azure-functions/functions-bindings-event-hubs-trigger.md)を使用してイベントを取得し、[イベント ハブの出力バインド](../azure-functions/functions-bindings-event-hubs-output.md)を使用してそれらを配信します。

## <a name="routing"></a>ルーティング

ルーティング パターンは[レプリケーション](#replication) パターンに基づいていますが、1 つのソースと 1 つのターゲットがあるのではなく、レプリケーション タスクに複数のターゲットがあります。これについては、以下に C# で示します。

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest1", Connection = "EventHubConnectionAppSetting")] EventHubClient output1,
    [EventHub("dest2", Connection = "EventHubConnectionAppSetting")] EventHubClient output2,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // send to output1 and/or output2 based on criteria
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output1, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2==0 ) ? inputEvent : null;
        });
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output2, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2!=0 ) ? inputEvent : null;
        });
    }
}
```

ルーティング関数では、メッセージ メタデータやメッセージ ペイロードを考慮し、送信先として使用可能な宛先のいずれかを選択します。

Azure Stream Analytics では、複数の出力を定義し、出力ごとにクエリを実行することで、同じことを実現できます。

```sql
select * into dest1Output from inputSource where Info = 1
select * into dest2Output from inputSource where Info = 2
```


## <a name="log-projection"></a>ログ プロジェクション

ログ プロジェクション パターンでは、イベント ストリームがインデックス付きデータベースにフラット化され、イベントはデータベース内のレコードになります。 通常、イベントは同じコレクションまたはテーブルに追加され、イベント ハブ パーティション キーは、レコードを一意にするための主キーのパーティになります。

ログ プロジェクションでは、イベント データの時系列ヒストリアンまたは圧縮されたビューを生成できます。これにより、パーティション キーごとに最新のイベントのみが保持されます。 ターゲット データベースのシェイプは、最終的にはユーザーとユーザーのアプリケーションのニーズによって決まります。 このパターンは "イベント ソーシング" とも呼ばれます。

> [!TIP]
> Azure Stream Analytics で [Azure SQL Database](../stream-analytics/sql-database-output.md) および [Azure Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) へのログ プロジェクションを簡単に作成することができます。このオプションを選択することをお勧めします。

次の Azure 関数では、Azure CosmosDB コレクションに圧縮されたイベント ハブの内容を射影します。

```C#
[FunctionName("Eh1ToCosmosDb1Json")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static async Task Eh1ToCosmosDb1Json(
    [EventHubTrigger("eh1", ConsumerGroup = "Eh1ToCosmosDb1", Connection = "Eh1ToCosmosDb1-source-connection")] EventData[] input,
    [CosmosDB(databaseName: "SampleDb", collectionName: "foo", ConnectionStringSetting = "CosmosDBConnection")] IAsyncCollector<object> output,
    ILogger log)
{
    foreach (var ev in input)
    {
        if (!string.IsNullOrEmpty(ev.SystemProperties.PartitionKey))
        {
            var record = new
            {
                id = ev.SystemProperties.PartitionKey,
                data = JsonDocument.Parse(ev.Body),
                properties = ev.Properties
            };
            await output.AddAsync(record);
        }
    }
}
```

## <a name="next-steps"></a>次のステップ

- [Azure Functions のイベント レプリケーター アプリケーション][1]
- [イベント ハブ間でのイベントのレプリケート][2]
- [Azure Service Bus へのイベントのレプリケート][3]

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
