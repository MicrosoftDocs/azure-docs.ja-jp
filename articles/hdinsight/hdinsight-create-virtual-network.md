---
title: Azure HDInsight クラスターの仮想ネットワークの作成
description: Azure Virtual Network を作成して HDInsight を他のクラウド リソースまたはデータセンター内のリソースに接続する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/16/2020
ms.openlocfilehash: 43d57eac94cabb5c648183911e0c0bf72889946d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946072"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Azure HDInsight クラスターの仮想ネットワークの作成

この記事では、[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) を作成および構成するための例とコード サンプルを紹介します。 Azure HDInsight クラスターで使用します。 ネットワーク セキュリティ グループ (NSG) を作成し、DNS を構成する方法の詳細な例を紹介します。

Azure HDInsight での仮想ネットワークの使用に関する背景情報については、[Azure HDInsight 用仮想ネットワークの計画](hdinsight-plan-virtual-network-deployment.md)に関する記事を参照してください。

## <a name="prerequisites-for-code-samples-and-examples"></a>コード サンプルと例についての前提条件

この記事のコード サンプルを実行する前に、TCP/IP ネットワークについて理解しておきます。 TCP/IP ネットワークに詳しくない方は、実稼働ネットワークに変更を加えた経験のある方に相談してください。

この記事のサンプルに関するその他の前提条件には、次の項目があります。

* PowerShell を使用する場合は、[AZ モジュール](/powershell/azure/)をインストールする必要があります。
* Azure CLI を使用する予定で、まだインストールしていない場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

> [!IMPORTANT]  
> Azure Virtual Network を使用して HDInsight をオンプレミス ネットワークに接続するための詳しい手順については、「[オンプレミス ネットワークへの HDInsight の接続](connect-on-premises-network.md)」を参照してください。

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>例: HDInsight でのネットワーク セキュリティ グループ

このセクションの例では、ネットワーク セキュリティ グループ規則を作成する方法について説明します。 これらのルールにより、HDInsight が Azure 管理サービスと通信できるようになります。 例を使用する前に、利用している Azure リージョンの IP アドレスと一致するように、IP アドレスを調整します。 この情報については、[HDInsight 管理 IP アドレス](hdinsight-management-ip-addresses.md)に関する記事を参照してください。

### <a name="azure-resource-management-template"></a>Azure Resource Management テンプレート

次の Resource Management テンプレートは、受信トラフィックを制限するが HDInsight が必要とする IP アドレスからのトラフィックは許可する仮想ネットワークを作成します。 さらに、このテンプレートは、仮想ネットワークに HDInsight クラスターを作成します。

