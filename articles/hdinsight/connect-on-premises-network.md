---
title: オンプレミス ネットワークへの HDInsight の接続 - Azure HDInsight
description: Azure Virtual Network で HDInsight クラスターを作成して、ご使用のオンプレミス ネットワークに接続する方法について説明します。 カスタム DNS サーバーを使用して、HDInsight とオンプレミス ネットワークとの間で名前解決を構成する方法について説明します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: hrasheed
ms.openlocfilehash: d8ab61d99b617f21557b6f1ef476418e25bcee55
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016932"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>オンプレミス ネットワークへの HDInsight の接続

Azure Virtual Network と VPN Gateway を使用して、HDInsight をオンプレミス ネットワークに接続する方法について説明します。 このドキュメントでは、次の計画情報を提供します。

* オンプレミス ネットワークに接続する Azure Virtual Network で HDInsight を使用する方法。

* 仮想ネットワークとオンプレミス ネットワークの間の DNS 名前解決を構成する方法。

* ネットワーク セキュリティ グループを構成して HDInsight へのインターネット アクセスを制限する方法。

* 仮想ネットワーク上の HDInsight によって提供されるポート。

## <a name="create-the-virtual-network-configuration"></a>仮想ネットワーク構成を作成する

次のドキュメントを使用して、オンプレミス ネットワークに接続されている Azure Virtual Network を作成する方法を学習してください。
    
