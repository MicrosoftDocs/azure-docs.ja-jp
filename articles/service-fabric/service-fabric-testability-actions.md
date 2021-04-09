---
title: Azure マイクロサービスでの障害をシミュレートする
description: この記事は、Microsoft Azure Service Fabric の Testability アクションについて説明します。
ms.topic: conceptual
ms.date: 06/07/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: c57f54096a9593f5ab25a5722d3f2d2b9878b511
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595016"
---
# <a name="testability-actions"></a>Testability アクション
Azure Service Fabric では、信頼性の低いインフラストラクチャをシミュレートするため、さまざまな現実世界の障害と状態遷移をシミュレートする方法を開発者に提供します。 これらは、Testability アクションとして公開されます。 これらのアクションは、特定のフォールト インジェクション、状態遷移、検証を発生させる低レベルの API です。 これらのアクションを組み合わせて、サービスに対する包括的なテスト シナリオを記述することができます。

Service Fabric には、これらのアクションで構成された、一般的なテスト シナリオが用意されています。 一般的な状態遷移と障害事例をテストするために慎重に選択されているため、これらの組み込みシナリオを利用することを強くお勧めします。 ただし、組み込みシナリオでは対応できないシナリオや、アプリケーション用に調整したカスタム シナリオを追加するときに、アクションを使用してカスタム テスト シナリオを作成することができます。

C# でのアクションの実装は System.Fabric.dll アセンブリ内にあります。 System Fabric PowerShell モジュールは Microsoft.ServiceFabric.Powershell.dll アセンブリ内にあります。 簡単に使用できるようにするため、ServiceFabric PowerShell モジュールはランタイム インストールの一部としてインストールされます。

## <a name="graceful-vs-ungraceful-fault-actions"></a>グレースフル障害アクションとアングレースフル障害アクション
Testability アクションは、次の 2 つに大きく分類されます。

* アングレースフル障害。これらの障害は、コンピューターの再起動やプロセスのクラッシュなどをシミュレートします。 このような障害では、プロセスの実行コンテキストは突然停止します。 つまり、アプリケーションを再起動する前に、状態のクリーンアップを実行できません。
* グレースフル障害。これらの障害は、負荷分散によってトリガーされるレプリカの移動や削除などの正常なアクションをシミュレートします。 このような場合、サービスは、終了の通知を取得し、終了する前に状態をクリーンアップすることができます。

品質をより深く検証するために、さまざまなグレースフル障害とアングレースフル障害エラーを発生させながら、サービスとビジネスのワークロードを実行します。 アングレースフル障害は、何らかのワークフローの途中でサービス プロセスが突然終了するシナリオで使用します。 Service Fabric によってサービスのレプリカが復元された後で復旧パスをテストします。 障害発生後のデータの整合性とサービス状態が正しく維持されているかどうかをテストするために役立ちます。 もう 1 つの障害セット (グレースフル障害) では、Service Fabric によって移動されるレプリカに対してサービスが正しく反応することをテストします。 これは、RunAsync メソッドでのキャンセル処理をテストします。 サービスは、設定されるキャンセル トークンをチェックし、その状態を正しく保存して、RunAsync メソッドを終了する必要があります。