* [セキュリティで保護された Azure Virtual Network と HDInsight Hadoop クラスターのデプロイ](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

次の PowerShell スクリプトを使用して、受信トラフィックを制限しながら北ヨーロッパ リージョンの IP アドレスからのトラフィックは許可する仮想ネットワークを作成します。

> [!IMPORTANT]  
> この例の `hdirule1` と `hdirule2` の IP アドレスを、使用している Azure のリージョンに合わせて変更してください。 この情報については、[HDInsight 管理 IP アドレス](hdinsight-management-ip-addresses.md)に関する記事を参照してください。

```azurepowershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"

# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName

# Get the region the Virtual network is in.
$location = $vnet.Location

# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName

# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `

# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg

# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

この例は、ルールを追加して、必要な IP アドレスの受信トラフィックを許可する方法を示しています。 この方法には、他のソースからの着信アクセスを制限するルールは含まれていません。 次のコードでは、インターネットからの SSH アクセスを可能にする方法を示します。

```azurepowershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Azure CLI

次の手順を使用して、受信トラフィックを制限するが HDInsight が必要とする IP アドレスからのトラフィックは許可する仮想ネットワークを作成します。

1. 次のコマンドを使用して、 `hdisecure`という名前の新しいネットワーク セキュリティ グループを作成します。 `RESOURCEGROUP` を、Azure Virtual Network が含まれているリソース グループに置き換えます。 `LOCATION` を、グループが作成された場所 (リージョン) に置き換えます。

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    グループが作成されたら、新しいグループに関する情報が表示されます。

2. ポート 443 上で Azure HDInsight の正常性と管理サービスからのインバウンド通信を許可する新しいネットワーク セキュリティ グループにルールを追加するには、次を使用します。 `RESOURCEGROUP` を、Azure Virtual Network が含まれているリソース グループの名前に置き換えます。

    > [!IMPORTANT]  
    > この例の `hdirule1` と `hdirule2` の IP アドレスを、使用している Azure のリージョンに合わせて変更してください。 この情報については、[HDInsight 管理 IP アドレス](hdinsight-management-ip-addresses.md)に関する記事を参照してください。

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. このネットワーク セキュリティ グループの一意識別子を取得するには、次のコマンドを使用します。

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    このコマンドにより、次のテキストのような値が返されます。

    ```output
    "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

4. 次のコマンドを使用して、ネットワーク セキュリティ グループをサブネットに適用します。 `GUID` と `RESOURCEGROUP` の値を、前の手順で返された値に置き換えます。 `VNETNAME` と `SUBNETNAME` を、作成する仮想ネットワーク名とサブネット名に置き換えます。

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    このコマンドが完了すると、仮想ネットワークに HDInsight をインストールできます。

これらの手順を実行すると、Azure クラウドの HDInsight 正常性と管理サービスへのアクセスのみが開きます。 それ以外の Virtual Network 外から HDInsight クラスターへのアクセスはすべてブロックされます。 仮想ネットワーク外からのアクセスを有効にする場合は、追加のネットワーク セキュリティ グループ ルールを追加する必要があります。

次のコードでは、インターネットからの SSH アクセスを可能にする方法を示します。

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a> 例: DNS の構成

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>仮想ネットワークとそれに接続されているオンプレミス ネットワークの間で名前解決

この例は、次のことを前提としています。

* VPN ゲートウェイを使用して、オンプレミスのネットワークに接続している Azure Virtual Network がある。

* 仮想ネットワーク内のカスタム DNS サーバーは、オペレーティング システムとして Linux または Unis を実行している。

* [バインド](https://www.isc.org/downloads/bind/)がカスタム DNS サーバーにインストールされている。

仮想ネットワークのカスタム DNS サーバーで次を実行します。

1. Azure PowerShell または Azure CLI を使用して、仮想ネットワークの DNS サフィックスを見つけます。

    `RESOURCEGROUP` を仮想ネットワークが含まれるリソース グループの名前に置き換えてから、コマンドを入力します。

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

1. 仮想ネットワークのカスタム DNS サーバーで、`/etc/bind/named.conf.local`ファイルの内容として次のテキストを使用します。

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`の値を、使用している仮想ネットワークの DNS サフィックスと置き換えます。

    この構成により、仮想ネットワークの DNS サフィックスのすべての DNS 要求は Azure の再帰リゾルバーにルーティングされます。

1. 仮想ネットワークのカスタム DNS サーバーで、`/etc/bind/named.conf.options`ファイルの内容として次のテキストを使用します。

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * `10.0.0.0/16`の値を、使用している仮想ネットワークの IP アドレス範囲と置き換えます。 このエントリにより、この範囲内のアドレスの名前解決要求が許可されます。

    * `acl goodclients { ... }`セクションに、オンプレミス ネットワークの IP アドレス範囲を追加します。  このエントリにより、オンプレミス ネットワーク内のリソースからの名前解決要求が許可されます。
    
    * `192.168.0.1` の値を、オンプレミスの DNS サーバーの IP アドレス と置き換えます。 このエントリにより、その他の DNS 要求はすべて、オンプレミスの DNS サーバーにルーティングされます。

1. 構成を使用するには、バインドを再起動します。 たとえば、「 `sudo service bind9 restart` 」のように入力します。

1. オンプレミスの DNS サーバーに、条件付きフォワーダーを追加します。 手順 1 で見つけた DNS サフィックスへの要求をカスタム DNS サーバーに送信するように条件付きフォワーダーを構成します。

    > [!NOTE]  
    > 条件付きフォワーダーを追加する方法の詳細については、DNS ソフトウェアのマニュアルをご覧ください。

これらの手順を完了した後には、完全修飾ドメイン名 (FQDN) を使用して、両方のネットワーク内のリソースに接続できます。 これで、仮想ネットワークに HDInsight をインストールできるようになりました。

### <a name="name-resolution-between-two-connected-virtual-networks"></a>2 つの接続されている仮想ネットワーク間で名前解決

この例は、次のことを前提としています。

* VPN ゲートウェイまたはピアリングを使用して接続している 2 つの Azure Virtual Network がある。

* 両方のネットワーク内のカスタム DNS サーバーは、オペレーティング システムとして Linux または Unis を実行している。

* [バインド](https://www.isc.org/downloads/bind/)がカスタム DNS サーバーにインストールされている。

1. Azure PowerShell または Azure CLI を使用して、両方の仮想ネットワークの DNS サフィックスを見つけます。

    `RESOURCEGROUP` を仮想ネットワークが含まれるリソース グループの名前に置き換えてから、コマンドを入力します。

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. カスタム DNS サーバーの `/etc/bind/named.config.local` ファイルの内容として、次のテキストを使用します。 両方の仮想ネットワークのカスタム DNS サーバーでこの変更を行います。

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` の値を、__別の__ 仮想ネットワークの DNS サフィックスに置き換えます。 このエントリにより、リモート ネットワークの DNS サフィックスへの要求はそのネットワークのカスタム DNS にルーティングされます。

3. 両方の仮想ネットワークのカスタム DNS サーバーで、`/etc/bind/named.conf.options` ファイルの内容として次のテキストを使用します。

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

   `10.0.0.0/16` と `10.1.0.0/16`の値を、使用している仮想ネットワークの IP アドレス範囲と置き換えます。 このエントリにより、それぞれのネットワーク内のリソースは DNS サーバーに要求を送信できるようになります。

    仮想ネットワークの DNS サフィックス以外 (microsoft.com など) の要求は、Azure の再帰リゾルバーによって処理されます。

4. 構成を使用するには、バインドを再起動します。 たとえば、「`sudo service bind9 restart` 」のように、両方の DNS サーバーで入力します。

これらの手順を完了した後には、完全修飾ドメイン名 (FQDN) を使用して、仮想ネットワーク内のリソースに接続できます。 これで、仮想ネットワークに HDInsight をインストールできるようになりました。

## <a name="next-steps"></a>次のステップ

* HDInsight をオンプレミス ネットワークに接続する構成方法の詳しい例については、[HDInsight のオンプレミス ネットワークへの接続](./connect-on-premises-network.md)に関するページを参照してください。
* Azure 仮想ネットワークでの Apache HBase クラスターの構成については、[Azure Virtual Network での HDInsight 上の Apache HBase クラスターの作成](hbase/apache-hbase-provision-vnet.md)に関するページを参照してください。
* Apache HBase geo レプリケーションの構成については、「[Azure 仮想ネットワーク内で Apache HBase クラスターのレプリケーションを設定する](hbase/apache-hbase-replication.md)」を参照してください。
* Azure 仮想ネットワークの詳細については、[Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)に関するページをご覧ください。

* ネットワーク セキュリティ グループの詳細については、[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)に関するページをご覧ください。

* ユーザー定義のルートについて詳しくは、「[ユーザー定義のルートと IP 転送](../virtual-network/virtual-networks-udr-overview.md)」をご覧ください。
