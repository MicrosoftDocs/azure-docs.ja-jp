---
title: "Service Fabric クラスターのスケールインとスケールアウト | Microsoft Docs"
description: "ノードの種類/仮想マシン スケール セットごとに自動スケール ルールを設定し、Service Fabric クラスターを需要に合わせてスケールインまたはスケールアウトします。 Service Fabric クラスターのノードの追加または削除"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2017
ms.author: chackdan
ms.openlocfilehash: d26a97ee0e5416fb1fe38ef0fb18fa4eb0e2963d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules"></a>自動スケール ルールを使用した Service Fabric クラスターのスケールインとスケールアウト
仮想マシン スケール セットは、セットとして仮想マシンのコレクションをデプロイおよび管理するために使用できる Azure コンピューティング リソースです。 Service Fabric クラスターで定義されているすべてのノード タイプは、個別の仮想マシン スケール セットとしてセットアップされます。 各ノードの種類は、個別にスケールインまたはスケールアウトでき、さまざまなセットのポートを開き、異なる容量のメトリックスを持つことができます。 詳細については、 [Service Fabric のノードの種類](service-fabric-cluster-nodetypes.md) に関するドキュメントを参照してください。 クラスター内の Service Fabric のノードの種類はバックエンドの仮想マシン スケール セットで構成されるため、ノードの種類/仮想マシン スケール セットごとに自動スケール ルールを設定する必要があります。

> [!NOTE]
> サブスクリプションに、このクラスターを構成する新しい VM を追加できるだけのコア数が割り当てられている必要があります。 現時点ではモデルの検証はないため、いずれかのクォータの制限に達した場合、デプロイ時のエラーが表示されます。
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>スケールするノードの種類/仮想マシン スケール セットの選択
現在、ポータルを使用して仮想マシン スケール セットの自動スケール ルールを指定することはできないため、Azure PowerShell (1.0 以降) を使用して、ノードの種類を一覧表示し、それらに自動スケール ルールを追加します。

クラスターを構成する仮想マシン スケール セットの一覧を取得するには、次のコマンドレットを実行します。

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <Virtual Machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>ノードの種類/仮想マシン スケール セットの自動スケール ルールの設定
クラスターに複数のノードの種類がある場合は、スケール (インまたはアウト) するノードの種類/仮想マシン スケール セットごとにこれを繰り返します。 自動スケールを設定する前に、必要なノードの数を考慮します。 ノードの種類がプライマリである場合に必要なノードの最小数は、選択した信頼性のレベルによって決まります。 信頼性のレベルの詳細については、 [こちら](service-fabric-cluster-capacity.md)を参照してください。

> [!NOTE]
> ノードの種類がプライマリである場合に前述の最小数未満にスケールダウンすると、クラスターが不安定になるか、停止します。 これにより、アプリケーションおよびシステム サービスのデータが失われる可能性があります。
> 
> 

現在、自動スケール機能は、アプリケーションによって Service Fabric に報告されている場合がある負荷では駆動されません。 したがって現時点では、取得する自動スケールは、各仮想マシン スケール セット インスタンスによって出力されるパフォーマンス カウンターのみで駆動されます。  

仮想マシン スケール セットごとに自動スケールを設定するには、[この手順](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)に従います。

> [!NOTE]
> スケールダウン シナリオでは、ノードの種類に Gold または Silver の耐久性レベルがない限り、適切なノードの名前を指定して、 [Remove-ServiceFabricNodeState cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) を呼び出す必要があります。
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>ノードの種類/仮想マシン スケール セットに VM を手動で追加する
[クイック スタート テンプレート ギャラリー](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) のサンプル/手順に従って、各 Nodetype の VM 数を変更します。 

> [!NOTE]
> VM の追加には時間がかかるため、すぐに追加されることを想定しないでください。 容量の追加には十分な時間を確保するように計画します。つまり、レプリカ/サービス インスタンスを配置する VM 容量が使用可能になるまでの時間として、10 分以上を考慮してください。
> 
> 

## <a name="manually-remove-vms-from-the-primary-node-typevirtual-machine-scale-set"></a>プライマリ ノードの種類/仮想マシン スケール セットから VM を手動で削除する
> [!NOTE]
> Service Fabric システム サービスは、クラスター内のプライマリ ノードの種類で実行されます。 したがって、このサービスをシャットダウンしたり、そのノードの種類のインスタンス数を、信頼性レベルで保証するよりも少ない数にスケールダウンしたりしないでください。 [こちらの信頼性レベルの詳細](service-fabric-cluster-capacity.md)に関するページをご覧ください。 
> 
> 

一度に 1 つの VM インスタンスに対して、次の手順を実行します。 これにより、削除する VM インスタンス上でシステム サービス (およびステートフル サービス) を正規の手順でシャットダウンし、新しいレプリカを別のノードに作成できます。