* [Azure Portal の使用](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

* [Azure PowerShell の使用](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

* [Azure CLI の使用](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="configure-name-resolution"></a>名前解決を構成する

接続されたネットワークで HDInsight とリソースが名前によって通信できるようにするには、次のアクションを実行する必要があります。

* Azure Virtual Network のカスタム DNS サーバーを作成する。

* 既定の Azure 再帰リゾルバーではなく、カスタム DNS サーバーを使用するように仮想ネットワークを構成する。

* カスタム DNS サーバーとオンプレミス DNS サーバーの間の転送を構成する。

この構成により次の動作が可能になります。

* __仮想ネットワークの__ DNS サフィックスを持つ完全修飾ドメイン名の要求が、カスタム DNS サーバーに転送されます。 カスタム DNS サーバーはその要求を Azure 再帰リゾルバーに転送し、リゾルバーは IP アドレスを返します。

* その他の要求はすべて、オンプレミス DNS サーバーに転送されます。 microsoft.com などのパブリック インターネット リソースの要求も、名前解決のためにオンプレミス DNS サーバーに転送されます。

次の図の緑色の線は、仮想ネットワークの DNS サフィックスで終わるリソースの要求です。 青色の線は、オンプレミス ネットワークまたはパブリック インターネットのリソースの要求です。

![このドキュメントで使用される構成で DNS 要求がどのように解決されるかを示す図](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

### <a name="create-a-custom-dns-server"></a>カスタム DNS サーバーを作成する

> [!IMPORTANT]
> DNS サーバーの作成と構成は、HDInsight を仮想ネットワークにインストールする前に行う必要があります。

[Bind](https://www.isc.org/downloads/bind/) DNS ソフトウェアを使用する Linux VM を作成するには、次の手順に従います。

> [!NOTE]
> 次の手順では [Azure Portal](https://portal.azure.com) を使用して Azure Virtual Machine を作成します。 仮想マシンを作成する他の方法については、次のドキュメントを参照してください。
>
> * [VM の作成 - Azure CLI](../virtual-machines/linux/quick-create-cli.md)
> * [VM の作成 - Azure PowerShell](../virtual-machines/linux/quick-create-portal.md)

1. [Azure Portal](https://portal.azure.com) で、__+__、__[コンピューティング]__、__[Ubuntu Server 16.04 LTS]__ の順に選択します。

    ![Ubuntu 仮想マシンを作成する](./media/connect-on-premises-network/create-ubuntu-vm.png)

2. __[基本]__ セクションで、次の情報を入力します。

    * __名前__: この仮想マシンを特定するフレンドリ名。 例、__DNSProxy__。
    * __ユーザー名__: SSH アカウントの名前。
    * __SSH 公開キー__ または __[パスワード]__: SSH アカウントの認証方法。 安全性の高いパブリック キーを使用することをお勧めします。 詳細については、[Linux VM 用の SSH キーの作成と使用](../virtual-machines/linux/mac-create-ssh-keys.md)に関するドキュメントをご覧ください。
    * __リソース グループ__: __[既存のものを使用]__ を選択し、前に作成した仮想ネットワークを含むリソース グループを選択します。
    * __場所__: 仮想ネットワークと同じ場所を選択します。

    ![仮想マシンの基本構成](./media/connect-on-premises-network/vm-basics.png)

    他のエントリは既定値のままにして、__[OK]__ を選択します。

3. __[サイズの選択]__ セクションで、VM サイズを選択します。 このチュートリアルでは、最小および最低コストのオプションを選択します。 続行するには、__[選択]__ をクリックします。

4. __[設定]__ セクションで、次の情報を入力します。

    * __仮想ネットワーク__: 前に作成した仮想ネットワークを選択します。

    * __サブネット__: 仮想ネットワークの既定のサブネットを選択します。 VPN Gateway で使用されているサブネットは選択 __しないでください__。

    * __診断ストレージ アカウント__: 既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成します。

    ![仮想ネットワークの設定](./media/connect-on-premises-network/virtual-network-settings.png)

    他のエントリは既定値のままにし、__[OK]__ を選択して続行します。

5. __[購入]__ セクションで、__[購入]__ ボタンを選択して仮想マシンを作成します。

6. 仮想マシンが作成されると、__[概要]__ セクションが表示されます。 左側の一覧で、__[プロパティ]__ を選択します。 __パブリック IP アドレス__ と __プライベート IP アドレス__ の値を保存します。 これは次のセクションで使用します。

    ![パブリックおよびプライベート IP アドレス](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Bind (DNS ソフトウェア) をインストールして構成する

1. SSH を使用して、仮想マシンの __パブリック IP アドレス__ にアクセスします。 次の例では、40.68.254.142 の仮想マシンに接続します。

    ```bash
    ssh sshuser@40.68.254.142
    ```

    `sshuser` は、クラスターの作成時に指定した SSH ユーザー アカウントで置き換えます。

    > [!NOTE]
    > `ssh` ユーティリティは、さまざまな方法で取得できます。 Linux、Unix、および macOS では、オペレーティング システムの一部として提供されます。 Windows を使用している場合は、次のオプションのいずれかを検討してください。
    >
    > * [Azure Cloud Shell](../cloud-shell/quickstart.md)
    > * [Bash on Ubuntu on Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

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

        dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

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

        Server:         10.0.0.4
        Address:        10.0.0.4#53

        Non-authoritative answer:
        Name:   dns.mynetwork.net
        Address: 192.168.0.4

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>カスタム DNS サーバーを使用するように仮想ネットワークを構成する

Azure 再帰リゾルバーではなく、カスタム DNS サーバーを使用するように仮想ネットワークを構成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で、仮想ネットワークを選択し、__[DNS サーバー]__ を選択します。

2. __[カスタム]__ を選択し、カスタム DNS サーバーの __内部 IP アドレス__ を入力します。 最後に、__[保存]__ を選択します。

    ![ネットワークのカスタム DNS サーバーを設定する](./media/connect-on-premises-network/configure-custom-dns.png)

### <a name="configure-the-on-premises-dns-server"></a>オンプレミス DNS サーバーを構成する

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

## <a name="optional-control-network-traffic"></a>省略可能: ネットワーク トラフィックを制御する

ネットワーク セキュリティ グループ (NSG) またはユーザー定義のルート (UDR) を使用して、ネットワーク トラフィックを制御できます。 NSG を使用すると、受信トラフィックと送信トラフィックをフィルター処理し、そのトラフィックを許可または拒否できます。 UDR を使用すると、仮想ネットワーク、インターネット、およびオンプレミス ネットワークのリソース間のトラフィック フローを制御できます。

> [!WARNING]
> HDInsight では、Azure クラウド内の特定の IP アドレスからの着信アクセスと、制限のない発信アクセスが必要です。 NSG または UDR を使用してトラフィックを制御する場合は、次の手順を実行する必要があります。

1. 仮想ネットワークがある場所の IP アドレスを調べます。 場所ごとの必須 IP アドレスの一覧については、「[必須 IP アドレス](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip)」を参照してください。

2. 手順 1 で識別された IP アドレスについて、その IP アドレスからの受信トラフィックを許可します。

   * __NSG__ を使用している場合: IP アドレスについて、__443__ ポートでの __受信__ トラフィックを許可します。
   * __UDR__ を使用している場合: IP アドレスについて、ルートの __次ホップ__ の種類を __インターネット__ に設定します。

Azure PowerShell または Azure CLI を使用して NSG を作成する例については、「[Azure Virtual Network を使用した HDInsight 機能の拡張](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg)」を参照してください。

## <a name="create-the-hdinsight-cluster"></a>HDInsight クラスターを作成する

> [!WARNING]
> HDInsight を仮想ネットワークにインストールする前に、カスタム DNS サーバーを構成する必要があります。

[Azure Portal を使用した HDInsight クラスターの作成](./hdinsight-hadoop-create-linux-clusters-portal.md)に関するドキュメントの手順に従って、HDInsight クラスターを作成します。

> [!WARNING]
> * クラスターの作成中に、仮想ネットワークが含まれる場所を選択してください。
>
> * 構成の __[詳細設定]__ では、前に作成した仮想ネットワークとサブネットを選択する必要があります。

## <a name="connecting-to-hdinsight"></a>HDInsight に接続する

HDInsight のほとんどのドキュメントでは、インターネット経由でクラスターにアクセスできることが前提となっています。 たとえば、 https://CLUSTERNAME.azurehdinsight.net でクラスターに接続できることが必要です。 このアドレスではパブリック ゲートウェイが使用されていますが、NSG または UDR を使用してインターネットからのアクセスが制限されている場合は、このゲートウェイを使用できません。

一部のドキュメントは、SSH セッションからクラスターに接続するときに `headnodehost` も参照しています。 このアドレスは、クラスター内のノードからのみ使用可能であり、仮想ネットワーク経由で接続しているクライアントでは使用できません。

仮想ネットワーク経由で HDInsight に直接接続するには、次の手順を使用します。

1. HDInsight クラスター ノードの内部完全修飾ドメイン名を検出するには、次のいずれかの方法を使用します。

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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

2. サービスを使用できるポートを特定するには、「[HDInsight 上の Hadoop サービスで使用されるポート](./hdinsight-hadoop-port-settings-for-services.md)」を参照してください。

    > [!IMPORTANT]
    > ヘッド ノードでホストされている一部のサービスは、一度に 1 つのノードでのみアクティブになります。 一方のヘッド ノードでサービスにアクセスしようとして、アクセスできなかった場合に、もう一方のヘッド ノードに切り替えてください。
    >
    > たとえば、Ambari は一度に 1 つのヘッド ノードでのみアクティブになります。 一方のヘッド ノードで Ambari にアクセスしようして、404 エラーが返された場合、Ambari はもう一方のヘッド ノードで実行されています。

## <a name="next-steps"></a>次の手順

* 仮想ネットワークでの HDInsight の使用の詳細については、「[Azure Virtual Network を使用した HDInsight 機能の拡張](./hdinsight-extend-hadoop-virtual-network.md)」を参照してください。

* Azure 仮想ネットワークの詳細については、[Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)に関するページをご覧ください。

* ネットワーク セキュリティ グループの詳細については、[ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)に関するページをご覧ください。

* ユーザー定義のルートについて詳しくは、「[ユーザー定義のルートと IP 転送](../virtual-network/virtual-networks-udr-overview.md)」をご覧ください。
