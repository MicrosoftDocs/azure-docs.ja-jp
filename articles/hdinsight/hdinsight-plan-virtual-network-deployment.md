---
title: Azure HDInsight 用の仮想ネットワークを計画する
description: Azure Virtual Network のデプロイを計画して HDInsight を他のクラウド リソースまたはデータ センター内のリソースに接続する方法について説明します。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 01/12/2021
ms.openlocfilehash: fe974a96b7f349c9d525d0cd0bb01a83ace57a4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939281"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Azure HDInsight 用の仮想ネットワークを計画する

この記事では、[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) (VNet) と Azure HDInsight の使用に関する背景情報を提供します。 また、HDInsight クラスター用の仮想ネットワークを実装する前に行う必要がある設計および実装上の決定についても説明します。 計画段階が完了したら、[Azure HDInsight クラスター用の仮想ネットワークの作成](hdinsight-create-virtual-network.md)に進むことができます。 ネットワーク セキュリティ グループ (NSG) とユーザー定義ルートを適切に構成するために必要な HDInsight 管理 IP アドレスの詳細については、「[HDInsight の管理 IP アドレス](hdinsight-management-ip-addresses.md)」を参照してください。

Azure Virtual Network を使用すると、次のシナリオが可能になります。

* オンプレミス ネットワークから HDInsight へ直接接続する。
* HDInsight を Azure Virtual Network 内のデータ ストアに接続する。
* インターネットで公開されていない Apache Hadoop サービスに直接アクセスする。 たとえば、Apache Kafka API や Apache HBase Java API などです。

> [!IMPORTANT]
> VNET で HDInsight クラスターを作成すると、NIC やロード バランサーなど、いくつかのネットワーク リソースが作成されます。 これらのネットワーク リソースは、クラスターが VNET で正常に機能するために必要なため、削除 **しないでください**。
>
> 2019 年 2 月 28 日以降、VNET で作成された "新しい" HDInsight クラスターのネットワーク リソース (NIC、LB など) は、同じ HDInsight クラスター リソース グループにプロビジョニングされます。 以前は、これらのリソースは、VNET リソース グループにプロビジョニングされていました。 現在実行中のクラスターや、VNET なしで作成されたクラスターへの変更はありません。

## <a name="planning"></a>計画

仮想ネットワークに HDInsight をインストールする計画を立てる際に確認しておく必要がある事項を次に示します。