## <a name="testability-actions-list"></a>Testability アクションの一覧
| アクション | 説明 | マネージド API | PowerShell コマンドレット | グレースフル/アングレースフル障害 |
| --- | --- | --- | --- | --- |
| CleanTestState |テスト ドライバーの異常なシャットダウンが発生した場合に、クラスターからすべてのテスト状態を削除します。 |CleanTestStateAsync |Remove-ServiceFabricTestState |適用なし |
| InvokeDataLoss |サービス パーティションでデータ損失を発生させます。 |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |グレースフル |
| InvokeQuorumLoss |特定のステートフル サービス パーティションをクォーラム損失状態にします。 |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |グレースフル |
| MovePrimary |ステートフル サービスの指定したプライマリ レプリカを、指定したクラスター ノードに移動します。 |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |グレースフル |
| MoveSecondary |ステートフル サービスの現在のセカンダリ レプリカを、別のクラスター ノードに移動します。 |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |グレースフル |
| RemoveReplica |クラスターからレプリカを削除することで、レプリカ障害をシミュレートします。 レプリカを閉じてロールを "None" に移行し、すべての状態をクラスターから削除します。 |RemoveReplicaAsync |Remove-ServiceFabricReplica |グレースフル |
| RestartDeployedCodePackage |クラスター内のノードにデプロイされているコード パッケージを再起動することで、コード パッケージ プロセス障害をシミュレートします。 そのプロセスでホストされているすべてのユーザー サービス レプリカを再起動するコード パッケージ プロセスを中止します。 |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |アングレースフル |
| RestartNode |ノードを再起動することで、Service Fabric のクラスター ノード障害をシミュレートします。 |RestartNodeAsync |Restart-ServiceFabricNode |アングレースフル |
| RestartPartition |パーティションの一部またはすべてのレプリカを再起動することで、データ センターのブラックアウトまたはクラスターのブラックアウト シナリオをシミュレートします。 |RestartPartitionAsync |Restart-ServiceFabricPartition |グレースフル |
| RestartReplica |クラスター内の永続化されたレプリカを再起動すし、レプリカを閉じてから再び開くことで、レプリカ障害をシミュレートします。 |RestartReplicaAsync |Restart-ServiceFabricReplica |グレースフル |
| StartNode |既に停止しているクラスター ノードを起動します。 |StartNodeAsync |Start-ServiceFabricNode |適用なし |
| StopNode |クラスター内のノードを停止することで、ノード障害をシミュレートします。 ノードは、StartNode が呼び出されるまで停止したままになります。 |StopNodeAsync |Stop-ServiceFabricNode |アングレースフル |
| ValidateApplication |アプリケーション内のすべての Service Fabric サービスの正常性と可用性を検証します。通常は、システムに何らかの障害を発生させた後で実行します。 |ValidateApplicationAsync |Test-ServiceFabricApplication |適用なし |
| ValidateService |Service Fabric サービスの正常性と可用性を検証します。通常は、システムに何らかの障害を発生させた後で実行します。 |ValidateServiceAsync |Test-ServiceFabricService |適用なし |

## <a name="running-a-testability-action-using-powershell"></a>PowerShell を使用した Testability アクションの実行
このチュートリアルでは、PowerShell を使用して Testability アクションを実行する方法を示します。 ローカル (ワンボックス) クラスターまたは Azure クラスターに対して Testability アクションを実行する方法を学習します。 Microsoft.Fabric.Powershell.dll (Service Fabric PowerShell モジュール) は Microsoft Service Fabric MSI のインストール時に自動的にインストールされます。 このモジュールは、PowerShell プロンプトを開いたときに自動的に読み込まれます。

チュートリアル セグメント:

* [ワンボックス クラスターに対してアクションを実行する](#run-an-action-against-a-one-box-cluster)
* [Azure クラスターに対してアクションを実行する](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>ワンボックス クラスターに対してアクションを実行する
ローカル クラスターに対して Testability アクションを実行するには、クラスターに接続した後、管理者モードで PowerShell プロンプトを開く必要があります。 **Restart-ServiceFabricNode** アクションについて説明します。

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

ここでは、"Node1" という名前のノードで **Restart-ServiceFabricNode** アクションが実行されます。 完了モードには、再起動ノード アクションが実際に成功したかどうかを検証する必要がないことを指定します。 完了モードを "Verify" と指定すると、再起動アクションが実際に成功したかどうかの検証が行われます。 ノードは、名前で直接指定する代わりに、パーティション キーとレプリカの種類を使用して次のように指定することができます。

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode** を使用する必要があります。 これは、そのノードでホストされているすべてのシステム サービスとユーザー サービスのレプリカを再起動する Fabric.exe プロセスを停止します。 この API を使用したサービスのテストは、フェールオーバー時の復旧パスに含まれるバグを検出するために役立ちます。 クラスター内のノード障害をシミュレートするために役立ちます。

次のスクリーンショットは、実行中の **Restart-ServiceFabricNode** Testability コマンドを示しています。

![PowerShell での Restart-ServiceFabricNode コマンド実行のスクリーンショット。](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

最初の **Get-ServiceFabricNode** (Service Fabric PowerShell モジュールのコマンドレット) の出力は、ローカル クラスターに 5 つのノードがあることを示しています (Node.1 ～ Node.5)。 Node.4 というノードで Testability アクション (コマンドレット) **Restart-ServiceFabricNode** を実行した後、このノードのアップタイムがリセットされていることが示されます。

### <a name="run-an-action-against-an-azure-cluster"></a>Azure クラスターに対してアクションを実行する
Azure クラスターに対する (PowerShell を使用した) Testability アクションの実行は、ローカル クラスターに対する実行に似ています。 唯一の違いは、アクションを実行する前に、ローカル クラスターではなく Azure クラスターに接続する必要があることです。

## <a name="running-a-testability-action-using-c35"></a>C&#35; を使用した Testability アクションの実行
C# を使用して Testability アクションを実行するには、FabricClient を使用してクラスターに接続する必要があります。 その後、アクションを実行するために必要なパラメーターを取得します。 異なるパラメーターを使用して同じアクションを実行することができます。
RestartServiceFabricNode アクションを実行する方法の 1 つは、クラスター内のノード情報 (ノード名とノード インスタンス ID) を使用することです。

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

パラメーターの説明:

* **CompleteMode** では、再起動アクションが実際に成功したかどうかを検証する必要がないことを指定します。 完了モードを "Verify" と指定すると、再起動アクションが実際に成功したかどうかの検証が行われます。  
* **OperationTimeout** では、操作が終了するまでの時間を設定します。この時間が経過すると TimeoutException 例外がスローされます。
* **CancellationToken** は、保留中の呼び出しをキャンセルできるようにします。

ノードは、名前で直接指定する代わりに、パーティション キーとレプリカの種類を使用して指定することができます。

詳細については、「PartitionSelector と ReplicaSelector」を参照してください。

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector と ReplicaSelector
### <a name="partitionselector"></a>PartitionSelector
PartitionSelector は Testability 内に公開されるヘルパーであり、Testability アクションを実行する特定のパーティションを選択するために使用されます。 パーティション ID が事前にわかっている場合は、このセレクターを使用して特定のパーティションを選択することができます。 パーティション キーを指定することもできます。パーティション ID は操作によって内部的に解決されます。 さらに、ランダム パーティションを選択することができます。

このヘルパーを使用するには、PartitionSelector オブジェクトを作成し、Select* メソッドのいずれかを使用してパーティションを選択します。 その後、PartitionSelector オブジェクトを使用してパーティションを必要とする API に渡します。 オプションが選択されていない場合は、ランダム パーティションが既定によって選択されます。

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector は、Testability 内に公開されるヘルパーであり、Testability アクションが実行されるレプリカを選択するために使用します。 レプリカ ID が事前にわかっている場合は、特定のレプリカを選択するために使用できます。 さらに、プライマリ レプリカまたはランダム セカンダリを選択することができます。 ReplicaSelector は PartitionSelector から派生するため、Testability 操作を実行するレプリカとパーティションの両方を選択する必要があります。

このヘルパーを使用するには、ReplicaSelector オブジェクトを作成し、レプリカとパーティションを選択する方法を設定します。 その後、必要とする API に渡すことができます。 オプションが選択されていない場合は、ランダム レプリカとランダム パーティションが既定によって選択されます。

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>次のステップ
* [Testability のシナリオ](service-fabric-testability-scenarios.md)
* サービスをテストする方法
  * [サービス ワークロード中のエラーのシミュレーション](service-fabric-testability-workload-tests.md)
  * [サービス間の通信障害](service-fabric-testability-scenarios-service-communication.md)

