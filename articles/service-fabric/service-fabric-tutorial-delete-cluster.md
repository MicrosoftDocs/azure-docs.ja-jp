---
title: Azure で Service Fabric クラスターを削除する | Microsoft Docs
description: このチュートリアルでは、Azure でホストされている Service Fabric クラスターとそのすべてのリソースを削除する方法について説明します。 クラスターを含むリソース グループを削除するか、リソースを選択して削除することができます。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: c6fd4234257cbb19580a5b406750cc72d03609c0
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598867"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>チュートリアル:Azure で実行されている Service Fabric クラスターを削除する

このチュートリアルはシリーズの第 5 部です。Azure で実行されている Azure Service Fabric クラスターを削除する方法について説明します。 Service Fabric クラスターを完全に削除するには、そのクラスターによって使用されているリソースも削除する必要があります。 オプションは 2 つあります。クラスターのあるリソース グループを削除 (クラスター リソースとリソース グループ内のその他すべてのリソースが削除されます) するか、クラスター リソースとその関連するリソース (リソース グループ内のその他のリソースは対象から除かれます) を個別に削除します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * リソース グループとそのすべてのリソースを削除する
> * リソース グループからリソースを選択して削除する

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * テンプレートを使用して Azure にセキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)を作成する
> * [クラスターを監視する](service-fabric-tutorial-monitor-cluster.md)
> * [クラスターをスケールインまたはスケールアウトする](service-fabric-tutorial-scale-cluster.md)
> * [クラスターのランタイムをアップグレードする](service-fabric-tutorial-upgrade-cluster.md)
> * クラスターの削除


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションを持っていない場合は[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成する
* [Azure Powershell](https://docs.microsoft.com/powershell/azure//install-Az-ps) または [Azure CLI](/cli/azure/install-azure-cli) をインストールする。
* セキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)を Azure に作成します

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>Service Fabric クラスターを含むリソース グループを削除する
クラスターと、そのクラスターによって使用されるすべてのリソースを削除するための最も簡単な方法は、リソース グループを削除することです。

Azure にサインインして、クラスターを削除するサブスクリプション ID を選択します。  サブスクリプション ID は、[Azure Portal](https://portal.azure.com) にログインして確認できます。 リソース グループとそのグループのクラスター リソースすべてを削除するには、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドレットまたは [az group delete](/cli/azure/group?view=azure-cli-latest) コマンドを使用します。

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $groupname -Force
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="selectively-delete-the-cluster-resource-and-the-associated-resources"></a>クラスター リソースと関連リソースを選択して削除する
リソース グループに、削除する Service Fabric クラスターに関連付けられているリソースのみがある場合は、リソース グループ全体を削除する方が簡単です。 リソース グループのリソースを選択して削除し、クラスターに関連付けられていないリソースを維持する場合、次の手順に従います。

リソース グループのリソースを一覧表示します。

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

削除するリソースごとに、次のスクリプトを実行します。

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

クラスター リソースを削除するには、次のスクリプトを実行します。

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "Microsoft.ServiceFabric/clusters" --resource-group $ResourceGroupName
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * リソース グループとそのすべてのリソースを削除する
> * リソース グループからリソースを選択して削除する

このチュートリアルはこれで完了です。次は以下の説明をお読みください。
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) を使用して Service Fabric クラスターを検査し、管理する方法。
* クラスター ノードの [Windows オペレーティング システムにパッチを適用す](service-fabric-patch-orchestration-application.md)る方法。
* [Windows クラスター](service-fabric-diagnostics-event-aggregation-wad.md)のイベントを集計および収集する方法と、クラスター イベントを監視するために [Log Analytics を設定](service-fabric-diagnostics-oms-setup.md)する方法。
