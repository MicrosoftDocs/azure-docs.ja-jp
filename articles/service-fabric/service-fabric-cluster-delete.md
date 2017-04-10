---
title: "Azure クラスターとそのリソースの削除 | Microsoft Docs"
description: "クラスターを含むリソース グループを削除するか、リソースを選択的に削除して、Service Fabric クラスターを完全に削除する方法について学習します。"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: de422950-2d22-4ddb-ac47-dd663a946a7e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: a957a70be915459baa8c687c92e251c6011b6172
ms.openlocfilehash: e73334373e9909069641cd4ad2c3280e51b5e2db
ms.lasthandoff: 12/06/2016


---
# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Azure の Service Fabric クラスターとその使用リソースの削除
Service Fabric クラスターは、クラスター リソース自体に加え、その他の多くの Azure リソースで構成されます。 そのため、Service Fabric クラスターを完全に削除するには、構成されるすべてのリソースも削除する必要があります。
オプションは 2 つあります。クラスターのあるリソース グループを削除 (クラスター リソースとリソース グループ内のその他のすべてのリソースが削除されます) するか、クラスター リソースとその関連するリソース (リソース グループ内のその他のリソースは対象から除かれます) を個別に削除します。

> [!NOTE]
> クラスター リソースを削除しても、Service Fabric クラスターを構成しているその他のすべてのリソースが削除されることは **ありません** 。
> 
> 

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>Service Fabric クラスターがあるリソース グループ (RG) 全体の削除
これは、リソース グループを含む、クラスターに関連付けられているすべてのリソースを確実に削除する最も簡単な方法です。 PowerShell または Azure ポータルを使用して、リソース グループを削除できます。 Service fabric クラスターに関連していないリソースがリソース グループにある場合は、特定のリソースを削除できます。

### <a name="delete-the-resource-group-using-azure-powershell"></a>Azure PowerShell を使用したリソース グループの削除
次の Azure PowerShell コマンドレットを実行して、リソース グループを削除することもできます。 Azure PowerShell 1.0 以降がコンピューターにインストールされていることを確認します。 まだインストールされていない場合は、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)

PowerShell ウィンドウを開き、次の PS コマンドレットを実行します。

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

*-Force* オプションを使用しなかった場合、削除の確認を求めるメッセージが表示されます。 確認すると、RG とそれに含まれるすべてのリソースが削除されます。

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Azure ポータルでのリソース グループの削除
1. [Azure ポータル](https://portal.azure.com)にログインします。
2. 削除する Service Fabric クラスターに移動します。
3. クラスターの [要点] ページでリソース グループの名前をクリックします。
4. これにより、 **リソース グループの [要点]** ページが表示されます。
5. [ **削除**] をクリックします。
6. リソース グループの削除を完了するには、そのページに表示される指示に従います。

![リソース グループの削除][ResourceGroupDelete]

## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>クラスター リソースと、クラスターが使用するリソース (リソース グループ内のその他のリソースを除く) の削除
リソース グループに、削除する Service Fabric クラスターに関連付けられているリソースのみがある場合は、リソース グループ全体を削除する方が簡単です。 リソース グループ内のリソースを 1 つずつ選択して削除する場合は、次の手順を実行します。

ポータルを使用するか、テンプレート ギャラリーに含まれる Service Fabric の Resource Manager テンプレートのいずれかを使用してクラスターをデプロイした場合は、クラスターで使用するすべてのリソースに次の 2 つのタグが付けられています。 それらを使用して、削除するリソースを決定することができます。

"***タグ 1:***" キー = clusterName、値 = <クラスターの名前>

"***タグ 2:***" キー = resourceName、値 = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>Azure ポータルでの特定のリソースの削除
1. [Azure ポータル](https://portal.azure.com)にログインします。
2. 削除する Service Fabric クラスターに移動します。
3. [要点] ブレードの **[すべての設定]** に移動します。
4. [設定] ブレードの **[リソース管理]** で、**[タグ]** をクリックします。
5. [タグ] ブレードでいずれかの **タグ** をクリックして、そのタグを持つすべてのリソースの一覧を取得します。
   
    ![リソース タグ][ResourceTags]
6. タグが付けられたリソースの一覧を取得したら、各リソースをクリックして削除します。
   
    ![タグ付けされたリソース][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Azure PowerShell を使用したリソースの削除
次の Azure PowerShell コマンドレットを実行して、リソースを 1 つずつ削除できます。 Azure PowerShell 1.0 以降がコンピューターにインストールされていることを確認します。 まだインストールされていない場合は、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)

PowerShell ウィンドウを開き、次の PS コマンドレットを実行します。

```powershell
Login-AzureRmAccount
```
削除するリソースごとに、次を実行します。

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

クラスター リソースを削除するには、次を実行します。

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>次のステップ
次を確認して、クラスターのアップグレードとサービスのパーティション分割についても学習してください。

* [クラスターのアップグレードの詳細](service-fabric-cluster-upgrade.md)
* [最大のスケールに対応するためのステートフル サービスのパーティション分割の詳細](service-fabric-concepts-partitioning.md)

<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG

