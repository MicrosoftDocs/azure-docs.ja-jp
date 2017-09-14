---
title: "Service Fabric クラスターでの混乱の誘発 |Microsoft ドキュメント"
description: "フォールト挿入とクラスター分析サービス API を使用して、クラスター内の混乱を管理します。"
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: motanv
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 3b3b93bc9ec5ecdcfc289e5b62e84de6aa4172ed
ms.contentlocale: ja-jp
ms.lasthandoff: 08/12/2017

---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Service Fabric クラスターでの制御された混乱の誘発
クラウド インフラストラクチャのような大規模な分散システムは、本質的に信頼性の低いものです。 Azure Service Fabric を使用すると、開発者が、信頼性の低いインフラストラクチャ上で信頼できる分散サービスのコードを記述できます。 信頼性の低いインフラストラクチャ上に強固な分散サービスを作成するために、開発者は、基になる信頼性の低いインフラストラクチャで障害のために複雑な状態遷移が発生している状態で、サービスの安定性をテストできる必要があります。

[フォールト挿入とクラスター分析サービス](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-testability-overview) (別名 Fault Analysis Service) によって、開発者が、障害アクションを誘発してサービスをテストできます。 [パーティションの再起動](https://docs.microsoft.com/en-us/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps)などのこれらのシミュレーション対象のエラーは、最も一般的な状態遷移の練習に役立ちます。 ただし、シミュレートされた対象のエラーは、定義でバイアスがかけられ、そのため予測が難しい、長くて複雑な状態のシーケンスでのみ発生し、バグに記録されない場合があります。 バイアスをかけないテストのために、混乱を使用することができます。

混乱により、長時間にわたり、クラスター全体で、交互に配置された (グレースフルと非グレースフル) 定期的な障害がシミュレートされます。 混乱の障害の発生率と種類を構成してから、C# または PowerShell API を使用して混乱を開始し、クラスターとサービスに障害を生成できます。 指定した時間 (例えば 1 時間) だけ実行されるように混乱を構成し、その後で自動的に混乱を停止するか、いつでも StopChaos API (C# または Powershell) を呼び出して混乱を停止することができます。

> [!NOTE]
> 最新の形式では、混乱は、安全な障害のみを発生させ、外部障害がなければ、クォーラム損失またはデータの損失は起こりません。
>

混乱の実行中は、その時点での実行状態をキャプチャするさまざまなイベントが生成されます。 たとえば、ExecutingFaultsEvent には、混乱がその反復で実行することを決定したすべての障害が含まれています。 ValidationFailedEvent には、クラスターの検証中に検出された検証エラー (正常性または安定性の問題) の詳細が含まれています。 GetChaosReportAsync API (C# または Powershell) を呼び出すと、混乱実行のレポートを取得することができます。 これらのイベントは、[信頼性の高いディクショナリ](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-reliable-collections)で永続化され、**MaxStoredChaosEventCount** (既定値は 25000) と**StoredActionCleanupIntervalInSeconds** (既定値は 3600) という 2 つの構成で既定される切り捨てポリシーが適用されます。 すべての *StoredActionCleanupIntervalInSeconds* 混乱チェックおよびほとんどのすべての最新の *MaxStoredChaosEventCount* イベントが、信頼性の高いディクショナリから削除されます。

## <a name="faults-induced-in-chaos"></a>混乱で誘発される障害
混乱により、Service Fabric クラスター全体で数か月または数年の間に発生する障害が、数時間に圧縮され生成されます。 障害率の高い交互に配置された障害の組み合わせにより、通常は見過ごされるめったに発生しないケースが検出されます。 この混乱を実施することで、サービスのコードの品質が大幅に向上します。

混乱によって誘発される障害のカテゴリは次のとおりです。

* ノードの再起動
* デプロイされたコード パッケージの再起動
* レプリカの削除
* レプリカの再起動
* プライマリ レプリカの移動 (構成可能)
* セカンダリ レプリカの移動 (構成可能)

混乱は複数回にわたり反復して実行されます。 それぞれの反復が、一定の期間にわたる障害とクラスター検証で構成されています。 クラスターが安定し、検証が成功するまでの時間を設定できます。 クラスター検証で 1 つの障害が見つかると、混乱により、ValidationFailedEvent が UTC タイムスタンプと障害の詳細と共に生成され、保持されます。 たとえば、1 時間実行して、最大 3 つの障害が同時に発生するように設定された混乱のインスタンスを考えてみます。 混乱では 3 つの障害が誘発されてから、クラスターの正常性が検証されます。 混乱は StopChaosAsync API によって明示的に停止されるまで、または 1 時間が経過するまで、前述の手順で反復処理されます。 繰り返しているうちに、クラスターの状態が異常 (つまり、渡された MaxClusterStabilizationTimeout 時間内に安定した状態にならない) になると、混乱は ValidationFailedEvent を生成します。 このイベントは、問題が発生しており詳細な調査が必要であることを示します。

混乱が発生させたエラーを取得するには、GetChaosReport API (Powershell または C# ) を使用できます。 この API は、渡された継続トークン、または渡された時間範囲に基づく混乱レポートの次のセグメントを取得します。 混乱レポートの次のセグメントを取得するように ContinuationToken を指定するか、StartTimeUtc と EndTimeUtc を使用して時間範囲を指定することができますが、同じ呼び出しで、ContinuationToken と時間範囲の両方を指定することはできません。 100 を超える混乱イベントが存在する場合、混乱レポートがセグメントで返され、セグメントに 100 を超える混乱イベントが含まれています。

## <a name="important-configuration-options"></a>重要な構成オプション
* **TimeToRun**: 混乱が正常に完了するまでの実行時間の合計。 実行中の混乱は、TimeToRun で指定された期間が経過する前に、StopChaos API で停止できます。

* **MaxClusterStabilizationTimeout**: ValidationFailedEvent を生成する前に、クラスターが正常になるまで待機する最大時間。 この待機は、回復中のクラスターの負荷を軽減するためのものです。 次のチェックが実行されます。
  * クラスターの正常性に問題がないかどうか
  * サービスの正常性に問題がないかどうか
  * サービス パーティションの対象となるレプリカ セットのサイズに達しているかどうか
  * InBuild レプリカが存在しないかどうか
* **MaxConcurrentFaults**: それぞれの反復で誘発される同時実行のエラーの最大数。 数が多いほど、混乱が激しくなり、クラスターで経験するフェイルオーバーと状態遷移の組み合わせが複雑になります。 

> [!NOTE]
> *MaxConcurrentFaults* の値がどれだけ大きいかに関係なく、混乱では、外部障害が存在しない場合、クォーラム損失またはデータ損失は発生しません。
>

* **EnableMoveReplicaFaults**: プライマリ レプリカまたはセカンダリ レプリカの移動を発生させる障害を有効または無効にします。 このようなエラーは、既定で無効になっています。
* **WaitTimeBetweenIterations**: 反復の間の待機時間。 つまり、一巡の障害を実行し、クラスターの正常性の対応する検証を完了した後に混乱が一時停止する時間の長さ。 値が大きいほど、平均障害挿入率は低くなります。
* **WaitTimeBetweenFaults**: 1 つの反復における 2 つの連続する障害間の待機時間。 値が大きいほど、障害の同時実行 (または重複) が少なくなります。
* **ClusterHealthPolicy**: クラスターの正常性ポリシーは、混乱の反復の間にクラスターの正常性の検証に使用されます。 クラスターの正常性に問題がある場合または障害の実行中に予期しない例外が発生した場合は、混乱は、クラスターが回復する時間を提供するために、次の正常性チェックの前に 30 分間待機します。
* **コンテキスト**: (文字列, 文字列) 型のキーと値のペアのコレクション。 マップを使用して、混乱の実行に関する情報を記録できます。 このようなペアが 100 を超えることはできませんし、各文字列 (キーまたは値) の最大は 4095 文字です。 このマップは、オプションで特定の実行に関するコンテキストを格納するために、混乱実行を開始するユーザーによって設定されます。

## <a name="how-to-run-chaos"></a>混乱を実行する方法

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```

