---
title: "Service Fabric サービスでデータ損失を発生させる方法 | Microsoft Docs"
description: "データ損失 API の使用方法を説明します。"
services: service-fabric
documentationcenter: .net
author: LMWF
manager: rsinha
editor: 
ms.assetid: f4e70f6f-cad9-4a3e-9655-009b4db09c6d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/19/2016
ms.author: lemai
redirect_url: /azure/service-fabric/service-fabric-testability-overview
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 8771556954be77543b0eaa21b7201f93ffa0ed70
ms.lasthandoff: 12/08/2016


---
# <a name="how-to-invoke-data-loss-on-services"></a>サービスでデータ損失を発生させる方法
> [!WARNING]
> このドキュメントでは、サービスでデータ損失を発生させる方法について説明するため、取り扱いにご注意ください。
> 
> 

## <a name="introduction"></a>はじめに
StartPartitionDataLossAsync() を呼び出すことで、Service Fabric サービスのパーティションでデータ損失を発生させることができます。  この API は、フォールト挿入と分析サービスを使用して、データが失われる状況が発生する処理を実行します。

## <a name="using-the-fault-injection-and-analysis-service"></a>フォールト挿入と分析サービスの使用
フォールト挿入と分析サービスは、現在、次の表にある API をサポートしています。  表の右側は、対応する PowerShell コマンドレットを示します。  詳細については、各 API に関する MSDN ドキュメントを参照してください。

| C# API | PowerShell コマンドレット |
| --- | ---:|
| [StartPartitionDataLossAsync][dl] |[Start-ServiceFabricPartitionDataLoss][psdl] |
| [StartPartitionQuorumLossAsync][ql] |[Start-ServiceFabricPartitionQuorumLoss][psql] |
| [StartPartitionRestartAsync][rp] |[Start-ServiceFabricPartitionRestart][psrp] |

## <a name="conceptual-overview-of-running-a-command"></a>コマンドの実行についての概念の概要
フォールト挿入と分析サービスは、1 つの API でコマンドを開始する非同期モデルを使用します。これは、このドキュメントで "Start" API と呼ばれ、終了状態に到達するかキャンセルされるまで、"GetProgress"API を使用してこのコマンドの進行状況をチェックします。
コマンドを開始するには、対応する API の "Start" API を呼び出します。  この API は、フォールト挿入と分析サービスが要求を受け入れると制御を返します。  ただし、コマンドの実行がどこまで進行したかや、まだ開始されていないことさえ示しません。  コマンドの進行状況を確認するには、前に呼び出した "Start" API に対応する "GetProgress" API を呼び出します。  "GetProgress" API は、State プロパティ内のコマンドの現在の状態を示すオブジェクトを返します。  コマンドは以下のタイミングまで無限に実行されます。

1. 正常に完了する。  ここで "GetProgress" を呼び出すと、進行状況オブジェクトの State は "Completed" になります。
2. 致命的なエラーが発生する。  ここで "GetProgress" を呼び出すと、進行状況オブジェクトの State は "Faulted" になります。
3. [CancelTestCommandAsync][cancel] API または [Stop-ServiceFabricTestCommand][cancelps] PowerShell コマンドレットを使用してキャンセルします。  こで "GetProgress" を呼び出すと、進行状況オブジェクトの State は API の引数に応じて "Cancelled" または "ForceCancelled" になります。  詳細については、[CancelTestCommandAsync][cancel] に関するドキュメントをご覧ください。

## <a name="details-of-running-a-command"></a>コマンドの実行の詳細
コマンドを開始するために、想定される引数を使用して Start API を呼び出します。  すべての Start API に、operationId という名前の Guid 引数が含まれます。  operationId 引数はこのコマンドの進行状況を追跡するために使用されているため、追跡を続ける必要があります。  これは、コマンドの進行状況を追跡するために、"GetProgress" API に渡す必要があります。  operationId は一意である必要があります。

Start API を正常に呼び出した後で、返される進行状況オブジェクトの State プロパティが "Completed" になるまで、GetProgress API をループ内で呼び出す必要があります。  すべての [FabricTransientException][fte] と OperationCanceledException を再試行する必要があります。
コマンドが終了状態 (Completed、Faulted、または Cancelled) に達すると、返される進行状況オブジェクトの Result プロパティに情報が追加されます。  状態が Completed の場合、Result.SelectedPartition.PartitionId に、選択したパーティション ID が格納されます。  Result.Exception は null になります。  状態が Faulted の場合、Result.Exception に、フォールト挿入と分析サービスでコマンドがエラーになった理由が格納されます。  Result.SelectedPartition.PartitionId に、選択したパーティション ID が格納されます。  場合によっては、パーティションを選択できるほどコマンドが進行していない可能性があります。  その場合、PartitionId は 0 になります。  状態が Cancelled の場合、Result.Exception は null になります。  Faulted の場合と同様、Result.SelectedPartition.PartitionId に、選択したパーティション ID が格納されますが、コマンドがそこまで進行していない場合、パーティション ID は 0 になります。  次の例も参照してください。

次のサンプル コードでは、コマンドを開始して、特定のパーティションでデータ損失を発生させるコマンドの進行状況をチェックする方法を示します。

```csharp
    static async Task PerformDataLossSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/MyService");

        // The id of the target partition inside the target service
        Guid targetPartitionId = new Guid("00000000-0000-0000-0000-000002233445");

        PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(targetServiceName, targetPartitionId);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.        

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                // In a terminal state .Result.SelectedPartition.PartitionId will have the chosen partition
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);
                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}'", operationId, progress.Result.Exception);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(5.0d)).ConfigureAwait(false);
        }
    }
```

次の例では、PartitionSelector を使用して、指定されたサービスのランダム パーティションを選択する方法を示します。

```csharp
    static async Task PerformDataLossUseSelectorSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/SampleService ");

        // Use a PartitionSelector that will have the Fault Injection and Analysis Service choose a random partition of “targetServiceName”
        PartitionSelector partitionSelector = PartitionSelector.RandomOf(targetServiceName);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }
            catch (Exception e)
            {
                Console.WriteLine("Unexpected exception '{0}'", e);
                throw;
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                Console.WriteLine("Printing progress.Result:");
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);

                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}', SelectedPartition {2}", operationId, progress.Result.Exception, progress.Result.SelectedPartition);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }
    }
```

## <a name="history-and-truncation"></a>履歴と切り捨て
コマンドが終了状態に達すると、そのメタデータは、スペースの節約のために削除されるまでフォールト挿入および分析サービスに一定期間残ります。  削除された後に、コマンドの operationId を使用して "GetProgress" が呼び出された場合、エラー コード KeyNotFound と一緒に FabricException が返されます。

[dl]: https://msdn.microsoft.com/library/azure/mt693569.aspx
[ql]: https://msdn.microsoft.com/library/azure/mt693558.aspx
[rp]: https://msdn.microsoft.com/library/azure/mt645056.aspx
[psdl]: https://msdn.microsoft.com/library/mt697573.aspx
[psql]: https://msdn.microsoft.com/library/mt697557.aspx
[psrp]: https://msdn.microsoft.com/library/mt697560.aspx
[cancel]: https://msdn.microsoft.com/library/azure/mt668910.aspx
[cancelps]: https://msdn.microsoft.com/library/mt697566.aspx
[fte]: https://msdn.microsoft.com/library/azure/system.fabric.fabrictransientexception.aspx

