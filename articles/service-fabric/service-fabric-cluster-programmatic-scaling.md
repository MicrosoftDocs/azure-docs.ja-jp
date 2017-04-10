---
title: "プログラムによる Azure Service Fabric のスケーリング | Microsoft Docs"
description: "カスタムのトリガーに従って、Azure Service Fabric クラスターをプログラムでスケールインまたはスケールアウトします。"
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2017
ms.author: mikerou
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 8d7052fabeb348b4bba744b43d9af78f058175a8
ms.lasthandoff: 03/17/2017


---

# <a name="scale-a-service-fabric-cluster-programmatically"></a>プログラムによる Service Fabric クラスターのスケール 

Azure での Service Fabric クラスターのスケーリングの基礎については、[クラスター スケーリング](./service-fabric-cluster-scale-up-down.md)に関する記事で説明されています。 その記事では、Service Fabric クラスターを仮想マシン スケール セット上に構築する方法のほか、手動か、自動スケール ルールを利用して Service Fabric クラスターをスケーリングする方法が示されています。 このドキュメントでは、より高度なシナリオで Azure のスケーリング操作をプログラムで調整する方法を説明します。 

## <a name="reasons-for-programmatic-scaling"></a>プログラムでスケーリングを行う理由
多くのシナリオでは、手動でのスケーリングまたは自動スケール ルールを使用したスケーリングが適しています。 しかし、シナリオによっては、この方法が適していない場合もあります。 このアプローチの潜在的な欠点は次のとおりです。

- 手動でスケーリングを行うには、ログインし、スケーリング操作を明示的に要求する必要があります。 スケーリング操作が頻繁に必要になる場合または予期せず発生する場合は、このアプローチは適さない可能性があります。
- 自動スケール ルールによって仮想マシン スケール セットからインスタンスが削除されても、そのノードのナレッジは、そのノードが Silver または Gold の耐久性レベルを持つ種類でない限り、関連付けられた Service Fabric クラスターからは自動的に削除されません。 自動スケール ルールは (Service Fabric レベルではなく) スケール セット レベルで適用されるため、自動スケール ルールを使用して、Service Fabric ノードをシャットダウンなしで削除できます。 このような方法でノードの削除を行うと、スケールイン操作の後で、Service Fabric ノードが "ゴースト" 状態で背後に残ります。 ユーザー (またはサービス) は、Service Fabric クラスターから削除したノードの状態を定期的にクリーンアップする必要があります。
  - Gold または Silver の耐久性レベルを持つノード型では、削除したノードが自動的にクリーンアップされることに注意してください。  
- 自動スケール ルールでは[多くのメトリック](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md)がサポートされていますが、これらは限定されたセットに過ぎません。 このセットでカバーされていないメトリックに基づくスケーリングが必要なシナリオでは、自動スケール ルールは適さない可能性があります。

これらの制限に基づいて、さらにカスタマイズされた自動スケール モデルの実装が必要になる可能性があります。 

## <a name="scaling-apis"></a>API のスケーリング
Azure API を使用すると、アプリケーションがプログラムで仮想マシン スケール セットと Service Fabric クラスターを使用できるようになります。 既存の自動スケール オプションが使用できないシナリオでは、これらの API を使用してカスタム スケーリング ロジックを実装できます。 

この "自作" の自動スケール機能を実装する 1 つの方法は、新しいステートレス サービスを Service Fabric アプリケーションに追加して、スケーリング操作を管理することです。 サービスの `RunAsync` メソッド内で、スケーリングが必要かどうかを一連のトリガーで判断できます (最大クラスター サイズやスケーリングのクールダウンなどのパラメーターのチェックを含みます)。   

仮想マシン スケール セットの操作に使用する API (現在の仮想マシン インスタンスの数の確認と変更の両方) は、fluent [Azure Management Compute ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/1.0.0-beta50)です。 fluent コンピューティング ライブラリは、仮想マシン スケール セットを操作するための使いやすい API です。

Service Fabric クラスター自体を操作するには、[System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient) を使用します。

もちろん、スケーリングのコードは、スケーリング対象クラスターのサービスとして実行する必要はありません。 `IAzure` と `FabricClient` は両方とも、関連する Azure リソースにリモートで接続できるので、スケーリング サービスは Service Fabric アプリケーション外で動作するコンソール アプリケーションまたは Windows サービスとして簡単に使用することができます。 

