---
title: "ステートフル Reliable Services の診断 | Microsoft Docs"
description: "ステートフル Reliable Services の診断機能"
services: service-fabric
documentationcenter: .net
author: AlanWarwick
manager: timlt
editor: 
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2017
ms.author: alanwar
translationtype: Human Translation
ms.sourcegitcommit: 3ed67788fbbe0c4fb820cfd1525d8c9ee5154446
ms.openlocfilehash: 5da5707900b35be69018ba4e9c8efb05d8a74a9d


---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>ステートフル Reliable Services の診断機能
ステートフル Reliable Services の StatefulServiceBase クラスは、サービスのデバッグに使用することができる [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) イベントを出力するため、ランタイムの動作状況を理解し、トラブルシューティングに役立ちます。

## <a name="eventsource-events"></a>EventSource イベント
ステートフル Reliable Services の StatefulServiceBase クラスの EventSource 名は、"Microsoft-ServiceFabric-Services" です。 このイベント ソースからのイベントは、サービスを [Visual Studio でデバッグしている](service-fabric-debugging-your-application.md)ときに、[[診断イベント](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio)] ウィンドウに表示されます。

EventSource イベントの収集や表示に役立つツールとテクノロジの例には、[PerfView](http://www.microsoft.com/download/details.aspx?id=28567)、[Microsoft Azure 診断](../cloud-services/cloud-services-dotnet-diagnostics.md)、および [Microsoft TraceEvent ライブラリ](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)があります。

## <a name="events"></a>イベント
| イベント名 | イベント ID | Level | イベントの説明 |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |情報 |サービスの RunAsync タスクが開始されたるときに出力されます |
| StatefulRunAsyncCancellation |2 |情報 |サービスの RunAsync タスクが取り消されたときに出力されます |
| StatefulRunAsyncCompletion |3 |情報 |サービスの RunAsync タスクが完了したときに出力されます |
| StatefulRunAsyncSlowCancellation |4 |警告 |サービスの RunAsync タスクが取り消しの実行に時間がかかりすぎたときに出力されます |
| StatefulRunAsyncFailure |5 |エラー |サービスの RunAsync タスクが例外をスローしたときに出力されます |

## <a name="interpret-events"></a>イベントの解釈
StatefulRunAsyncInvocation、StatefulRunAsyncCompletion、および StatefulRunAsyncCancellation イベントは、サービスの作成者が、サービスのライフ サイクルに加えて、サービスが開始されたとき、取り消されたとき、完了したときのタイミングを知るために役立ちます。 これはサービスの問題をデバッグする場合やサービスのライフ サイクルを理解する場合に役立つことがあります。

StatefulRunAsyncSlowCancellation および StatefulRunAsyncFailure イベントはサービスの問題を示しているため、サービスの作成者はこれらのイベントに細心の注意を払う必要があります。

StatefulRunAsyncFailure は、サービスの RunAsync() タスクが例外をスローするたびに出力されます。 一般に、スローされる例外は、サービスのエラーやバグを示します。 さらに、この例外はサービスが失敗する原因となるため、別のノードに移動されます。 この動作には高い負荷がかかる可能性があるため、サービスの移動中には要求の受信が遅れることがあります。 サービスの作成者は例外の原因を特定し、可能であればそれを軽減する必要があります。

StatefulRunAsyncSlowCancellation は、RunAsync タスクの取り消し要求が&4; 秒より長くかかる場合に出力されます。 取り消しの完了に時間がかかると、サービスが別のノードで速やかに再開されなくなります。 これはサービスの全体の可用性に影響を与える可能性があります。

## <a name="next-steps"></a>次のステップ
* [PerfView での EventSource プロバイダー](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)



<!--HONumber=Jan17_HO1-->


