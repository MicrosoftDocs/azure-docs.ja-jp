---
title: "IPv6 でインターネットに接続するロード バランサーの作成 - Azure CLI | Microsoft Docs"
description: "Azure Resource Manager で Azure CLI を使用して、IPv6 でインターネットに接続するロード バランサーを作成する方法について説明します。"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-resource-manager
keywords: "ipv6, azure load balancer, デュアル スタック, パブリック IP, ネイティブ ipv6, モバイル, iot"
ms.assetid: a1957c9c-9c1d-423e-9d5c-d71449bc1f37
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: d009cf50be1f597c266fd565c9ff4799c9df6dbb
ms.lasthandoff: 03/21/2017

---

# <a name="create-an-internet-facing-load-balancer-with-ipv6-in-azure-resource-manager-using-the-azure-cli"></a>Azure Resource Manager で Azure CLI を使用して、IPv6 でインターネットに接続するロード バランサーを作成する

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [テンプレート](load-balancer-ipv6-internet-template.md)

Azure Load Balancer は、第&4; 層 (TCP、UDP) のロード バランサーです。 ロード バランサーは、ロード バランサー セット内のクラウド サービスまたは仮想マシンの正常なサービス インスタンスに着信トラフィックを分散することによって高可用性を提供します。 さらに、Azure Load Balancer は、これらのサービスを複数のポート、複数の IP アドレス、またはその両方に提供できます。

## <a name="example-deployment-scenario"></a>デプロイ シナリオの例

次の図は、この記事で説明されているテンプレートの例を使用してデプロイされる負荷分散メソッドを示します。

