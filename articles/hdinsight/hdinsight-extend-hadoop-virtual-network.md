---
title: Virtual Network を使用した HDInsight の拡張 - Azure
description: Azure Virtual Network を使用して HDInsight を他のクラウド リソースまたはデータ センター内のリソースに接続する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/29/2019
ms.openlocfilehash: a2d06cdbcc6ce995c55c858cb7a50a93ef6b3fb1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58883566"
---
# <a name="extend-azure-hdinsight-using-an-azure-virtual-network"></a>Azure Virtual Network を使用した Azure HDInsight の拡張

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) での HDInsight の使用方法について説明します。 Azure Virtual Network を使用すると、次のシナリオが可能になります。

* オンプレミス ネットワークから HDInsight へ直接接続する。

* HDInsight を Azure Virtual Network 内のデータ ストアに接続する。

* インターネットで公開されていない [Apache Hadoop](https://hadoop.apache.org/) サービスに直接アクセスする。 たとえば、[Apache Kafka](https://kafka.apache.org/) API や [Apache HBase](https://hbase.apache.org/) Java API にアクセスできます。

> [!IMPORTANT]  
> 2019 年 2 月 28 日以降、VNET で作成された "新しい" クラスターのネットワーク リソース (NIC、LB など) は、同じ HDInsight クラスター リソース グループにプロビジョニングされます。 以前は、これらのリソースは、VNET リソース グループにプロビジョニングされていました。 現在実行中のクラスターや、VNET なしで作成されたクラスターへの変更はありません。

## <a name="prerequisites-for-code-samples-and-examples"></a>コード サンプルと例についての前提条件

* TCP/IP ネットワークの知識。 TCP/IP ネットワークに詳しくない方は、実稼働ネットワークに変更を加えた経験のある方をパートナーにすることをおすすめします。
* PowerShell を使用する場合は、[AZ モジュール](https://docs.microsoft.com/powershell/azure/overview)が必要です。
* Azure CLI を使用したい場合で、なおかつまだインストールしていない場合は、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

> [!IMPORTANT]  
> Azure Virtual Network を使用して HDInsight をオンプレミス ネットワークに接続するための詳しい手順については、「[オンプレミス ネットワークへの HDInsight の接続](connect-on-premises-network.md)」を参照してください。

## <a name="planning"></a>計画

仮想ネットワークに HDInsight をインストールする計画を立てる際に確認しておく必要がある事項を次に示します。

* HDInsight を既存の仮想ネットワークにインストールする必要がありますか。 または、新しいネットワークを作成しますか。

    既存の仮想ネットワークを使用している場合は、HDInsight をインストールする前に、ネットワークの構成を変更する必要があります。 詳細については、 「[既存の仮想ネットワークへの HDInsight の追加](#existingvnet)」のセクションをご覧ください。

* HDInsight を含む仮想ネットワークを別の仮想ネットワークまたはオンプレミス ネットワークに接続しますか。

    ネットワーク間でリソースを簡単に利用できるようにするには、カスタムの DNS を作成し、DNS 転送を構成する必要があります。 詳細については、「[複数のネットワークの接続](#multinet)」のセクションをご覧ください。

* HDInsight への送受信トラフィックを制限/リダイレクトしますか。

    HDInsight は、Azure データ センター内の特定の IP アドレスとの制限のない通信を必要とします。 クライアントとの通信用に、ファイアウォールの通過が許可されているポートも複数必要です。 詳細については、「[ネットワーク トラフィックのコントロール](#networktraffic)」のセクションをご覧ください。

## <a id="existingvnet"></a>既存の仮想ネットワークへの HDInsight の追加

このセクションの手順を使用して、新しい HDInsight を既存の Azure Virtual Network に追加する方法をご確認ください。

> [!NOTE]  
> 仮想ネットワークに既存の HDInsight クラスターを追加することはできません。

1. 使用中の仮想ネットワークは、クラシック デプロイ モデルですか、Resource Manager デプロイ モデルですか。

    HDInsight 3.4 以降では、Resource Manager の仮想ネットワークが必要です。 HDInsight の以前のバージョンでは、従来の仮想ネットワークが必要です。

    使用している既存のネットワークが従来の仮想ネットワークの場合は、Resource Manager の仮想ネットワークを作成してから、それぞれのネットワークを接続する必要があります。 [従来の VNet を新しい VNet に接続します](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)。

    一度接続すると、Resource Manager ネットワークにインストールされた HDInsight は従来のネットワーク内のリソースとやり取りできます。

2. 強制トンネリングを使用していますか。 強制トンネリングとは、ログ記録と検査を目的として、送信インターネット トラフィックをデバイスに強制的に向かわせるサブネット設定です。 HDInsight は強制トンネリングをサポートしていません。 HDInsight を既存のサブネットにデプロイする前に強制トンネリングを削除するか、HDInsight 用の強制トンネリングなしで新しいサブネットを作成してください。

3. 仮想ネットワークの送受信トラフィックを制限するために、ネットワーク セキュリティ グループ、ユーザー定義のルート、Virtual Network Appliances を使用していますか。

    マネージド サービスとして、HDInsight は Azure データ センターの複数の IP アドレスに制限なくアクセスできる必要があります。 これらの IP アドレスとの通信を可能にするために、既存のネットワーク セキュリティ グループやユーザー定義のルートを更新してください。
    
    HDInsight では、さまざまなポートを使用する複数のサービスをホストします。 これらのポートへのトラフィックはブロックしないでください。 仮想アプライアンスのファイアウォールの通過を許可するポートの一覧については、「セキュリティ」のセクションをご覧ください。
    
    既存のセキュリティ構成を検索するには、次の Azure PowerShell または Azure CLI コマンドを使用します。

    * ネットワーク セキュリティ グループ

        `RESOURCEGROUP` を仮想ネットワークが含まれるリソース グループの名前に置き換えてから、コマンドを入力します。
    
        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```
    
        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        詳細については、「 [ネットワーク セキュリティ グループのトラブルシューティング](../virtual-network/diagnose-network-traffic-filter-problem.md) 」をご覧ください。

        > [!IMPORTANT]  
        > ネットワーク セキュリティ グループ ルールは、ルールの優先順位に基づく順序で適用されます。 トラフィック パターンに一致する最初のルールが適用され、そのトラフィックには他のルールは適用されません。 ルールは、最も制限の緩やかなルールから最も制限の厳しいルールへと順番付けします。 詳細については、「[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルタリング](../virtual-network/security-overview.md)」をご覧ください。

    * ユーザー定義のルート

        `RESOURCEGROUP` を仮想ネットワークが含まれるリソース グループの名前に置き換えてから、コマンドを入力します。

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        詳細については、「 [ルートのトラブルシューティング](../virtual-network/diagnose-network-routing-problem.md)」をご覧ください。

4. HDInsight クラスターを作成し、構成時に Azure Virtual Network を選択します。 次のドキュメントの手順を使って、クラスターの作成プロセスを理解してください。

    * [Azure Portal を使用した HDInsight の作成](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Azure PowerShell を使用した HDInsight の作成](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Azure クラシック CLI を使用した HDInsight の作成](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Azure Resource Manager テンプレートを使用した HDInsight の作成](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > 仮想ネットワークへの HDInsight の追加はオプションの構成手順です。 必ず、クラスターを構成するときに仮想ネットワークを選択してください。

## <a id="multinet"></a>複数のネットワークの接続

複数のネットワークを構成する際の最大の課題は、ネットワーク間の名前解決です。

Azure には、仮想ネットワークにインストールされている Azure サービスの名前解決が用意されています。 この組み込みの名前解決を使用することにより、HDInsight は、完全修飾ドメイン名 (FQDN) を使用して、次のリソースに接続することができます。

* インターネットで利用可能なリソース。 たとえば、microsoft.com、windowsupdate.com など。

* 同じ Azure Virtual Network 内にあるリソース (リソースの __内部 DNS 名__ を使用)。 たとえば、既定の名前解決を使用する場合、HDInsight ワーカー ノードに割り当てられている内部 DNS 名の例として次のようなものがあります。

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    これら両方のノードは、内部 DNS 名を使用して、互いに直接通信でき、また HDInsight 内の他のノードとも直接通信できます。

既定の名前解決では、仮想ネットワークに結合されているネットワーク内のリソースの名前解決を HDInsight が行うことは __できません__。 よくある例として、オンプレミス ネットワークと仮想ネットワークの結合があります。 既定の名前解決のみ使用している場合、 HDInsight は名前で、オンプレミス ネットワーク内のリソースにアクセスすることはできません。 逆の場合も同様で、オンプレミス ネットワーク内のリソースは、仮想ネットワーク内のリソースに名前でアクセスすることはできません。

> [!WARNING]  
> HDInsight クラスターを作成する前に、カスタムの DNS サーバーを作成し、これを使用するように仮想ネットワークを構成する必要があります。

結合されたネットワーク内のリソースと仮想ネットワーク間の名前解決を可能にするには、次のアクションを実行する必要があります。

1. HDInsight のインストールを計画している Azure Virtual Network でカスタムの DNS サーバーを作成します。

2. カスタム DNS サーバーを使用するように仮想ネットワークを構成します。

3. Azure が仮想ネットワークに割り当てた DNS サフィックスを見つけます。 この値は、`0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` のようになります。 DNS サフィックスを見つける方法については、「[例:カスタム DNS](#example-dns)」のセクションをご覧ください。

4. DNS サーバー間の転送を構成します。 構成は、リモート ネットワークの種類によって異なります。

   * リモート ネットワークがオンプレミス ネットワークの場合は、次のように DNS を構成します。
        
     * __カスタム DNS__ (仮想ネットワーク内): 

         * Azure の再帰リゾルバー (168.63.129.16) に仮想ネットワークの DNS サフィックスの要求を転送します。 Azure が、仮想ネットワーク内のリソースへの要求を処理します。

         * その他の要求はすべて、オンプレミス DNS サーバーに転送されます。 その他のすべての名前解決要求は、Microsoft.com などのインターネット リソースへの要求も含めて、オンプレミス DNSが処理します。 

     * __オンプレミス DNS__:仮想ネットワークの DNS サフィックスの要求をカスタム DNS サーバーに転送します。 その後、カスタム DNS サーバーにより、Azure の再帰リゾルバーに転送されます。

       この構成では、仮想ネットワークの DNS サフィックスを持つ完全修飾ドメイン名の要求は、カスタム DNS サーバーに送信されます。 他のすべての要求は、(公開インターネットのアドレスの要求も含め) オンプレミスの DNS サーバーによって処理されます。

   * リモート ネットワークが別の Azure Virtual Network の場合は、次のように DNS を構成します。

     * __カスタム DNS__ (各仮想ネットワーク内): 

         * 仮想ネットワークの DNS サフィックスの要求をカスタム DNS サーバーに転送します。 それぞれの仮想ネットワークの DNS は、自身のネットワーク内のリソースの解決を担当します。

         * その他の要求はすべて Azure の再帰リゾルバーに転送します。 再帰リゾルバーは、ローカルとインターネットのリソースの解決を担当します。

       それぞれのネットワークの DNS サーバーは DNS サフィックスに基づいて要求を他のサーバーに転送します。 その他の要求は Azure の再帰リゾルバーを使用して解決されます。

     各構成の例については、「[例:カスタム DNS](#example-dns)」のセクションをご覧ください。

詳細については、「[Name resolution for VMs and Role instances (VM とロール インスタンスの名前解決)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)」をご覧ください。

## <a name="directly-connect-to-apache-hadoop-services"></a>Apache Hadoop サービスへの直接接続

`https://CLUSTERNAME.azurehdinsight.net` でクラスターに接続できます。 このアドレスはパブリック IP を使用しているため、NSG を使用してインターネットからの着信トラフィックを制限している場合は到達できない可能性があります。 さらに、VNet にクラスターをデプロイするときは、プライベート エンドポイント `https://CLUSTERNAME-int.azurehdinsight.net` を使用してそれにアクセスできます。 このエンドポイントはクラスターがアクセスできるように VNet 内のプライベート IP に解決されます。

仮想ネットワーク経由で Apache Ambari やその他の Web ページに接続するには、次の手順を使用します。

1. HDInsight クラスター ノードの内部完全修飾ドメイン名 (FQDN) を検出するには、次のいずれかの方法を使用します。

    `RESOURCEGROUP` を仮想ネットワークが含まれるリソース グループの名前に置き換えてから、コマンドを入力します。

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    返されたノードの一覧で、ヘッド ノードの FQDN を見つけ、その FQDN を使用してAmbari やその他の Web サービスに接続します。 たとえば、`http://<headnode-fqdn>:8080` を使用して Ambari にアクセスします。

    > [!IMPORTANT]  
    > ヘッド ノードでホストされている一部のサービスは、一度に 1 つのノードでのみアクティブになります。 一方のヘッド ノードのサービスにアクセスしようとして、404 エラーが返された場合は、もう一方のヘッド ノードに切り替えてください。

2. サービスを使用できるノードとポートを特定するには、「[HDInsight 上の Hadoop サービスで使用されるポート](./hdinsight-hadoop-port-settings-for-services.md)」をご覧ください。

## <a id="networktraffic"></a>ネットワーク トラフィックのコントロール

Azure Virtual Network のネットワーク トラフィックは次のメソッドを使用してコントロールできます。

* **ネットワーク セキュリティ グループ** (NSG) を使用すると、ネットワーク の送受信トラフィックをフィルター処理できます。 詳細については、「[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルタリング](../virtual-network/security-overview.md)」をご覧ください。

    > [!WARNING]  
    > HDInsight では、送信トラフィックの制限をサポートしていません。 すべての送信トラフィックを許可する必要があります。

* **ユーザー定義ルート**(UDR) は、ネットワーク内のリソース間のトラフィックのフローを定義します。 ユーザー定義ルートの詳細については、「[user-defined routes and IP forwarding (ユーザー定義ルートと IP 転送)](../virtual-network/virtual-networks-udr-overview.md)」をご覧ください。

* **ネットワーク仮想アプライアンス**は、ファイアウォールやルーターなどのデバイスの機能をレプリケートします。 詳細については、「[ネットワーク アプライアンス](https://azure.microsoft.com/solutions/network-appliances)」をご覧ください。

マネージド サービスとして HDInsight には、VNET からの受信および送信トラフィックの両方に対して、HDInsight の正常性および管理サービスへの無制限アクセスが必要です。 NSG と UDR を使用する場合は、これらのサービスが引き続き HDInsight クラスターと通信できることを確認する必要があります。

### <a id="hdinsight-ip"></a>HDInsight でネットワーク セキュリティ グループとユーザー定義のルートを使用する

**ネットワーク セキュリティ グループ**や**ユーザー定義ルート**を使用してネットワーク トラフィックを制御する予定の場合は、HDInsight をインストールする前に、次の操作を実行します。

1. HDInsight を使用する予定の Azure リージョンを特定します。

2. HDInsight が必要とする IP アドレスを特定します。 詳細ついては、[HDInsight が必要とする IP アドレス](#hdinsight-ip)に関するセクションをご覧ください。

3. HDInsight をインストールする予定のサブネットのネットワーク セキュリティ グループまたはユーザー定義のルートを作成または変更します。

    * __ネットワーク セキュリティ グループ__: IP アドレスからの __受信__ トラフィックをポート __443__ で許可します。 これにより、HDI 管理サービスが VNET の外部から、クラスターに確実に到達できます。
    * __ユーザー定義のルート__:UDR を使用する予定がある場合は、IP アドレスごとにルートを作成し、__次ホップの種類__ を __インターネット__ に設定します。 その他の VNET からのすべての送信トラフィックを制限なしで許可する必要もあります。 たとえば、監視のために、その他のすべてのトラフィックを Azure ファイアウォールやネットワーク仮想アプライアンス (Azure でホストされている) にルーティングできますが、送信トラフィックがブロックされないようにする必要があります。

ネットワーク セキュリティ グループまたはユーザー定義のルートの詳細については、次のドキュメントをご覧ください。

* [ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)

* [ユーザー定義のルート](../virtual-network/virtual-networks-udr-overview.md)

#### <a name="forced-tunneling-to-on-premise"></a>オンプレミスへの強制トンネリング

強制トンネリングは、サブネットからのすべてのトラフィックを強制的に、特定のネットワークまたは場所に送るユーザー定義のルーティングの構成です。 HDInsight では、オンプレミス ネットワークへの強制トンネリングはサポートされて__いません__。 Azure Firewall または Azure でホストされているネットワーク仮想アプライアンスを使用している場合は、UDR を使用して、監視のために、それにトラフィックをルーティングし、すべての送信トラフィックを許可できます。

## <a id="hdinsight-ip"></a>必須 IP アドレス

> [!IMPORTANT]  
> Azure の正常性と管理サービスは、HDInsight と通信できる必要があります。 ネットワーク セキュリティ グループまたはユーザー定義のルートを使用する場合は、これらのサービスの IP アドレスからのトラフィックが HDInsight に到達できるように許可してください。
>
> トラフィックの制御に、ネットワーク セキュリティ グループもユーザー定義のルートも使用しない場合は、このセクションを無視してもかまいません。

ネットワーク セキュリティ グループを使用する場合は、Azure の正常性および管理サービスからのトラフィックがポート 443 で HDInsight クラスターに到達できるように許可する必要があります。 また、サブネット内の VM 間のトラフィックも許可する必要があります。 次の手順を使用して、許可する必要がある IP アドレスを見つけます。

1. 常に次の IP アドレスからのトラフィックを許可する必要があります。

    | 送信元 IP アドレス | 宛先ポート | Direction |
    | ---- | ----- | ----- |
    | 168.61.49.99 | 443 | 受信 |
    | 23.99.5.239 | 443 | 受信 |
    | 168.61.48.131 | 443 | 受信 |
    | 138.91.141.162 | 443 | 受信 |

2. HDInsight クラスターが次のリージョンのいずれかにある場合は、そのリージョンに対して表示されている IP アドレスからのトラフィックを許可する必要があります。

    > [!IMPORTANT]  
    > 使用している Azure リージョンが一覧にない場合は、手順 1. の 4 つの IP アドレスのみを使用してください。

    | Country | リージョン | 許可されているソース IP アドレス | 許可されている宛先ポート | Direction |
    | ---- | ---- | ---- | ---- | ----- |
    | アジア | 東アジア | 23.102.235.122</br>52.175.38.134 | 443 | 受信 |
    | &nbsp; | 東南アジア | 13.76.245.160</br>13.76.136.249 | 443 | 受信 |
    | オーストラリア | オーストラリア東部 | 104.210.84.115</br>13.75.152.195 | 443 | 受信 |
    | &nbsp; | オーストラリア南東部 | 13.77.2.56</br>13.77.2.94 | 443 | 受信 |
    | ブラジル | ブラジル南部 | 191.235.84.104</br>191.235.87.113 | 443 | 受信 |
    | カナダ | カナダ東部 | 52.229.127.96</br>52.229.123.172 | 443 | 受信 |
    | &nbsp; | カナダ中部 | 52.228.37.66</br>52.228.45.222 | 443 | 受信 |
    | 中国 | 中国 (北部) | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | 443 | 受信 |
    | &nbsp; | 中国 (東部) | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | 443 | 受信 |
    | &nbsp; | 中国北部 2 | 40.73.37.141</br>40.73.38.172 | 443 | 受信 |
    | &nbsp; | 中国東部 2 | 139.217.227.106</br>139.217.228.187 | 443 | 受信 |
    | ヨーロッパ | 北ヨーロッパ | 52.164.210.96</br>13.74.153.132 | 443 | 受信 |
    | &nbsp; | 西ヨーロッパ| 52.166.243.90</br>52.174.36.244 | 443 | 受信 |
    | フランス | フランス中部| 20.188.39.64</br>40.89.157.135 | 443 | 受信 |
    | ドイツ | ドイツ中部 | 51.4.146.68</br>51.4.146.80 | 443 | 受信 |
    | &nbsp; | ドイツ北東部 | 51.5.150.132</br>51.5.144.101 | 443 | 受信 |
    | インド | インド中部 | 52.172.153.209</br>52.172.152.49 | 443 | 受信 |
    | &nbsp; | インド南部 | 104.211.223.67<br/>104.211.216.210 | 443 | 受信 |
    | 日本 | 東日本 | 13.78.125.90</br>13.78.89.60 | 443 | 受信 |
    | &nbsp; | 西日本 | 40.74.125.69</br>138.91.29.150 | 443 | 受信 |
    | 韓国 | 韓国中部 | 52.231.39.142</br>52.231.36.209 | 433 | 受信 |
    | &nbsp; | 韓国南部 | 52.231.203.16</br>52.231.205.214 | 443 | 受信
    | イギリス | 英国西部 | 51.141.13.110</br>51.141.7.20 | 443 | 受信 |
    | &nbsp; | 英国南部 | 51.140.47.39</br>51.140.52.16 | 443 | 受信 |
    | 米国 | 米国中部 | 13.67.223.215</br>40.86.83.253 | 443 | 受信 |
    | &nbsp; | 米国東部 | 13.82.225.233</br>40.71.175.99 | 443 | 受信 |
    | &nbsp; | 米国中北部 | 157.56.8.38</br>157.55.213.99 | 443 | 受信 |
    | &nbsp; | 米国中西部 | 52.161.23.15</br>52.161.10.167 | 443 | 受信 |
    | &nbsp; | 米国西部 | 13.64.254.98</br>23.101.196.19 | 443 | 受信 |
    | &nbsp; | 米国西部 2 | 52.175.211.210</br>52.175.222.222 | 443 | 受信 |

    Azure Government に使用する IP アドレスについては、「[Azure Government Intelligence + Analytics (Azure Government のインテリジェンスと分析)](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics)」をご覧ください。

3. また、__168.63.129.16__ からのアクセスも許可する必要があります。 これは、Azure の再帰リゾルバーのアドレスです。 詳細については、「[Name resolution for VMs and Role instances (VM とロール インスタンスの名前解決)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)」をご覧ください。

詳細については、「[ネットワーク トラフィックのコントロール](#networktraffic)」のセクションをご覧ください。

ユーザー定義ルート (UDR) を使用している場合、次ホップが "インターネット" に設定された上記 IP へのルートを指定し、VNET からそれらの IP への送信トラフィックを許可してください。
    
## <a id="hdinsight-ports"></a>必須ポート

**ファイアウォール**を使用して、特定のポートで外部からクラスターにアクセスすることを計画している場合、シナリオに必要なポートでトラフィックを許可することが必要な可能性があります。 既定では、前のセクションで説明した Azure 管理トラフィックがポート 443 でクラスターに到達することを許可されている限り、ポートの特別なホワイトリスト登録は必要ありません。

特定のサービスのポート一覧については、「[HDInsight 上の Apache Hadoop サービスで使用されるポート](hdinsight-hadoop-port-settings-for-services.md)」をご覧ください。

仮想アプライアンスのファイアウォール ルールの詳細については、「[virtual appliance scenario (仮想アプライアンス シナリオ)](../virtual-network/virtual-network-scenario-udr-gw-nva.md)」をご覧ください。

## <a id="hdinsight-nsg"></a>例: HDInsight でのネットワーク セキュリティ グループ

このセクションの例は、HDInsight に Azure 管理サービスとの通信を許可するネットワーク セキュリティ グループのルールを作成する方法を示します。 例を使用する前に、IP アドレスを、使用している Azure のリージョンの IP アドレスに合わせて変更してください。 詳細については、「[HDInsight でネットワーク セキュリティ グループとユーザー定義のルートを使用](#hdinsight-ip)」のセクションをご覧ください。

### <a name="azure-resource-management-template"></a>Azure Resource Management テンプレート

次の Resource Management テンプレートは、受信トラフィックを制限するが HDInsight が必要とする IP アドレスからのトラフィックは許可する仮想ネットワークを作成します。 さらに、このテンプレートは、仮想ネットワークに HDInsight クラスターを作成します。

* [セキュリティで保護された Azure Virtual Network と HDInsight Hadoop クラスターのデプロイ](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

> [!IMPORTANT]  
> この例で使用されている IP アドレスを、使用している Azure のリージョンに合わせて変更してください。 詳細については、「[HDInsight でネットワーク セキュリティ グループとユーザー定義のルートを使用](#hdinsight-ip)」のセクションをご覧ください。

### <a name="azure-powershell"></a>Azure PowerShell

次の PowerShell スクリプトを使用して、受信トラフィックを制限しながら北ヨーロッパ リージョンの IP アドレスからのトラフィックは許可する仮想ネットワークを作成します。

> [!IMPORTANT]  
> この例で使用されている IP アドレスを、使用している Azure のリージョンに合わせて変更してください。 詳細については、「[HDInsight でネットワーク セキュリティ グループとユーザー定義のルートを使用](#hdinsight-ip)」のセクションをご覧ください。

```powershell
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

> [!IMPORTANT]  
> この例は、ルールを追加して、必要な IP アドレスの受信トラフィックを許可する方法を示しています。 この方法には、他のソースからの着信アクセスを制限するルールは含まれていません。
>
> 次の例では、インターネットからの SSH アクセスを可能にする方法を示します。
>
> ```powershell
> Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
> ```

### <a name="azure-cli"></a>Azure CLI

次の手順を使用して、受信トラフィックを制限するが HDInsight が必要とする IP アドレスからのトラフィックは許可する仮想ネットワークを作成します。

1. 次のコマンドを使用して、 `hdisecure`という名前の新しいネットワーク セキュリティ グループを作成します。 `RESOURCEGROUP` を、Azure 仮想ネットワークが含まれているリソース グループに置き換えます。 `LOCATION` を、グループが作成された場所 (リージョン) に置き換えます。

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    グループが作成されたら、新しいグループに関する情報が表示されます。

2. ポート 443 上で Azure HDInsight の正常性と管理サービスからのインバウンド通信を許可する新しいネットワーク セキュリティ グループにルールを追加するには、次を使用します。 `RESOURCEGROUP` を、Azure 仮想ネットワークが含まれているリソース グループの名前に置き換えます。

    > [!IMPORTANT]  
    > この例で使用されている IP アドレスを、使用している Azure のリージョンに合わせて変更してください。 詳細については、「[HDInsight でネットワーク セキュリティ グループとユーザー定義のルートを使用](#hdinsight-ip)」のセクションをご覧ください。

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
    az network nsg show -g RESOURCEGROUP -n hdisecure --query 'id'
    ```

    このコマンドにより、次のテキストのような値が返されます。

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    期待どおりの結果が得られない場合は、コマンド内の `id` を二重引用符で囲んでください。

4. 次のコマンドを使用して、ネットワーク セキュリティ グループをサブネットに適用します。 `GUID` と `RESOURCEGROUP` の値を、前の手順で返された値に置き換えます。 `VNETNAME` と `SUBNETNAME` を、作成する仮想ネットワーク名とサブネット名に置き換えます。

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    このコマンドが完了すると、仮想ネットワークに HDInsight をインストールできます。

> [!IMPORTANT]  
> これらの手順を実行すると、Azure クラウドの HDInsight 正常性と管理サービスへのアクセスのみが開きます。 それ以外の Virtual Network 外から HDInsight クラスターへのアクセスはすべてブロックされます。 仮想ネットワーク外からのアクセスを有効にする場合は、追加のネットワーク セキュリティ グループ ルールを追加する必要があります。
>
> 次の例では、インターネットからの SSH アクセスを可能にする方法を示します。
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
> ```

## <a id="example-dns"></a> 例:DNS の構成

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>仮想ネットワークとそれに接続されているオンプレミス ネットワークの間で名前解決

この例は、次のことを前提としています。

* VPN ゲートウェイを使用して、オンプレミスのネットワークに接続している Azure Virtual Network がある。

* 仮想ネットワーク内のカスタム DNS サーバーは、オペレーティング システムとして Linux または Unis を実行している。

* [バインド](https://www.isc.org/downloads/bind/)がカスタム DNS サーバーにインストールされている。

仮想ネットワークのカスタム DNS サーバーで次を実行します。

1. Azure PowerShell または Azure CLI を使用して、仮想ネットワークの DNS サフィックスを見つけます。

    `RESOURCEGROUP` を仮想ネットワークが含まれるリソース グループの名前に置き換えてから、コマンドを入力します。

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. 仮想ネットワークのカスタム DNS サーバーで、`/etc/bind/named.conf.local`ファイルの内容として次のテキストを使用します。

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`の値を、使用している仮想ネットワークの DNS サフィックスと置き換えます。

    この構成により、仮想ネットワークの DNS サフィックスのすべての DNS 要求は Azure の再帰リゾルバーにルーティングされます。

2. 仮想ネットワークのカスタム DNS サーバーで、`/etc/bind/named.conf.options`ファイルの内容として次のテキストを使用します。

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

3. 構成を使用するには、バインドを再起動します。 たとえば、「 `sudo service bind9 restart` 」のように入力します。

4. オンプレミスの DNS サーバーに、条件付きフォワーダーを追加します。 手順 1 で見つけた DNS サフィックスへの要求をカスタム DNS サーバーに送信するように条件付きフォワーダーを構成します。

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

    ```powershell
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

## <a name="next-steps"></a>次の手順

* HDInsight を オンプレミス ネットワークに接続する構成方法の詳しい例については、 [HDInsight のオンプレミス ネットワークへの接続](./connect-on-premises-network.md)に関するページをご覧ください。
* Azure 仮想ネットワークでの Apache HBase クラスターの構成については、[Azure Virtual Network での HDInsight 上の Apache HBase クラスターの作成](hbase/apache-hbase-provision-vnet.md)に関するページを参照してください。
* Apache HBase geo レプリケーションの構成については、「[Azure 仮想ネットワーク内で Apache HBase クラスターのレプリケーションを設定する](hbase/apache-hbase-replication.md)」を参照してください。
* Azure 仮想ネットワークの詳細については、[Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)に関するページをご覧ください。

* ネットワーク セキュリティ グループの詳細については、[ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)に関するページをご覧ください。

* ユーザー定義のルートについて詳しくは、「[ユーザー定義のルートと IP 転送](../virtual-network/virtual-networks-udr-overview.md)」をご覧ください。
