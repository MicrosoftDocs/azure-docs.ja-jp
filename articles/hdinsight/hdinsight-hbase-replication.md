---
title: "HBase レプリケーションの構成 | Microsoft Docs"
description: "負荷分散、高可用性、ダウンタイムなしの移行/HDInsight バージョンの更新、および障害復旧を実現するために HBase レプリケーションを構成する方法について説明します。"
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: a62cd616f0abd59c83c834bf13b4ba8549a9c73e
ms.openlocfilehash: 83aabe0e5161cd3f94caa996dec29bc020e5308b
ms.lasthandoff: 02/23/2017


---
# <a name="configure-hbase-replication"></a>HBase レプリケーションの構成

1 つの仮想ネットワーク (VNet) 内または&2; つの仮想ネットワーク間で HBase レプリケーションを構成する方法について説明します。

クラスターのレプリケーションでは、ソース プッシュの手法が使用されます。 HBase クラスターは、ソースまたはターゲットになることも、両方のロールを同時に満たすこともできます。 レプリケーションは非同期であり、レプリケーションの目的は最終的な整合性です。 レプリケーションが有効になった列ファミリに対する編集をソースが受け取ると、その編集はすべてのターゲット クラスターに伝達されます。 クラスター間でデータがレプリケートされるときは、ソース クラスターとそのデータを既に消費されているすべてのクラスターが追跡されて、レプリケーション ループが防止されます。

