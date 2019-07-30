---
title: Azure 仮想ネットワーク内で HBase クラスターのレプリケーションを設定する - Azure HDInsight
description: 負荷分散、高可用性、ダウンタイムなしの移行と更新、およびディザスター リカバリーを実現するために、ある HDInsight バージョンから別のバージョンへの HBase レプリケーションを設定する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/15/2018
ms.openlocfilehash: 38d3c61acee9dca18ab1f863d878e02f7437a600
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67433717"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Azure 仮想ネットワーク内で Apache HBase クラスターのレプリケーションを設定する

Azure の 1 つの仮想ネットワーク内または 2 つの仮想ネットワーク間で [Apache HBase](https://hbase.apache.org/) レプリケーションを設定する方法について説明します。

クラスターのレプリケーションでは、ソース プッシュの手法が使用されます。 HBase クラスターは、ソースまたはターゲットになることも、両方のロールを同時に満たすこともできます。 レプリケーションは非同期です。 レプリケーションの目的は、最終的な一貫性です。 レプリケーションが有効になった列ファミリに対する編集をソースが受け取ると、その編集はすべてのターゲット クラスターに伝達されます。 クラスター間でデータがレプリケートされるときは、ソース クラスターとそのデータを既に消費しているすべてのクラスターが追跡されて、レプリケーション ループが防止されます。

この記事では、ソースとターゲット間のレプリケーションを設定します。 他のクラスター トポロジについては、[Apache HBase のリファレンス ガイド](https://hbase.apache.org/book.html#_cluster_replication)を参照してください。

次に示すのは、単一の仮想ネットワークでの HBase レプリケーションの使用例です。

* 負荷分散。 たとえば、ターゲット クラスターでスキャンや MapReduce ジョブを実行し、ソース クラスターでデータを取り込みます。
* 高可用性の追加。
* HBase クラスター間のデータの移行。
* Azure HDInsight クラスターのバージョンのアップグレード。

次に示すのは、2 つの仮想ネットワーク間での HBase レプリケーションの使用例です。

* ディザスター リカバリーの設定。
* アプリケーションの負荷分散とパーティション分割。
* 高可用性の追加。

クラスターは、[GitHub](https://github.com/Azure/hbase-utils/tree/master/replication) の[スクリプト アクション](../hdinsight-hadoop-customize-cluster-linux.md)のスクリプトを使用してレプリケートできます。

## <a name="prerequisites"></a>前提条件
この記事を開始する前に、Azure サブスクリプションが必要です。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

## <a name="set-up-the-environments"></a>環境を設定する

3 つの構成オプションがあります。

- 1 つの Azure 仮想ネットワーク内の 2 つの Apache HBase クラスター。
- 同じリージョンの 2 つの異なる仮想ネットワーク内の 2 つの Apache HBase クラスター。
- 2 つの異なるリージョンの 2 つの異なる仮想ネットワーク内の 2 つの Apache HBase クラスター (geo レプリケーション)。

この記事では、geo レプリケーション シナリオについて説明します。

環境を設定しやすくするために、複数の [Azure Resource Manager テンプレート](../../azure-resource-manager/resource-group-overview.md)が用意されています。 他の方法で環境を設定する場合は、次の記事を参照してください。

- [HDInsight で Apache Hadoop クラスターを作成する](../hdinsight-hadoop-provision-linux-clusters.md)
- [Azure Virtual Network での Apache HBase クラスターの作成](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>2 つの異なるリージョンに 2 つの仮想ネットワークを設定する

2 つの異なるリージョンの 2 つの仮想ネットワークと、その VNet 間の VPN 接続を作成するテンプレートを使用するには、次の **[Deploy to Azure]** ボタンを選択します。 テンプレートの定義は、[パブリック BLOB ストレージ](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json)に格納されています。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

テンプレートには、一部の値がハードコーディングされています。それらの値を次に示します。

**VNet 1**

| プロパティ | 値 |
|----------|-------|
| Location | 米国西部 |
| VNet の名前 | &lt;クラスター名のプレフィックス>-vnet1 |
| アドレス空間プレフィックス | 10.1.0.0/16 |
| サブネット名 | subnet 1 |
| サブネットのプレフィックス | 10.1.0.0/24 |
| サブネット (ゲートウェイ) 名 | GatewaySubnet (変更不可) |
| サブネット (ゲートウェイ) プレフィックス | 10.1.255.0/27 |
| ゲートウェイ名 | vnet1gw |
| ゲートウェイの種類 | Vpn |
| ゲートウェイ VPN の種類 | RouteBased |
| ゲートウェイ SKU | Basic |
| ゲートウェイの IP | vnet1gwip |

**VNet 2**

| プロパティ | 値 |
|----------|-------|
| Location | East US |
| VNet の名前 | &lt;クラスター名のプレフィックス>-vnet2 |
| アドレス空間プレフィックス | 10.2.0.0/16 |
| サブネット名 | subnet 1 |
| サブネットのプレフィックス | 10.2.0.0/24 |
| サブネット (ゲートウェイ) 名 | GatewaySubnet (変更不可) |
| サブネット (ゲートウェイ) プレフィックス | 10.2.255.0/27 |
| ゲートウェイ名 | vnet2gw |
| ゲートウェイの種類 | Vpn |
| ゲートウェイ VPN の種類 | RouteBased |
| ゲートウェイ SKU | Basic |
| ゲートウェイの IP | vnet1gwip |

## <a name="setup-dns"></a>DNS をセットアップする

最後のセクションで、テンプレートは、2 つの仮想ネットワークのそれぞれの中に Ubuntu 仮想マシンを作成します。  このセクションでは、2 つの DNS 仮想マシンに Bind をインストールし、2 つの仮想マシンで DNS の転送を構成します。

Bind をインストールするには、2 つの DNS 仮想マシンのパブリック IP アドレスを見つける必要があります。

1. [Azure Portal](https://portal.azure.com)を開きます。
2. **[リソース グループ] > <リソース グループ名> [vnet1DNS]** を選択して、DNS 仮想マシンを開きます。  リソース グループ名は、最後の手順で作成する名前です。 既定の DNS 仮想マシン名は、*vnet1DNS* と *vnet2NDS* です。
3. **[プロパティ]** を選択して、仮想ネットワークのプロパティ ページを開きます。
4. **[パブリック IP アドレス]** を書き留めます。さらに、 **[プライベート IP アドレス]** を確認します。  プライベート IP アドレスは、vnet1DNS では **10.1.0.4**、vnet2DNS では **10.2.0.4** です。  
5. 既定の (Azure で提供されている) DNS サーバーを使用して受信および送信アクセスでパッケージをダウンロードして Bind をインストールできるように、次の手順で両方の仮想ネットワークの DNS サーバーを変更します。

Bind をインストールするには、次の手順に従います。

1. SSH を使用して、DNS 仮想マシンの __パブリック IP アドレス__ にアクセスします。 次の例では、40.68.254.142 の仮想マシンに接続します。

    ```bash
    ssh sshuser@40.68.254.142
    ```

    `sshuser` を、DNS 仮想マシンの作成時に指定した SSH ユーザー アカウントに置き換えます。

    > [!NOTE]  
    > `ssh` ユーティリティは、さまざまな方法で取得できます。 Linux、Unix、および macOS では、オペレーティング システムの一部として提供されます。 Windows を使用している場合は、次のオプションのいずれかを検討してください。
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash on Ubuntu on Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Bind をインストールするには、SSH セッションから次のコマンドを使用します。

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. オンプレミスの DNS サーバーに名前解決要求を転送するように Bind を構成します。 そのために、`/etc/bind/named.conf.options` ファイルの内容として次のテキストを使用します。

    ```
    acl goodclients {
        10.1.0.0/16; # Replace with the IP address range of the virtual network 1
        10.2.0.0/16; # Replace with the IP address range of the virtual network 2
        localhost;
        localhost;
    };
    
    options {
        directory "/var/cache/bind";
        recursion yes;
        allow-query { goodclients; };

        forwarders {
            168.63.129.16; #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]  
    > `goodclients` セクションの値を、2 つの仮想ネットワークの IP アドレス範囲に置き換えます。 このセクションは、この DNS サーバーが受け入れる要求の転送元アドレスを定義します。

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

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` テキストは、この仮想ネットワークの __DNS サフィックス__ です。 この値を保存します。これは後で使用します。

    他の DNS サーバーの DNS サフィックスも見つけておく必要があります。 次の手順で必要になります。

5. 仮想ネットワークでリソースの DNS 名を解決するように Bind を構成するには、`/etc/bind/named.conf.local` ファイルの内容として、次のテキストを使用します。

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` を、別の仮想ネットワークの DNS サフィックスに置き換えます。 フォワーダー IP は、他の仮想ネットワーク内の DNS サーバーのプライベート IP アドレスです。

    このファイルを編集するには、次のコマンドを使用します。

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    ファイルを保存するには、__Ctrl + X__ キー、__Y__ キー、__Enter__ キーの順に押します。

6. Bind を起動するには、次のコマンドを使用します。

    ```bash
    sudo service bind9 restart
    ```

7. Bind が他の仮想ネットワーク内のリソース名を解決できることを確認するには、次のコマンドを使用します。

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` を、他のネットワーク内の DNS 仮想マシンの完全修飾ドメイン名 (FQDN) に置き換えます。
    >
    > `10.2.0.4` を、他の仮想ネットワーク内のカスタム DNS サーバーの __内部 IP アドレス__ に置き換えます。

    次のテキストのような応答が表示されます。

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    ここまでは、DNS サーバーの IP アドレスを指定せずに他のネットワークからの IP アドレスを参照することはできません。

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>カスタム DNS サーバーを使用するように仮想ネットワークを構成する

Azure 再帰リゾルバーではなく、カスタム DNS サーバーを使用するように仮想ネットワークを構成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で、仮想ネットワークを選択し、 __[DNS サーバー]__ を選択します。

2. __[カスタム]__ を選択し、カスタム DNS サーバーの __内部 IP アドレス__ を入力します。 最後に、 __[保存]__ を選択します。

6. vnet1 の DNS サーバー仮想マシンを開き、 **[再起動]** をクリックします。  DNS 構成が有効であることを確認するには、仮想ネットワーク内のすべての仮想マシンを再起動する必要があります。
7. 手順を繰り返して、vnet2 のカスタムの DNS サーバーを構成します。

DNS 構成をテストするには、SSH を使用して 2 つの DNS 仮想マシンに接続し、他の仮想ネットワークの DNS サーバーをそのホスト名を使用して ping します。 うまくいかない場合は、次のコマンドを使用して DNS の状態をチェックします。

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Apache HBase クラスターを作成する

2 つの仮想ネットワークのそれぞれに、次の構成の [Apache HBase](https://hbase.apache.org/) クラスターを作成します。

- **リソース グループ名**: 仮想ネットワークの作成時と同じリソース グループ名を使用します。
- **クラスターの種類**:hbase
- **バージョン**:HBase 1.1.2 (HDI 3.6)
- **場所**:仮想ネットワークと同じ場所を使います。  既定では、vnet1 は *[米国西部]* 、vnet2 は *[米国東部]* です。
- **ストレージ**:クラスター用の新しいストレージ アカウントを作成します。
- **仮想ネットワーク**(ポータルの [詳細設定]):最後の手順で作成した vnet1 を選択します。
- **サブネット**:テンプレートで使われる既定の名前は **subnet1** です。

環境が正しく構成されていることを確認するには、2 つのクラスター間でヘッド ノードの FQDN に ping できる必要があります。

## <a name="load-test-data"></a>テスト データの読み込み

クラスターをレプリケートする場合は、レプリケートするテーブルを指定する必要があります。 このセクションでは、ソース クラスターにデータを読み込みます。 次のセクションで、2 つのクラスター間のレプリケーションを有効にします。

**Contacts** テーブルを作成し、そのテーブルにいくつかデータを挿入するには、[Apache HBase のチュートリアル:HDInsight の Apache HBase の使用を開始する方法](apache-hbase-tutorial-get-started-linux.md)に関するページの指示に従います。

## <a name="enable-replication"></a>レプリケーションを有効にする

次の手順は、Azure Portal からスクリプト アクションのスクリプトを呼び出す方法を示しています。 Azure PowerShell と Azure クラシック CLI を使用したスクリプト アクションの実行については、[スクリプト アクションを使用して HDInsight クラスターをカスタマイズする方法](../hdinsight-hadoop-customize-cluster-linux.md)に関するページを参照してください。

**Azure Portal から HBase レプリケーションを有効にするには**

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ソース HBase クラスターを開きます。
3. クラスター メニューの **[スクリプト アクション]** を選択します。
4. ページの上部にある **[新規で送信]** を選択します。
5. 次の情報を選択するか入力します。

   1. **名前**:「**Enable replication**」と入力します。
   2. **バッシュ スクリプト URI**: **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh** を入力します。
   3. **ヘッド**:これが選択されていることを確認します。 他のノード タイプをオフにします。
   4. **パラメーター**:次のサンプル パラメーターは、すべての既存のテーブルに対するレプリケーションを有効にし、ソース クラスターからデスティネーション クラスターにすべてのデータをコピーします。

          -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
      > [!NOTE]
      > ソースと宛先の両方のクラスター DNS 名に FQDN ではなくホスト名を使用します。

6. **作成** を選択します。 このスクリプトの実行には、少し時間がかかます (特に **-copydata** 引数を使用する場合)。

必須の引数:

|Name|説明|
|----|-----------|
|-s, --src-cluster | ソース HBase クラスターの DNS 名を指定します。 例: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | デスティネーション (レプリカ) HBase クラスターの DNS 名を指定します。 例: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | ソース HBase クラスターでの Ambari の管理パスワードを指定します。 |
|-dp, --dst-ambari-password | デスティネーション HBase クラスターでの Ambari の管理パスワードを指定します。|

省略可能な引数:

|Name|説明|
|----|-----------|
|-su, --src-ambari-user | ソース HBase クラスターでの Ambari の管理ユーザー名を指定します。 既定値は **admin** です。 |
|-du, --dst-ambari-user | デスティネーション HBase クラスターでの Ambari の管理者ユーザー名を指定します。 既定値は **admin** です。 |
|-t, --table-list | レプリケートされるテーブルを指定します。 例: --table-list="table1;table2;table3"。 テーブルを指定しない場合は、すべての既存の HBase テーブルがレプリケートされます。|
|-m, --machine | スクリプト アクションを実行するヘッド ノードを指定します。 値は**hn0**または**hn1** のいずれかであり、アクティブなヘッド ノードがどちらであるかに基づいて選択される必要があります。 このオプションは、HDInsight ポータルまたは Azure PowerShell からスクリプト アクションとして $0 スクリプトを実行する場合に使用します。|
|-cp, -copydata | レプリケーションが有効になっているテーブルの既存のデータの移行を有効にします。 |
|-rpm, -replicate-phoenix-meta | Phoenix システム テーブルのレプリケーションを有効にします。 <br><br>*このオプションは慎重に使用してください。* このスクリプトを使用する前に、レプリカ クラスターで Phoenix テーブルを再作成しておくことをお勧めします。 |
|-h, --help | 使用方法に関する情報を表示します。 |

[スクリプト](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) の `print_usage()` セクションに、パラメーターの詳細な説明が用意されています。

スクリプト アクションが正常に実行された後、SSH を使用してデスティネーション HBase クラスターに接続して、データがレプリケートされたことを確認できます。

### <a name="replication-scenarios"></a>レプリケーション シナリオ

いくつかの一般的な使用例とそのパラメーターの設定を次に示します。

- **2 つのクラスター間ですべてのテーブルのレプリケーションを有効にする**。 このシナリオでは、テーブルの既存のデータのコピー/移行は不要であり、Phoenix テーブルは使用しません。 次のパラメーターを使用します。

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **特定のテーブルのレプリケーションを有効にする**。 table1、table2、および table3 のレプリケーションを有効にするには、次のパラメーターを使用します。

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **特定のテーブルのレプリケーションを有効にし、既存のデータをコピーする**。 table1、table2、および table3 のレプリケーションを有効にするには、次のパラメーターを使用します。

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **ソースからデスティネーションに Phoenix メタデータをレプリケートして、すべてのテーブルのレプリケーションを有効にする**。 Phoenix メタデータのレプリケーションは完璧ではありません。 慎重に使用してください。 次のパラメーターを使用します。

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>データのコピーと移行

レプリケーションを有効にした後でデータをコピー/移行するスクリプト アクションのスクリプトは 2 つあります。

- [小さなテーブル向けのスクリプト](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (サイズが数ギガバイトであり、全部のコピーが 1 時間以内に完了することが予期されるテーブル)

- [大きなテーブル向けのスクリプト](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (コピー時間が 1 時間を超えることが予期されるテーブル)

「[レプリケーションを有効にする](#enable-replication)」と同じ手順でスクリプト アクションを呼び出すことができます。 次のパラメーターを使用します。

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

[スクリプト](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) の `print_usage()` セクションに、パラメーターの詳細な説明が用意されています。

### <a name="scenarios"></a>シナリオ

- **特定のテーブル (test1、test2、および test3) の現在のタイムスタンプまでに編集されたすべての行をコピーする**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  または

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **特定のテーブルの指定した時間範囲のデータをコピーする**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>レプリケーションを無効にする

レプリケーションを無効にするには、[GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) にある別のスクリプト アクションのスクリプトを使用します。 「[レプリケーションを有効にする](#enable-replication)」と同じ手順でスクリプト アクションを呼び出すことができます。 次のパラメーターを使用します。

    -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

[スクリプト](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) の `print_usage()` セクションに、パラメーターの詳細な説明が用意されています。

### <a name="scenarios"></a>シナリオ

- **すべてのテーブルのレプリケーションを無効にする**:

        -m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all
  or

        --src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **指定したテーブル (table1、table2、および table3) のレプリケーションを無効にする**:

        -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>次の手順

この記事では、1 つの仮想ネットワーク内または 2 つの仮想ネットワーク間で Apache HBase レプリケーションを設定する方法を説明しました。 HDInsight と Apache HBase の詳細については、以下の記事を参照してください。

* [HDInsight での Apache HBase の使用](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight Apache HBase の概要](./apache-hbase-overview.md)
* [Azure Virtual Network での Apache HBase クラスターの作成](./apache-hbase-provision-vnet.md)

