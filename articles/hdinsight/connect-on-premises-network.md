---
title: オンプレミス ネットワークへの Azure HDInsight の接続
description: Azure Virtual Network で HDInsight クラスターを作成して、ご使用のオンプレミス ネットワークに接続する方法について説明します。 カスタム DNS サーバーを使用して、HDInsight とオンプレミス ネットワークとの間で名前解決を構成する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: ad43af0f6f9bd8d5d78cef78b26345436169c0fd
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034126"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>オンプレミス ネットワークへの HDInsight の接続

Azure Virtual Network と VPN Gateway を使用して、HDInsight をオンプレミス ネットワークに接続する方法について説明します。 このドキュメントでは、次の計画情報を提供します。

* オンプレミス ネットワークに接続する Azure Virtual Network で HDInsight を使用する方法。
* 仮想ネットワークとオンプレミス ネットワークの間の DNS 名前解決を構成する方法。
* ネットワーク セキュリティ グループを構成して HDInsight へのインターネット アクセスを制限する方法。
* 仮想ネットワーク上の HDInsight によって提供されるポート。

## <a name="overview"></a>概要

接続されたネットワークで HDInsight とリソースが名前によって通信できるようにするには、次のアクションを実行する必要があります。

* Azure 仮想ネットワークを作成する。
* Azure Virtual Network のカスタム DNS サーバーを作成する。
* 既定の Azure 再帰リゾルバーではなく、カスタム DNS サーバーを使用するように仮想ネットワークを構成する。
* カスタム DNS サーバーとオンプレミス DNS サーバーの間の転送を構成する。

この構成により次の動作が可能になります。

* __仮想ネットワークの__ DNS サフィックスを持つ完全修飾ドメイン名の要求が、カスタム DNS サーバーに転送されます。 カスタム DNS サーバーはその要求を Azure 再帰リゾルバーに転送し、リゾルバーは IP アドレスを返します。
* その他の要求はすべて、オンプレミス DNS サーバーに転送されます。 microsoft.com などのパブリック インターネット リソースの要求も、名前解決のためにオンプレミス DNS サーバーに転送されます。

次の図の緑色の線は、仮想ネットワークの DNS サフィックスで終わるリソースの要求です。 青色の線は、オンプレミス ネットワークまたはパブリック インターネットのリソースの要求です。

