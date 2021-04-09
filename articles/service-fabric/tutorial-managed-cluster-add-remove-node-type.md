---
title: Service Fabric マネージド クラスターのノード タイプを追加および削除する (プレビュー)
description: このチュートリアルでは、Service Fabric マネージド クラスターのノード タイプを追加および削除する方法について説明します。
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: bb33512652677fc4e46d8ba3668dca985bbcfe01
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791242"
---
# <a name="tutorial-add-and-remove-node-types-from-a-service-fabric-managed-cluster-preview"></a>チュートリアル:Service Fabric マネージド クラスターのノード タイプを追加および削除する (プレビュー)

このチュートリアル シリーズでは、次のことについて説明します。

> [!div class="checklist"]
> * [Service Fabric マネージド クラスターをデプロイする方法](tutorial-managed-cluster-deploy.md)
> * [Service Fabric マネージド クラスターをスケールアウトする方法](tutorial-managed-cluster-scale.md)
> * Service Fabric マネージド クラスターにノードを追加および削除する方法
> * [Service Fabric マネージド クラスターにアプリケーションをデプロイする方法](tutorial-managed-cluster-deploy-app.md)

シリーズのこの部分では、次を行う方法について説明します。

> [!div class="checklist"]
> * Service Fabric マネージド クラスターにノード タイプを追加する
> * Service Fabric マネージド クラスターからノード タイプを削除する

## <a name="prerequisites"></a>前提条件

* Service Fabric マネージド クラスター ([*マネージド クラスターのデプロイ*](tutorial-managed-cluster-deploy.md) に関するページを参照)。
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) 以降 (「[*Azure PowerShell のインストール*](/powershell/azure/install-az-ps)」を参照)。

## <a name="add-a-node-type-to-a-service-fabric-managed-cluster"></a>Service Fabric マネージド クラスターにノード タイプを追加する

Azure Resource Manager テンプレート、PowerShell、または CLI を使用して、Service Fabric マネージド クラスターにノード タイプを追加できます。 このチュートリアルでは、Azure PowerShell を使用してノード タイプを追加します。

新しいノード タイプを作成するには、以下の 3 つのプロパティを定義する必要があります。

* **ノード タイプ名**:クラスター内のどの既存のノード タイプにもない名前。
* **インスタンス数**:新しいノード タイプの初期ノード数。
* **[VM サイズ]** :ノードの VM SKU。 指定されていない場合は、既定値の *Standard_D2* が使用されます。

> [!NOTE]
> 追加するノード タイプがクラスター内の最初のノード タイプまたは唯一のノード タイプである場合は、Primary プロパティを使用する必要があります。

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "nt2"
$vmSize = "Standard_D2_v2"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeTypeName -InstanceCount 3 -vmSize $vmSize
```

## <a name="remove-a-node-type-from-a-service-fabric-managed-cluster"></a>Service Fabric マネージド クラスターからノード タイプを削除する

Service Fabric マネージド クラスターからノード タイプを削除するには、PowerShell または CLI を使用する必要があります。 このチュートリアルでは、Azure PowerShell を使用してノード タイプを削除します。

> [!NOTE]
> あるプライマリ ノード タイプが、クラスター内で唯一のプライマリ ノード タイプである場合、それを削除することはできません。  

ノード タイプを削除するには:

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "myCluster"
$nodeTypeName = "nt2"

Remove-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName  -Name $nodeTypeName
```

## <a name="next-steps"></a>次の手順

 このセクションでは、ノード タイプを追加および削除しました。 Service Fabric マネージド クラスターにアプリケーションをデプロイする方法については、次を参照してください。

> [!div class="nextstepaction"]
> [Service Fabric マネージド クラスターにアプリをデプロイする](tutorial-managed-cluster-deploy-app.md)
