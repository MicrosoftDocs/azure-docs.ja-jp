---
title: Azure Service Fabric の診断と監視
description: この記事では、Service Fabric Reliable ServiceRemoting ランタイムから出力されるパフォーマンス カウンターなどのパフォーマンス監視機能について説明します。
author: suchiagicha
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: pepogors
ms.openlocfilehash: 9c7d466d6e8fd36b4445966b92ee753becf96c64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791763"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Reliable Service Remoting の診断とパフォーマンス監視
Reliable ServiceRemoting ランタイムからは[パフォーマンス カウンター](/dotnet/api/system.diagnostics.performancecounter)が出力されます。 パフォーマンス カウンターは ServiceRemoting の動作状況を示し、トラブルシューティングとパフォーマンス監視に役立ちます。


## <a name="performance-counters"></a>パフォーマンス カウンター
Reliable ServiceRemoting ランタイムは、次のパフォーマンス カウンター カテゴリを定義します。

| カテゴリ | 説明 |
| --- | --- |
| Service Fabric サービス |Azure Service Fabric Service Remoting に固有のカウンター (要求の処理にかかる平均時間など) |
| Service Fabric サービス メソッド |Service Fabric Remoting Service に実装されるメソッドに固有のカウンター (サービス メソッドが呼び出される頻度など) |

上記の各カテゴリには、1 つまたは複数のカウンターがあります。

パフォーマンス カウンター データの収集と表示には、Windows オペレーティング システムで既定で使用できる [Windows パフォーマンス モニター](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) アプリケーションを使用できます。 [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) があります。

### <a name="performance-counter-instance-names"></a>パフォーマンス カウンター インスタンス名
多数の Service Remoting サービスまたはパーティションを持つクラスターには、多数のパフォーマンス カウンター インスタンスがあります。 パフォーマンス カウンター インスタンス名は、パフォーマンス カウンター インスタンスが関連付けられている特定のパーティションとサービス メソッド (該当する場合) を識別するのに役立ちます。

#### <a name="service-fabric-service-category"></a>Service Fabric サービス カテゴリ
カテゴリ `Service Fabric Service`では、カウンター インスタンス名の形式は次のようになります。

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* は、パフォーマンス カウンター インスタンスが関連付けられている Service Fabric パーティション ID の文字列表現です。 パーティション ID は GUID で、その文字列表現は、書式指定子 "D" を持つ [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) メソッドを使用して生成されます。

*ServiceReplicaOrInstanceId* は、パフォーマンス カウンター インスタンスが関連付けられている Service Fabric レプリカ/インスタンス ID の文字列表現です。

*ServiceRuntimeInternalID* は、内部使用のために Fabric サービス ランタイムによって生成される 64 ビット整数の文字列表現です。 これは、その一意性を確保し、他のパフォーマンス カウンター インスタンス名と競合しないようにするために、パフォーマンス カウンター インスタンス名に含まれます。 パフォーマンス カウンター インスタンス名のこの部分を解釈しようとしないでください。

`Service Fabric Service` カテゴリに属するカウンターのカウンター インスタンス名の例を次に示します。

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

上記の例で、`2740af29-78aa-44bc-a20b-7e60fb783264` は Service Fabric パーティション ID の文字列表現、`635650083799324046` はレプリカ/インスタンス ID の文字列表現、`5008379932` はランタイムの内部使用のために生成される 64 ビットの ID です。

#### <a name="service-fabric-service-method-category"></a>Service Fabric サービス メソッド カテゴリ
カテゴリ `Service Fabric Service Method`では、カウンター インスタンス名の形式は次のようになります。

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* は、パフォーマンス カウンター インスタンスが関連付けられているサービス メソッドの名前です。 メソッド名の形式は、名前の読みやすさと、Windows におけるパフォーマンス カウンター インスタンス名の最大長の制約とのバランスを取る、Fabric サービス ランタイム内の何らかのロジックに基づいて決定されます。

