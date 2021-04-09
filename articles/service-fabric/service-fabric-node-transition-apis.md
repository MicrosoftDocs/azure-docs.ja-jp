---
title: クラスター ノードを起動および停止する
description: フォールト挿入を使い、クラスター ノードを起動および停止することによって、Service Fabric アプリケーションをテストする方法について説明します。
author: LMWF
ms.topic: conceptual
ms.date: 6/12/2017
ms.author: lemai
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c31040ec13084f9e4b08bbc9a347e4ad44975bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89021257"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Start Node API と Stop Node API を Node Transition API に置き換える

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Stop Node API と Start Node API の機能

Stop Node API (マネージド: [StopNodeAsync()][stopnode]、PowerShell: [Stop-ServiceFabricNode][stopnodeps]) は、Service Fabric ノードを停止します。  Service Fabric ノードはプロセスであり、VM やマシンではありません。そのため、VM やマシンは停止されません。  以降、"ノード" という記述は Service Fabric ノードのことを指します。  ノードを停止すると、そのノードは "*停止*" 状態になり、クラスターのメンバーから外れ、サービスをホストできなくなります。このようにして、"*ダウン*" 状態のノードをシミュレートします。  これは、アプリケーションをテストするためにシステム エラーを作り出すのに便利な方法です。  Start Node API (マネージド: [StartNodeAsync()][startnode]、PowerShell: [Start-ServiceFabricNode][startnodeps]]) は、Stop Node API で停止したノードを通常の状態に戻します。

## <a name="why-are-we-replacing-these"></a>API を置き換える理由

前述したとおり、"*停止*" 状態の Service Fabric ノードは、Stop Node API を使用して意図的にターゲットにしたノードです。  "*ダウン*" 状態のノードは、VM やマシンの停止など、別の理由によって停止しているノードです。  Stop Node API では、"*停止*" ノードと "*ダウン*" ノードを区別するための情報が提供されません。

また、これらの API によって返されるエラーの中には、その内容を判別しにくいものがあります。  たとえば、既に "*停止*" 状態のノードに対して Stop Node API を呼び出すと、*InvalidAddress* というエラーが返されます。  このような状況を改善する目的もあります。

さらに、Start Node API が呼び出されるまで、ノードは "無期限に" 停止します。  これにより問題が発生したり、エラーが起こリやすくなる可能性があります。  たとえば、あるノードに対して Stop Node API を呼び出したまま、そのことを忘れてしまったケースがありました。  時間が経ってしまった後なので、そのノードが "*ダウン*" しているのか、"*停止*" しているのか判断できませんでした。


## <a name="introducing-the-node-transition-apis"></a>Node Transition API の概要

上記の問題に対処するため、新しい API セットを開発しました。  新しい Node Transition API (マネージド: [StartNodeTransitionAsync()][snt]) は、Service Fabric ノードを "*停止*" 状態にするためにも、"*停止*" 状態から通常の稼働状態に戻すためにも使用できます。  API の名前に "Start" という文字が含まれていますが、これはノードを開始するという意味ではありません。  ノードを "*停止*" または "開始" のいずれかの状態に遷移させるための非同期操作を開始することを意味しています。

**使用方法**

Node Transition API を呼び出したときに例外がスローされなければ、非同期操作はシステムによって受け入れられおり、実行されます。  呼び出しが成功していても、操作が完了しているとは限りません。  操作の現在の状態に関する情報を取得するには、Node Transition API を呼び出したときに使用した guid を指定して、Node Transition Progress API (マネージド: [GetNodeTransitionProgressAsync()][gntp]) を呼び出します。  Node Transition Progress API は、NodeTransitionProgress オブジェクトを返します。  このオブジェクトの State プロパティを見れば、操作の現在の状態がわかります。  値が "Running" の場合、操作は実行中です。  "Completed" の場合、エラーが発生することなく操作が完了しています。  "Faulted" の場合、操作の実行に問題が発生しています。  問題の内容は、Result プロパティの Exception プロパティに示されています。  State プロパティの詳細については https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate を参照してください。また、コード例については、下の “使用例” セクションをご覧ください。


**停止したノードとダウンしたノードを見分ける方法** Node Transition API を使用してノードを "*停止*" した場合、ノード クエリの出力 (マネージド: [GetNodeListAsync()][nodequery]、PowerShell: [Get-servicefabricnode][nodequeryps]) を見ると、このノードの *IsStopped* プロパティの値が true になっているのがわかります。  これは、*NodeStatus* プロパティの値 (*Down*) とは異なります。  *NodeStatus* プロパティの値が *Down* であるにもかかわらず、*IsStopped* が false の場合、そのノードは Node Transition API で停止されたのではなく、別の理由により "*ダウン*" しています。  *IsStopped* プロパティが true で、*NodeStatus* プロパティが *Down* の場合、そのノードは Node Transition API で停止されています。

"*停止*" されているノードを Node Transition API を使って開始すると、そのノードはクラスターの通常のメンバーとして再び機能するようになります。  ノード クエリ API の出力では、*IsStopped* は false に、*NodeStatus* は Down 以外のいずれか (Up など) になります。


**期間の制限** Node Transition API を使用してノードを停止する場合、必須パラメーターの 1 つである *stopNodeDurationInSeconds* は、ノードを "*停止*" する時間を秒単位で表します。  この値は 600 ～ 14400 の範囲内でなければなりません。  この時間を過ぎると、ノードは自動的に再起動して稼働状態に戻ります。  使用例については、下のサンプル 1 をご覧ください。

