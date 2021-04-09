---
title: クラスターを最新版に移行する
titleSuffix: Azure HDInsight
description: Azure HDInsight クラスターを新しいバージョンに移行するためのガイドラインを説明します。
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: 4aa25368e156ce793e969f866490352e253559fc
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871726"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>HDInsight クラスターを新しいバージョンに移行する

最新の HDInsight 機能を利用するために、HDInsight クラスターを最新版に定期的に移行することをお勧めします。 HDInsight では、既存のクラスターが新しいコンポーネント バージョンにアップグレードされるインプレース アップグレードがサポートされていません。 希望するコンポーネントとプラットフォーム バージョンで新しいクラスターを作成し、その新しいクラスターを使用するために、お使いのアプリケーションを移行する必要があります。 HDInsight クラスターのバージョンを移行するには、下のガイドラインに従います。

> [!NOTE]  
> HDInsight のサポートされているバージョンについては、[HDInsight コンポーネントのバージョン](hdinsight-component-versioning.md#supported-hdinsight-versions)に関するページを参照してください。

## <a name="migration-tasks"></a>移行タスク

HDInsight クラスターをアップグレードするワークフローは次のとおりです。
:::image type="content" source="./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png" alt-text="HDInsight アップグレード ワークフローの図" border="false":::

1. このドキュメントの各セクションを読んで、HDInsight クラスターに移行するときに必要になる場合がある変更について理解します。
2. テスト/品質保証環境として、クラスターを作成します。 クラスターの作成の詳細については、「[HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。
3. 既存のジョブ、データ ソース、およびシンクを新しい環境にコピーします。
4. 検証テストを実行し、新しいクラスターで予期したとおりにジョブが動作していることを確認します。

すべて予期したとおりに動作していることを確認したら、移行のダウンタイムをスケジュールします。 このダウンタイム中に、次の操作を実行します。

1. クラスター ノードでローカルに格納されている一時的なデータをバックアップします。 たとえば、ヘッド ノードに直接データを格納している場合です。
1. [既存のクラスターを削除します](./hdinsight-delete-cluster.md)。
1. 同じ VNET サブネットの中に、前のクラスターで使用していたのと同じ既定のデータ ストアを使用する、最新の (またはサポートされている) バージョンの HDI クラスターを作成します。 これで、新しいクラスターで既存の運用データを引き続き使用できます。
1. バックアップしたすべての一時的なデータをインポートします。
1. 新しいクラスターを使用して、ジョブを開始または処理を続行します。

## <a name="workload-specific-guidance"></a>ワークロード固有のガイダンス

特定のワークロードを移行する方法については、次のドキュメントにガイドラインが記載されています。

* [HBase を移行する](./hbase/apache-hbase-migrate-new-version.md)
* [Kafka を移行する](./kafka/migrate-versions.md)
* [Hive/Interactive Query を移行する](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>バックアップと復元

データベースのバックアップと復元の詳細については、[自動データベース バックアップを使用した Azure SQL Database 内のデータベースの復旧](../azure-sql/database/recovery-using-backups.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [Linux ベースの HDInsight クラスターを作成する方法を確認する](hdinsight-hadoop-provision-linux-clusters.md)
* [SSH を使用して HDInsight に接続する](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Apache Ambari を使用して Linux ベースのクラスターを管理する](hdinsight-hadoop-manage-ambari.md)
* [HDInsight リリース ノート](./hdinsight-version-release.md)
