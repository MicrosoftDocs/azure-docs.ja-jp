---
title: 外部メタデータ ストアの使用 - Azure HDInsight
description: HDInsight クラスターで外部メタデータ ストアを使用します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 3daa71c91d1e49a497a979b9b5b89df1fcb9418c
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889683"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Azure HDInsight での外部メタデータ ストアの使用

HDInsight の Apache Hive metastore は、Apache Hadoop アーキテクチャの不可欠な部分です。 metastore は、Apache Spark、Interactive Query (LLAP)、Presto、Apache Pig などの他のビッグ データ アクセス ツールで使用できる主要スキーマ リポジトリです。 HDInsight は、Azure SQL Database を Hive metastore として使用します。

![HDInsight Hive メタデータ ストアのアーキテクチャ](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

HDInsight クラスター用の metastore をセットアップできる方法は 2 つあります。

* [既定の metastore](#default-metastore)
* [カスタム metastore ](#custom-metastore)

## <a name="default-metastore"></a>既定の metastore

既定では、すべてのクラスターの種類を含む metastore が作成されます。 代わりに、カスタム metastore を指定できます。 既定の metastore には、次の考慮事項が含まれます。
- 追加コストはありません。 HDInsight では、すべてのクラスターの種類を含む metastore が追加コストなしで作成されます。
- それぞれの既定の metastore は、クラスターのライフ サイクルの一部です。 クラスターを削除すると、対応する metastore とメタデータも削除されます。
- 既定の metastore は、他のクラスターと共有できません。
- 既定の metastore では、5 DTU (データベース トランザクション ユニット) の制限がある基本的な Azure SQL DB が使用されます。
この既定の metastore は、通常は、複数のクラスターを必要とせず、クラスターのライフ サイクルを超えてメタデータを保持する必要がない、比較的単純なワークロードで使用されます。


## <a name="custom-metastore"></a>カスタム metastore

HDInsight では、カスタム metastore もサポートします。運用クラスターではこれを推奨します。
- 独自の Azure SQL Database を metastore として指定します。
- metastore のライフ サイクルは、クラスターのライフ サイクルに関連付けられないため、メタデータを失わずにクラスターの作成と削除を実行できます。 Hive スキーマなどのメタデータは、HDInsight クラスターを削除して再作成した後でも保持されます。
- カスタム metastore では、複数のクラスターとクラスターの種類をその metastore にアタッチできます。 たとえば、1 つの metastore を HDInsight の Interactive Query、Hive、および Spark クラスター間で共有できます。
- metastore (Azure SQL DB) のコストは、選択したパフォーマンス レベルに応じて支払います。
- metastore は、必要に応じて拡張できます。

![HDInsight Hive メタデータ ストアのユース ケース](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)


### <a name="select-a-custom-metastore-during-cluster-creation"></a>クラスターの作成時にカスタム metastore を選択する

クラスターの作成中に、以前に作成した Azure SQL Database をクラスターとしてポイントするか、クラスターの作成後に SQL Database を構成できます。 このオプションは、Azure Portal から新しい Hadoop、Spark、または対話型 Hive クラスターの作成中に、[ストレージ] > [metastore の設定] で指定できます。

![Azure Portal の HDInsight Hive メタデータ ストア](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Azure Portal または Ambari 構成 ([Hive] > [詳細設定]) から、他のクラスターをカスタム metastore に追加することもできます。

![Ambari の HDInsight Hive メタデータ ストア](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Hive metastore のベスト プラクティス

いくつかの一般的な HDInsight Hive metastore のベスト プラクティスを次に示します。

- 可能な限り、カスタム metastore を使用します。これにより、コンピューティング リソース (実行中のクラスター) とメタデータ (metastore に格納されます) を分離できます。
- 50 DTU と 250 GB のストレージを提供する S2 レベルから開始します。 ボトルネックを確認した場合は、データベースをスケール アップできます。
- 複数の HDInsight クラスターで異なるデータにアクセスする場合は、クラスターごとに metastore に対して別のデータベースを使用します。 複数の HDInsight クラスターで metastore を共有する場合は、クラスターが同じメタデータおよび基になるユーザー データ ファイルを使用することを意味します。
- カスタム metastore を定期的にバックアップします。 Azure SQL Database ではバックアップが自動的に生成されますが、バックアップのリテンション期間は異なります。 詳しくは、「[SQL Database 自動バックアップについての詳細情報](../sql-database/sql-database-automated-backups.md)」をご覧ください。
- パフォーマンスを最高にして、ネットワーク エグレスの課金を最小にするには、metastore と HDInsight クラスターを同じリージョンで検索します。
- Azure portal や Azure Monitor ログなどの Azure SQL Database 監視ツールを使用して、metastore のパフォーマンスと可用性を監視します。
- 既存のカスタム metastore データベースに対して、より高いバージョンの Azure HDInsight が新しく作成された場合、システムは metastore のスキーマをアップグレードします。バックアップからデータベースを復元しないかぎり、これを元に戻すことはできません。
- 複数のクラスター間で metastore を共有する場合は、すべてのクラスターの HDInsight を確実に同じバージョンにします。 異なるバージョンの Hive は、異なる metastore データベース スキーマを使用します。 たとえば、バージョン 1.2 の Hive クラスターと 2.1 の Hive クラスターで metastore を共有することはできません。 

##  <a name="apache-oozie-metastore"></a>Apache Oozie metastore

Apache Oozie は、Hadoop ジョブを管理するワークフロー調整システムです。  Oozie は、Apache MapReduce、Pig、Hive、その他の Hadoop ジョブをサポートします。  Oozie では、metastore を使用して、現在のワークフローと完了したワークフローの詳細情報を格納します。 Oozie の使用時にパフォーマンスを向上させるために、カスタム metastore として Azure SQL Database を使用できます。 metastore は、クラスター削除後の Oozie ジョブ データへのアクセスを提供することもできます。

Azure SQL Database を使用する Oozie metastore の作成手順については、[ワークフローでの Apache Oozie の使用](hdinsight-use-oozie-linux-mac.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

- [Apache Hadoop、Apache Spark、Apache Kafka などを使用して HDInsight でクラスターを設定する](./hdinsight-hadoop-provision-linux-clusters.md)