> [!WARNING]
> Node Transition API と Stop Node API/Start Node API を混在させないようにしてください。  Node Transition API のみを使用することをお勧めします。  Stop Node API で停止されているノードがある場合は、Start Node API でノードを開始してから、Node Transition API の使用を開始してください。

> [!WARNING]
> 1 つのノードに対して並列して複数の Node Transition API を呼び出すことはできません。  複数の呼び出しを行うと、Node Transition API は FabricException をスローします (ErrorCode プロパティの値は NodeTransitionInProgress)。  ノードの遷移が開始したら、その操作が終了状態 (Completed、Faulted、または ForceCancelled) になるまで、同じノード上で新しい遷移を開始しないでください。  別のノードで並行してノード遷移を呼び出すことはできます。


#### <a name="sample-usage"></a>使用例


**サンプル 1** - Node Transition API を使用してノードを停止します。

```csharp
        // Helper function to get information about a node
        static Node GetNodeInfo(FabricClient fc, string node)
        {
            NodeList n = null;
            while (n == null)
            {
                n = fc.QueryManager.GetNodeListAsync(node).GetAwaiter().GetResult();
                Task.Delay(TimeSpan.FromSeconds(1)).GetAwaiter();
            };

            return n.FirstOrDefault();
        }

        static async Task WaitForStateAsync(FabricClient fc, Guid operationId, TestCommandProgressState targetState)
        {
            NodeTransitionProgress progress = null;

            do
            {
                bool exceptionObserved = false;
                try
                {
                    progress = await fc.TestManager.GetNodeTransitionProgressAsync(operationId, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                    exceptionObserved = true;
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                    exceptionObserved = true;
                }

                if (!exceptionObserved)
                {
                    Console.WriteLine("Current state of operation '{0}': {1}", operationId, progress.State);

                    if (progress.State == TestCommandProgressState.Faulted)
                    {
                        // Inspect the progress object's Result.Exception.HResult to get the error code.
                        Console.WriteLine("'{0}' failed with: {1}, HResult: {2}", operationId, progress.Result.Exception, progress.Result.Exception.HResult);

                        // ...additional logic as required
                    }

                    if (progress.State == targetState)
                    {
                        Console.WriteLine("Target state '{0}' has been reached", targetState);
                        break;
                    }
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (true);
        }

        static async Task StopNodeAsync(FabricClient fc, string nodeName, int durationInSeconds)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            // Create a Guid
            Guid guid = Guid.NewGuid();

            // Create a NodeStopDescription object, which will be used as a parameter into StartNodeTransition
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, durationInSeconds);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStopDescription from above, which will stop the target node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    // This is retryable
                }
                catch (FabricTransientException fte)
                {
                    // This is retryable
                }

                // Backoff
                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**サンプル 2** - "*停止*" 状態のノードを開始します。  最初のサンプルと同じヘルパー メソッドをいくつか使用します。

```csharp
        static async Task StartNodeAsync(FabricClient fc, string nodeName)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = n.NodeInstanceId;

            // Create a NodeStartDescription object, which will be used as a parameter into StartNodeTransition
            NodeStartDescription description = new NodeStartDescription(guid, n.NodeName, nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**サンプル 3** - 間違った使い方を示すサンプルです。  *stopDurationInSeconds* の値が許容範囲を超えています。  StartNodeTransitionAsync() が致命的なエラーで失敗するので、操作は受け入れられず、Transition Progress API も呼び出されません。  このサンプルでは、最初のサンプルと同じヘルパー メソッドをいくつか使用します。

```csharp
        static async Task StopNodeWithOutOfRangeDurationAsync(FabricClient fc, string nodeName)
        {
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();

            // Use an out of range value for stopDurationInSeconds to demonstrate error
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, 99999);

            try
            {
                await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
            }

            catch (FabricException e)
            {
                Console.WriteLine("Caught {0}", e);
                Console.WriteLine("ErrorCode {0}", e.ErrorCode);
                // Output:
                // Caught System.Fabric.FabricException: System.Runtime.InteropServices.COMException (-2147017629)
                // StopDurationInSeconds is out of range ---> System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80071C63
                // << Parts of exception omitted>>
                //
                // ErrorCode InvalidDuration
            }
        }
```

**サンプル 4** - Node Transition API によって開始された操作が受け入れられた後、実行中に失敗した場合に、Node Transition Progress API から返されるエラー情報を示します。  ここでは、Node Transition API が存在しないノードを開始しようとするため失敗します。  このサンプルでは、最初のサンプルと同じヘルパー メソッドをいくつか使用します。

```csharp
        static async Task StartNodeWithNonexistentNodeAsync(FabricClient fc)
        {
            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = 12345;

            // Intentionally use a nonexistent node
            NodeStartDescription description = new NodeStartDescription(guid, "NonexistentNode", nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.  In this case, it will end up in the Faulted state since the node does not exist.
            // When StartNodeTransitionProgressAsync()'s returned progress object has a State if Faulted, inspect the progress object's Result.Exception.HResult to get the error code.
            // In this case, it will be NodeNotFound.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Faulted).ConfigureAwait(false);
        }
```

[stopnode]: /dotnet/api/system.fabric.fabricclient.faultmanagementclient
[stopnodeps]: /previous-versions/azure/mt125982(v=azure.100)
[startnode]: /dotnet/api/system.fabric.fabricclient.faultmanagementclient
[startnodeps]: /previous-versions/azure/mt163520(v=azure.100)
[nodequery]: /dotnet/api/system.fabric.fabricclient.queryclient
[nodequeryps]: /powershell/module/servicefabric/get-servicefabricnode
[snt]: /dotnet/api/system.fabric.fabricclient.testmanagementclient
[gntp]: /dotnet/api/system.fabric.fabricclient.testmanagementclient