このチュートリアルでは、ソースとターゲット間のレプリケーションを構成します。 他のクラスター トポロジについては、[Apache HBase のリファレンス ガイド](http://hbase.apache.org/book.html#_cluster_replication)を参照してください。

単一の仮想ネットワークでの HBase レプリケーションの使用例:

* 負荷分散。たとえば、ターゲット クラスターでスキャンや MapReduce ジョブを実行し、ソース クラスターでデータを取り込みます
* 高可用性
* HBase クラスター間のデータの移行
* Azure HDInsight クラスターのバージョンのアップグレード

2 つの仮想ネットワークでの HBase レプリケーションの使用例:

* 障害復旧
* アプリケーションの負荷分散とパーティション分割
* 高可用性

クラスターは、[GitHub](https://github.com/Azure/hbase-utils/tree/master/replication) の[スクリプト アクション](hdinsight-hadoop-customize-cluster-linux.md)のスクリプトを使用してレプリケートします。

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に、Azure サブスクリプションが必要です。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

## <a name="configure-the-environments"></a>環境の構成

可能な構成オプションは&3; つあります。

- 1 つの Azure Virtual Network 内の&2; つの HBase クラスター
- 同じリージョンの&2; つの異なる仮想ネットワーク内の&2; つの HBase クラスター
- 2 つの異なるリージョンの&2; つの異なる仮想ネットワーク内の&2; つの HBase クラスター (geo レプリケーション)

環境を構成しやすくするために、複数の [Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-overview.md)が用意されています。 他の方法で環境を構成する場合は、次の記事を参照してください。

- [HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)
- [Azure Virtual Network での HBase クラスターの作成](hdinsight-hbase-provision-vnet.md)

### <a name="configure-one-virtual-network"></a>1 つの仮想ネットワークの構成

2 つの HBase クラスターを同じ仮想ネットワーク内に作成するには、次のイメージをクリックします。 テンプレートは [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/)に格納されています。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="configure-two-virtual-networks-in-the-same-region"></a>同じリージョンでの&2; つの仮想ネットワークの構成

次のイメージをクリックして、同じリージョンに、VNet ピアリングされた&2; つの仮想ネットワークと&2; つの HBase クラスターを作成します。 テンプレートは [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/)に格納されています。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



このシナリオでは、[VNet ピアリング](../virtual-network/virtual-network-peering-overview.md)が必要です。 このテンプレートは、VNet ピアリングを有効にします。   

HBase レプリケーションでは、ZooKeeper VM の IP アドレスを使用します。 デスティネーション HBase ZooKeeper ノードの静的 IP アドレスを構成する必要があります。

**静的 IP アドレスを構成するには**

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 左側のメニューで **[リソース グループ]** をクリックします。
3. デスティネーション HBase クラスターを含むリソース グループをクリックします。 これは、Resource Manager テンプレートを使用して環境を作成するときに指定したリソース グループです。 フィルターを使用して一覧を絞り込むことができます。 2 つの仮想ネットワークを含むリソースの一覧を表示できます。
4. デスティネーション HBase クラスターを含む仮想ネットワークをクリックします。 たとえば、**[xxxx-vnet2]** をクリックします。 **nic-zookeepermode-** で始まる名前を持つ&3; つのデバイスを確認できます。 これらのデバイスは、3 つの ZooKeeper VM です。
5. いずれかの ZooKeeper VM をクリックします。
6. **[IP 構成]** をクリックします。
7. 一覧の **[ipConfig1]** をクリックします。
8. **[静的]** をクリックし、実際の IP アドレスをメモします。 この IP アドレスは、スクリプト アクションを実行してレプリケーションを有効にするときに必要です。

  ![HDInsight HBase レプリケーションの ZooKeeper の静的 IP](./media/hdinsight-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. 手順 6 を繰り返して、他の 2 つの ZooKeeper ノードの静的 IP アドレスを確認します。

VNet 間のシナリオの場合は、**hdi_enable_replication.sh**スクリプト アクションを呼び出すときに **-ip** スイッチを使用する必要があります。

### <a name="configure-two-virtual-networks-in-two-different-regions"></a>2 つの異なるリージョンでの&2; つの仮想ネットワークの構成

2 つの異なるリージョンに&2; つの仮想ネットワークを作成するには、次のイメージをクリックします。 このテンプレートは、パブリック Azure BLOB コンテナーに格納されています。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fdeploy-hbase-geo-replication.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

2 つの仮想ネットワーク間の VPN ゲートウェイを作成します。 手順については、[サイト間接続を持つ VNet の作成](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)に関する記事を参照してください。

HBase レプリケーションでは、ZooKeeper VM の IP アドレスを使用します。 デスティネーション HBase ZooKeeper ノードの静的 IP アドレスを構成する必要があります。 静的 IP を構成するには、この記事の「同じリージョンに&2; つの仮想ネットワークを構成する」を参照してください。

VNet 間のシナリオの場合は、**hdi_enable_replication.sh**スクリプト アクションを呼び出すときに **-ip** スイッチを使用する必要があります。

## <a name="load-test-data"></a>テスト データの読み込み

クラスターをレプリケートする場合は、レプリケートするテーブルを指定する必要があります。 このセクションでは、ソース クラスターにデータを読み込みます。 次のセクションで、2 つのクラスター間のレプリケーションを有効にします。

「[HBase チュートリアル: HDInsight の Linux ベースの Hadoop で Apache HBase を使用する](hdinsight-hbase-tutorial-get-started-linux.md)」の指示に従って **Contacts** テーブルを作成し、そのテーブルにいくつかデータを挿入します。

## <a name="enable-replication"></a>レプリケーションを有効にする

次の手順は、Azure Portal からスクリプト アクションのスクリプトを呼び出す方法を示しています。 Azure PowerShell と Azure コマンド ライン インターフェイス (CLI) を使用したスクリプト アクションの実行については、「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

**Azure Portal から HBase レプリケーションを有効にするには**

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ソース HBase クラスターを開きます。
3. クラスター メニューの **[スクリプト アクション]** をクリックします。
4. ブレードの上部にある**[新規で送信]** をクリックします。
5. 次の情報を選択するか入力します。

  - **名前**: 「**Enable replication**」と入力します。
  - **Bash スクリプト URL**: 「**https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**」と入力します。
  - **ヘッド**: 選択されています。 他のノード タイプをオフにします。
  - **パラメーター**: 次のサンプル パラメーターは、すべての既存のテーブルに対するレプリケーションを有効にし、ソース クラスターからからデスティネーション クラスターにすべてのデータをコピーします。

            -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata

6. **[作成]**をクリックします。 このスクリプトの実行には、少し時間がかかます (特に copydata 引数を使用する場合)。

必須の引数:

|名前|説明|
|----|-----------|
|-s, --src-cluster | ソース HBase クラスターの DNS 名を指定します。 例: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | デスティネーション (レプリカ) HBase クラスターの DNS 名を指定します。 例: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | ソース HBase クラスターでの Ambari の管理パスワードを指定します。 |
|-dp, --dst-ambari-password | デスティネーション HBase クラスターでの Ambari の管理パスワードを指定します。|

省略可能な引数:

|名前|説明|
|----|-----------|
|-su, --src-ambari-user | ソース HBase クラスターでの Ambari の管理ユーザー名を指定します。 既定値は **admin** です。 |
|-du, --dst-ambari-user | デスティネーション HBase クラスターでの Ambari の管理ユーザー名を指定します。 既定値は **admin** です。 |
|-t, --table-list | レプリケートされるテーブルを指定します。 例: --table-list="table1;table2;table3"。 テーブルを指定しない場合は、すべての既存の HBase テーブルがレプリケートされます。|
|-m, --machine | スクリプト アクションが実行されるヘッド ノードを指定します。 値は、hn1 または hn0 のいずれかです。 通常は hn0 のほうがビジーになるため、hn1 を使用することをお勧めします。 このオプションは、HDInsight ポータルまたは Azure PowerShell からスクリプト アクションとして $0 スクリプトを実行する場合に使用します。|
|-ip | 2 つの仮想ネットワーク間のレプリケーションを有効にする場合、この引数は必須です。 この引数は、FQDN 名の代わりにレプリカ クラスターの ZooKeeper ノードの静的 IP アドレスを使用するためのスイッチとして機能します。 レプリケーションを有効にする前に静的 IP アドレスを構成しておく必要があります。 |
|-cp, -copydata | レプリケーションが有効になっているテーブルの既存のデータの移行を有効にします。 |
|-rpm, -replicate-phoenix-meta | Phoenix システム テーブルのレプリケーションを有効にします。 <br><br>*このオプションは慎重に使用してください。* このスクリプトを使用する前に、レプリカ クラスターで Phoenix テーブルを再作成しておくことをお勧めします。 |
|-h, --help | 使用方法に関する情報を表示します。 |

[スクリプト](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) の print_usage() セクションに、パラメーターの詳細な説明が用意されています。

スクリプト アクションが正常に実行された後、SSH を使用してデスティネーション HBase クラスターに接続して、データがレプリケートされたことを確認できます。

### <a name="replication-scenarios"></a>レプリケーション シナリオ

いくつかの一般的な使用例とそのパラメーターの設定を次に示します。

- **2 つのクラスター間ですべてのテーブルのレプリケーションを有効にする**。 このシナリオでは、テーブルの既存のデータのコピー/移行は不要であり、Phoenix テーブルは使用しません。 次のパラメーターを使用します。

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **特定のテーブルのレプリケーションを有効にする**。 table1、table2、および ｔable3 のレプリケーションを有効にするには、次のパラメーターを使用します。

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **特定のテーブルのレプリケーションを有効にし、既存のデータをコピーする**。 table1、table2、および ｔable3 のレプリケーションを有効にするには、次のパラメーターを使用します。

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **ソースからデスティネーションに Phoenix メタデータをレプリケートして、すべてのテーブルのレプリケーションを有効にする**。 Phoenix メタデータのレプリケーションは完全ではないため、慎重に有効にする必要があります。

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>データのコピーと移行

レプリケーションを有効にした後でデータをコピー/移行するスクリプト アクションのスクリプトは&2; つあります。

- [小さなテーブル向けのスクリプト](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (サイズが数ギガバイトであり、全部のコピーが&1; 時間以内に完了することが予期されます)

- [大きなテーブル向けのスクリプト](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh)(コピー時間が&1; 時間を超えることが予期されます)

「[レプリケーションを有効にする](#enable-replication)」と同じ手順で、次のパラメーターを指定したスクリプト アクションを呼び出すことができます。

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

[スクリプト](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) の print_usage() セクションに、パラメーターの詳細な説明が用意されています。

### <a name="scenarios"></a>シナリオ

- **特定のテーブル (test1、test2、および test3) の現在のタイムスタンプまでに編集されたすべての行をコピーする**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  または

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **特定のテーブルの指定した時間範囲のデータをコピーする**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>レプリケーションを無効にする

レプリケーションを無効にするには、[GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) にある別のスクリプト アクションのスクリプトを使用します。 「[レプリケーションを有効にする](#enable-replication)」と同じ手順で、次のパラメーターを指定したスクリプト アクションを呼び出すことができます。

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari Password> <-all|-t "table1;table2;...">  

[スクリプト](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) の print_usage() セクションに、パラメーターの詳細な説明が用意されています。

### <a name="scenarios"></a>シナリオ

- **すべてのテーブルのレプリケーションを無効にする**:

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  または

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari username> --src-ambari-password=<source cluster Ambari password>

- **指定したテーブル (table1、table2、および table3) のレプリケーションを無効にする**:

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、2 つのデータセンター間での HBase レプリケーションを構成する方法を説明しました。 HDInsight と HBase の詳細については、以下を参照してください。

* [HDInsight での Apache HBase の使用][hdinsight-hbase-get-started]
* [HDInsight HBase の概要][hdinsight-hbase-overview]
* [Azure Virtual Network での HBase クラスターの作成][hdinsight-hbase-provision-vnet]
* [HBase で Twitter のセンチメントをリアルタイム分析する][hdinsight-hbase-twitter-sentiment]
* [HDInsight (Hadoop) での Storm と HBase を使用したセンサー データの分析][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started-linux.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

