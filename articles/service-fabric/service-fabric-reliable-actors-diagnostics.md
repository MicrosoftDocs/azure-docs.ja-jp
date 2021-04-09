---
title: アクターによる診断と監視
description: この記事では、出力されるイベントおよびパフォーマンス カウンターを含め、Service Fabric の Reliable Actors ランタイムの診断およびパフォーマンス監視機能について説明します。
author: abhishekram
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: ebaedb5369f3b39372262bfde526706e8d069418
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789618"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Reliable Actors の診断とパフォーマンス監視
Reliable Actors ランタイムは、[EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) イベントおよび[パフォーマンス カウンター](/dotnet/api/system.diagnostics.performancecounter)を出力します。 これらは、ランタイムがどのように動作するかを示し、トラブルシューティングとパフォーマンス監視に役立ちます。

## <a name="eventsource-events"></a>EventSource イベント
Reliable Actors ランタイムの EventSource プロバイダー名は "Microsoft-ServiceFabric-Actors" です。 このイベント ソースのイベントは、アクター アプリケーションが [Visual Studio でデバッグ](service-fabric-debugging-your-application.md)されているときに、[[診断イベント](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio)] ウィンドウに表示されます。

EventSource イベントの収集または表示に役立つツールとテクノロジの例には、[PerfView](https://www.microsoft.com/download/details.aspx?id=28567)、[Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md)、[セマンティック ログ記録](/previous-versions/msp-n-p/dn774980(v=pandp.10))、[Microsoft TraceEvent ライブラリ](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)があります。

### <a name="keywords"></a>キーワード
Reliable Actors EventSource に属しているすべてのイベントは、1 つまたは複数のキーワードに関連付けられます。 これにより、収集されたイベントをフィルター処理できます。 次のキーワードのビットが定義されています。

| ビット | 説明 |
| --- | --- |
| 0x1 |Fabric アクター ランタイムの操作を要約する重要なイベントのセット。 |
| 0x2 |アクター メソッドの呼び出しを記述するイベントのセット。 詳細については、[アクターに関する入門のトピック](service-fabric-reliable-actors-introduction.md)を参照してください。 |
| 0x4 |アクター状態に関連するイベントのセット。 詳細については、 [アクターの状態管理](service-fabric-reliable-actors-state-management.md)に関するページをご覧ください。 |
| 0x8 |アクターでのターンベースのコンカレンシーに関連するイベントのセット。 詳細については、 [コンカレンシー](service-fabric-reliable-actors-introduction.md#concurrency)に関するトピックを参照してください。 |

## <a name="performance-counters"></a>パフォーマンス カウンター
Reliable Actors ランタイムは、次のパフォーマンス カウンター カテゴリを定義します。

| カテゴリ | 説明 |
| --- | --- |
| Service Fabric アクター |アクター状態の保存にかかる時間など、Azure Service Fabric アクターに固有のカウンター。 |
| Service Fabric アクター メソッド |アクター メソッドが呼び出される頻度など、Service Fabric アクターによって実装されたメソッドに固有のカウンター。 |

上記の各カテゴリには、1 つまたは複数のカウンターがあります。

パフォーマンス カウンター データの収集と表示には、Windows オペレーティング システムで既定で使用できる [Windows パフォーマンス モニター](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) アプリケーションを使用できます。 [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) があります。

### <a name="performance-counter-instance-names"></a>パフォーマンス カウンター インスタンス名
多数のアクター サービスまたはアクター サービス パーティションを持つクラスターには、多数のアクター パフォーマンス カウンター インスタンスが含まれます。 パフォーマンス カウンター インスタンス名は、パフォーマンス カウンター インスタンスが関連付けられている特定の [パーティション](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) とアクター メソッド (該当する場合) を識別するのに役立ちます。

#### <a name="service-fabric-actor-category"></a>Service Fabric アクター カテゴリ
カテゴリ `Service Fabric Actor`では、カウンター インスタンス名の形式は次のようになります。

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* は、パフォーマンス カウンター インスタンスが関連付けられている Service Fabric パーティション ID の文字列表現です。 パーティション ID は GUID で、その文字列表現は、書式指定子 "D" を持つ [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) メソッドを使用して生成されます。

*ActorRuntimeInternalID* は、内部使用のために Fabric アクター ランタイムによって生成される 64 ビット整数の文字列表現です。 これは、その一意性を確保し、他のパフォーマンス カウンター インスタンス名と競合しないようにするために、パフォーマンス カウンター インスタンス名に含まれます。 パフォーマンス カウンター インスタンス名のこの部分を解釈しようとしないでください。

`Service Fabric Actor` カテゴリに属するカウンターのカウンター インスタンス名の例を次に示します。

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

上記の例で、`2740af29-78aa-44bc-a20b-7e60fb783264` は Service Fabric パーティション ID の文字列表現、`635650083799324046` はランタイムの内部使用のために生成される 64 ビットの ID です。

#### <a name="service-fabric-actor-method-category"></a>Service Fabric アクター メソッド カテゴリ
カテゴリ `Service Fabric Actor Method`では、カウンター インスタンス名の形式は次のようになります。

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* は、パフォーマンス カウンター インスタンスが関連付けられているアクター メソッドの名前です。 メソッド名の形式は、名前の読みやすさと、Windows におけるパフォーマンス カウンター インスタンス名の最大長の制約とのバランスを取る、Fabric アクター ランタイム内の何らかのロジックに基づいて決定されます。

*ActorsRuntimeMethodId* は、内部使用のために Fabric アクター ランタイムによって生成される 32 ビット整数の文字列表現です。 これは、その一意性を確保し、他のパフォーマンス カウンター インスタンス名と競合しないようにするために、パフォーマンス カウンター インスタンス名に含まれます。 パフォーマンス カウンター インスタンス名のこの部分を解釈しようとしないでください。

*ServiceFabricPartitionID* は、パフォーマンス カウンター インスタンスが関連付けられている Service Fabric パーティション ID の文字列表現です。 パーティション ID は GUID で、その文字列表現は、書式指定子 "D" を持つ [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) メソッドを使用して生成されます。

*ActorRuntimeInternalID* は、内部使用のために Fabric アクター ランタイムによって生成される 64 ビット整数の文字列表現です。 これは、その一意性を確保し、他のパフォーマンス カウンター インスタンス名と競合しないようにするために、パフォーマンス カウンター インスタンス名に含まれます。 パフォーマンス カウンター インスタンス名のこの部分を解釈しようとしないでください。

`Service Fabric Actor Method` カテゴリに属するカウンターのカウンター インスタンス名の例を次に示します。

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

上記の例で、`ivoicemailboxactor.leavemessageasync` はメソッド名、`2` はランタイムの内部使用のために生成される 32 ビットの ID、`89383d32-e57e-4a9b-a6ad-57c6792aa521` は Service Fabric パーティション ID の文字列表現、`635650083804480486` はランタイムの内部使用のために生成される 64 ビットの ID です。

## <a name="list-of-events-and-performance-counters"></a>イベントとパフォーマンス カウンターの一覧
### <a name="actor-method-events-and-performance-counters"></a>アクター メソッド イベントとパフォーマンス カウンター
Reliable Actors ランタイムは、[アクター メソッド](service-fabric-reliable-actors-introduction.md)に関連する次のイベントを出力します。

| イベント名 | イベント ID | Level | キーワード | 説明 |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |"詳細" |0x2 |アクター ランタイムがアクター メソッドを呼び出そうとしています。 |
| ActorMethodStop |8 |"詳細" |0x2 |アクター メソッドが実行を完了しました。 つまり、アクター メソッドに対するランタイムの非同期呼び出しが返され、アクター メソッドによって返されたタスクが完了しました。 |
| ActorMethodThrewException |9 |警告 |0x3 |アクター メソッドに対するランタイムの非同期呼び出し中、またはアクター メソッドによって返されたタスクの実行中に、アクター メソッドの実行中の例外がスローされました。 このイベントは、調査を必要とするアクター コードの何らかのエラーを示します。 |

Reliable Actors ランタイムは、アクター メソッドの実行に関連する次のパフォーマンス カウンターを発行します。

| カテゴリ名 | カウンター名 | 説明 |
| --- | --- | --- |
| Service Fabric アクター メソッド |呼び出し/秒 |1 秒あたりにアクター サービス メソッドが呼び出される回数 |
| Service Fabric アクター メソッド |呼び出しあたりの平均時間 (ミリ秒) |アクター サービス メソッドの実行にかかった時間 (ミリ秒単位) |
| Service Fabric アクター メソッド |スローされた例外の数/秒 |1 秒あたりにアクター サービス メソッドが例外をスローした回数 |

### <a name="concurrency-events-and-performance-counters"></a>コンカレンシー イベントとパフォーマンス カウンター
Reliable Actors ランタイムは、 [コンカレンシー](service-fabric-reliable-actors-introduction.md#concurrency)に関連する次のイベントを出力します。

| イベント名 | イベント ID | Level | キーワード | 説明 |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |"詳細" |0x8 |このイベントは、アクター内の新しい各ターンの開始時に記述されます。 ターンベースのコンカレンシーを強制するアクターごとのロックを取得するために待機している、保留中のアクター呼び出しの数が含まれています。 |

Reliable Actors ランタイムは、コンカレンシーに関連する次のパフォーマンス カウンターを発行します。

| カテゴリ名 | カウンター名 | 説明 |
| --- | --- | --- |
| Service Fabric アクター |アクター ロックを待機しているアクター呼び出し数 |ターンベースのコンカレンシーを強制するアクターごとのロックを取得するために待機している、保留中のアクター呼び出しの数。 |
| Service Fabric アクター |ロック待機あたりの平均時間 (ミリ秒) |ターンベースのコンカレンシーを強制するアクターごとのロックの取得にかかった時間 (ミリ秒単位) |
| Service Fabric アクター |アクター ロック保持の平均時間 (ミリ秒) |アクターごとのロックが保持される時間 (ミリ秒単位) |

### <a name="actor-state-management-events-and-performance-counters"></a>アクター状態管理イベントとパフォーマンス カウンター
Reliable Actors ランタイムは、 [アクター状態管理](service-fabric-reliable-actors-state-management.md)に関連する次のイベントを出力します。

| イベント名 | イベント ID | Level | キーワード | 説明 |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |"詳細" |0x4 |アクター ランタイムが、アクター状態を保存しようとしています。 |
| ActorSaveStateStop |11 |"詳細" |0x4 |アクター ランタイムが、アクター状態の保存を完了しました。 |

Reliable Actors ランタイムは、アクター状態管理に関連する次のパフォーマンス カウンターを発行します。

| カテゴリ名 | カウンター名 | 説明 |
| --- | --- | --- |
| Service Fabric アクター |状態保存操作あたりの平均時間 (ミリ秒) |アクター状態の保存にかかった時間 (ミリ秒単位) |
| Service Fabric アクター |状態読み込み操作あたりの平均時間 (ミリ秒) |アクター状態の読み込みにかかった時間 (ミリ秒単位) |

### <a name="events-related-to-actor-replicas"></a>アクター レプリカに関連するイベント
Reliable Actors ランタイムは、[アクター レプリカ](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)に関連する次のイベントを出力します。

| イベント名 | イベント ID | Level | キーワード | 説明 |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Informational |0x1 |アクター レプリカのロールがプライマリに変わりました。 これは、このパーティションのアクターが、このレプリカ内で作成されることを意味します。 |
| ReplicaChangeRoleFromPrimary |2 |Informational |0x1 |アクター レプリカのロールがプライマリ以外に変わりました。 これは、このパーティションのアクターが、このレプリカ内で作成されなくなったことを意味します。 このレプリカ内で既に作成されているアクターに、新しい要求は配信されません。 アクターは、処理中のすべての要求が完了したら破棄されます。 |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>アクターのアクティブ化イベントと非アクティブ化イベントおよびパフォーマンス カウンター
Reliable Actors ランタイムは、 [アクターのアクティブ化と非アクティブ化](service-fabric-reliable-actors-lifecycle.md)に関連する次のイベントを出力します。

| イベント名 | イベント ID | Level | キーワード | 説明 |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Informational |0x1 |アクターがアクティブ化されました。 |
| ActorDeactivated |6 |Informational |0x1 |アクターが非アクティブ化されました。 |

Reliable Actors ランタイムは、アクターのアクティブ化と非アクティブ化に関連する次のパフォーマンス カウンターを発行します。

| カテゴリ名 | カウンター名 | 説明 |
| --- | --- | --- |
| Service Fabric アクター |OnActivateAsync の平均時間 (ミリ秒) |OnActivateAsync メソッドの実行にかかった時間 (ミリ秒単位) |

### <a name="actor-request-processing-performance-counters"></a>アクター要求処理のパフォーマンス カウンター
クライアントがアクター プロキシ オブジェクト経由でメソッドを呼び出すと、要求メッセージがネットワーク経由でアクター サービスに送信されます。 サービスは要求メッセージを処理し、クライアントに応答を送り返します。 Reliable Actors ランタイムは、アクター要求処理に関連する次のパフォーマンス カウンターを発行します。

| カテゴリ名 | カウンター名 | 説明 |
| --- | --- | --- |
| Service Fabric アクター |未処理の要求の数 |サービスで処理中の要求の数 |
| Service Fabric アクター |要求あたりの平均時間 (ミリ秒) |サービスで要求の処理にかかった時間 (ミリ秒単位) |
| Service Fabric アクター |要求の逆シリアル化の平均時間 (ミリ秒) |サービスで受信されたときに、アクターの要求メッセージの逆シリアル化にかかった時間 (ミリ秒単位) |
| Service Fabric アクター |応答のシリアル化の平均時間 (ミリ秒) |クライアントに応答を送信する前に、サービスでアクターの応答メッセージをシリアル化するのにかかった時間 (ミリ秒単位) |

## <a name="next-steps"></a>次のステップ
* [Reliable Actors の Service Fabric プラットフォームの使用方法](service-fabric-reliable-actors-platform.md)
* [Actor API リファレンス ドキュメント](/previous-versions/azure/dn971626(v=azure.100))
* [サンプル コード](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [PerfView での EventSource プロバイダー](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
