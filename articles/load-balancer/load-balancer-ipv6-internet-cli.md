---
title: IPv6 のパブリック ロード バランサーを作成する - Azure CLI
titlesuffix: Azure Load Balancer
description: Azure CLI を使用して IPv6 のパブリック ロード バランサーを作成する方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure load balancer, デュアル スタック, パブリック IP, ネイティブ ipv6, モバイル, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: 0ee85a92753845e0e67fff22da894a048acb1b14
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274956"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Azure CLI を使用して IPv6 のパブリック ロード バランサーを作成する


Azure Load Balancer は、第 4 層 (TCP、UDP) のロード バランサーです。 ロード バランサーは、ロード バランサー セット内のクラウド サービスまたは仮想マシンの正常なサービス インスタンスに着信トラフィックを分散することによって高可用性を提供します。 さらに、ロード バランサーは、これらのサービスを複数のポート、複数の IP アドレス、またはその両方に提供できます。

## <a name="example-deployment-scenario"></a>デプロイ シナリオの例

次の図は、この記事で説明されているテンプレートの例を使用してデプロイされる負荷分散メソッドを示します。

![ロード バランサーのシナリオ](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

このシナリオでは、次の Azure リソースを作成します。

* 2 つの仮想マシン (VM)
* IPv4 と IPv6 の両方のアドレスが割り当てられている各 VM の仮想ネットワーク インターフェイス
* IPv4 と IPv6 のパブリック IP アドレスを使用するパブリック ロード バランサー
* 2 つの VM が含まれる可用性セット
* パブリック VIP をプライベート エンドポイントにマップする 2 つの負荷分散規則

## <a name="deploy-the-solution-by-using-azure-cli"></a>Azure CLI を使用したソリューションのデプロイ

次の手順では、Azure CLI を使用して、パブリック ロード バランサーを作成する方法を示します。 CLI を使用して、各オブジェクトを個別に作成および構成した後、それらをまとめてリソースを作成します。

ロード バランサーをデプロイするには、次のオブジェクトを作成して構成します。

* **フロントエンド IP 構成**:受信ネットワーク トラフィックのパブリック IP アドレスが含まれます。
* **バックエンド アドレス プール**:ロード バランサーからネットワーク トラフィックを受信する、仮想マシンのネットワーク インターフェイス (NIC) が含まれます。
* **負荷分散規則**:ロード バランサーのパブリック ポートをバック エンド アドレス プール内のポートにマッピングする規則が含まれます。
* **受信 NAT 規則**:ロード バランサーのパブリック ポートをバックエンド アドレス プール内の特定の仮想マシンのポートにマッピングするネットワーク アドレス変換 (NAT) 規則が含まれます。
* **プローブ**:バックエンド アドレス プール内の仮想マシン インスタンスの可用性を確認するために使用する正常性プローブが含まれます。

## <a name="set-up-azure-cli"></a>Azure CLI をセットアップする

この例では、PowerShell コマンド ウィンドウで Azure CLI ツールを実行しています。 読みやすさを改善し、再利用できるようにするために、Azure PowerShell コマンドレットではなく、PowerShell のスクリプト機能を使用します。

1. リンクされている記事の手順に従って [Azure CLI をインストールして構成](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)し、Azure アカウントにサインインします。

2. Azure CLI コマンドで使用するために PowerShell 変数を設定します。

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>リソース グループ、ロード バランサー、仮想ネットワークおよびサブネットの作成

1. リソース グループを作成します。

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. ロード バランサーを作成します。

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. 仮想ネットワークを作成します。

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. この仮想ネットワークでは、2 つのサブネットを作成します。

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>フロントエンド プール用のパブリック IP アドレスの作成

1. PowerShell 変数を設定します。

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. フロントエンド IP プール用のパブリック IP アドレスを作成します。

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > ロード バランサーはその完全修飾ドメイン名 (FQDN) としてパブリック IP のドメイン ラベルを使用します。 これはロード バランサー FQDN としてクラウド サービス名を使用する従来のデプロイメントからの変更点です。
    >
    > この例では、FQDN は *contoso09152016.southcentralus.cloudapp.azure.com*です。

## <a name="create-front-end-and-back-end-pools"></a>フロントエンド プールとバックエンド プールの作成

このセクションでは、以下の IP プールを作成します。
* ロード バランサーの着信ネットワーク トラフィックを受け取るフロントエンド IP プール。
* フロントエンド プールから負荷分散されたネットワーク トラフィックを送信する先のバックエンド IP プール。

1. PowerShell 変数を設定します。

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. 前の手順で作成したパブリック IP とロード バランサーを関連付けてフロントエンド IP プールを作成します。

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>プローブ、NAT 規則、およびロード バランサー規則の作成

次の例では、以下の項目が作成されます:

* TCP ポート 80 への接続を確認するプローブ規則。
* ポート 3389 のすべての受信トラフィックを RDP のポート 3389 に転送する NAT 規則。\*
* ポート 3391 のすべての受信トラフィックをリモート デスクトップ プロトコル (RDP) のポート 3389 に転送する NAT 規則。\*
* ポート 80 のすべての受信トラフィックをバックエンド プールのアドレスのポート 80 に分散するロード バランサー規則。

\* NAT 規則は、ロード バランサーの背後にある特定の仮想マシン インスタンスに関連付けられます。 ポート 3389 に到着したネットワーク トラフィックは、この NAT 規則に関連付けられている特定の仮想マシンとポートに送信されます。 NAT 規則のプロトコル (UDP または TCP) を指定する必要があります。 両方のプロトコルを同じポートに割り当てることはできません。

1. PowerShell 変数を設定します。

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. プローブを作成します。

    次の例では、15 秒ごとにバックエンド TCP ポート 80 への接続を確認する TCP プローブを作成します。 2 回連続して失敗すると、バックエンド リソースは利用不可とマークされます。

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. バックエンド リソースへの RDP 接続を許可する受信 NAT 規則を作成します。

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. 要求を受信したフロントエンドによって、トラフィックを別のバックエンド ポートに送信するロード バランサー規則を作成します。

    ```azurecli
    $lbruleIPv4 = az network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = az network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. 設定を確認します。

    ```azurecli
    az network lb show --resource-group $rgName --name $lbName
    ```

    予想される出力:

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show

## <a name="create-nics"></a>NIC の作成

NIC を作成し、それらを NAT 規則、ロード バランサー規則、およびプローブに関連付けます。

1. PowerShell 変数を設定します。

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. 各バックエンドに NIC を作成し、IPv6 構成を追加します。

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>バックエンドの VM のリソースの作成および各 NIC の関連付け

VM を作成するには、ストレージ アカウントが必要です。 負荷分散には、VM を可用性セットのメンバーにする必要があります。 VM の作成の詳細については、[PowerShell で Azure VM を作成する方法](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)に関するページを参照してください。

1. PowerShell 変数を設定します。

    ```powershell
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $imageurn = "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > この例では、VM のユーザー名とパスワードをクリア テキストで使用します。 クリア テキストでこれらの資格情報を使用するときは、適切な注意を払ってください。 PowerShell で資格情報を処理するより安全な方法については、[`Get-Credential`](https://technet.microsoft.com/library/hh849815.aspx) コマンドレットを参照してください。

2. 可用性セットを作成します。

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. 関連付けられた NIC を使用して仮想マシンを作成します。

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```

## <a name="next-steps"></a>次の手順

[内部ロード バランサーの構成の開始](load-balancer-get-started-ilb-arm-cli.md)  
[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)  
[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)
