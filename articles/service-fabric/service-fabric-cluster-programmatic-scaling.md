---
title: プログラムによる Azure Service Fabric のスケーリング
description: カスタムのトリガーに従って、Azure Service Fabric クラスターをプログラムでスケールインまたはスケールアウトします。
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.custom: devx-track-csharp
ms.openlocfilehash: 9f39bd874c1f5a1be42ca1c88e6ea2fe8df22f87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94648210"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>プログラムによる Service Fabric クラスターのスケール 

Azure で実行される Service Fabric クラスターは仮想マシン スケール セットの上に構築されます。  [クラスター スケーリング](./service-fabric-cluster-scale-in-out.md)では、Service Fabric クラスターが手動または自動スケール ルールでスケーリングする方法について説明します。 この記事では、資格情報を管理し、fluent Azure コンピューティング SDK を使用してクラスターをスケールインまたはスケールアウトする方法について説明します。これはより高度なシナリオになります。 概要については、[Azure のスケーリング操作をプログラムで調整する方法](service-fabric-cluster-scaling.md#programmatic-scaling)を読んでください。 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>資格情報を管理する
スケーリングを処理するサービスを作成する際に難しいのは、そのサービスが、対話型ログインを行わずに仮想マシン スケール セットのリソースにアクセスできるようにする必要があるということです。 スケーリング サービスが独自の Service Fabric アプリケーションを変更するようであれば、Service Fabric クラスターへのアクセスは簡単です。しかし、スケール セットへのアクセスには資格情報が必要です。 サインインには、[Azure CLI](https://github.com/azure/azure-cli) で作成した[サービス プリンシパル](/cli/azure/create-an-azure-service-principal-azure-cli)を使用できます。

サービス プリンシパルは、次の手順で作成できます。

1. 仮想マシン スケール セットへのアクセス権を持つユーザーとして Azure CLI (`az login`) にサインインします。
2. `az ad sp create-for-rbac` でサービス プリンシパルを作成します。
    1. appId (場所によっては "クライアント ID" と呼ばれる)、名前、パスワード、テナントを、後で使用するためにメモしておきます。
    2. サブスクリプション ID も必要になります。これは `az account list` で表示できます。

fluent コンピューティング ライブラリは、次のとおり、これらの資格情報を使用してサインインできます (`IAzure` などのコアの fluent Azure タイプは [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) パッケージにあります)。

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
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

ログインすると、`AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity` でスケール セット インスタンス数を照会できます。

## <a name="scaling-out"></a>スケールアウト
fluent Azure コンピューティング SDK を使用すると、数度の呼び出しだけでインスタンスを仮想マシン スケール セットに追加できます。

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

または、PowerShell コマンドレットを使用して、仮想マシン スケール セットのサイズを管理することもできます。 [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) では、仮想マシン スケール セットのオブジェクトを取得できます。 現在の容量は `.sku.capacity` プロパティを通じて使用できます。 容量を目的の値に変更したら、Azure の仮想マシン スケール セットを [`Update-AzVmss`](/powershell/module/az.compute/update-azvmss) コマンドで更新できます。

手動でノードを追加する場合と同様に、スケール セット インスタンスを追加するだけで新しい Service Fabric ノードを開始できます。スケール セット テンプレートには、新しいインスタンスを Service Fabric クラスターに自動的に参加させる拡張機能が含まれているためです。 

## <a name="scaling-in"></a>スケールイン

スケールインは、スケールアウトに似ています。実際の仮想マシン スケール セットの変更も、実質的には同じです。 しかし、先ほど説明したとおり、Service Fabric では、Gold または Silver の耐久性レベルを持つノードの場合だけは、ノードが削除されると自動的にクリーンアップが行われます。 そのため、Bronze の耐久性レベルでスケールインする場合は、Service Fabric クラスターを操作して削除するノードをシャットダウンした後に、ノードの状態の削除を行う必要があります。

ノードのシャットダウンを準備する際は、削除するノード (最後に追加された仮想マシン スケール セット インスタンス) を探して非アクティブ化します。 仮想マシン スケール セット インスタンスには、追加された順序で番号が付けられるため、ノノードの名前 (基になる仮想マシン スケール セット インスタンス名と一致します) に含まれる数値サフィックスを比較することによって新しいノードを見つけることができます。 

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

削除するノードが見つかったら、非アクティブ化し、削除します。これには前に使ったのと同じ `FabricClient` インスタンスと `IAzure` インスタンスを使用します。

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

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
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

スクリプティングの方が適している場合は、スケールアウトと同様に、仮想マシン スケール セット容量を変更するために PowerShell コマンドレットを使用することもできます。 仮想マシン インスタンスを削除したら、Service Fabric ノードの状態を削除することができます。

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>次のステップ

自動スケールのロジックを独自に実装するには、次の概念と、便利な API をご確認ください。

- [手動でのスケーリングまたは自動スケール ルールを使用したスケーリング](./service-fabric-cluster-scale-in-out.md)
- [.NET 用 Azure Management ライブラリ](https://github.com/Azure/azure-libraries-for-net) (Service Fabric クラスターの基盤となる仮想マシン スケール セットの操作に便利です)
- [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient) (Service Fabric クラスターとそのノードの操作に便利です)
