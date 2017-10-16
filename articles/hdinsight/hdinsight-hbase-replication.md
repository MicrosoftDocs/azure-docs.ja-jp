---
title: "Azure 仮想ネットワーク内で HBase クラスターのレプリケーションを設定する | Microsoft Docs"
description: "負荷分散、高可用性、ダウンタイムなしの移行と更新、およびディザスター リカバリーを実現するために、ある HDInsight バージョンから別のバージョンへの HBase レプリケーションを設定する方法について説明します。"
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
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 9d1b629ad05f45efc8d01799616c82b4a11ecaab
ms.contentlocale: ja-jp
ms.lasthandoff: 09/28/2017

---
# <a name="set-up-hbase-cluster-replication-in-azure-virtual-networks"></a>Azure 仮想ネットワーク内で HBase クラスターのレプリケーションを設定する

Azure の 1 つの仮想ネットワーク内または 2 つの仮想ネットワーク間で HBase レプリケーションを設定する方法について説明します。

クラスターのレプリケーションでは、ソース プッシュの手法が使用されます。 HBase クラスターは、ソースまたはターゲットになることも、両方のロールを同時に満たすこともできます。 レプリケーションは非同期です。 レプリケーションの目的は、最終的な一貫性です。 レプリケーションが有効になった列ファミリに対する編集をソースが受け取ると、その編集はすべてのターゲット クラスターに伝達されます。 クラスター間でデータがレプリケートされるときは、ソース クラスターとそのデータを既に消費しているすべてのクラスターが追跡されて、レプリケーション ループが防止されます。