![ロード バランサーのシナリオ](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

このシナリオでは、次の Azure リソースを作成します。

* 2 つの仮想マシン (VM)
* IPv4 と IPv6 の両方のアドレスが割り当てられている各 VM の仮想ネットワーク インターフェイス
* IPv4 と IPv6 のパブリック IP アドレスでインターネットに接続するロード バランサー
* 2 つの VM が含まれる可用性セット
* パブリック VIP をプライベート エンドポイントにマップする&2; つの負荷分散規則

## <a name="deploying-the-solution-using-the-azure-cli"></a>Azure CLI を使用したソリューションのデプロイ

次の手順では、CLI で Azure Resource Manager を使用して、インターネットに接続するロード バランサーを作成する方法を示します。 Azure Resource Manager では、それぞれのリソースは個別に作成され構成された後、リソースを作成するためにまとめられます。

ロード バランサーをデプロイするには、次のオブジェクトを作成して構成します。

* フロントエンド IP 構成 - 受信ネットワーク トラフィックのパブリック IP アドレスが含まれます。
* バックエンド アドレス プール - ロード バランサーからネットワーク トラフィックを受信する、仮想マシンのネットワーク インターフェイス (NIC) が含まれます。
* 負荷分散規則 - ロード バランサーのパブリック ポートをバックエンド アドレス プール内のポートにマッピングする規則が含まれます。
* 受信 NAT 規則 - ロード バランサーのパブリック ポートをバックエンド アドレス プール内の特定の仮想マシンのポートにマッピングする規則が含まれます。
* プローブ - バックエンド アドレス プール内の仮想マシン インスタンスの可用性を確認するために使用する正常性プローブが含まれます。

詳細については、「 [Azure Resource Manager によるロード バランサーのサポート](load-balancer-arm.md)」を参照してください。

## <a name="set-up-your-cli-environment-to-use-azure-resource-manager"></a>Azure Resource Manager を使用するための CLI 環境の設定

この例では、PowerShell コマンド ウィンドウで CLI ツールを実行しています。 Azure PowerShell コマンドレットは使用しませんが、PowerShell のスクリプト機能を使用して読みやすさを向上させ、再利用できるようにします。

1. Azure CLI を初めて使用する場合は、「 [Azure CLI のインストール](../cli-install-nodejs.md) 」を参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。
2. **azure config mode** コマンドを実行して Resource Manager モードに切り替えます。

    ```azurecli
    azure config mode arm
    ```

    予想される出力:

        info:    New mode is arm

3. Azure にサインインしてサブスクリプションの一覧を取得します。

    ```azurecli
    azure login
    ```

    メッセージが表示されたら、Azure の資格情報を入力します。

    ```azurecli
    azure account list
    ```

    使用するサブスクリプションを選択します。 次の手順のためにサブスクリプション ID をメモしておきます。

4. CLI コマンドで使用するために PowerShell 変数を設定します。

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

1. リソース グループの作成

    ```azurecli
    azure group create $rgName $location
    ```

2. ロード バランサーの作成

    ```azurecli
    $lb = azure network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. 仮想ネットワーク (VNet) を作成します。

    ```azurecli
    $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

    この VNet に&2; つのサブネットを作成します。

    ```azurecli
    $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>フロントエンド プール用のパブリック IP アドレスの作成

1. PowerShell 変数を設定します。

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. フロントエンド IP プール用のパブリック IP アドレスを作成します。

    ```azurecli
    $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
    $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel
    ```

    > [!IMPORTANT]
    > ロード バランサーはその FQDN としてパブリック IP のドメイン ラベルを使用します。 これはロード バランサー FQDN としてクラウド サービス名を使用する従来のデプロイメントからの変更点です。
    > この例では、FQDN は *contoso09152016.southcentralus.cloudapp.azure.com*です。

## <a name="create-front-end-and-back-end-pools"></a>フロントエンド プールとバックエンド プールの作成

次の例では、ロード バランサーへの受信ネットワーク トラフィックを受信するフロントエンド IP プールと、負荷分散されたネットワーク トラフィックをフロントエンド プールが送信するバックエンド IP プールを作成します。

1. PowerShell 変数を設定します。

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. 前の手順で作成したパブリック IP とロード バランサーを関連付けてフロントエンド IP プールを作成します。

    ```azurecli
    $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
    $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-lb-rules"></a>プローブ、NAT 規則、および LB 規則の作成

次の例では、以下の項目が作成されます:

* TCP ポート 80 への接続を確認するプローブ規則
* ポート 3389 のすべての受信トラフィックを RDP<sup>1</sup> のポート 3389 に転送する NAT 規則
* ポート 3391 のすべての受信トラフィックを RDP<sup>1</sup> のポート 3389 に転送する NAT 規則
* ポート 80 のすべての受信トラフィックをバックエンド プールのアドレスのポート 80 に分散するロード バランサー規則

<sup>1</sup> NAT 規則は、ロード バランサーの背後にある特定の仮想マシン インスタンスに関連付られます。 ポート 3389 に到着したネットワーク トラフィックは、この NAT 規則に関連付けられている特定の仮想マシンとポートに送信されます。 NAT 規則のプロトコル (UDP または TCP) を指定する必要があります。 両方のプロトコルを、同じポートに割り当てることはできません。

1. PowerShell 変数を設定します。

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. プローブを作成する

    次の例では、15 秒ごとにバックエンド TCP ポート 80 への接続を確認する TCP プローブを作成します。 2 回連続して失敗すると、バックエンド リソースを利用不可とマークします。

    ```azurecli
    $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName
    ```

3. バックエンド リソースへの RDP 接続を許可する受信 NAT 規則を作成します。

    ```azurecli
    $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. 要求を受信したフロントエンドによって、トラフィックを別のバックエンド ポートに送信するロード バランサー規則を作成します。

    ```azurecli
    $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. 設定を確認します。

    ```azurecli
    azure network lb show --resource-group $rgName --name $lbName
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
    $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
    $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule2V4Id
    $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>バックエンドの VM のリソースの作成および各 NIC の関連付け

VM を作成するには、ストレージ アカウントが必要です。 負荷分散には、VM を可用性セットのメンバーにする必要があります。 VM の作成の詳細については、「 [PowerShell を使用して Azure VM を作成する](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)

1. PowerShell 変数を設定します。

    ```powershell
    $storageAccountName = "ps08092016v6sa0"
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $disk1Name = "WindowsVMosDisk1"
    $disk2Name = "WindowsVMosDisk2"
    $osDisk1Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk1Name.vhd"
    $osDisk2Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk2Name.vhd"
    $imageurn "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > この例では、VM のユーザー名とパスワードをクリア テキストで使用します。 クリア テキストで資格情報取り扱う際には十分に注意する必要があります。 PowerShell で資格情報を処理するより安全な方法については、 [Get-credential](https://technet.microsoft.com/library/hh849815.aspx) コマンドレットを参照してください。

2. ストレージ アカウントと可用性セットの作成

    VM を作成するときに、既存のストレージ アカウントを使用できます。 次のコマンドは、新しいストレージ アカウントを作成します。

    ```azurecli
    $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"
    ```

    次に、可用性セットを作成します。

    ```azurecli
    $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. 関連付けられた NIC を使用した仮想マシンの作成

    ```azurecli
    $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

    $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension
    ```

## <a name="next-steps"></a>次のステップ

[内部ロード バランサーの構成の開始](load-balancer-get-started-ilb-arm-cli.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