![構成で DNS 要求がどのように解決されるかを示す図](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>前提条件

* SSH クライアント 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](./hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。
* PowerShell を使用する場合は、[AZ モジュール](https://docs.microsoft.com/powershell/azure/overview)が必要です。
* Azure CLI を使用したい場合で、なおかつまだインストールしていない場合は、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

## <a name="create-virtual-network-configuration"></a>仮想ネットワーク構成を作成する

次のドキュメントを使用して、オンプレミス ネットワークに接続されている Azure Virtual Network を作成する方法を学習してください。

* [Azure Portal の使用](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Azure PowerShell の使用](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Azure CLI の使用](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>カスタム DNS サーバーを作成する

> [!IMPORTANT]  
> DNS サーバーの作成と構成は、HDInsight を仮想ネットワークにインストールする前に行う必要があります。

次の手順では [Azure portal](https://portal.azure.com) を使用して Azure Virtual Machine を作成します。 他の方法で仮想マシンを作成する場合は、[VM の作成 - Azure CLI](../virtual-machines/linux/quick-create-cli.md) および [VM の作成 - Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md) に関するページをご覧ください。  [Bind](https://www.isc.org/downloads/bind/) DNS ソフトウェアを使用する Linux VM を作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
  
2. 左側のメニューから、 **[+ リソースの作成]**  >  **[Compute]**  >  **[Ubuntu Server 18.04 LTS]** に移動します。

    ![Ubuntu 仮想マシンを作成する](./media/connect-on-premises-network/create-ubuntu-virtual-machine.png)

3. __[基本]__ タブで、次の情報を入力します。  
  
    | フィールド | 値 |
    | --- | --- |
    |Subscription |適切なサブスクリプションを選択します。|
    |Resource group |前に作成した仮想ネットワークが含まれているリソース グループを選択します。|
    |仮想マシン名 | この仮想マシンを特定するフレンドリ名を入力します。 この例では、**DNSProxy** を使用します。|
    |リージョン | 前に作成した仮想ネットワークと同じリージョンを選択します。  すべてのリージョンですべての VM サイズを使用できるわけではありません。  |
    |可用性のオプション |  必要な可用性のレベルを選択します。  Azure は、アプリケーションの可用性と耐障害性を管理するためのさまざまなオプションを提供しています。  可用性ゾーンまたは可用性セット内のレプリケートされた VM を使用して、データセンターの障害やメンテナンス イベントからアプリやデータを保護するためのソリューションを設計します。 この例では、 **[インフラストラクチャ冗長は必要ありません]** を使用します。 |
    |Image | **[Ubuntu Server 18.04 LTS]** のままにしてください。 |
    |認証の種類 | __パスワード__ または __SSH 公開キー__:SSH アカウントの認証方法。 安全性の高いパブリック キーを使用することをお勧めします。 この例では、 **[パスワード]** を使用します。  詳細については、[Linux VM 用の SSH キーの作成と使用](../virtual-machines/linux/mac-create-ssh-keys.md)に関するドキュメントをご覧ください。|
    |ユーザー名 |VM の管理者ユーザー名を入力します。  この例では、**sshuser** を使用します。|
    |[パスワード] または [SSH 公開キー] | 使用可能なフィールドは、 **[認証の種類]** として選択された内容によって決定されます。  適切な値を入力します。|
    |パブリック受信ポート|**[選択したポートを許可する]** を選択します。 次に、 **[受信ポートを選択]** ボックスの一覧から **[SSH (22)]** を選択します。|

    ![仮想マシンの基本構成](./media/connect-on-premises-network/virtual-machine-basics.png)

    他のエントリは既定値のままにして、 **[ネットワーク]** タブを選択します。

4. **[ネットワーク]** タブで、次の情報を入力します。

    | フィールド | 値 |
    | --- | --- |
    |仮想ネットワーク | 前に作成した仮想ネットワークを選択します。|
    |Subnet | 前に作成した仮想ネットワークの既定のサブネットを選択します。 VPN Gateway で使用されているサブネットは選択 __しないでください__。|
    |パブリック IP | 自動入力されている値を使用します。  |

    ![HDInsight 仮想ネットワークの設定](./media/connect-on-premises-network/virtual-network-settings.png)

    他のエントリは既定値のままにして、 **[確認と作成]** を選択します。

5. **[確認と作成]** タブで、 **[作成]** を選択して仮想マシンを作成します。

### <a name="review-ip-addresses"></a>IP アドレスの確認
仮想マシンが作成されると、 **[リソースに移動]** ボタンが含まれている **[デプロイメントに成功しました]** という通知を受け取ります。  **[リソースに移動]** を選択して、新しい仮想マシンに移動します。  新しい仮想マシンの既定のビューで、次の手順に従って関連する IP アドレスを特定します。

1. **[設定]** で **[プロパティ]** を選択します。

2. 後で使用するために **[パブリック IP アドレス/DNS 名ラベル]** と **[プライベート IP アドレス]** の値をメモします。

   ![パブリックおよびプライベート IP アドレス](./media/connect-on-premises-network/virtual-machine-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Bind (DNS ソフトウェア) をインストールして構成する

1. SSH を使用して、仮想マシンの __パブリック IP アドレス__ にアクセスします。 `sshuser` は、VM の作成時に指定した SSH ユーザー アカウントで置き換えます。 次の例では、40.68.254.142 の仮想マシンに接続します。

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. Bind をインストールするには、SSH セッションから次のコマンドを使用します。

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. 名前解決の要求をオンプレミス DNS サーバーに転送するように Bind を構成するには、`/etc/bind/named.conf.options` ファイルの内容として、次のテキストを使用します。

        acl goodclients {
            10.0.0.0/16; # Replace with the IP address range of the virtual network
            10.1.0.0/16; # Replace with the IP address range of the on-premises network
            localhost;
            localnets;
        };

        options {
                directory "/var/cache/bind";

                recursion yes;

                allow-query { goodclients; };

                forwarders {
                192.168.0.1; # Replace with the IP address of the on-premises DNS server
                };

                dnssec-validation auto;

                auth-nxdomain no;    # conform to RFC1035
                listen-on { any; };
        };

    > [!IMPORTANT]  
    > `goodclients` セクションの値は、仮想ネットワークとオンプレミス ネットワークの IP アドレス範囲で置き換えます。 このセクションは、この DNS サーバーが受け入れる要求の転送元アドレスを定義します。
    >
    > `forwarders` セクションの `192.168.0.1` エントリは、オンプレミス DNS サーバーの IP アドレスで置き換えます。 このエントリは、DNS 要求を解決のためにオンプレミス DNS サーバーにルーティングします。

    このファイルを編集するには、次のコマンドを使用します。

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    ファイルを保存するには、__Ctrl + X__ キー、__Y__ キー、__Enter__ キーの順に押します。

4. SSH セッションでは、次のコマンドを使用します。

    ```bash
    hostname -f
    ```

    このコマンドにより、次のテキストのような値が返されます。

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` テキストは、この仮想ネットワークの __DNS サフィックス__ です。 この値を保存します。これは後で使用します。

5. 仮想ネットワークでリソースの DNS 名を解決するように Bind を構成するには、`/etc/bind/named.conf.local` ファイルの内容として、次のテキストを使用します。

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]  
    > `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` は、前に取得した DNS サフィックスで置き換える必要があります。

    このファイルを編集するには、次のコマンドを使用します。

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    ファイルを保存するには、__Ctrl + X__ キー、__Y__ キー、__Enter__ キーの順に押します。

6. Bind を起動するには、次のコマンドを使用します。

    ```bash
    sudo service bind9 restart
    ```

7. Bind がオンプレミス ネットワークのリソース名を解決できることを確認するには、次のコマンドを使用します。

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > `dns.mynetwork.net` は、オンプレミス ネットワークのリソースの完全修飾ドメイン名 (FQDN) で置き換えます。
    >
    > `10.0.0.4` は、仮想ネットワークのカスタム DNS サーバーの __内部 IP アドレス__ に置き換えます。

    次のテキストのような応答が表示されます。

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>カスタム DNS サーバーを使用するように仮想ネットワークを構成する

Azure 再帰リゾルバーではなく、カスタム DNS サーバーを使用するように仮想ネットワークを構成するには、[Azure portal](https://portal.azure.com) から次の手順に従います。

1. 左側のメニューから **[すべてのサービス]**  >  **[ネットワーキング]**  >  **[仮想ネットワーク]** の順に移動します。

2. リストから仮想ネットワークを選択して、仮想ネットワークの既定のビューを開きます。  

3. 既定のビューの **[設定]** で、 **[DNS サーバー]** を選択します。  

4. __[カスタム]__ を選択し、カスタム DNS サーバーの**プライベート IP アドレス** を入力します。   

5. __[保存]__ を選択します。  <br />  

    ![ネットワークのカスタム DNS サーバーを設定する](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>オンプレミス DNS サーバーを構成する

前のセクションでは、オンプレミス DNS サーバーに要求を転送するように、カスタム DNS サーバーを構成しました。 次は、カスタム DNS サーバーに要求を転送するように、オンプレミス DNS サーバーを構成する必要があります。

DNS サーバーを構成する具体的な手順については、DNS サーバー ソフトウェアのドキュメントを参照してください。 __条件付きフォワーダー__ を構成する手順を検索してください。

条件付き転送では、特定の DNS サフィックスの要求のみが転送されます。 この場合は、仮想ネットワークの DNS サフィックスのフォワーダーを構成する必要があります。 このサフィックスの要求は、カスタム DNS サーバーの IP アドレスに転送する必要があります。 

次のテキストは、**Bind** DNS ソフトウェアの条件付きフォワーダー構成の例です。

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

**Windows Server 2016** での DNS の使用については、[Add-DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone) のドキュメントを参照してください。

オンプレミス DNS サーバーを構成したら、オンプレミス ネットワークから `nslookup` を使用して、仮想ネットワークで名前を解決できることを確認できます。 次の例をご覧ください。 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

この例では、196.168.0.4 のオンプレミス DNS サーバーを使用して、カスタム DNS サーバーの名前を解決します。 IP アドレスは、オンプレミス DNS サーバーの IP アドレスで置き換えてください。 `dnsproxy` アドレスは、カスタム DNS サーバーの完全修飾ドメイン名で置き換えてください。

## <a name="optional-control-network-traffic"></a>省略可能:ネットワーク トラフィックを制御する

ネットワーク セキュリティ グループ (NSG) またはユーザー定義のルート (UDR) を使用して、ネットワーク トラフィックを制御できます。 NSG を使用すると、受信トラフィックと送信トラフィックをフィルター処理し、そのトラフィックを許可または拒否できます。 UDR を使用すると、仮想ネットワーク、インターネット、およびオンプレミス ネットワークのリソース間のトラフィック フローを制御できます。

> [!WARNING]  
> HDInsight では、Azure クラウド内の特定の IP アドレスからの着信アクセスと、制限のない発信アクセスが必要です。 NSG または UDR を使用してトラフィックを制御する場合は、次の手順を実行する必要があります。

1. 仮想ネットワークがある場所の IP アドレスを調べます。 場所ごとの必須 IP アドレスの一覧については、「[必須 IP アドレス](./hdinsight-management-ip-addresses.md)」を参照してください。

2. 手順 1 で識別された IP アドレスについて、その IP アドレスからの受信トラフィックを許可します。

   * __NSG__ を使用している場合:IP アドレスについて、__443__ ポートでの __受信__ トラフィックを許可します。
   * __UDR__ を使用している場合:IP アドレスについて、ルートの __[次ホップ]__ の種類を __[インターネット]__ に設定します。

Azure PowerShell または Azure CLI を使用して NSG を作成する例については、「[Azure Virtual Network を使用した HDInsight 機能の拡張](hdinsight-create-virtual-network.md#hdinsight-nsg)」を参照してください。

## <a name="create-the-hdinsight-cluster"></a>HDInsight クラスターを作成する

> [!WARNING]  
> HDInsight を仮想ネットワークにインストールする前に、カスタム DNS サーバーを構成する必要があります。

[Azure Portal を使用した HDInsight クラスターの作成](./hdinsight-hadoop-create-linux-clusters-portal.md)に関するドキュメントの手順に従って、HDInsight クラスターを作成します。

> [!WARNING]  
> * クラスターの作成中に、仮想ネットワークが含まれる場所を選択してください。
> * 構成の __[詳細設定]__ では、前に作成した仮想ネットワークとサブネットを選択する必要があります。

## <a name="connecting-to-hdinsight"></a>HDInsight に接続する

HDInsight のほとんどのドキュメントでは、インターネット経由でクラスターにアクセスできることが前提となっています。 たとえば、`https://CLUSTERNAME.azurehdinsight.net` でクラスターに接続できることが必要です。 このアドレスではパブリック ゲートウェイが使用されていますが、NSG または UDR を使用してインターネットからのアクセスが制限されている場合は、このゲートウェイを使用できません。

一部のドキュメントは、SSH セッションからクラスターに接続するときに `headnodehost` も参照しています。 このアドレスは、クラスター内のノードからのみ使用可能であり、仮想ネットワーク経由で接続しているクライアントでは使用できません。

仮想ネットワーク経由で HDInsight に直接接続するには、次の手順を使用します。

1. HDInsight クラスター ノードの内部完全修飾ドメイン名を検出するには、次のいずれかの方法を使用します。

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. サービスを使用できるポートを特定するには、「[HDInsight 上の Apache Hadoop サービスで使用されるポート](./hdinsight-hadoop-port-settings-for-services.md)」を参照してください。

    > [!IMPORTANT]  
    > ヘッド ノードでホストされている一部のサービスは、一度に 1 つのノードでのみアクティブになります。 一方のヘッド ノードでサービスにアクセスしようとして、アクセスできなかった場合に、もう一方のヘッド ノードに切り替えてください。
    >
    > たとえば、Apache Ambari は一度に 1 つのヘッド ノードでのみアクティブになります。 一方のヘッド ノードで Ambari にアクセスしようして、404 エラーが返された場合、Ambari はもう一方のヘッド ノードで実行されています。

## <a name="next-steps"></a>次の手順

* 仮想ネットワークにおける HDInsight の使用方法の詳細については、[Azure HDInsight クラスター用の仮想ネットワークのデプロイ計画](./hdinsight-plan-virtual-network-deployment.md)に関するページを参照してください。

* Azure 仮想ネットワークの詳細については、[Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)に関するページをご覧ください。

* ネットワーク セキュリティ グループの詳細については、[ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)に関するページをご覧ください。

* ユーザー定義のルートについて詳しくは、「[ユーザー定義のルートと IP 転送](../virtual-network/virtual-networks-udr-overview.md)」をご覧ください。