このチュートリアルでは、ソースとターゲット間のレプリケーションを設定します。 他のクラスター トポロジについては、[Apache HBase のリファレンス ガイド](http://hbase.apache.org/book.html#_cluster_replication)を参照してください。

次に示すのは、単一の仮想ネットワークでの HBase レプリケーションの使用例です。

* 負荷分散。 たとえば、ターゲット クラスターでスキャンや MapReduce ジョブを実行し、ソース クラスターでデータを取り込みます。
* 高可用性の追加。
* HBase クラスター間のデータの移行。
* Azure HDInsight クラスターのバージョンのアップグレード。

次に示すのは、2 つの仮想ネットワーク間での HBase レプリケーションの使用例です。

* ディザスター リカバリーの設定。
* アプリケーションの負荷分散とパーティション分割。
* 高可用性の追加。

クラスターは、[GitHub](https://github.com/Azure/hbase-utils/tree/master/replication) の[スクリプト アクション](hdinsight-hadoop-customize-cluster-linux.md)のスクリプトを使用してレプリケートできます。

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に、Azure サブスクリプションが必要です。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

## <a name="set-up-the-environments"></a>環境を設定する

3 つの構成オプションがあります。

- 1 つの Azure 仮想ネットワーク内の 2 つの HBase クラスター。
- 同じリージョンの 2 つの異なる仮想ネットワーク内の 2 つの HBase クラスター。
- 2 つの異なるリージョンの 2 つの異なる仮想ネットワーク内の 2 つの HBase クラスター (geo レプリケーション)。

環境を設定しやすくするために、複数の [Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-overview.md)が用意されています。 他の方法で環境を設定する場合は、次の記事を参照してください。

- [HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)
- [Azure Virtual Network での HBase クラスターの作成](hdinsight-hbase-provision-vnet.md)

### <a name="set-up-one-virtual-network"></a>1 つの仮想ネットワークを設定する

2 つの HBase クラスターを同じ仮想ネットワーク内に作成するには、次のイメージを選択します。 テンプレートは [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/)にあります。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="set-up-two-virtual-networks-in-the-same-region"></a>同じリージョンに 2 つの仮想ネットワークを設定する

同じリージョンに、ピアリングされた 2 つの仮想ネットワークと 2 つの HBase クラスターを作成するには、次のイメージを選択します。 テンプレートは [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/)にあります。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



このシナリオでは、[仮想ネットワークのピアリング](../virtual-network/virtual-network-peering-overview.md)が必要です。 テンプレートを使用することで、仮想ネットワークのピアリングが有効になります。   

HBase レプリケーションでは、ZooKeeper VM の IP アドレスを使用します。 デスティネーション HBase ZooKeeper ノードの静的 IP アドレスを設定する必要があります。

**静的 IP アドレスを構成するには**

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 左側のメニューの **[リソース グループ]** を選択します。
3. デスティネーション HBase クラスターを持つリソース グループを選択します。 これは、Resource Manager テンプレートを使用して環境を作成するときに指定したリソース グループです。 フィルターを使用して一覧を絞り込むことができます。 2 つの仮想ネットワークを含むリソースの一覧を表示できます。
4. デスティネーション HBase クラスターを含む仮想ネットワークを選択します。 たとえば、**[xxxx-vnet2]** を選択します。 **nic-zookeepermode-** で始まる名前を持つ 3 つのデバイスがリストに表示されます。 これらのデバイスは、3 つの ZooKeeper VM です。
5. いずれかの ZooKeeper VM を選択します。
6. **[IP 構成]** を選択します。
7. 一覧で **[ipConfig1]** を選択します。
8. **[静的]** を選択し、実際の IP アドレスをコピーするかメモします。 この IP アドレスは、スクリプト アクションを実行してレプリケーションを有効にするときに必要です。

  ![HDInsight HBase レプリケーションの ZooKeeper の静的 IP アドレス](./media/hdinsight-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. 手順 6 を繰り返して、他の 2 つの ZooKeeper ノードの静的 IP アドレスを確認します。

クロス仮想ネットワークのシナリオでは、`hdi_enable_replication.sh` スクリプト アクションを呼び出すときに **-ip** スイッチを使用する必要があります。

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>2 つの異なるリージョンに 2 つの仮想ネットワークを設定する

2 つの異なるリージョンに 2 つの仮想ネットワークを作成するには、次のイメージを選択します。 このテンプレートは、グローバル Azure BLOB コンテナーにあります。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fdeploy-hbase-geo-replication.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

2 つの仮想ネットワーク間の VPN ゲートウェイを作成します。 手順については、[サイト間接続を持つ仮想ネットワークの作成](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)に関する記事を参照してください。

HBase レプリケーションでは、ZooKeeper VM の IP アドレスを使用します。 デスティネーション HBase ZooKeeper ノードの静的 IP アドレスを設定する必要があります。 静的 IP を構成するには、この記事の「[同じリージョンに 2 つの仮想ネットワークを設定する](#set-up-two-virtual-networks-in-the-same-region)」を参照してください。

クロス仮想ネットワークのシナリオでは、`hdi_enable_replication.sh` スクリプト アクションを呼び出すときに **-ip** スイッチを使用する必要があります。

## <a name="load-test-data"></a>テスト データの読み込み

クラスターをレプリケートする場合は、レプリケートするテーブルを指定する必要があります。 このセクションでは、ソース クラスターにデータを読み込みます。 次のセクションで、2 つのクラスター間のレプリケーションを有効にします。

**Contacts** テーブルを作成し、そのテーブルにいくつかデータを挿入するには、「[HBase チュートリアル: HDInsight の Linux ベースの Hadoop で Apache HBase を使用する](hdinsight-hbase-tutorial-get-started-linux.md)」の指示に従います。

## <a name="enable-replication"></a>Enable replication

次の手順は、Azure Portal からスクリプト アクションのスクリプトを呼び出す方法を示しています。 Azure PowerShell と Azure コマンドライン ツール (Azure CLI) を使用したスクリプト アクションの実行については、「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

**Azure Portal から HBase レプリケーションを有効にするには**

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ソース HBase クラスターを開きます。
3. クラスター メニューの **[スクリプト アクション]** を選択します。
4. ページの上部にある **[新規で送信]** を選択します。
5. 次の情報を選択するか入力します。

  1. **名前**: 「**Enable replication**」と入力します。
  2. **Bash スクリプト URL**: 「**https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**」と入力します。
  3.  **ヘッド**: これが選択されていることを確認します。 他のノード タイプをオフにします。
  4. **パラメーター**: 次のサンプル パラメーターは、すべての既存のテーブルに対するレプリケーションを有効にし、ソース クラスターからデスティネーション クラスターにすべてのデータをコピーします。

          -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
    >[!note]
    >
    > ソースと宛先の両方のクラスター DNS 名に FQDN ではなくホスト名を使用します。

6. **[作成]**を選択します。 このスクリプトの実行には、少し時間がかかます (特に **-copydata** 引数を使用する場合)。

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
|-du, --dst-ambari-user | デスティネーション HBase クラスターでの Ambari の管理者ユーザー名を指定します。 既定値は **admin** です。 |
|-t, --table-list | レプリケートされるテーブルを指定します。 例: --table-list="table1;table2;table3"。 テーブルを指定しない場合は、すべての既存の HBase テーブルがレプリケートされます。|
|-m, --machine | スクリプト アクションを実行するヘッド ノードを指定します。 値は、**hn1** または **hn0** のいずれかです。 通常、**hn0** ヘッド ノードはビジー状態であるため、**hn1** の使用をお勧めします。 このオプションは、HDInsight ポータルまたは Azure PowerShell からスクリプト アクションとして $0 スクリプトを実行する場合に使用します。|
|-ip | 2 つの仮想ネットワーク間のレプリケーションを有効にする場合は必須です。 この引数は、FQDN 名の代わりにレプリカ クラスターの ZooKeeper ノードの静的 IP アドレスを使用するためのスイッチとして機能します。 レプリケーションを有効にする前に、静的 IP アドレスを構成しておく必要があります。 |
|-cp, -copydata | レプリケーションが有効になっているテーブルの既存のデータの移行を有効にします。 |
|-rpm, -replicate-phoenix-meta | Phoenix システム テーブルのレプリケーションを有効にします。 <br><br>*このオプションは慎重に使用してください。* このスクリプトを使用する前に、レプリカ クラスターで Phoenix テーブルを再作成しておくことをお勧めします。 |
|-h, --help | 使用方法に関する情報を表示します。 |

[スクリプト](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) の `print_usage()` セクションに、パラメーターの詳細な説明が用意されています。

スクリプト アクションが正常に実行された後、SSH を使用してデスティネーション HBase クラスターに接続して、データがレプリケートされたことを確認できます。

### <a name="replication-scenarios"></a>レプリケーション シナリオ

いくつかの一般的な使用例とそのパラメーターの設定を次に示します。

- **2 つのクラスター間ですべてのテーブルのレプリケーションを有効にする**。 このシナリオでは、テーブルの既存のデータのコピー/移行は不要であり、Phoenix テーブルは使用しません。 次のパラメーターを使用します。

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **特定のテーブルのレプリケーションを有効にする**。 table1、table2、および table3 のレプリケーションを有効にするには、次のパラメーターを使用します。

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **特定のテーブルのレプリケーションを有効にし、既存のデータをコピーする**。 table1、table2、および table3 のレプリケーションを有効にするには、次のパラメーターを使用します。

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **ソースからデスティネーションに Phoenix メタデータをレプリケートして、すべてのテーブルのレプリケーションを有効にする**。 Phoenix メタデータのレプリケーションは完璧ではありません。 慎重に使用してください。 次のパラメーターを使用します。

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

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

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

[スクリプト](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) の `print_usage()` セクションに、パラメーターの詳細な説明が用意されています。

### <a name="scenarios"></a>シナリオ

- **すべてのテーブルのレプリケーションを無効にする**:

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  または

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **指定したテーブル (table1、table2、および table3) のレプリケーションを無効にする**:

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、1 つの仮想ネットワーク内または 2 つの仮想ネットワーク間で HBase レプリケーションを設定する方法を説明しました。 HDInsight と HBase の詳細については、以下の記事を参照してください。

* [HDInsight での Apache HBase の使用][hdinsight-hbase-get-started]
* [HDInsight HBase の概要][hdinsight-hbase-overview]
* [Azure Virtual Network での HBase クラスターの作成][hdinsight-hbase-provision-vnet]
* [HBase で Twitter のセンチメントをリアルタイム分析する][hdinsight-hbase-twitter-sentiment]
* [Apache Storm、Event Hub、HBase を HDInsight (Hadoop) で使用してセンサー データを分析する][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started-linux.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