## <a name="credential-management"></a>資格情報の管理
スケーリングを処理するサービスを作成する際に難しいのは、そのサービスが、対話型ログインを行わずに仮想マシン スケール セットのリソースにアクセスできるようにする必要があるということです。 スケーリング サービスが独自の Service Fabric アプリケーションを変更するようであれば、Service Fabric クラスターへのアクセスは簡単です。しかし、スケール セットへのアクセスには資格情報が必要です。 ログインには、[Azure CLI 2.0](https://github.com/azure/azure-cli) で作成した[サービス プリンシパル](https://github.com/Azure/azure-sdk-for-net/blob/Fluent/AUTH.md#creating-a-service-principal-in-azure)を使用できます。

サービス プリンシパルは、次の手順で作成できます。

1. 仮想マシン スケール セットへのアクセス権を持つユーザーとして Azure CLI (`az login`) にログインします。
2. `az ad sp create-for-rbac` でサービス プリンシパルを作成します。
    1. appId (場所によっては "クライアント ID" と呼ばれる)、名前、パスワード、テナントを、後で使用するためにメモしておきます。
    2. サブスクリプション ID も必要になります。これは `az account list` で表示できます。

fluent コンピューティング ライブラリは、次のとおり、これらの資格情報を使用してログインできます。

```C#
var credentials = AzureCredentials.FromServicePrincipal(AzureClientId, AzureClientKey, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

ログインすると、`AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity` でスケール セット インスタンスを照会できます。

## <a name="scaling-out"></a>スケールアウト
fluent Azure コンピューティング SDK を使用すると、数度の呼び出しだけでインスタンスを仮想マシン スケール セットに追加できます。

```C#
var scaleSet = AzureClient?.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = Math.Min(MaximumNodeCount, NodeCount.Value + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

**現在、このコードが動作しなくなる[バグ](https://github.com/Azure/azure-sdk-for-net/issues/2716)が存在しています**が、修正プログラムがマージされているため、間もなく公開バージョンの Microsoft.Azure.Management.Compute.Fluent でこの問題が解決されます。 このバグは、仮想マシン スケール セットのプロパティ (容量など) を fluent コンピューティング API で変更すると、保護された設定がスケール セットの Resource Manager テンプレートから失われるというものです。 このように設定が失われると、新しい仮想マシン インスタンスにおいて、(中でも) Service Fabric サービスで適切な設定が行われなくなる原因となります。

一時的な回避策として、スケーリング サービスから PowerShell コマンドレットを呼び出し、同じ変更を適用することができます (ただし、この場合、PowerShell ツールが必要です)。

```C#
using (var psInstance = PowerShell.Create())
{
    psInstance.AddScript($@"
        $clientId = ""{AzureClientId}""
        $clientKey = ConvertTo-SecureString -String ""{AzureClientKey}"" -AsPlainText -Force
        $Credential = New-Object -TypeName ""System.Management.Automation.PSCredential"" -ArgumentList $clientId, $clientKey
        Login-AzureRmAccount -Credential $Credential -ServicePrincipal -TenantId {AzureTenantId}
        
        $vmss = Get-AzureRmVmss -ResourceGroupName {ResourceGroup} -VMScaleSetName {NodeTypeToScale}
        $vmss.sku.capacity = {newCapacity}
        Update-AzureRmVmss -ResourceGroupName {ResourceGroup} -Name {NodeTypeToScale} -VirtualMachineScaleSet $vmss
    ");

    psInstance.Invoke();

    if (psInstance.HadErrors)
    {
        foreach (var error in psInstance.Streams.Error)
        {
            ServiceEventSource.Current.ServiceMessage(Context, $"ERROR adding node: {error.ToString()}");
        }
    }                
}
```

手動でノードを追加する場合は、スケール セット インスタンスを追加するだけで新しい Service Fabric ノードを開始できます。スケール セット テンプレートには、新しいインスタンスを Service Fabric クラスターに自動的に結合する拡張機能が含まれているためです。 

## <a name="scaling-in"></a>スケールイン

スケールインは、スケールアウトに似ています。 実際の仮想マシン スケール セットの変更も、実質的には同じです。 しかし、先ほど説明したとおり、Service Fabric では、Gold または Silver の耐久性レベルを持つノードの場合だけは、ノードが削除されると自動的にクリーンアップが行われます。 そのため、Bronze の耐久性レベルでスケールインする場合は、Service Fabric クラスターを操作してノードをシャットダウンし、ノードの削除と状態の削除を行う必要があります。

ノードのシャットダウンを準備する際は、削除するノード (最後に追加されたノード) を探して非アクティブ化する必要があります。 非シード ノードの場合、`NodeInstanceId` と比較することで新しいノードを検出できます。 

```C#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n => n.NodeInstanceId)
        .FirstOrDefault();
```

"*シード*" ノードは、より大きなインスタンス ID から削除されるという規則に常に従っているようには見えないので、注意してください。

削除するノードが見つかったら、非アクティブ化し、削除します。これには前に使ったのと同じ `FabricClient` インスタンスと `IAzure` インスタンスを使用します。

```C#
var scaleSet = AzureClient?.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = Math.Max(MinimumNodeCount, NodeCount.Value - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

仮想マシン インスタンスを削除したら、Service Fabric ノードの状態を削除することができます。

```C#
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

[Azure/azure-sdk-for-net#2716](https://github.com/Azure/azure-sdk-for-net/issues/2716) が対処されるまでは、以前と同様に、`IVirtualMachineScaleSet.Update()` が動作しない問題を回避する必要があります。

## <a name="potential-drawbacks"></a>潜在的な欠点

上記のコード スニペットで示したように、独自のスケーリング サービスを作成すると、アプリケーションのスケーリング動作において最高レベルの制御とカスタマイズ性を得ることができます。 これは、アプリケーションをいつ、どのようにスケールインまたはスケールアウトするかについて、細かい制御が必要なシナリオに役立てることができます。 ただし、制御を細かくすると、代わりにコードが複雑になります。 このアプローチでは、独自のスケーリング コードを少なからず使用する必要があります。

Service Fabric のスケーリングの方法は、シナリオによって異なります。 スケーリングが一般的ではない場合、ノードを手動で追加または削除する機能だけでおそらく十分です。 さらに複雑なシナリオの場合は、自動スケール ルールと、プログラムでスケーリングを行う機能を公開している SDK が強力な代替手段となります。

## <a name="next-steps"></a>次のステップ

自動スケールのロジックを独自に実装するには、次の概念と、便利な API をご確認ください。

- [手動でのスケーリングまたは自動スケール ルールを使用したスケーリング](./service-fabric-cluster-scale-up-down.md)
- [.NET 用 Fluent Azure Management ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (Service Fabric クラスターの基盤となる仮想マシン スケール セットの操作に便利です)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (Service Fabric クラスターとそのノードの操作に便利です)
