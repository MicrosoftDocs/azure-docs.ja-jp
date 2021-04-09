---
title: HDInsight で Apache Hadoop、Apache Spark、Apache Kafka などを使用してクラスターを設定する
description: Hadoop、Kafka、Spark、HBase、R Server、Storm の HDInsight クラスターをブラウザーや Azure クラシック CLI、Azure PowerShell、REST、SDK からセットアップします。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 08/06/2020
ms.openlocfilehash: 04da79509ac40ba870014995d8318a49c98e1ce5
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863413"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>HDInsight で Apache Hadoop、Apache Spark、Apache Kafka などを使用してクラスターを設定する

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

HDInsight で Apache Hadoop、Apache Spark、Apache Kafka、Interactive Query、Apache HBase、ML Services、Apache Storm を設定および構成する方法について説明します。 クラスターをドメインに参加させて、クラスターをカスタマイズしたりセキュリティを強化したりする方法についても説明します。

Hadoop クラスターは、タスクの分散処理に使用される複数の仮想マシン (ノード) で構成されます。 各ノードのインストールと構成にかかわる細部の実装は Azure HDInsight が担うため、ユーザーは一般的な構成情報を指定するだけで済みます。

> [!IMPORTANT]  
> HDInsight クラスターの課金は、クラスターが作成されると開始し、クラスターが削除されると停止します。 課金は分単位なので、クラスターを使わなくなったら必ず削除してください。 詳細については、[クラスターの削除方法](hdinsight-delete-cluster.md)に関するページを参照してください。

複数のクラスターを一緒に使用している場合は、仮想ネットワークを作成する必要があります。また、Spark クラスターを使用している場合は、Hive Warehouse Connector を使用することもできます。 詳細については、「[Azure HDInsight 用の仮想ネットワークを計画する](./hdinsight-plan-virtual-network-deployment.md)」と「[Hive Warehouse Connector を使用して Apache Spark と Apache Hive を統合する](interactive-query/apache-hive-warehouse-connector.md)」を参照してください。

## <a name="cluster-setup-methods"></a>クラスターのセットアップ方法

次の表は、HDInsight クラスターのセットアップに使用できる各種の方法を示しています。