*ServiceRuntimeMethodId* は、内部使用のために Fabric サービス ランタイムによって生成される 32 ビット整数の文字列表現です。 これは、その一意性を確保し、他のパフォーマンス カウンター インスタンス名と競合しないようにするために、パフォーマンス カウンター インスタンス名に含まれます。 パフォーマンス カウンター インスタンス名のこの部分を解釈しようとしないでください。

*ServiceFabricPartitionID* は、パフォーマンス カウンター インスタンスが関連付けられている Service Fabric パーティション ID の文字列表現です。 パーティション ID は GUID で、その文字列表現は、書式指定子 "D" を持つ [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) メソッドを使用して生成されます。

*ServiceReplicaOrInstanceId* は、パフォーマンス カウンター インスタンスが関連付けられている Service Fabric レプリカ/インスタンス ID の文字列表現です。

*ServiceRuntimeInternalID* は、内部使用のために Fabric サービス ランタイムによって生成される 64 ビット整数の文字列表現です。 これは、その一意性を確保し、他のパフォーマンス カウンター インスタンス名と競合しないようにするために、パフォーマンス カウンター インスタンス名に含まれます。 パフォーマンス カウンター インスタンス名のこの部分を解釈しようとしないでください。

`Service Fabric Service Method` カテゴリに属するカウンターのカウンター インスタンス名の例を次に示します。

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

上記の例で、`ivoicemailboxservice.leavemessageasync` はメソッド名、`2` はランタイムの内部使用のために生成される 32 ビットの ID、`89383d32-e57e-4a9b-a6ad-57c6792aa521` は Service Fabric パーティション ID の文字列表現、`635650083804480486` は Service Fabric レプリカ/インスタンス ID の文字列表現、`5008380` はランタイムの内部使用のために生成される 64 ビットの ID です。

## <a name="list-of-performance-counters"></a>パフォーマンス カウンター一覧
### <a name="service-method-performance-counters"></a>サービス メソッドのパフォーマンス カウンター

Reliable Service ランタイムは、サービス メソッドの実行に関連する次のパフォーマンス カウンターを発行します。

| カテゴリ名 | カウンター名 | 説明 |
| --- | --- | --- |
| Service Fabric サービス メソッド |呼び出し/秒 |1 秒あたりにサービス メソッドが呼び出される回数 |
| Service Fabric サービス メソッド |呼び出しあたりの平均時間 (ミリ秒) |サービス メソッドの実行にかかった時間 (ミリ秒単位) |
| Service Fabric サービス メソッド |スローされた例外の数/秒 |1 秒あたりにサービス メソッドが例外をスローした回数 |

### <a name="service-request-processing-performance-counters"></a>サービス要求処理のパフォーマンス カウンター
クライアントがサービス プロキシ オブジェクト経由でメソッドを呼び出すと、要求メッセージがネットワーク経由でリモート処理サービスに送信されます。 サービスは要求メッセージを処理し、クライアントに応答を送り返します。 Reliable ServiceRemoting ランタイムは、サービス要求処理に関連する次のパフォーマンス カウンターを発行します。

| カテゴリ名 | カウンター名 | 説明 |
| --- | --- | --- |
| Service Fabric サービス |未処理の要求の数 |サービスで処理中の要求の数 |
| Service Fabric サービス |要求あたりの平均時間 (ミリ秒) |サービスで要求の処理にかかった時間 (ミリ秒単位) |
| Service Fabric サービス |要求の逆シリアル化の平均時間 (ミリ秒) |サービスで受信されたときに、サービス要求メッセージの逆シリアル化にかかった時間 (ミリ秒単位) |
| Service Fabric サービス |応答のシリアル化の平均時間 (ミリ秒) |クライアントに応答を送信する前に、サービスでサービス応答メッセージをシリアル化するのにかかった時間 (ミリ秒単位) |

## <a name="next-steps"></a>次のステップ
* [サンプル コード](https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0)
* [PerfView での EventSource プロバイダー](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
