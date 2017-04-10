---
title: "Virtual Network を使用した HDInsight の拡張 | Microsoft Docs"
description: "Azure Virtual Network を使用して HDInsight を他のクラウド リソースまたはデータ センター内のリソースに接続する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: afd43fb536278d6aa76acaf7c9d18714bc5acd2b
ms.lasthandoff: 03/15/2017


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Azure Virtual Network を使用した HDInsight 機能の拡張
Azure Virtual Network では、Hadoop ソリューションを拡張して、SQL Server などのオンプレミスのリソースに組み込むことができます。 また、複数の種類の HDInsight クラスターを組み合わせたり、クラウドのリソース間にセキュリティで保護されたプライベート ネットワークを作成することもできます。

## <a name="prerequisites"></a>前提条件

* Azure CLI 2.0: 詳細については、[Azure CLI 2.0 のインストールおよび構成](https://docs.microsoft.com/cli/azure/install-az-cli2)に関するページをご覧ください。

* Azure PowerShell: 詳細については、[Azure PowerShell のインストールおよび構成](/powershell/azureps-cmdlets-docs)に関するページをご覧ください。

> [!NOTE]
> このドキュメントには、Azure CLI および Azure PowerShell の最新バージョンが必要です。 以前のバージョンを使用している場合は、コマンドが異なる場合があります。 適切に実行するためには、前のリンクを使用して最新バージョンをインストールします。

## <a id="whatis"></a>Azure Virtual Network とは

[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) によって、ソリューションに必要なリソースを含む、セキュリティで保護された永続的なネットワークを作成できます。 仮想ネットワークでは、次のことが可能になります。

* プライベート ネットワーク (クラウドのみ) 内でのクラウド リソース間の接続
  
    ![クラウドのみの構成の図](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)
  
    Virtual Network を使用して Azure サービスと Azure HDInsight を連携させることで、以下のシナリオが実現できるようになります。
  
    * **HDInsight サービスまたはジョブを呼び出す** 。
    * **データを直接転送する** 。
    * **複数の HDInsight サーバーを結合して単一のソリューションにする。** HDInsight クラスターにはさまざまな種類があり、それぞれ適したワークロードやテクノロジに対応しています。 複数の種類 (Storm と HBase など) を組み合わせたクラスターを作成することはできません。 仮想ネットワークを使用することで、複数のクラスターが互いに直接通信することができます。

* 仮想プライベート ネットワーク (VPN) を使用したローカル データセンター ネットワークへのクラウド リソースの接続 (サイト間またはポイント対サイト)。
  
    サイト間構成では、データセンターから複数のリソースを Azure Virtual Network に接続できます。 この接続は、ハードウェア VPN デバイスを使用するか、ルーティングとリモート アクセス サービスを使用して行います。
  
    ![サイト間構成の図](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)
  
    ポイント対サイト構成では、ソフトウェア VPN を使用して、特定のリソースを Azure Virtual Network に接続できます。
  
    ![ポイント対サイト構成の図](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)
  
    Virtual Network を使用してクラウドとデータ センターをリンクすると、クラウドのみの構成と同様のシナリオを実現できます。 ただし、クラウド内のリソースの操作に制限されるのではなく、データ センターのリソースも使用できます。
  
    * **データを直接転送する** 。 たとえば、Sqoop を使用して SQL Server に、または SQL Server からデータを転送したり、基幹業務 (LOB) アプリケーションで生成されたデータを読み取ったりします。
    * **LOB アプリケーションから HDInsight サービスを呼び出す。** たとえば、HBase Java API を使用して HDInsight HBase クラスターにデータを格納したり、クラスターからデータを取得したりします。

Virtual Network の機能、利点の詳細については、「 [Azure 仮想ネットワークの概要](../virtual-network/virtual-networks-overview.md)」を参照してください。

> [!NOTE]
> HDInsight クラスターをプロビジョニングする前に、Azure Virtual Network を作成します。その後、クラスターを作成するときにネットワークを指定します。 詳細については、「 [仮想ネットワークの構成タスク](https://azure.microsoft.com/documentation/services/virtual-network/)」を参照してください。

## <a name="virtual-network-requirements"></a>Virtual Network に関する要件

> [!IMPORTANT]
> Virtual Network で HDInsight クラスターを作成するには、このセクションで説明する、特定の Virtual Network 構成が必要です。

### <a name="location-based-virtual-networks"></a>場所ベースの仮想ネットワーク

Azure HDInsight は場所ベースの仮想ネットワークのみをサポートし、アフィニティ グループ ベースの仮想ネットワークは現在取り扱っていません。

### <a name="classic-or-v2-virtual-network"></a>クラシックまたは v2 Virtual Network

Linux ベースのクラスターでは、Azure Resource Manager Virtual Network が必要です (Windows ベースのクラスターでは、クラシック Virtual Network が必要です)。 ネットワークの種類が正しくない場合、クラスターの作成時にその種類を選択できません。

異なるネットワークの種類を結合できます。これにより、互換性のない仮想ネットワーク上のリソースにアクセスできるようになります。 必要なネットワーク バージョンでクラスターを作成すると、2 つのネットワークは結合されているので、もう一方のネットワークのリソースへのアクセスが可能になります。 クラシック仮想ネットワークと Resource Manager 仮想ネットワークを接続する方法の詳細については、[新しい Vnet へのクラシック VNet の接続](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)に関する記事をご覧ください。

### <a name="custom-dns"></a>カスタム DNS

仮想ネットワークを作成するとき、そのネットワークにインストールされている Azure サービス (HDInsight) には Azure によって既定の名前解決が行われます。 ただしクロス ネットワーク ドメイン名解決など、状況によっては独自のドメイン ネーム システム (DNS) を使用しなければならないこともあります。 たとえば、参加している 2 つの仮想ネットワークに置かれているサービス間の通信が挙げられます。 HDInsight は、Azure Virtual Network と組み合わせて使用した場合、既定の Azure の名前解決とカスタム DNS をサポートします。

カスタム DNS サーバーの使用方法の詳細については、「[VM とロール インスタンスの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)」をご覧ください。

### <a name="secured-virtual-networks"></a>セキュリティで保護された Virtual Networks

HDInsight サービスは管理されたサービスで、プロビジョニング中や実行中にインターネット アクセスが必要です。 Azure は以下の目的でインターネット接続を使用します。

* クラスターの正常性の監視
* クラスター リソースのフェールオーバーの開始
* スケーリング操作によるクラスター内のノード数の変更
* その他の管理タスク

これらの操作には、インターネットへのフル アクセスは必要ありません。 インターネット アクセスを制限する場合は、次の IP アドレスのポート 443 上で着信アクセスを許可します。 これにより、Azure が HDInsight を管理できるようになります。

> [!IMPORTANT]
> 許可される IP アドレスは、HDInsight クラスターと Virtual Network が存在するリージョンに固有である必要があります。 次のリストを参照して、使用しているリージョンに適した IP アドレスを見つけてください。

__ブラジル南部__リージョン:

* 191.235.84.104
* 191.235.87.113

__カナダ東部__リージョン:

* 52.229.127.96
* 52.229.123.172

__カナダ中部__リージョン:

* 52.228.37.66
* 52.228.45.222

__インド中部__リージョン:

* 52.172.153.209
* 52.172.152.49

__米国中西部__リージョン:

* 52.161.23.15
* 52.161.10.167

__米国西部 2__ リージョン:

* 52.175.211.210
* 52.175.222.222

__その他のすべてのリージョン__:

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

これらのアドレスのポート 443 上で着信アクセスを許可すると、セキュリティ保護された仮想ネットワークに HDInsight を正常にインストールできます。

> [!IMPORTANT]
> HDInsight では、送信トラフィックの制限をサポートしていません。受信トラフィックのみをサポートしています。 HDInsight が含まれているサブネットのネットワーク セキュリティ グループ規則を定義するときは、受信規則だけを使用します。

次の例では、必要なアドレスを許可し Virtual Network 内のサブネットにセキュリティ グループを適用する、ネットワーク セキュリティ グループを作成する方法を示します。 この例で使用されている IP アドレスを、使用している Azure リージョンに応じて変更してください。

これらの手順では、HDInsight をインストールする Virtual Network とサブネットを既に作成していることを前提としています。 「[Azure ポータルを使用した仮想ネットワークの作成](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)」をご覧ください。

> [!IMPORTANT]
> これらの例で使用する `priority` 値に注意してください。 優先順位の高い順に、ネットワーク トラフィックに対するルールのテストが行われます。 1 つのルールがテスト条件に一致して適用されると、ルールのテストはそれ以上行われません。
> 
> 着信トラフィックを幅広くブロックするカスタム ルール (**すべて拒否**のルールなど) がある場合は、これらの例での優先順位値を調整する必要が生じることがあります。 例に含まれるルールは、アクセスをブロックするルールよりも前に実行される必要があります。 そうしないと、**すべて拒否**のルールが最初にテストされて、この例に含まれるルールが適用されなくなります。 また、Azure Virtual Network の既定のルールをブロックしないでください。 たとえば、既定の **ALLOW VNET INBOUND** ルール (優先順位 65000) よりも前に適用される、**すべて拒否**のルールを作成するべきではありません。
> 
> ネットワーク セキュリティ グループのルールの詳細については、[ネットワーク セキュリティ グループの概要](../virtual-network/virtual-networks-nsg.md)に関する記事をご覧ください。

**Azure Resource Manager テンプレートの使用**

[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/)から次のリソース管理テンプレートを使用して、ネットワーク構成がセキュリティ保護された VNet で HDInsight クラスターを作成します。

[Deploy a secured Azure VNet and an HDInsight Hadoop cluster within the VNet (セキュリティで保護された Azure VNet と HDInsight Hadoop クラスターを VNet 内にデプロイする)](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

**Azure PowerShell の使用**

    $vnetName = "Replace with your virtual network name"
    $resourceGroupName = "Replace with the resource group the virtual network is in"
    $subnetName = "Replace with the name of the subnet that HDInsight will be installed into"
    # Get the Virtual Network object
    $vnet = Get-AzureRmVirtualNetwork `
        -Name $vnetName `
        -ResourceGroupName $resourceGroupName
    # Get the region the Virtual network is in.
    $location = $vnet.Location
    # Get the subnet object
    $subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
    # Create a new Network Security Group.
    # And add exemptions for the HDInsight health and management services.
    $nsg = New-AzureRmNetworkSecurityGroup `
        -Name "hdisecure" `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -name "hdirule1" `
            -Description "HDI health and management address 168.61.49.99" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.49.99" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 300 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule2" `
            -Description "HDI health and management 23.99.5.239" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "23.99.5.239" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 301 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule3" `
            -Description "HDI health and management 168.61.48.131" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.48.131" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 302 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule4" `
            -Description "HDI health and management 138.91.141.162" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "138.91.141.162" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 303 `
            -Direction Inbound
    # Set the changes to the security group
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    # Apply the NSG to the subnet
    Set-AzureRmVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name $subnetName `
        -AddressPrefix $subnet.AddressPrefix `
        -NetworkSecurityGroup $nsg

**Azure CLI の使用**

1. 次のコマンドを使用して、 `hdisecure`という名前の新しいネットワーク セキュリティ グループを作成します。 **RESOURCEGROUPNAME** を、Azure Virtual Network が含まれているリソース グループに置き換えます。 **LOCATION** を、グループが作成された場所 (リージョン) に置き換えます。
   
        az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION

    グループが作成されたら、新しいグループに関する情報が表示されます。

2. ポート 443 上で Azure HDInsight の正常性と管理サービスからのインバウンド通信を許可する新しいネットワーク セキュリティ グループにルールを追加するには、次を使用します。 **RESOURCEGROUPNAME** を、Azure Virtual Network が含まれているリソース グループの名前に置き換えます。
    
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"

3. ルールが作成されたら、次を使用してこのネットワーク セキュリティ グループの一意の識別子を取得します。

        az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'

    このコマンドにより、次のテキストのような値が返されます。

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    期待どおりの結果が得られない場合は、コマンド内の ID を二重引用符で囲んでください。

4. 次のコマンドを使用して、ネットワーク セキュリティ グループをサブネットに適用します。 __GUID__ と __RESOURCEGROUPNAME__ の値を、前の手順で返された値に置き換えます。 __VNETNAME__ と __SUBNETNAME__ を、HDInsight クラスターの作成時に使用する仮想ネットワーク名とサブネット名に置き換えます。
   
        az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
   
    このコマンドが完了すると、これらの手順で使用されるサブネット上のセキュリティで保護された Virtual Network に、HDInsight を正常にインストールできます。

> [!IMPORTANT]
> 上記の手順を使用すると、Azure クラウドの HDInsight 正常性および管理サービスへのアクセスのみが開きます。 それ以外の Virtual Network 外から HDInsight クラスターへのアクセスはすべてブロックされます。 Virtual Network 外からのアクセスを可能にする場合は、追加のネットワーク セキュリティ グループ ルールを追加する必要があります。
> 
> 次の例では、インターネットからの SSH アクセスを可能にする方法を示します。 
> 
> * Azure PowerShell - ```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * Azure CLI - ```az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"```

ネットワーク セキュリティ グループの詳細については、 [ネットワーク セキュリティ グループの概要](../virtual-network/virtual-networks-nsg.md)に関する記事を参照してください。 Azure Virtual Network におけるルーティングの制御については、「[ユーザー定義のルートと IP 転送](../virtual-network/virtual-networks-udr-overview.md)」をご覧ください。

## <a id="tasks"></a>タスクと情報

このセクションでは、仮想ネットワークで HDInsight を使用する場合の一般的なタスクに関する情報と、必要になる可能性がある情報を記載しています。

### <a name="determine-the-fqdn"></a>FQDN の決定

HDInsight クラスターには、Virtual Network インターフェイスを表す特定の完全修飾ドメイン名 (FQDN) が割り当てられます。 この FQDN は、仮想ネットワーク内の他の Azure リソースからクラスターに接続するときに使用する必要があるアドレスです。 FQDN を確認するには、次の URL を使用して Ambari 管理サービスを照会します。

    https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

クラスター名と、クラスターで実行するサービスおよびコンポーネント (YARN リソース マネージャーなど) を使用します。

> [!NOTE]
> 返されるデータは JavaScript Object Notation (JSON) ドキュメントです。 FQDN のみを抽出するには、JSON のパーサーを使用して、 `host_components[0].HostRoles.host_name` の値を取得する必要があります。

たとえば、HDInsight Hadoop クラスターから FQDN を取得するには、次のいずれかのメソッドを使用して、YARN リソース マネージャーのデータを取得します。

* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
  
        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix
  
        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
  
        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/        components/resourcemanager"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        $FQDN = $JsonObject.host_components[0].HostRoles.host_name
        Write-host $FQDN

* [cURL](http://curl.haxx.se/) および [jq](http://stedolan.github.io/jq/)
  
        curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

> [!IMPORTANT]
> クラスターへのインターネット アクセスを制限している場合は、インターネット経由で Ambari を使用できません。 代わりに、次のいずれかのメソッドを使用して FQDN を取得する必要があります。
>
> * Azure PowerShell: `Get-AzureRmNetworkInterface -ResourceGroupName GROUPNAME | Foreach-object { Write-Output $_.DnsSettings.InternalFqdn }`
> * Azure CLI 2.0: ` az network nic list --resource-group GROUPNAME --query '[].dnsSettings.internalFqdn'`
>
> どちらの例でも、__GROUPNAME__ を、Virtual Network が含まれているリソース グループの名前に置き換えます。

### <a name="connecting-to-hbase"></a>HBase への接続

Java API を使用して HBase にリモート接続するには、HBase クラスターの ZooKeeper クォーラム アドレスを確認し、クォーラム情報をアプリケーションで指定する必要があります。

ZooKeeper クォーラム アドレスを取得するには、次のいずれかのメソッドを使用して、Ambari 管理サービスに問い合わせます。

* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
  
        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix
  
        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
  
        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [cURL](http://curl.haxx.se/) および [jq](http://stedolan.github.io/jq/)
  
        curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [!NOTE]
> HDInsight での Ambari の使用の詳細については、「 [Ambari API を使用した HDInsight の Hadoop クラスターの監視](hdinsight-monitor-use-ambari-api.md)」を参照してください。

クォーラムの情報を取得したら、それをクライアント アプリケーションで使用します。

たとえば、HBase API を使用する Java アプリケーションでは、 **hbase-site.xml** ファイルをプロジェクトに追加し、次のようにそのファイル内にクォーラム情報を指定します。

```xml
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

### <a name="verify-network-connectivity"></a>ネットワーク接続の確認

SQL Server などの一部のサービスでは、着信ネットワーク接続数が制限される場合があります。 HDInsight からサービスへのアクセスで問題が発生した場合は、サービスのドキュメントを参照して、ネットワーク アクセスが有効になっていることを確認してください。 ネットワーク アクセスを確認するもう 1 つの方法は、同じ仮想ネットワーク上に Azure 仮想マシンを作成することです。 その後、Virtual Machine 上でクライアント ユーティリティを使用して仮想マシンが仮想ネットワーク経由でサービスに接続できることを確認します。

## <a id="nextsteps"></a>次のステップ

次の例は、Azure Virtual Network で HDInsight を使用する方法を示しています。

* [HDInsight での Storm および HBase を使用したセンサー データの分析](hdinsight-storm-sensor-data-analysis.md) - 仮想ネットワークでの Storm および HBase クラスターの構成方法を示しています。
* [HDInsight での Hadoop クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md) - Azure Virtual Network の使用に関する情報を含めて、Hadoop クラスターのプロビジョニングに関する情報を提供します。
* [HDInsight での Hadoop と Sqoop の使用](hdinsight-use-sqoop-mac-linux.md) - SQL Server と Sqoop を使用した仮想ネットワーク経由のデータ転送に関する情報を提供します。

Azure のかそうネットワークの詳細については、 [Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)に関するページを参照してください。


