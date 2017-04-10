---
title: "Azure CLI を使用した複数の IP 構成での負荷分散 | Microsoft Docs"
description: "Azure CLI または Resource Manager を使用して仮想マシンに複数の IP アドレスを割り当てる方法を説明します。"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: fef0d6007aa3f9357d7288033220a7d5d6eb5a49
ms.lasthandoff: 03/22/2017


---
# <a name="load-balancing-on-multiple-ip-configurations"></a>複数の IP 構成での負荷分散

> [!div class="op_single_selector"]
> * [ポータル](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)

この記事では、1 つのセカンダリ ネットワーク インターフェイス (NIC) に複数の IP アドレスがある Azure Load Balancer の使用方法について説明します。 このシナリオには、Windows を実行する 2 つの VM があり、各 VM にプライマリ NIC とセカンダリ NIC が 1 つずつ存在します。 セカンダリ NIC には、どちらも 2 つの IP 構成が割り当てられています。 それぞれの VM は、contoso.com と fabrikam.com の両方の Web サイトをホストします。 それぞれの Web サイトは、セカンダリ NIC の IP 構成の 1 つにバインドされています。 ここで、Azure Load Balancer を使用して、それぞれの Web サイト用に 2 つのフロントエンド IP アドレスを公開して、トラフィックを Web サイトのそれぞれの IP 構成に分散します。 このシナリオでは、両方のバックエンド プール IP アドレスに加えて、両方のフロントエンドで同じポート番号を使用します。

![LB シナリオの画像](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>複数の IP 構成で負荷分散を行うための手順

この記事で紹介するシナリオを実現するには、次の手順に従います。

1. [この記事](../cli-install-nodejs.md)の手順に従って、Azure CLI をインストールして構成し、Azure アカウントにログインします。
2. 上記のように *contosofabrikam* という名前の[リソース グループを作成](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations)します。

    ```azurecli
    azure group create contosofabrikam westcentralus
    ```

3. 2 つの VM の[可用性セットを作成](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set)します。 このシナリオでは、次のコマンドを使用します。

    ```azurecli
    azure availset create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. *myVNet* という名前の[仮想ネットワークを作成](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet)し、*mySubnet* という名前のサブネットを作成します。

    ```azurecli
    azure network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus

    azure network vnet subnet create --resource-group contosofabrikam --vnet-name myVnet --name mySubnet --address-prefix 10.0.0.0/24
    ```

5. *mylb* という名前の[ロード バランサーを作成](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-load-balancer-and-ip-pools)します。

    ```azurecli
    azure network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. ロード バランサーのフロントエンド IP 構成用に 2 つの動的パブリック IP アドレスを作成します。

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. *contosofe* と *fabrikamfe* の 2 つのフロントエンド IP 構成を作成します。

    ```azurecli
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. バックエンド アドレス プール (*contosopool*、*fabrikampool*)、[プローブ](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-load-balancer-health-probe)  - (*HTTP*)、負荷分散規則 (*HTTPc*、*HTTPf*) を作成します。

    ```azurecli
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    azure network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. 次のコマンドを実行します。その出力を調べて、[ロード バランサーが正常に作成されたことを確認](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#verify-the-load-balancer)します。

    ```azurecli
    azure network lb show --resource-group contosofabrikam --name mylb
    ```

10. 次に示すように、1 つ目の仮想マシン VM1 の[パブリック IP](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address) (*myPublicIp*) と[ストレージ アカウント](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account) (*mystorageaccont1*) を作成します。

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. 次に示すように、VM1 の[ネットワーク インターフェイスを作成](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-nic-to-use-with-the-linux-vm)し、2 番目の IP 構成 (*VM1-ipconfig2*) を追加して [VM を作成](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms)します。

    ```azurecli
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic1 --ip-config-name NIC1-ipconfig1
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic2 --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name VM1Nic2 --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-names VM1Nic1,VM1Nic2  --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. 2 番目の VM に対して手順 10. ～ 11. を繰り返します。

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount2
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic1
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name VM2Nic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-names VM2Nic1,VM2Nic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. 最後に、Load Balancer の各フロントエンド IP アドレスを指すように DNS リソース レコードを構成する必要があります。 ドメインを Azure DNS でホストする場合もあります。 Azure DNS を Load Balancer で使用する方法の詳細については、「[Azure DNS を他の Azure サービスで使用する](../dns/dns-for-azure-services.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
- Azure で負荷分散サービスを組み合わせて使う方法について詳しくは、「[Azure で負荷分散サービスを使用する](../traffic-manager/traffic-manager-load-balancing-azure.md)」をご覧ください。
- Azure の各種ログを使って、ロード バランサーの管理やトラブルシューティングを行う方法については、「[Azure Load Balancer のログ分析](../load-balancer/load-balancer-monitor-log.md)」をご覧ください。