| クラスターの作成方法 | Web ブラウザー | コマンド ライン | REST API | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Azure リソース マネージャーのテンプレート](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

この記事では、[Azure portal](https://portal.azure.com) で、HDInsight クラスターを作成するためのセットアップ方法を説明します。

## <a name="basics"></a>基本

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png" alt-text="HDInsight の作成オプション: カスタムおよび簡易":::

### <a name="project-details"></a>プロジェクトの詳細

[Azure Resource Manager](../azure-resource-manager/management/overview.md) を使用すると、アプリケーション内の複数のリソースを、Azure [リソース グループ](../azure-resource-manager/management/overview.md#resource-groups)と呼ばれる 1 つのグループとして使用できます。 アプリケーションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、監視、または削除できます。

### <a name="cluster-details"></a>クラスターの詳細

#### <a name="cluster-name"></a>クラスター名

HDInsight クラスター名には次の制限があります。

* 使用できる文字: a-z、0-9、A-Z
* 最大長:59
* 予約済みの名前: apps
* クラスターの名前付けのスコープは、すべてのサブスクリプションにわたる Azure 全体です。 そのため、クラスター名は世界全域で一意である必要があります。
* 最初の 6 文字は仮想ネットワーク内で一意である必要があります

#### <a name="region"></a>リージョン

クラスターの場所を明示的に指定する必要はありません。クラスターは、既定のストレージと同じ場所に存在します。 サポートされているリージョンのリストについては、「**HDInsight の価格**」の [[リージョン]](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409) ドロップダウン リストを選択してください。

#### <a name="cluster-type"></a>クラスターの種類

現在、Azure HDInsight では、以下の種類のクラスターを提供しています。それぞれのクラスターは特定の機能を提供する一連のコンポーネントを備えています。

> [!IMPORTANT]  
> HDInsight クラスターには、さまざまな種類があり、それぞれ単一のワークロードまたはテクノロジに対応しています。 複数の種類 (Storm と HBase など) を組み合わせたクラスターを作成することはできません。 複数の種類の HDInsight クラスターにまたがるテクノロジがソリューションに必要な場合は、必要な種類のクラスターを [Azure 仮想ネットワーク](../virtual-network/index.yml) で接続してください。

| クラスターの種類 | 機能 |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |格納されたデータのバッチ クエリとバッチ分析 |
| [HBase](hbase/apache-hbase-overview.md) |大量のスキーマレス NoSQL データの処理 |
| [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) |対話型で高速な Hive クエリのメモリ内キャッシュ |
| [Kafka](kafka/apache-kafka-introduction.md) | リアルタイムのストリーミング データ パイプラインとアプリケーションの構築に使用できる分散ストリーム プラットフォーム |
| [ML サービス](r-server/r-server-overview.md) |さまざまなビッグ データ統計、予測モデリング、機械学習の機能 |
| [Spark](spark/apache-spark-overview.md) |メモリ内処理、対話型クエリ、マイクロバッチ ストリーム処理 |
| [Storm](storm/apache-storm-overview.md) |リアルタイム イベント処理 |

#### <a name="version"></a>Version

このクラスターの HDInsight のバージョンを選択します。 詳細については、「[サポートされる HDInsight のバージョン](hdinsight-component-versioning.md#supported-hdinsight-versions)」を参照してください。

### <a name="cluster-credentials"></a>クラスターの資格情報

HDInsight クラスターでは、クラスターの作成時に次の 2 つのユーザー アカウントを構成できます。

* クラスター ログイン ユーザー名:既定のユーザー名は *admin* です。Azure Portal の基本的な構成を使用します。 "クラスター ユーザー" または "HTTP ユーザー" と呼ばれることもあります。
* Secure Shell (SSH) ユーザー名:SSH を使用してクラスターに接続する際に使用します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

HTTP ユーザー名には次の制限があります。

* 使用できる特殊文字: `_` と `@`
* 使用できない文字: #;."',\/:`!*?$(){}[]<>|&--=+%~^スペース
* 最大長:20

SSH ユーザー名には次の制限があります。

* 使用できる特殊文字: `_` と `@`
* 使用できない文字: #;."',\/:`!*?$(){}[]<>|&--=+%~^スペース
* 最大長:64
* 予約済みの名前: hadoop、users、oozie、hive、mapred、ambari、zookeeper、tez、hdfs、sqoop、yarn、hcat、ams、hbase、storm、administrator、admin、user、user1、test、user2、test1、user3、admin1、1、123、a、actuser、adm、admin2、aspnet、backup、console、david、guest、john、owner、root、server、sql、support、support_388945a0、sys、test2、test3、user4、user5、spark

## <a name="storage"></a>ストレージ

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png" alt-text="クラスター ストレージの設定: HDFS と互換性のあるエンドポイント":::

クラスターのストレージには、Hadoop のオンプレミス環境では Hadoop 分散ファイル システム (HDFS) が使用されますが、クラウドでは、クラスターに接続されたストレージ エンドポイントを使用します。 クラウド ストレージを使用すると、データを維持したまま、計算に使用する HDInsight クラスターを安全に削除できます。

HDInsight クラスターでは、次のストレージ オプションを使用できます。

* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1
* Azure Storage General Purpose v2
* Azure Storage General Purpose v1
* Azure Storage ブロック BLOB (**セカンダリ ストレージとしてのみサポート**)

HDInsight を使用したストレージ オプションの詳細については、「[Azure HDInsight クラスターで使用するストレージ オプションを比較する](hdinsight-hadoop-compare-storage-options.md)」を参照してください。

> [!WARNING]  
> HDInsight クラスター以外の場所で追加のストレージ アカウントを使用することはできません。

構成時、既定のストレージ エンドポイントには、Azure Storage アカウントの BLOB コンテナーまたは Data Lake Storage を指定します。 既定のストレージには、アプリケーション ログとシステム ログが格納されます。 必要に応じて、クラスターからアクセスできるリンクされた Azure Storage アカウントおよび Data Lake Storage アカウントを追加指定できます。 HDInsight クラスターとそのクラスターで使用されるストレージ アカウントは、同じ Azure リージョンに存在している必要があります。

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

> [!IMPORTANT]
> クラスターの作成後にセキュリティで保護されたストレージ転送を有効にすると、ストレージ アカウントを使用する際にエラーが発生する可能性があるため、推奨されません。 セキュリティで保護された転送が既に有効になっているストレージ アカウントを使用して新しいクラスターを作成することをお勧めします。

> [!Note]  
> Azure HDInsight では、Azure Storage に格納されているデータがリージョン間で自動的に転送、移動、コピーされることはありません。

### <a name="metastore-settings"></a>メタストアの設定

Hive metastore または Apache Oozie metastore を作成できます (任意)。 ただし、クラスターの種類によっては metastore がサポートされません。また Azure Synapse Analytics は metastore と互換性がありません。

詳細については、[Azure HDInsight での外部メタデータ ストアの使用](./hdinsight-use-external-metadata-stores.md)に関する記事を参照してください。

> [!IMPORTANT]  
> カスタム metastore を作成するとき、ダッシュ、ハイフン、またはスペースをデータベース名に使用しないでください。 クラスター作成プロセスが失敗する可能性があります。

#### <a name="sql-database-for-hive"></a>Hive 用の SQL データベース

HDInsight クラスターを削除した後も Hive テーブルを保持する場合は、カスタムの metastore を使用することをお勧めします。 その metastore を別の HDInsight クラスターにアタッチすることができます。

あるバージョンの HDInsight クラスター用に作成された HDInsight metastore は、別の HDInsight クラスター バージョン間で共有できません。 HDInsight のバージョンの一覧は、「[サポートされる HDInsight のバージョン](hdinsight-component-versioning.md#supported-hdinsight-versions)」をご覧ください。

> [!IMPORTANT]
> 既定のメタストアでは、**DTU 上限が Basic レベルの 5 (アップグレード不可能)** である Azure SQL Database が提供されます。 基本的なテスト目的に適しています。 より大きなワークロードや運用環境のワークロードの場合は、外部のメタストアに移行することをお勧めします。

#### <a name="sql-database-for-oozie"></a>Oozie 用の SQL データベース

Oozie の使用時にパフォーマンスを向上させるには、カスタム メタストアを使用します。 また、metastore を使用すると、クラスターの削除後に、Oozie ジョブ データにアクセスすることができます。

#### <a name="sql-database-for-ambari"></a>Ambari 用の SQL データベース

Ambari は、HDInsight クラスターの監視、構成の変更、およびクラスター管理情報とジョブ履歴の保存に使用されます。 カスタム Ambari DB 機能を使用すると、新しいクラスターをデプロイし、自分が管理する外部データベースに Ambari を設定できます。 詳細については、[カスタムの Ambari DB](./hdinsight-custom-ambari-db.md) に関する記事を参照してください。

> [!IMPORTANT]  
> カスタム Oozie メタストアを再利用することはできません。 カスタム Oozie メタストアを使用するには、HDInsight クラスターの作成時に空の Azure SQL Database を提供する必要があります。

## <a name="security--networking"></a>セキュリティとネットワーク

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png" alt-text="hdinsight の作成オプション [Enterprise セキュリティ パッケージ] を選択":::

### <a name="enterprise-security-package"></a>Enterprise セキュリティ パッケージ

クラスターの種類が Hadoop、Spark、HBase、Kafka、および対話型クエリの場合は、**Enterprise セキュリティ パッケージ** を有効にすることができます。 このパッケージは、Apache Ranger を使用し、Azure Active Direcotry と統合することによってより安全なクラスターのセットアップを行うオプションを提供します。 詳細については、「[Azure HDInsight のエンタープライズ セキュリティの概要](./domain-joined/hdinsight-security-overview.md)」を参照してください。

Enterprise セキュリティ パッケージでは、HDInsight を Active Directory と Apache Ranger と統合することができます。 Enterprise セキュリティ パッケージを使用して、複数のユーザーを作成できます。

ドメイン参加済みの HDInsight クラスターの作成の詳細については、[ドメイン参加済みの HDInsight サンドボックス環境の作成](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)に関する記事を参照してください。

### <a name="tls"></a>TLS

詳細については、「[トランスポート層セキュリティ](./transport-layer-security.md)」を参照してください。

### <a name="virtual-network"></a>仮想ネットワーク

複数の種類の HDInsight クラスターにまたがるテクノロジがソリューションに必要な場合は、必要な種類のクラスターを [Azure 仮想ネットワーク](../virtual-network/index.yml) で接続してください。 この構成により、クラスターと、それにデプロイするすべてのコードが互いに通信できるようになります。

Azure の仮想ネットワークの HDInsight との併用の詳細については、[HDInsight 用の仮想ネットワークの計画](hdinsight-plan-virtual-network-deployment.md)に関するページをご覧ください。

Azure の仮想ネットワーク内で 2 つのクラスターの種類を使用した例の詳細については、[Apache Kafka を使用した Apache Spark 構造化ストリーミングの使用](hdinsight-apache-kafka-spark-structured-streaming.md)に関するページを参照してください。 仮想ネットワークの具体的な構成要件など、仮想ネットワークで HDInsight を使用する方法の詳細については、[HDInsight 用の仮想ネットワークの計画](hdinsight-plan-virtual-network-deployment.md)に関するページをご覧ください。

### <a name="disk-encryption-setting"></a>ディスク暗号化設定

詳細については、「[お客様が管理するキー ディスクの暗号化](./disk-encryption.md)」を参照してください。

### <a name="kafka-rest-proxy"></a>Kafka REST プロキシ

この設定は、Kafka のクラスターの種類にのみ使用できます。 詳細については、[REST プロキシの使用](./kafka/rest-proxy.md)に関する記事を参照してください。

### <a name="identity"></a>ID

詳細については、「[Azure HDInsight のマネージド ID](./hdinsight-managed-identities.md)」を参照してください。

## <a name="configuration--pricing"></a>構成と価格

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png" alt-text="HDInsight でノード サイズを選択する":::

ノードの使用に対する料金は、クラスターが存在する限り発生します。 課金はクラスターが作成されると開始され、クラスターが削除されると停止されます。 クラスターを割り当て解除または保留にすることはできません。

### <a name="node-configuration"></a>ノード構成

クラスターのノード数、ノードを表す用語、既定の VM サイズは、クラスターの種類によって異なります。 次の表では、各ノードの種類のノード数がかっこ内に示されています。

| Type | Nodes | ダイアグラム |
| --- | --- | --- |
| Hadoop |ヘッド ノード (2)、ワーカー ノード (1 以上) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png" alt-text="HDInsight Hadoop クラスター ノード" border="false"::: |
| hbase |ヘッド サーバー (2)、リージョン サーバー (1 以上)、マスター/ZooKeeper ノード (3) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png" alt-text="HDInsight HBase クラスターの種類のセットアップ" border="false"::: |
| Storm |Nimbus ノード (2)、Supervisor サーバー (1 以上)、ZooKeeper ノード (3) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png" alt-text="HDInsight storm クラスターの種類のセットアップ" border="false"::: |
| Spark |ヘッド ノード (2)、ワーカー ノード (1 以上)、ZooKeeper ノード (3) (A1 ZooKeeper VM サイズでは無料) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png" alt-text="HDInsight spark クラスターの種類のセットアップ" border="false"::: |

詳細については、HDInsight における Hadoop のコンポーネントとバージョンに関するページの「[Default node configuration and virtual machine sizes for clusters (クラスターの既定のノード構成と仮想マシン サイズ)](hdinsight-supported-node-configuration.md)」を参照してください。

HDInsight クラスターのコストは、ノード数とノードの仮想マシンのサイズによって決まります。

クラスターの種類によって、ノードの種類、ノード数、ノード サイズが異なります。
* 種類が Hadoop のクラスターにおける既定のノード数:
    * "*ヘッド ノード*" (x 2)
    * 4 つの *ワーカー ノード*
* 種類が Storm のクラスターにおける既定のノード数:
    * "*Nimbus ノード*" (x 2)
    * "*Zookeeper ノード*" (x 3)
    * "*スーパーバイザー ノード*" (x 4)

HDInsight を試すだけの目的ならば、使用するワーカー ノードは 1 つにすることをお勧めします。 HDInsight の価格の詳細については、「 [HDInsight 価格](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)」をご覧ください。

> [!NOTE]  
> クラスター サイズの制限は、Azure サブスクリプションによって異なります。 制限値を上げるには、[Azure の課金サポート](../azure-portal/supportability/how-to-create-azure-support-request.md)にお問い合わせください。

Azure portal を使用してクラスターを構成する際、ノード サイズは **[Configuration + pricing]\(構成と価格\)** タブで確認できます。また、別のノード サイズに関連するコストをポータルで確認することもできます。

### <a name="virtual-machine-sizes"></a>仮想マシン サイズ

クラスターをデプロイするとき、デプロイ予定のソリューションに応じてコンピューティング リソースを選択します。 HDInsight クラスターには次の VM が使用されます。

* A シリーズおよび D1 - D4 シリーズの VM: [Linux VM の汎用サイズ](../virtual-machines/sizes-general.md)
* D11 - D14 シリーズの VM: [メモリ最適化された Linux VM のサイズ](../virtual-machines/sizes-memory.md)

各種の SDK または Azure PowerShell を使用してクラスターを作成する際、VM サイズの指定で必要となる値については、[HDInsight クラスターに使用する VM サイズ](../cloud-services/cloud-services-sizes-specs.md#size-tables)に関するページを参照してください。 リンク先の記事に掲載されている表の「**サイズ**」列の値を使用します。

> [!IMPORTANT]  
> クラスターで 32 以上のワーカー ノードが必要になる場合、少なくとも 8 コア、14 GB のヘッド ノード サイズを選択する必要があります。

詳細については、 [仮想マシンのサイズ](../virtual-machines/sizes.md)に関するページをご覧ください。 さまざまなサイズの価格については、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight)」をご覧ください。

### <a name="add-application"></a>[アプリケーションの追加]

HDInsight アプリケーションは、ユーザーが Linux ベースの HDInsight クラスターにインストールすることのできるアプリケーションです。 Microsoft やサード パーティから提供されたアプリケーションのほか、独自に開発したアプリケーションを使用することができます。 詳細については、「[Azure HDInsight にサードパーティ製 Apache Hadoop アプリケーションをインストールする](hdinsight-apps-install-applications.md)」を参照してください。

HDInsight のアプリケーションのほとんどは、空のエッジ ノードにインストールされます。  空のエッジ ノードは、ヘッド ノードの場合と同じクライアント ツールがインストールされ、構成された Linux 仮想マシンです。 エッジ ノードは、クラスターへのアクセス、クライアント アプリケーションのテスト、およびクライアント アプリケーションのホストに使用できます。 詳細については、「 [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md)」(HDInsight で空のエッジ ノードを使用する) を参照してください。

### <a name="script-actions"></a>スクリプト操作

追加コンポーネントをインストールするか、作成中にスクリプトを使用してクラスターの構成をカスタマイズできます。 このようなスクリプトは、**スクリプト操作** を使用して実行します。これは Azure ポータル、HDInsight Windows PowerShell コマンドレット、HDInsight .NET SDK で使用できる構成オプションです。 詳しくは、「[Script Action を使って HDInsight をカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」をご覧ください。

Apache Mahout や Cascading などの一部のネイティブ Java コンポーネントは、Java アーカイブ (JAR) ファイルとしてクラスター上で実行できます。 これらの JAR ファイルは、Azure Storage に分配し、Hadoop ジョブ送信メカニズムによって HDInsight クラスターに送信できます。 詳しくは、 [プログラムによる Apache Hadoop ジョブの送信](hadoop/submit-apache-hadoop-jobs-programmatically.md)に関するページをご覧ください。

> [!NOTE]  
> HDInsight クラスターへの JAR ファイルのデプロイ、または HDInsight クラスターでの JAR ファイルの呼び出しに関する問題がある場合は、[Microsoft サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。
>
> Cascading は HDInsight ではサポートされておらず、Microsoft サポートの対象でもありません。 サポートされているコンポーネントの一覧については、[HDInsight で提供されるクラスター バージョンの新機能](hdinsight-component-versioning.md)に関する記事をご覧ください。

場合によっては、作成プロセス中に次の構成ファイルを設定する必要があります。

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

詳細については、「 [ブートストラップを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-bootstrap.md) 」をご覧ください。

## <a name="next-steps"></a>次のステップ

* [Azure HDInsight のクラスター作成に失敗する問題のトラブルシューティング](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [HDInsight、Apache Hadoop エコシステム、Hadoop クラスターの概要](hadoop/apache-hadoop-introduction.md)
* [HDInsight で Apache Hadoop を使用する](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Windows PC から HDInsight の Apache Hadoop で作業する](hdinsight-hadoop-windows-tools.md)