1. [Disable-ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) を、インテント "RemoveNode" を指定して実行し、削除するノードを無効にします (そのノードの種類の最上位インスタンス)。
2. [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) を実行して、ノードが無効になったことを確認します。 無効になっていない場合は、無効になるまで待ちます。 この手順を高速化することはできません。
3. [クイック スタート テンプレート ギャラリー](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) のサンプル/手順に従って、その Nodetype の VM 数を 1 だけ変更します。 削除されたインスタンスは、最上位の VM インスタンスです。 
4. 必要に応じて手順 1. ～ 3. を繰り返します。ただし、プライマリ ノードの種類のインスタンス数を、信頼性レベルで保証するよりも少ない数にスケールダウンしないでください。 [こちらの信頼性レベルの詳細](service-fabric-cluster-capacity.md)に関するページをご覧ください。 

## <a name="manually-remove-vms-from-the-non-primary-node-typevirtual-machine-scale-set"></a>プライマリ以外のノードの種類/仮想マシン スケール セットから VM を手動で削除する
> [!NOTE]
> ステートフル サービスについては、可用性を維持し、サービスの状態を保持するために、所定の数のノードが常に稼働している必要があります。 少なくとも、パーティション/サービスのターゲット レプリカ セットと同じ数のノードが必要です。 
> 
> 

一度に 1 つの VM インスタンスに対して、次の手順を実行します。 これにより、削除する VM インスタンス上でシステム サービス (およびステートフル サービス) を正規の手順でシャットダウンし、新しいレプリカを他の場所に作成できます。

1. [Disable-ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) を、インテント "RemoveNode" を指定して実行し、削除するノードを無効にします (そのノードの種類の最上位インスタンス)。
2. [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) を実行して、ノードが無効になったことを確認します。 無効になっていない場合は、無効になるまで待ちます。 この手順を高速化することはできません。
3. [クイック スタート テンプレート ギャラリー](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) のサンプル/手順に従って、その Nodetype の VM 数を 1 だけ変更します。 これにより、最上位の VM インスタンスが削除されます。 
4. 必要に応じて手順 1. ～ 3. を繰り返します。ただし、プライマリ ノードの種類のインスタンス数を、信頼性レベルで保証するよりも少ない数にスケールダウンしないでください。 [こちらの信頼性レベルの詳細](service-fabric-cluster-capacity.md)に関するページをご覧ください。

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Service Fabric Explorer で確認できる動作
クラスターをスケールアップすると、Service Fabric Explorer に、クラスターの一部であるノード (仮想マシン スケール セット インスタンス) の数が反映されます。  ただし、クラスターをスケールダウンすると、適切なノードの名前を指定して [Remove-ServiceFabricNodeState cmd](https://msdn.microsoft.com/library/mt125993.aspx) を呼び出さない限り、削除されたノード/VM インスタンスが異常状態を示したまま表示されます。   

この動作についての説明を次に示します。

Service Fabric Explorer で示されているノードは、現在/過去におけるクラスターのノード数について、Service Fabric システム サービス (具体的には FM) が認識している内容が反映されたものです。 仮想マシン スケール セットをスケールダウンした場合、VM が削除された一方で、FM システム サービスはノード (削除された VM にマップされていたもの) が復元されると認識したままとなります。 そのため、(正常性状態がエラーまたは不明になる場合でも) Service Fabric Explorer はそのノードを表示し続けます。

VM が削除されたときに、ノードが削除されるようにするには、2 つのオプションを使用できます。

1) クラスターのノードの種類に Gold または Silver の耐久性レベルを選択します。これにより、インフラストラクチャの統合が提供されます。 これによって、スケールダウンしたときに、システム サービス (FM) の状態から自動的にノードが削除されます。
[耐久性レベルの詳細](service-fabric-cluster-capacity.md)に関するページをご覧ください

2) VM インスタンスがスケールダウンされたら、[Remove-ServiceFabricNodeState コマンドレット](https://msdn.microsoft.com/library/mt125993.aspx)を呼び出す必要があります。

> [!NOTE]
> Service Fabric クラスターが可用性を維持し、状態を保持するには、一定数のノードが常にアップしている必要があります。これは、"維持クォーラム" と呼ばれます。 そのため、先に[状態の完全バックアップ](service-fabric-reliable-services-backup-restore.md)を実行していた場合を除き、クラスター内のすべてのコンピューターをシャットダウンするのは一般に安全ではありません。
> 
> 

## <a name="next-steps"></a>次のステップ
次を確認して、クラスター容量の計画、クラスターのアップグレード、およびサービスのパーティション分割についても学習してください。

* [クラスター容量の計画](service-fabric-cluster-capacity.md)
* [クラスターのアップグレード](service-fabric-cluster-upgrade.md)
* [最大のスケールに対応するためのパーティションのステートフル サービス](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
