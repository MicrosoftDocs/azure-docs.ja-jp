---
title: "Azure Service Fabric のノードの種類と仮想マシン スケール セット | Microsoft Docs"
description: "Azure Service Fabric のノードの種類を仮想マシン スケール セットに関連付ける方法のほか、スケール セット インスタンスまたはクラスター ノードにリモートで接続する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: chackdan
ms.openlocfilehash: 2bd3053d645d9acd4850fddf7f27237ff954e8c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric のノードの種類と仮想マシン スケール セット
仮想マシン スケール セットは、Azure コンピューティング リソースです。 スケール セットを使用すると、仮想マシンのコレクションをセットとしてデプロイおよび管理できます。 Azure Service Fabric クラスターで定義するノードの種類ごとに、別個のスケール セットを設定します。 各ノードの種類は、個別にスケールアップまたはスケールダウンしたり、異なるポートのセットを開いたり、別の容量メトリックを使用したりすることができます。

次の図は、2 種類のノードがあるクラスターを示しています (ノードの名前は FrontEnd と BackEnd)。 各ノードの種類には、5 つのノードがあります。

![2 種類のノードがあるクラスター][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>仮想マシン スケール セットのインスタンスをノードにマップする
前の図に示されているように、スケール セットのインスタンスはインスタンス 0 で始まり、1 つずつ増えていきます。 番号はノード名に反映されます。 たとえば、ノード BackEnd_0 は、BackEnd スケール セットのインスタンス 0 です。 この特定のスケール セットには、BackEnd_0、BackEnd_1、BackEnd_2、BackEnd_3、BackEnd_4 という名前の 5 つのインスタンスがあります。

スケール セットをスケールアップすると、新しいインスタンスが作成されます。 通常、新しいスケール セットのインスタンス名は、スケール セットの名前に次のインスタンス番号を付加したものになります。 この例では、BackEnd_5 です。

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>スケール セットのロード バランサーをノードの種類とスケール セットにマップする
Azure Portal でクラスターをデプロイした場合、またはサンプル Azure Resource Manager テンプレートを使用した場合、リソース グループのすべてのリソースが一覧に表示されます。 また、各スケール セットまたはノードの種類のロード バランサーを確認できます。 ロード バランサー名には、**LB-&lt;ノードの種類の名前&gt;** という形式が使用されます。 たとえば、次の図で示すように、LB-sfcluster4doc-0 などです。

![リソース][Resources]
## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>仮想マシン スケール セット インスタンスまたはクラスター ノードにリモート接続する
クラスターで定義されたノードの種類ごとに、個別のスケール セットを設定します。 ノードの種類を個別にスケールアップまたはスケールダウンすることができます。 異なる VM SKU を使用することもできます。 単一インスタンス VM とは異なり、スケール セット インスタンスには独自の仮想 IP アドレスがありません。 そのため、特定のインスタンスにリモート接続するために使用できる IP アドレスとポートの検索が難しい場合があります。

特定のインスタンスにリモート接続するために使用できる IP アドレスとポートを検索するには、以下の手順を実行します。

**手順 1**: リモート デスクトップ プロトコル (RDP) の受信 NAT 規則を取得して、ノードの種類の仮想 IP アドレスを検索します。

まず、`Microsoft.Network/loadBalancers` のリソース定義の一部として定義された受信 NAT 規則の値を取得します。

Azure Portal のロード バランサー ページで、**[設定]** > **[受信 NAT 規則]** の順に選択します。 これで、最初のスケール セット インスタンスにリモート接続するために使用できる IP アドレスとポートを取得できます。 

![Load Balancer][LBBlade]

次の図では、IP アドレスとポートは **104.42.106.156** と **3389** です。

![NAT 規則][NATRules]

**手順 2**: 特定のスケール セット インスタンスまたはノードにリモート接続するために使用できるポートを検索します。

スケール セット インスタンスをノードにマップします。 使用するポートを正確に判断するために、スケール セットの情報を使用します。

ポートは、スケール セット インスタンスと一致する昇順で割り当てられます。 次の表では、ノードの種類が FrontEnd である前の例について、5 つのノード インスタンスの各ポートを示しています。 スケール セット インスタンスに同じマッピングを適用します。

| **仮想マシン スケール セットのインスタンス** | **ポート** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

**手順 3**: 特定のスケール セット インスタンスにリモート接続します。

次の図は、リモート デスクトップ接続を使用して FrontEnd_1 スケール セット インスタンスに接続する場合を示しています。

![リモート デスクトップ接続][RDP]

## <a name="change-the-rdp-port-range-values"></a>RDP ポート範囲の値を変更する

### <a name="before-cluster-deployment"></a>クラスター デプロイの前
Resource Manager テンプレートを使用してクラスターを設定する場合、`inboundNatPools` で範囲を指定します。

`Microsoft.Network/loadBalancers` のリソース定義に移動します。 `inboundNatPools` の説明を探します。  `frontendPortRangeStart` と `frontendPortRangeEnd` の各値を置き換えます。

![inboundNatPools の値][InboundNatPools]

### <a name="after-cluster-deployment"></a>クラスター デプロイの後
クラスターのデプロイ後に RDP ポート範囲の値を変更すると、より複雑になります。 VM を再利用しないようにするには、Azure PowerShell を使用して新しい値を設定します。 

> [!NOTE]
> Azure PowerShell 1.0 以降のバージョンがコンピューターにインストールされていることを確認してください。 Azure PowerShell 1.0 以降のバージョンをまだインストールしていない場合は、[Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)に関するページで説明されている手順に従うことをお勧めします。

1. Azure アカウントにサインインします。 次の PowerShell コマンドが失敗する場合は、PowerShell が正しくインストールされていることを確認してください。

    ```
    Login-AzureRmAccount
    ```

2. ロード バランサーの詳細を取得し、`inboundNatPools` の説明の値を表示するには、次のコードを実行します。

    ```
    Get-AzureRmResource -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
    ```

3. `frontendPortRangeEnd` と `frontendPortRangeStart` を適切な値に設定します。

    ```
    $PropertiesObject = @{
        #Property = value;
    }
    Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name> -ApiVersion <use the API version that is returned> -Force
    ```

## <a name="change-the-rdp-user-name-and-password-for-nodes"></a>ノードの RDP ユーザー名とパスワードを変更する

特定のノードの種類についてすべてのノードのパスワードを変更するには、以下の手順を実行します。 これらの変更は、スケール セットの現在のノードと今後のノードすべてに適用されます。

1. PowerShell を管理者として開きます。 
2. ログインしてセッションのサブスクリプションを選択するには、次のコマンドを実行します。 `SUBSCRIPTIONID` パラメーターを自分のサブスクリプション ID に変更します。 

    ```powershell
    Login-AzureRmAccount
    Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
    ```

3. 次のスクリプトを実行します。 関連する `NODETYPENAME`、`RESOURCEGROUP`、`USERNAME`、`PASSWORD` の値を使用してください。 `USERNAME` 値と `PASSWORD` 値は、今後の RDP セッションで使用する新しい資格情報になります。 

    ```powershell
    $nodeTypeName = 'NODETYPENAME'
    $resourceGroup = 'RESOURCEGROUP'
    $publicConfig = @{'UserName' = 'USERNAME'}
    $privateConfig = @{'Password' = 'PASSWORD'}
    $extName = 'VMAccessAgent'
    $publisher = 'Microsoft.Compute'
    $node = Get-AzureRmVmss -ResourceGroupName $resourceGroup -VMScaleSetName $nodeTypeName
    $node = Add-AzureRmVmssExtension -VirtualMachineScaleSet $node -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion '2.0' -AutoUpgradeMinorVersion $true

    Update-AzureRmVmss -ResourceGroupName $resourceGroup -Name $nodeTypeName -VirtualMachineScaleSet $node
    ```

## <a name="next-steps"></a>次のステップ
* ["任意の場所にデプロイ" 機能の概要と Azure で管理されるクラスターとの比較](service-fabric-deploy-anywhere.md)に関するページを参照します。
* [クラスター セキュリティ](service-fabric-cluster-security.md)について学習します。
* [Service Fabric SDK と概要](service-fabric-get-started.md)について学習します。

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png
