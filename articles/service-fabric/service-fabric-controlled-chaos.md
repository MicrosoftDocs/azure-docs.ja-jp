---
title: Service Fabric クラスターでの混乱の誘発
description: フォールト挿入とクラスター分析サービス API を使用して、クラスター内の混乱を管理します。
ms.topic: conceptual
ms.date: 02/05/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 72b8f7e9e4934b516f843ae8bc9bb7adc1c349ec
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720512"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Service Fabric クラスターでの制御された混乱の誘発
クラウド インフラストラクチャのような大規模な分散システムは、本質的に信頼性の低いものです。 Azure Service Fabric を使用すると、開発者が、信頼性の低いインフラストラクチャ上で信頼できる分散サービスのコードを記述できます。 信頼性の低いインフラストラクチャ上に強固な分散サービスを作成するために、開発者は、基になる信頼性の低いインフラストラクチャで障害のために複雑な状態遷移が発生している状態で、サービスの安定性をテストできる必要があります。

[フォールト挿入とクラスター分析サービス](./service-fabric-testability-overview.md) (別名 Fault Analysis Service) によって、開発者が、障害アクションを誘発してサービスをテストできます。 [パーティションの再起動](/powershell/module/servicefabric/start-servicefabricpartitionrestart)などのこれらのシミュレーション対象のエラーは、最も一般的な状態遷移の練習に役立ちます。 ただし、シミュレートされた対象のエラーは、定義でバイアスがかけられ、そのため予測が難しい、長くて複雑な状態のシーケンスでのみ発生し、バグに記録されない場合があります。 バイアスをかけないテストのために、混乱を使用することができます。

混乱により、長時間にわたり、クラスター全体で、交互に配置された (グレースフルと非グレースフル) 定期的な障害がシミュレートされます。 グレースフル障害は、一連の Service Fabric API の呼び出しで構成されます。たとえば、レプリカの再起動障害は、レプリカを閉じる操作に続いて開く操作が発生するため、グレースフル障害です。 レプリカの削除、プライマリ レプリカの移動、およびセカンダリ レプリカの移動は、混乱によって実行されるその他のグレースフル障害です。 非グレースフル障害はプロセスの終了であり、ノードの再起動やコード パッケージの再起動などがあります。 

障害の発生率と種類を指定して混乱を構成したら、C#、Powershell、または REST API を使用して混乱を開始し、クラスターとサービスでの障害の生成を開始できます。 指定した時間 (たとえば 1 時間) だけ実行されるように混乱を構成し、その後で自動的に混乱を停止できます。または、いつでも StopChaos API (C#、Powershell、または REST) を呼び出して混乱を停止できます。

> [!NOTE]
> 最新の形式では、混乱は、安全な障害のみを発生させ、外部障害がなければ、クォーラム損失またはデータの損失は起こりません。
>

混乱の実行中は、その時点での実行状態をキャプチャするさまざまなイベントが生成されます。 たとえば、ExecutingFaultsEvent には、混乱がその反復で実行することを決定したすべての障害が含まれています。 ValidationFailedEvent には、クラスターの検証中に検出された検証エラー (正常性または安定性の問題) の詳細が含まれています。 GetChaosReport API (C#、Powershell、または REST) を呼び出して、混乱実行のレポートを取得できます。 これらのイベントは、[リライアブル ディクショナリ](./service-fabric-reliable-services-reliable-collections.md)内に永続化されます。それには、次の 2 つの構成によって決定される切り捨てポリシーがあります。**MaxStoredChaosEventCount** (既定値は 25,000) と **StoredActionCleanupIntervalInSeconds** (既定値は 3,600)。 すべての *StoredActionCleanupIntervalInSeconds* 混乱チェックおよびほとんどのすべての最新の *MaxStoredChaosEventCount* イベントが、信頼性の高いディクショナリから削除されます。

## <a name="faults-induced-in-chaos"></a>混乱で誘発される障害
混乱により、Service Fabric クラスター全体で数か月または数年の間に発生する障害が、数時間に圧縮され生成されます。 障害率の高い交互に配置された障害の組み合わせにより、通常は見過ごされるめったに発生しないケースが検出されます。 この混乱を実施することで、サービスのコードの品質が大幅に向上します。

混乱によって誘発される障害のカテゴリは次のとおりです。

* ノードの再起動
* デプロイされたコード パッケージの再起動
* レプリカの削除
* レプリカの再起動
* プライマリ レプリカの移動 (構成可能)
* セカンダリ レプリカの移動 (構成可能)

混乱は複数回にわたり反復して実行されます。 それぞれの反復が、一定の期間にわたる障害とクラスター検証で構成されています。 クラスターが安定し、検証が成功するまでの時間を設定できます。 クラスター検証で 1 つの障害が見つかると、混乱により、ValidationFailedEvent が UTC タイムスタンプと障害の詳細と共に生成され、保持されます。 たとえば、1 時間実行して、最大 3 つの障害が同時に発生するように設定された混乱のインスタンスを考えてみます。 混乱では 3 つの障害が誘発されてから、クラスターの正常性が検証されます。 混乱は StopChaosAsync API によって明示的に停止されるまで、または 1 時間が経過するまで、前述の手順で反復処理されます。 反復処理中にクラスターの状態が異常になる (安定しなくなるか、渡された MaxClusterStabilizationTimeout 時間内に正常な状態にならなくなる) と、混乱は ValidationFailedEvent を生成します。 このイベントは、問題が発生しており詳細な調査が必要であることを示します。

混乱が誘発させた障害を取得するには、GetChaosReport API (PowerShell、C#、または REST) を使用できます。 この API は、渡された継続トークン、または渡された時間範囲に基づく混乱レポートの次のセグメントを取得します。 混乱レポートの次のセグメントを取得するように ContinuationToken を指定するか、StartTimeUtc と EndTimeUtc を使用して時間範囲を指定することができますが、同じ呼び出しで、ContinuationToken と時間範囲の両方を指定することはできません。 100 を超える混乱イベントが存在する場合、混乱レポートがセグメントで返され、セグメントに 100 を超える混乱イベントが含まれています。

## <a name="important-configuration-options"></a>重要な構成オプション
* **TimeToRun**:混乱が正常に完了するまでの実行時間の合計。 実行中の混乱は、TimeToRun で指定された期間が経過する前に、StopChaos API で停止できます。

* **MaxClusterStabilizationTimeout**:ValidationFailedEvent を生成する前に、クラスターが正常になるまで待機する最大時間。 この待機は、回復中のクラスターの負荷を軽減するためのものです。 次のチェックが実行されます。
  * クラスターの正常性に問題がないかどうか
  * サービスの正常性に問題がないかどうか
  * サービス パーティションの対象となるレプリカ セットのサイズに達しているかどうか
  * InBuild レプリカが存在しないかどうか
* **MaxConcurrentFaults**:それぞれの反復で誘発される同時実行のエラーの最大数。 数が多いほど、混乱が激しくなり、クラスターで経験するフェイルオーバーと状態遷移の組み合わせが複雑になります。 

> [!NOTE]
> *MaxConcurrentFaults* の値がどれだけ大きいかに関係なく、混乱では、外部障害が存在しない場合、クォーラム損失またはデータ損失は発生しません。
>

* **EnableMoveReplicaFaults**:プライマリ レプリカまたはセカンダリ レプリカの移動を発生させる障害を有効または無効にします。 これらの障害は、既定で有効になっています。
* **WaitTimeBetweenIterations**:反復の間の待機時間。 つまり、一巡の障害を実行し、クラスターの正常性の対応する検証を完了した後に混乱が一時停止する時間の長さ。 値が大きいほど、平均障害挿入率は低くなります。
* **WaitTimeBetweenFaults**:1 つの反復における 2 つの連続する障害間の待機時間。 値が大きいほど、障害のコンカレンシー (または重複) が少なくなります。
* **ClusterHealthPolicy**:クラスターの正常性ポリシーは、混乱の反復の間にクラスターの正常性の検証に使用されます。 クラスターの正常性に問題がある場合または障害の実行中に予期しない例外が発生した場合は、混乱は、クラスターが回復する時間を提供するために、次の正常性チェックの前に 30 分間待機します。
* **コンテキスト**:(文字列, 文字列) 型のキーと値のペアのコレクション。 マップを使用して、混乱の実行に関する情報を記録できます。 このようなペアが 100 を超えることはできませんし、各文字列 (キーまたは値) の最大は 4095 文字です。 このマップは、オプションで特定の実行に関するコンテキストを格納するために、混乱実行を開始するユーザーによって設定されます。
* **ChaosTargetFilter**:このフィルターを使用して、混乱による障害のターゲットを特定のノードの種類またはアプリケーション インスタンスに限定することができます。 ChaosTargetFilter が使用されない場合、混乱による障害のターゲットはすべてのクラスターのエンティティになります。 ChaosTargetFilter が使用された場合、ChaosTargetFilter の指定に合致するエンティティのみに、混乱による障害が発生します。 NodeTypeInclusionList と ApplicationInclusionList では、和集合セマンティクスのみが可能です。 つまり、NodeTypeInclusionList と ApplicationInclusionList の積集合を指定することはできません。 たとえば、"アプリケーションが特定のノードの種類上にある場合のみ障害を発生させる" と指定することはできません。 エンティティが NodeTypeInclusionList または ApplicationInclusionList のどちらかに含まれている時点で、ChaosTargetFilter を使用してエンティティを除外することはできません。 applicationX が ApplicationInclusionList に含まれていない場合でも、そのアプリケーションが NodeTypeInclusionList に含まれている nodeTypeY のノード上にあるという理由で、何らかの混乱の反復処理中にそのアプリケーションで障害が発生する可能性があります。 NodeTypeInclusionList と ApplicationInclusionList の両方が null または空の場合は、ArgumentException がスローされます。
    * **NodeTypeInclusionList**:混乱による障害のターゲットとなるノードの種類の一覧。 すべての種類の障害 (ノードの再起動、コード パッケージの再起動、レプリカの削除、レプリカの再起動、プライマリの移動、およびセカンダリの移動) は、指定されたノードの種類のノードで有効になります。 ノードの種類 (たとえば NodeTypeX) が NodeTypeInclusionList に含まれていない場合、ノード レベルの障害 (ノードの再起動など) が NodeTypeX のノードで有効になることはありませんが、ApplicationInclusionList 内のアプリケーションが NodeTypeX のノードに存在する場合、コード パッケージ障害とレプリカ障害が NodeTypeX のノードで有効になる可能性があります。 この一覧には最大 100 種のノードの種類を含めることができ、この数を増やすには、MaxNumberOfNodeTypesInChaosTargetFilter 構成をアップグレードする必要があります。
    * **ApplicationInclusionList**:混乱による障害のターゲットとなるアプリケーションの URI の一覧。 指定されたアプリケーションのサービスに属すすべてのレプリカは、混乱によって誘発されるレプリカ障害 (レプリカの再起動、レプリカの削除、プライマリの移動、およびセカンダリの移動) を受け入れます。 コード パッケージがこれらのアプリケーションのレプリカをホストしている場合のみ、混乱は、コード パッケージを再起動できます。 アプリケーションがこの一覧に含まれていない場合でも、NodeTypeInclusionList に含まれるノードの種類のノード上にアプリケーションが存在する場合は、混乱の反復処理によって障害が発生する可能性があります。 ただし、applicationX が配置制約によって nodeTypeY に関連付けられているときに、applicationX が ApplicationInclusionList になく、nodeTypeY が NodeTypeInclusionList にない場合、applicationX で障害が発生することはありません。 この一覧には最大 1,000 個のアプリケーションの名前を含めることができ、この数を増やすには、MaxNumberOfApplicationsInChaosTargetFilter 構成をアップグレードする必要があります。

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

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster
            // entities. 
            // During validation if a cluster entity is not stable and healthy within
            // MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex
            // series of states to uncover bugs.
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be
            // used to record information about the Chaos run. There cannot be more than 100 such pairs and
            // each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the
            // lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration.
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of
            // state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica,
            // and move secondary replica will happen for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList,
            // restart code package, restart replica, move primary replica, move secondary replica faults will
            // happen for 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing
            // on a node which is not of type included in nodeypeInclusionList.
            var applicationInclusionList = new List<string> { "fabric:/TestApp2" };

            // List of cluster entities to target for Chaos faults.
            var chaosTargetFilter = new ChaosTargetFilter
            {
                NodeTypeInclusionList = nodetypeInclusionList,
                ApplicationInclusionList = applicationInclusionList
            };

            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy) {ChaosTargetFilter = chaosTargetFilter};

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

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

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
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy.
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities.
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds,
# Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration.
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of
# states to uncover bugs.
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the
# fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration.
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state
# transitions that the cluster goes through.
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record
# information about the Chaos run.
# There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move
# secondary replica will happen for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for
# 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is
# not of type included in nodeypeInclusionList.
$chaosTargetFilter.ApplicationInclusionList.Add("fabric:/TestApp2")

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy -ChaosTargetFilter $chaosTargetFilter

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
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}
```
