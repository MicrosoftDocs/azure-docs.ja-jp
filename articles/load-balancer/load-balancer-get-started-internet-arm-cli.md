---
title: "インターネットに接続するロード バランサーの作成 - Azure CLI | Microsoft Docs"
description: "Azure CLI を使用して、リソース マネージャーでインターネットに接続するロード バランサーを作成する方法について説明します"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 3b1780033cbc8aa3e108a213a4d2bfd0332fd7d7
ms.lasthandoff: 03/21/2017

---
# <a name="creating-an-internet-load-balancer-using-the-azure-cli"></a>Azure CLI を使用したインターネット ロード バランサーの作成

> [!div class="op_single_selector"]
> * [ポータル](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [テンプレート](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、リソース マネージャーのデプロイ モデルについて説明します。 [従来のデプロイを使用してインターネットに接続するロード バランサーを作成する方法](load-balancer-get-started-internet-classic-portal.md)

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-using-the-azure-cli"></a>Azure CLI を使用したソリューションのデプロイ

次の手順では、CLI で Azure Resource Manager を使用して、インターネットに接続するロード バランサーを作成する方法を示します。 Azure Resource Manager では、それぞれのリソースは個別に作成され構成された後、リソースを作成するためにまとまられます。

ロード バランサーをデプロイするには、次のオブジェクトを作成して構成する必要があります。

* フロントエンド IP 構成 - 受信ネットワーク トラフィックのパブリック IP アドレスが含まれます。
* バックエンド アドレス プール - ロード バランサーからネットワーク トラフィックを受信する、仮想マシンのネットワーク インターフェイス (NIC) が含まれます。
* 負荷分散規則 - ロード バランサーのパブリック ポートをバックエンド アドレス プール内のポートにマッピングする規則が含まれます。
* 受信 NAT 規則 - ロード バランサーのパブリック ポートをバックエンド アドレス プール内の特定の仮想マシンのポートにマッピングする規則が含まれます。
* プローブ - バックエンド アドレス プール内の仮想マシン インスタンスの可用性を確認するために使用する正常性プローブが含まれます。

詳細については、「 [Azure Resource Manager によるロード バランサーのサポート](load-balancer-arm.md)」を参照してください。

## <a name="set-up-cli-to-use-resource-manager"></a>Resource Manager を使用するための CLI のセットアップ

1. Azure CLI を初めて使用する場合は、「 [Azure CLI のインストール](../cli-install-nodejs.md) 」を参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。
2. 次に示すように、 **azure config mode** コマンドを実行してリソース マネージャー モードに切り替えます。

    ```azurecli
        azure config mode arm
    ```

    予想される出力:

        info:    New mode is arm

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Virtual Network と、フロント エンド IP プールのパブリック IP アドレスの作成

1. *NRPRG* という名前のリソース グループを使用して、米国東部の場所に *NRPVnet* という名前の仮想ネットワーク (VNet) を作成します。

    ```azurecli
        azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16
    ```

    *NRPVnetSubnet* という名前のサブネットを作成し、*NRPVnet* に 10.0.0.0/24 の CIDR ブロックを設定します。

    ```azurecli
        azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24
    ```

2. フロントエンド IP プールで使用される *NRPPublicIP* という名前のパブリック IP アドレスを作成します。DNS 名は *loadbalancernrp.eastus.cloudapp.azure.com* です。 次のコマンドでは、静的な割り当てタイプと 4 分のアイドル タイムアウトを使用しています。

    ```azurecli
        azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4
    ```

   > [!IMPORTANT]
   > ロード バランサーはその FQDN としてパブリック IP のドメイン ラベルを使用します。 これはロード バランサーの完全修飾ドメイン名 (FQDN) としてクラウド サービスを使用するクラシック デプロイメントからの変更点です。
   > この例では、FQDN は *loadbalancernrp.eastus.cloudapp.azure.com*になります。

## <a name="create-a-load-balancer"></a>ロード バランサーの作成

次のコマンドは、Azure の場所 "*米国東部*" の *NRPRG* リソース グループに *NRPlb* という名前のロード バランサーを作成します。

    ```azurecli
    azure network lb create NRPRG NRPlb eastus
    ```

## <a name="create-a-front-end-ip-pool-and-a-backend-address-pool"></a>フロントエンド IP プールとバックエンド アドレス プールの作成
次の例では、ロード バランサーへの受信ネットワーク トラフィックを受信するフロントエンド IP プールと、負荷分散されたネットワーク トラフィックをフロントエンド プールが送信するバックエンド IP プールを作成する方法を説明します。

1. 前の手順で作成したパブリック IP とロード バランサーを関連付けてフロントエンド IP プールを作成します。

    ```azurecli
        azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip
    ```

2. フロントエンド IP プールから受信トラフィックを受け取るために使用するバックエンド アドレス プールをセットアップします。

    ```azurecli
        azure network lb address-pool create NRPRG NRPlb NRPbackendpool
    ```

## <a name="create-lb-rules-nat-rules-and-probe"></a>LB 規則、NAT 規則、およびプローブの作成

次の例では、以下の項目が作成されます。

* ポート 21 のすべての受信トラフィックをポート 22 に転送する NAT 規則<sup>1</sup>
* ポート 23 のすべての受信トラフィックをポート 22 に転送する NAT 規則
* ポート 80 のすべての受信トラフィックをバックエンド プールのアドレスのポート 80 に分散するロード バランサー規則
* *HealthProbe.aspx*という名前のページで正常性状態を確認するためのプローブ規則

<sup>1</sup> NAT 規則は、ロード バランサーの背後にある特定の仮想マシン インスタンスに関連付られます。 ポート 21 に到着したネットワーク トラフィックは、この NAT 規則に関連付けられている特定の仮想マシンのポート 22 に送信されます。 NAT 規則のプロトコル (UDP または TCP) を指定する必要があります。 両方のプロトコルを、同じポートに割り当てることはできません。

1. NAT 規則を作成します。

    ```azurecli
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh1 --protocol tcp --frontend-port 21 --backend-port 22
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh2 --protocol tcp --frontend-port 23 --backend-port 22
    ```

2. ロード バランサー規則を作成します。

    ```azurecli
        azure network lb rule create --resource-group nrprg --lb-name nrplb --name lbrule --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name NRPfrontendpool --backend-address-pool-name NRPbackendpool
    ```

3. 正常性プローブを作成します。

    ```azurecli
        azure network lb probe create --resource-group nrprg --lb-name nrplb --name healthprobe --protocol "http" --port 80 --path healthprobe.aspx --interval 15 --count 4
    ```

4. 設定を確認します。

    ```azurecli
        azure network lb show nrprg nrplb
    ```

    予想される出力:

        info:    Executing command network lb show
        + Looking up the load balancer "nrplb"
        + Looking up the public ip "NRPPublicIP"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
        data:    Name                            : nrplb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : eastus
        data:    Provisioning State              : Succeeded
        data:    Frontend IP configurations:
        data:      Name                          : NRPfrontendpool
        data:      Provisioning state            : Succeeded
        data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
        data:      Public IP allocation method   : Static
        data:      Public IP address             : 40.114.13.145
        data:
        data:    Backend address pools:
        data:      Name                          : NRPbackendpool
        data:      Provisioning state            : Succeeded
        data:
        data:    Load balancing rules:
        data:      Name                          : HTTP
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 80
        data:      Backend port                  : 80
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:
        data:    Inbound NAT rules:
        data:      Name                          : ssh1
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 21
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:      Name                          : ssh2
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 23
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:    Probes:
        data:      Name                          : healthprobe
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Http
        data:      Port                          : 80
        data:      Interval in seconds           : 15
        data:      Number of probes              : 4
        data:
        info:    network lb show command OK

## <a name="create-nics"></a>NIC の作成

NIC を作成し (あるいは、既存の NIC を変更し)、それを NAT 規則、ロード バランサー規則、プローブに関連付ける必要があります。

1. *lb-nic1-be* という名前の NIC を作成し、それを *rdp1* NAT 規則と *NRPbackendpool* バックエンド アドレス プールに関連付けます。

    ```azurecli
        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus
    ```

    予想される出力:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. *lb-nic2-be* という名前の NIC を作成し、それを *rdp2* NAT 規則と *NRPbackendpool* バックエンド アドレス プールに関連付けます。

    ```azurecli
        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus
    ```

3. *web1* という名前の仮想マシン (VM) を作成し、それを *lb-nic1-be* という名前の NIC に関連付けます。 *web1nrp* と呼ばれるストレージ アカウントが次のコマンドを実行する前に作成されました。

    ```azurecli
        azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

    > [!IMPORTANT]
    > ロード バランサーの VM は、同じ可用性セットに含まれている必要があります。 可用性セットを作成するには、 `azure availset create` を使用します。

    出力は次のようになります。

        info:    Executing command vm create
        + Looking up the VM "web1"
        Enter username: azureuser
        Enter password for azureuser: *********
        Confirm password: *********
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account web1nrp
        + Looking up the availability set "nrp-avset"
        info:    Found an Availability set "nrp-avset"
        + Looking up the NIC "lb-nic1-be"
        info:    Found an existing NIC "lb-nic1-be"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
        info:    This is a NIC without publicIP configured
        + Creating VM "web1"
        info:    vm create command OK

    > [!NOTE]
    > "**This is a NIC without publicIP configured**" という情報メッセージは、想定どおりの動作です。これは、ロード バランサー用に作成される NIC は、ロード バランサーのパブリック IP アドレスを使用してインターネットに接続されるためです。

    *lb-nic1-be* NIC は *rdp1* NAT 規則に関連付けられているため、ロード バランサーのポート 3441 で RDP を使用して *web1* に接続することができます。

4. *web2* という名前の仮想マシン (VM) を作成し、それを *lb-nic2-be* という名前の NIC に関連付けます。 *web1nrp* と呼ばれるストレージ アカウントが次のコマンドを実行する前に作成されました。

    ```azurecli
        azure vm create --resource-group nrprg --name web2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

## <a name="update-an-existing-load-balancer"></a>既存のロード バランサーの更新
既存のロード バランサーを参照する規則を追加できます。 次の例では、既存のロード バランサー **NRPlb**

```azurecli
azure network lb rule create --resource-group nrprg --lb-name nrplb --name lbrule2 --protocol tcp --frontend-port 8080 --backend-port 8051 --frontend-ip-name frontendnrppool --backend-address-pool-name NRPbackendpool
```

## <a name="delete-a-load-balancer"></a>ロード バランサーの削除
次のコマンドを使用してロード バランサーを削除します。

```azurecli
azure network lb delete --resource-group nrprg --name nrplb
```

## <a name="next-steps"></a>次のステップ
[内部ロード バランサーの構成の開始](load-balancer-get-started-ilb-arm-cli.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

