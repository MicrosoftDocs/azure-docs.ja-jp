---
title: 仮想マシン スケール セットで使用される既存のロード バランサーを更新または削除する
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: このハウツー記事に従って、Azure Standard Load Balancer と仮想マシン スケール セットの使用を開始します。
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: 52f2a2ed301bf734ad605a2ee68a0ab672a97014
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218725"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>仮想マシン スケール セットで使用されるロード バランサーを更新または削除する

仮想マシン スケール セットと Azure Load Balancer のインスタンスを使用すると、次の操作を実行できます。

- 規則を追加、更新、削除する。
- 構成を追加する。
- ロード バランサーを削除する。

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>仮想マシン スケール セットをスケールアウトするためにロード バランサーを設定する

Azure Load Balancer のインスタンスに[インバウンド NAT プール](/cli/azure/network/lb/inbound-nat-pool)が設定されていること、および仮想マシン スケール セットがロード バランサーのバックエンド プールに配置されていることを確認します。 新しい仮想マシン インスタンスが仮想マシン スケール セットに追加されると、Load Balancer によって、インバウンド NAT プールに新しいインバウンド NAT 規則が自動的に作成されます。

インバウンド NAT プールが適切に設定されているかどうかを確認するには、次の手順を行います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側のメニューで、**[すべてのリソース]** を選択します。 リソースの一覧から **[MyLoadBalancer]** を選択します。
1. **[設定]** で、 **[インバウンド NAT 規則]** を選択します。 右ペインに、仮想マシン スケール セット内の個々のインスタンスごとに作成された規則の一覧が表示されている場合は、すべての設定が完了しているので、いつでもスケールアップできます。

## <a name="add-inbound-nat-rules"></a>インバウンド NAT 規則を追加する

個々のインバウンド NAT 規則を追加することはできません。 ただし、仮想マシン スケール セット内のすべてのインスタンスのフロントエンド ポート範囲とバックエンド ポートが定義されたインバウンド NAT 規則のセットを追加することは可能です。

仮想マシン スケール セットのインバウンド NAT 規則のセット全体を追加するには、最初にロード バランサーでインバウンド NAT プールを作成します。 次に、仮想マシン スケール セットのネットワーク プロファイルからインバウンド NAT プールを参照します。 CLI を使用した完全な例を示します。

新しいインバウンド NAT プールのフロントエンド ポート範囲が、既存のインバウンド NAT プールと重複しないようにする必要があります。 既存のインバウンド NAT プールの設定を表示するには、この [CLI コマンド](/cli/azure/network/lb/inbound-nat-pool#az_network_lb_inbound_nat_pool_list)を使用します。
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIp
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>インバウンド NAT 規則を更新する

個々のインバウンド NAT 規則を更新することはできません。 ただし、仮想マシン スケール セット内のすべてのインスタンスのフロントエンド ポート範囲とバックエンド ポートが定義されたインバウンド NAT 規則のセットを更新することは可能です。

仮想マシン スケール セットのインバウンド NAT 規則のセット全体を更新するには、ロード バランサーでインバウンド NAT プールを更新します。
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>インバウンド NAT 規則を削除する

個々のインバウンド NAT 規則を削除することはできませんが、インバウンド NAT プールを削除することで一連のインバウンド NAT 規則全体を削除することはできます。

NAT プールを削除するには、まず、スケール セットから削除します。 CLI を使用した完全な例を次に示します。

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
     az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
    az network lb inbound-nat-pool delete
       --resource-group MyResourceGroup
       -–lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>複数の IP 構成を追加する

複数の IP 構成を追加するには、次の手順に従います。

1. 左側のメニューで、**[すべてのリソース]** を選択します。 リソースの一覧から **[MyLoadBalancer]** を選択します。
1. **[設定]** で、**[フロントエンド IP の構成]** を選択します。 その後、 **[追加]** を選択します。
1. **[フロントエンド IP アドレスの追加]** ページで値を入力し、 **[OK]** を選択します。
1. 新しい負荷分散規則が必要な場合は、このチュートリアルの[ステップ 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) と[ステップ 6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) に従ってください。
1. 必要に応じて、新しく作成したフロントエンド IP 構成を使用して、インバウンド NAT 規則の新しいセットを作成します。 前のセクションで例を確認できます。

## <a name="multiple-virtual-machine-scale-sets-behind-a-single-load-balancer"></a>1 つの Load Balancer の背後に複数の Virtual Machine Scale Sets

Load Balancer でインバウンド NAT プールを作成し、仮想マシン スケール セットのネットワーク プロファイルでこのインバウンド NAT プールを参照し、最後に、変更を有効にするためにインスタンスを更新します。 すべての仮想マシン スケール セットに対してこの手順を繰り返します。

重複しないフロントエンド ポート範囲を持つ個別のインバウンド NAT プールを作成してください。
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
          
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool2
          --protocol Tcp 
          --frontend-port-range-start 100 
          --frontend-port-range-end 109 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig2
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS2 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool2'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS2
```

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>仮想マシン スケール セットで使用されているフロントエンド IP 構成を削除する

スケール セットで使用されているフロントエンド IP 構成を削除するには、次の手順に従います。

 1. まず、フロントエンド IP 構成を参照するインバウンド NAT プール (インバウンド NAT 規則のセット) を削除します。 インバウンド規則を削除する方法については、前のセクションを参照してください。
 1. フロントエンド IP 構成を参照する負荷分散規則を削除します。
 1. フロントエンド IP 構成を削除します。

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>仮想マシン スケール セットで使用されるロード バランサーを削除する

スケール セットで使用されているフロントエンド IP 構成を削除するには、次の手順に従います。

 1. まず、フロントエンド IP 構成を参照するインバウンド NAT プール (インバウンド NAT 規則のセット) を削除します。 インバウンド規則を削除する方法については、前のセクションを参照してください。
 1. 仮想マシン スケール セットを含むバックエンド プールを参照する負荷分散規則を削除します。
 1. 仮想マシン スケール セットのネットワーク プロファイルから `loadBalancerBackendAddressPool` 参照を削除します。
 
 CLI を使用した完全な例を次に示します。

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
    az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
```
最後に、ロード バランサーのリソースを削除します。
 
## <a name="next-steps"></a>次のステップ

Azure Load Balancer と仮想マシン スケール セットの詳細については、概念の詳細をご覧ください。

> [Azure Load Balancer と仮想マシン スケール セット](load-balancer-standard-virtual-machine-scale-sets.md)