* HDInsight を既存の仮想ネットワークにインストールする必要がありますか。 または、新しいネットワークを作成しますか。

    既存の仮想ネットワークを使用している場合は、HDInsight をインストールする前に、ネットワーク構成の変更が必要な場合があります。 詳細については、 「[既存の仮想ネットワークへの HDInsight の追加](#existingvnet)」のセクションをご覧ください。

* HDInsight を含む仮想ネットワークを別の仮想ネットワークまたはオンプレミス ネットワークに接続しますか。

    ネットワーク間でリソースを簡単に利用できるようにするには、カスタムの DNS を作成し、DNS 転送を構成する必要があります。 詳細については、「[複数のネットワークの接続](#multinet)」のセクションをご覧ください。

* HDInsight への送受信トラフィックを制限/リダイレクトしますか。

    HDInsight は、Azure データ センター内の特定の IP アドレスとの制限のない通信を必要とします。 クライアントとの通信用に、ファイアウォールの通過が許可されているポートも複数必要です。 詳細については、[ネットワーク トラフィックのコントロール](./control-network-traffic.md)に関する記事を参照してください。

## <a name="add-hdinsight-to-an-existing-virtual-network"></a><a id="existingvnet"></a>既存の仮想ネットワークへの HDInsight の追加

このセクションの手順を使用して、新しい HDInsight を既存の Azure Virtual Network に追加する方法をご確認ください。

> [!NOTE]  
> - 仮想ネットワークに既存の HDInsight クラスターを追加することはできません。
> - VNET と、作成されるクラスターは、同じサブスクリプション内に存在する必要があります。

1. 使用中の仮想ネットワークは、クラシック デプロイ モデルですか、Resource Manager デプロイ モデルですか。

    HDInsight 3.4 以降では、Resource Manager の仮想ネットワークが必要です。 HDInsight の以前のバージョンでは、従来の仮想ネットワークが必要です。

    使用している既存のネットワークが従来の仮想ネットワークの場合は、Resource Manager の仮想ネットワークを作成してから、それぞれのネットワークを接続する必要があります。 [従来の VNet を新しい VNet に接続します](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)。

    一度接続すると、Resource Manager ネットワークにインストールされた HDInsight は従来のネットワーク内のリソースとやり取りできます。

2. 仮想ネットワークの送受信トラフィックを制限するために、ネットワーク セキュリティ グループ、ユーザー定義のルート、Virtual Network Appliances を使用していますか。

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
        > ネットワーク セキュリティ グループ ルールは、ルールの優先順位に基づく順序で適用されます。 トラフィック パターンに一致する最初のルールが適用され、そのトラフィックには他のルールは適用されません。 ルールは、最も制限の緩やかなルールから最も制限の厳しいルールへと順番付けします。 詳細については、「[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルタリング](../virtual-network/network-security-groups-overview.md)」をご覧ください。

    * ユーザー定義のルート

        `RESOURCEGROUP` を仮想ネットワークが含まれるリソース グループの名前に置き換えてから、コマンドを入力します。

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        詳細については、「 [ルートのトラブルシューティング](../virtual-network/diagnose-network-routing-problem.md)」をご覧ください。

3. HDInsight クラスターを作成し、構成時に Azure Virtual Network を選択します。 次のドキュメントの手順を使って、クラスターの作成プロセスを理解してください。

    * [Azure Portal を使用した HDInsight の作成](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Azure PowerShell を使用した HDInsight の作成](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Azure クラシック CLI を使用した HDInsight の作成](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Azure Resource Manager テンプレートを使用した HDInsight の作成](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > 仮想ネットワークへの HDInsight の追加はオプションの構成手順です。 必ず、クラスターを構成するときに仮想ネットワークを選択してください。

## <a name="connecting-multiple-networks"></a><a id="multinet"></a>複数のネットワークの接続

複数のネットワークを構成する際の最大の課題は、ネットワーク間の名前解決です。

Azure には、仮想ネットワークにインストールされている Azure サービスの名前解決が用意されています。 この組み込みの名前解決を使用することにより、HDInsight は、完全修飾ドメイン名 (FQDN) を使用して、次のリソースに接続することができます。

* インターネットで利用可能なリソース。 たとえば、microsoft.com、windowsupdate.com など。

* 同じ Azure Virtual Network 内にあるリソース (リソースの __内部 DNS 名__ を使用)。 たとえば、既定の名前解決を使用するときに、HDInsight ワーカー ノードに割り当てられる内部 DNS 名の例を、次に示します。

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    これら両方のノードは、内部 DNS 名を使用して、互いに直接通信でき、また HDInsight 内の他のノードとも直接通信できます。

既定の名前解決では、仮想ネットワークに結合されているネットワーク内のリソースの名前解決を HDInsight が行うことは __できません__。 よくある例として、オンプレミス ネットワークと仮想ネットワークの結合があります。 既定の名前解決のみを使用している場合、HDInsight はオンプレミス ネットワーク内のリソースに名前でアクセスすることはできません。 逆の場合も同様で、オンプレミス ネットワーク内のリソースは、仮想ネットワーク内のリソースに名前でアクセスすることはできません。

> [!WARNING]  
> HDInsight クラスターを作成する前に、カスタムの DNS サーバーを作成し、これを使用するように仮想ネットワークを構成する必要があります。

結合されたネットワーク内のリソースと仮想ネットワーク間の名前解決を可能にするには、次のアクションを実行する必要があります。

1. HDInsight のインストールを計画している Azure Virtual Network でカスタムの DNS サーバーを作成します。

2. カスタム DNS サーバーを使用するように仮想ネットワークを構成します。

3. Azure が仮想ネットワークに割り当てた DNS サフィックスを見つけます。 この値は、`0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` のようになります。 DNS サフィックスを見つける方法については、「[例:カスタム DNS](hdinsight-create-virtual-network.md#example-dns)」のセクションをご覧ください。

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

     各構成の例については、「[例:カスタム DNS](hdinsight-create-virtual-network.md#example-dns)」のセクションをご覧ください。

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

## <a name="load-balancing"></a>負荷分散

HDInsight クラスターを作成すると、ロード バランサーも作成されます。 このロード バランサーの種類は [Basic SKU レベル](../load-balancer/skus.md)にあり、一定の制約があります。 これらの制約の 1 つは、異なるリージョンに 2 つの仮想ネットワークがある場合、基本的なロード バランサーに接続できないことです。 詳細については、[仮想ネットワークに関する FAQ: グローバル vnet ピアリングでの制約](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* コード サンプルおよび Azure Virtual Network の作成例については、[Azure HDInsight クラスター用の仮想ネットワークの作成](hdinsight-create-virtual-network.md)に関するページを参照してください。
* HDInsight を オンプレミス ネットワークに接続する構成方法の詳しい例については、 [HDInsight のオンプレミス ネットワークへの接続](./connect-on-premises-network.md)に関するページをご覧ください。
* Azure 仮想ネットワークの詳細については、[Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)に関するページをご覧ください。
* ネットワーク セキュリティ グループの詳細については、[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)に関するページをご覧ください。
* ユーザー定義のルートについて詳しくは、「[ユーザー定義のルートと IP 転送](../virtual-network/virtual-networks-udr-overview.md)」をご覧ください。
* ファイアウォールの統合を含むトラフィックのコントロールの詳細については、[ネットワーク トラフィックのコントロール](./control-network-traffic.md)に関するページをご覧ください。
