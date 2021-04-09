---
title: 外部メタデータ ストアの使用 - Azure HDInsight
description: Azure HDInsight クラスターで外部メタデータ ストアを使用します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 08/06/2020
ms.openlocfilehash: a3bfcfbe59ccc15278b30470c6a060a9c1dd609c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871746"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Azure HDInsight での外部メタデータ ストアの使用

HDInsight を使用すると、外部データ ストアを使用してデータとメタデータを制御できます。 この機能は、[Apache Hive メタストア](#custom-metastore)、[Apache Oozie メタストア](#apache-oozie-metastore)、および [Apache Ambari データベース](#custom-ambari-db)で使用できます。

HDInsight の Apache Hive metastore は、Apache Hadoop アーキテクチャの不可欠な部分です。 メタストアは、中央のスキーマ リポジトリです。 メタストアは、Apache Spark、Interactive Query (LLAP)、Presto、Apache Pig などの他のビッグ データ アクセス ツールから使用されます。 HDInsight は、Azure SQL Database を Hive metastore として使用します。

:::image type="content" source="./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png" alt-text="HDInsight Hive メタデータ ストアのアーキテクチャ" border="false":::

HDInsight クラスター用の metastore をセットアップできる方法は 2 つあります。

* [既定の metastore](#default-metastore)
* [カスタム metastore ](#custom-metastore)

## <a name="default-metastore"></a>既定の metastore

既定では、すべてのクラスターの種類を含む metastore が作成されます。 代わりに、カスタム metastore を指定できます。 既定の metastore には、次の考慮事項が含まれます。

* 追加コストはありません。 HDInsight では、すべてのクラスターの種類を含む metastore が追加コストなしで作成されます。

* それぞれの既定の metastore は、クラスターのライフ サイクルの一部です。 クラスターを削除すると、対応する metastore とメタデータも削除されます。

* 既定の metastore は、他のクラスターと共有できません。

* 既定のメタストアは、単純なワークロード用にのみ推奨されます。 複数のクラスターを必要とせず、クラスターのライフサイクルを超えてメタデータを保持する必要がないワークロード。

> [!IMPORTANT]
> 既定のメタストアでは、**DTU 上限が Basic レベルの 5 (アップグレード不可能)** である Azure SQL Database が提供されます。 基本的なテスト目的に適しています。 より大きなワークロードや運用環境のワークロードの場合は、外部のメタストアに移行することをお勧めします。

## <a name="custom-metastore"></a>カスタム metastore

HDInsight では、カスタム metastore もサポートします。運用クラスターではこれを推奨します。

* 独自の Azure SQL Database を metastore として指定します。

* metastore のライフ サイクルは、クラスターのライフ サイクルに関連付けられないため、メタデータを失わずにクラスターの作成と削除を実行できます。 Hive スキーマなどのメタデータは、HDInsight クラスターを削除して再作成した後でも保持されます。

* カスタム metastore では、複数のクラスターとクラスターの種類をその metastore にアタッチできます。 たとえば、1 つの metastore を HDInsight の Interactive Query、Hive、および Spark クラスター間で共有できます。

* metastore (Azure SQL Database) のコストは、選択したパフォーマンス レベルに応じて支払います。

* metastore は、必要に応じて拡張できます。

* クラスターと外部メタストアは、同じリージョン内でホストされる必要があります。

:::image type="content" source="./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png" alt-text="HDInsight Hive メタデータ ストアのユース ケース" border="false":::

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>カスタム metastore 用の Azure SQL Database を作成および構成する

HDInsight クラスター用のカスタム Hive メタストアを設定する前に、Azure SQL Database を作成するか、既存のものを用意します。  詳細については、「[クイック スタート: Azure SQL Database の単一データベースを作成する](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)」を参照してください。

クラスターの作成時には、HDInsight サービスによって外部メタストアに接続され、資格情報が確認される必要があります。 Azure SQL Database のファイアウォール規則を構成して、Azure サービスとリソースがサーバーにアクセスできるようにします。 Azure portal で **[サーバー ファイアウォールの設定]** を選択して、このオプションを有効にします。 次に、Azure SQL Database に対して、 **[Deny public network access]\(パブリック ネットワーク アクセスを拒否する\)** の下の **[いいえ]** と、 **[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する]** の下の **[はい]** を選択します。 詳細については、「[IP ファイアウォール規則の作成および管理](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)」を参照してください。

SQL ストアのプライベート エンドポイントは、`outbound` ResourceProviderConnection を使用して作成されたクラスターでのみサポートされます。 詳細については、こちらの[ドキュメント](./hdinsight-private-link.md)を参照してください。

:::image type="content" source="./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png" alt-text="[サーバー ファイアウォールの設定] ボタン":::

:::image type="content" source="./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png" alt-text="Azure サービスへのアクセスを許可":::

### <a name="select-a-custom-metastore-during-cluster-creation"></a>クラスターの作成時にカスタム metastore を選択する

いつでもクラスターを以前に作成した Azure SQL Database にポイントできます。 ポータルを使用したクラスターの作成の場合、オプションは **[ストレージ] > [metastore の設定]** から指定します。

:::image type="content" source="./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png" alt-text="Azure Portal の HDInsight Hive メタデータ ストア":::

## <a name="hive-metastore-guidelines"></a>Hive メタストアのガイドライン

> [!NOTE]
> 可能な限り、カスタム metastore を使用します。これにより、コンピューティング リソース (実行中のクラスター) とメタデータ (metastore に格納されます) を分離できます。 50 DTU と 250 GB のストレージが提供される S2 レベルから開始します。 ボトルネックを確認した場合は、データベースをスケール アップできます。

* 複数の HDInsight クラスターで異なるデータにアクセスする場合は、クラスターごとに metastore に対して別のデータベースを使用します。 複数の HDInsight クラスターで metastore を共有する場合は、クラスターが同じメタデータおよび基になるユーザー データ ファイルを使用することを意味します。

* カスタム metastore を定期的にバックアップします。 Azure SQL Database ではバックアップが自動的に生成されますが、バックアップのリテンション期間は異なります。 詳しくは、「[SQL Database 自動バックアップについての詳細情報](../azure-sql/database/automated-backups-overview.md)」をご覧ください。

* メタストアと HDInsight クラスターを同じリージョンで保持します。 この構成を使用すると、最高のパフォーマンスと最低のネットワーク エグレス料金を実現できます。

* Azure SQL Database 監視ツールまたは Azure Monitor ログを使用して、メタストアのパフォーマンスと可用性を監視します。

* Azure HDInsight の新しい上位バージョンが既存のカスタム メタストア データベースに対して作成されると、システムによってメタストアのスキーマはアップグレードされます。 バックアップからデータベースを復元しないと、アップグレードを元に戻すことはできません。

* 複数のクラスター間で metastore を共有する場合は、すべてのクラスターの HDInsight を確実に同じバージョンにします。 異なるバージョンの Hive は、異なる metastore データベース スキーマを使用します。 たとえば、バージョン 2.1 の Hive クラスターと 3.1 の Hive クラスターで metastore を共有することはできません。

* HDInsight 4.0 では、Spark と Hive は、SparkSQL または Hive テーブルへのアクセスに、独立したカタログを使用します。 Spark によって作成されたテーブルは、Spark カタログ内に存在します。 Hive によって作成されたテーブルは、Hive カタログ内に存在します。 この動作は、Hive と Spark が共通のカタログを共有する HDInsight 3.6 とは異なります。 HDInsight 4.0 での Hive と Spark の統合は、Hive Warehouse Connector (HWC) に依存します。 HWC は、Spark と Hive 間の橋として動作します。 [Hive Warehouse Connector について学習](../hdinsight/interactive-query/apache-hive-warehouse-connector.md)します。

* HDInsight 4.0 では、Hive と Spark の間でメタストアを共有する場合は、プロパティ metastore.catalog.default を Spark クラスターのハイブに変更することで、これを行うことができます。 このプロパティは、Ambari Advanced spark2-hive-site-override で見つかります。 メタストアの共有は外部のハイブ テーブルに対してのみ機能することを理解しておくことが重要です。内部またはマネージド ハイブ テーブルまたは ACID テーブルがある場合、これは機能しません。  

## <a name="apache-oozie-metastore"></a>Apache Oozie metastore

Apache Oozie は、Hadoop ジョブを管理するワークフロー調整システムです。 Oozie は、Apache MapReduce、Pig、Hive、その他の Hadoop ジョブをサポートします。  Oozie では、メタストアを使用してワークフローの詳細が格納されます。 Oozie の使用時にパフォーマンスを向上させるために、カスタム metastore として Azure SQL Database を使用できます。 メタストアを使用すると、クラスターを削除した後、Oozie ジョブ データにアクセスできます。

Azure SQL Database を使用する Oozie metastore の作成手順については、[ワークフローでの Apache Oozie の使用](hdinsight-use-oozie-linux-mac.md)に関する記事を参照してください。

## <a name="custom-ambari-db"></a>カスタム Ambari DB

HDInsight の Apache Ambari で独自の外部データベースを使用する方法については、[カスタムの Apache Ambari データベース](hdinsight-custom-ambari-db.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [Apache Hadoop、Apache Spark、Apache Kafka などを使用して HDInsight でクラスターを設定する](./hdinsight-hadoop-provision-linux-clusters.md)