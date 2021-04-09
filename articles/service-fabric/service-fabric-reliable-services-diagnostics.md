---
title: Azure Service Fabric ステートフル Reliable Services の診断
description: Azure Service Fabric のステートフル Reliable Services の診断機能
ms.topic: conceptual
ms.date: 8/24/2018
ms.openlocfilehash: 774a771d0c9701076a5d6c070963bf6224a571dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789332"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>ステートフル Reliable Services の診断機能
Azure Service Fabric ステートフル Reliable Services の StatefulServiceBase クラスは、サービスのデバッグに使用することができる [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) イベントを出力するため、ランタイムの動作状況を理解し、トラブルシューティングに役立ちます。

## <a name="eventsource-events"></a>EventSource イベント
ステートフル Reliable Services の StatefulServiceBase クラスの EventSource 名は、"Microsoft-ServiceFabric-Services" です。 このイベント ソースからのイベントは、サービスを [Visual Studio でデバッグしている](service-fabric-debugging-your-application.md)ときに、[[診断イベント](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio)] ウィンドウに表示されます。

EventSource イベントの収集や表示に役立つツールとテクノロジの例には、[PerfView](https://www.microsoft.com/download/details.aspx?id=28567)、[Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md)、および [Microsoft TraceEvent ライブラリ](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)があります。

## <a name="events"></a>events
| イベント名 | イベント ID | Level | イベントの説明 |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informational |サービスの RunAsync タスクが開始されたときに出力されます |
| StatefulRunAsyncCancellation |2 |Informational |サービスの RunAsync タスクが取り消されたときに出力されます |
| StatefulRunAsyncCompletion |3 |Informational |サービスの RunAsync タスクが完了したときに出力されます |
| StatefulRunAsyncSlowCancellation |4 |警告 |サービスの RunAsync タスクが取り消しの実行に時間がかかりすぎたときに出力されます |
| StatefulRunAsyncFailure |5 |エラー |サービスの RunAsync タスクが例外をスローしたときに出力されます |

## <a name="interpret-events"></a>イベントの解釈
StatefulRunAsyncInvocation、StatefulRunAsyncCompletion、および StatefulRunAsyncCancellation イベントは、サービスの作成者が、サービスのライフ サイクルや、サービスの開始、取り消し、または完了のタイミングを知るために役立ちます。 この情報はサービスの問題をデバッグする場合やサービスのライフ サイクルを理解する場合に役立つことがあります。

StatefulRunAsyncSlowCancellation および StatefulRunAsyncFailure イベントはサービスの問題を示しているため、サービスの作成者はこれらのイベントに細心の注意を払う必要があります。

StatefulRunAsyncFailure は、サービスの RunAsync() タスクが例外をスローするたびに出力されます。 一般に、スローされる例外は、サービスのエラーやバグを示します。 さらに、この例外はサービスが失敗する原因となるため、別のノードに移動されます。 この操作には高い負荷がかかる可能性があるため、サービスの移動中には要求の受信が遅れることがあります。 サービスの作成者は例外の原因を特定し、可能であればそれを軽減する必要があります。

StatefulRunAsyncSlowCancellation は、RunAsync タスクの取り消し要求が 4 秒より長くかかる場合に出力されます。 取り消しの完了に時間がかかると、サービスが別のノードで速やかに再開できなくなります。 このシナリオはサービスの全体の可用性に影響を与える可能性があります。

## <a name="performance-counters"></a>パフォーマンス カウンター
Reliable Services ランタイムでは、次のパフォーマンス カウンター カテゴリを定義しています。

| カテゴリ | 説明 |
| --- | --- |
| Service Fabric トランザクション レプリケーター |Azure Service Fabric トランザクション レプリケーターに固有のカウンター |
| Service Fabric TStore |Azure Service Fabric TStore に固有のカウンター |

Service Fabric トランザクション レプリケーターは、特定の一連の[レプリカ](service-fabric-concepts-replica-lifecycle.md)内でトランザクションをレプリケートするために、[Reliable State Manager](./service-fabric-reliable-services-introduction.md) によって使用されます。

Service Fabric TStore は、[Reliable Collections](./service-fabric-reliable-services-introduction.md) でキーと値のペアを格納および取得するために使用されるコンポーネントです。

パフォーマンス カウンター データの収集と表示には、Windows オペレーティング システムで既定で使用できる [Windows パフォーマンス モニター](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) アプリケーションを使用できます。 [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) があります。

### <a name="performance-counter-instance-names"></a>パフォーマンス カウンター インスタンス名
多数の Reliable Service または Reliable Service パーティションを持つクラスターには、多数のトランザクション レプリケーターのパフォーマンス カウンター インスタンスが含まれます。 これは TStore パフォーマンス カウンターの場合と同様ですが、使用されているリライアブル ディクショナリとリライアブル キューの数も乗算されます。 パフォーマンス カウンター インスタンス名は、パフォーマンス カウンター インスタンスが関連付けられている特定の[パーティション](service-fabric-concepts-partitioning.md)、サービス レプリカ、および TStore の場合の状態プロバイダーを識別するために役立ちます。

#### <a name="service-fabric-transactional-replicator-category"></a>Service Fabric トランザクション レプリケーターのカテゴリ
カテゴリ `Service Fabric Transactional Replicator`では、カウンター インスタンス名の形式は次のようになります。

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* は、パフォーマンス カウンター インスタンスが関連付けられている Service Fabric パーティション ID の文字列表現です。 パーティション ID は GUID であり、その文字列表現は書式指定子 "D" を持つ [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) を使用して生成されます。

*ServiceFabricReplicaId* は、Reliable Service の特定のレプリカに関連付けられている ID です。 一意性を確保し、同じパーティションによって生成された他のパフォーマンス カウンター インスタンスと競合しないように、レプリカ ID は、パフォーマンス カウンター インスタンス名の中に組み入れられています。 Reliable Services におけるレプリカとその役割に関する詳細については、[こちら](service-fabric-concepts-replica-lifecycle.md)で確認できます。

次のカウンター インスタンス名は、`Service Fabric Transactional Replicator` カテゴリ下のカウンターでは一般的です。

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

この例の `00d0126d-3e36-4d68-98da-cc4f7195d85e` は Service Fabric パーティション ID の文字列表現であり、`131652217797162571` はレプリカ ID です。

#### <a name="service-fabric-tstore-category"></a>Service Fabric TStore カテゴリ
カテゴリ `Service Fabric TStore`では、カウンター インスタンス名の形式は次のようになります。

`ServiceFabricPartitionId:ServiceFabricReplicaId:StateProviderId_PerformanceCounterInstanceDifferentiator_StateProviderName`

*ServiceFabricPartitionId* は、パフォーマンス カウンター インスタンスが関連付けられている Service Fabric パーティション ID の文字列表現です。 パーティション ID は GUID であり、その文字列表現は書式指定子 "D" を持つ [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) を使用して生成されます。

*ServiceFabricReplicaId* は、Reliable Service の特定のレプリカに関連付けられている ID です。 一意性を確保し、同じパーティションによって生成された他のパフォーマンス カウンター インスタンスと競合しないように、レプリカ ID は、パフォーマンス カウンター インスタンス名の中に組み入れられています。 Reliable Services におけるレプリカとその役割に関する詳細については、[こちら](service-fabric-concepts-replica-lifecycle.md)で確認できます。

*StateProviderId* は、リライアブル サービス内の状態プロバイダーと関連付けられている ID です。 状態プロバイダー ID は、TStore を別の TStore と区別するために、パフォーマンス カウンター インスタンス名の中に組み入れられています。

*PerformanceCounterInstanceDifferentiator* は、状態プロバイダー内のパフォーマンス カウンター インスタンスに関連付けられた差別化 ID です。 一意性を確保し、同じ状態プロバイダーによって生成された他のパフォーマンス カウンター インスタンスと競合しないように、この差別化要素は、パフォーマンス カウンター インスタンス名の中に組み入れられています。

*StateProviderName* は、リライアブル サービス内の状態プロバイダーと関連付けられている名前です。 状態プロバイダー名は、提供する状態をユーザーが簡単に識別できるように、パフォーマンス カウンターのインスタンス名の中に組み入れられています。

次のカウンター インスタンス名は、`Service Fabric TStore` カテゴリ下のカウンターでは一般的です。

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337_urn:MyReliableDictionary/dataStore`

上記の例で、`00d0126d-3e36-4d68-98da-cc4f7195d85e` は Service Fabric パーティション ID の文字列表現、`131652217797162571` はレプリカ ID、`142652217797162571` は状態プロバイダー ID、`1337` はパフォーマンス カウンター インスタンスの差別化要素です。 `urn:MyReliableDictionary/dataStore` は、`urn:MyReliableDictionary` という名前のコレクションのデータを格納する状態プロバイダーの名前です。

### <a name="transactional-replicator-performance-counters"></a>トランザクション レプリケーター パフォーマンス カウンター

Reliable Services ランタイムでは、`Service Fabric Transactional Replicator` カテゴリにある次のイベントを出力します。

 カウンター名 | 説明 |
| --- | --- |
| トランザクションの開始操作数/秒 | 1 秒あたりに作成された新しい書き込みトランザクションの数。|
| トランザクション操作数/秒 | 1 秒あたりにリライアブル コレクションで実行される追加/更新/削除操作の数。|
| ログのフラッシュ バイト数/秒 | 1 秒あたりにトランザクション レプリケーターによってディスクにフラッシュされるバイト数。 |
| 調整された操作数/秒 | 調整のため、トランザクション レプリケーターによって 1 秒あたりに拒否される操作の数。 |
| Avg.コミットあたりのトランザクション ミリ秒 | トランザクションあたりのミリ秒単位での平均コミット遅延時間 |
| Avg.フラッシュ待機時間 (ミリ秒) | トランザクション レプリケーターによって開始されたディスク フラッシュ操作のミリ秒単位での平均時間 |

### <a name="tstore-performance-counters"></a>TStore のパフォーマンス カウンター

Reliable Services ランタイムでは、`Service Fabric TStore` カテゴリにある次のイベントを出力します。

 カウンター名 | 説明 |
| --- | --- |
| 項目数 | ストアに含まれる項目数。|
| ディスク サイズ | ストアのチェックポイント ファイルの合計ディスク サイズ (バイト)。|
| チェックポイント ファイル書き込みバイト数/秒 | 最新のチェックポイント ファイルで 1 秒あたりに書き込まれたバイト数。|
| コピー ディスク転送バイト数/秒 | ストア コピー中に (プライマリ レプリカ上で) 読み取られたディスク バイト数/秒と (セカンダリ レプリカ上で) 書き込まれたディスク バイト数/秒。|

## <a name="next-steps"></a>次のステップ
[PerfView での EventSource プロバイダー](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
