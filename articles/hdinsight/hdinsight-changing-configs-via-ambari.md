---
title: Azure HDInsight で Apache Ambari を使用してクラスターを最適化する
description: Apache Ambari Web UI を使用して、Azure HDInsight クラスターを構成および最適化します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: 2146ccb0c4d7f263c3e1a69db9b172649fcd25ea
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863498"
---
# <a name="optimize-clusters-with-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight で Apache Ambari を使用してクラスターを最適化する

HDInsight は、大規模なデータ処理アプリケーションの Apache Hadoop クラスターを提供します。 これらの複雑なマルチノード クラスターの管理、監視、最適化は困難な場合があります。 Apache Ambari は、HDInsight Linux クラスターを管理および監視するための Web インターフェイスです。

Ambari Web UI の使用の概要については、[Apache Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)に関するページを参照してください

クラスターの資格情報を使用して Ambari (`https://CLUSTERNAME.azurehdidnsight.net`) にログインします。 初期画面に概要ダッシュボードが表示されます。

:::image type="content" source="./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png" alt-text="Apache Ambari のユーザー ダッシュボードの表示":::

Ambari Web UI は、ホスト、サービス、アラート、構成、ビューの管理に使用されます。 Ambari を使用して HDInsight クラスターを作成したり、サービスをアップグレードしたりすることはできません。 また、スタックとバージョンを管理したり、ホストの使用を停止または再開したり、クラスターにサービスを追加したりすることもできません。

## <a name="manage-your-clusters-configuration"></a>クラスターの構成の管理

構成設定は、特定のサービスを調整する際に役立ちます。 サービスの構成設定を変更するには、 **[Services]\(サービス\)** サイドバー (左側) でサービスを選択します。 次に、サービスの詳細ページの **[構成]** タブに移動します。

:::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png" alt-text="Apache Ambari のサービス サイドバー":::

## <a name="modify-namenode-java-heap-size"></a>NameNode の Java ヒープ サイズを変更する

NameNode の Java ヒープ サイズは、クラスターの負荷などの多くの要素によって決まります。 これには、ファイル数やブロック数も含まれます。 既定のサイズの 1 GB はほとんどのクラスターに適していますが、一部のワークロードではメモリを増減することが必要な場合があります。

NameNode の Java ヒープ サイズを変更するには、次の手順を実行します。

1. [Services]\(サービス\) サイドバーで **[HDFS]** を選択し、 **[Configs]\(構成\)** タブに移動します。

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png" alt-text="Apache Ambari の HDFS 構成":::

1. **[NameNode Java heap size]\(NameNode の Java ヒープ サイズ\)** 設定を見つけます。 **フィルター** テキスト ボックスに特定の設定を入力して検索することもできます。 設定名の横の **ペン** アイコンをクリックします。

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png" alt-text="Apache Ambari の NameNode の Java ヒープ サイズ":::

1. テキスト ボックスに新しい値を入力し、**Enter** キーを押して変更を保存します。

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png" alt-text="Ambari の NameNode の Java ヒープ サイズ 1 の編集":::

1. NameNode の Java ヒープ サイズが 2 GB から 1 GB に変更されます。

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png" alt-text="編集された NameNode の Java ヒープ サイズ 2":::

1. 構成画面の上部にある緑色の **[Save]\(保存\)** ボタンをクリックして変更を保存します。

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png" alt-text="'Apache Ambari の保存の構成'":::

## <a name="next-steps"></a>次のステップ

* [Apache Ambari Web UI を使用して HDInsight クラスターを管理する](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Apache HBase の最適化](./optimize-hbase-ambari.md)
* [Apache Hive を最適化する](./optimize-hive-ambari.md)
* [Apache Pig を最適化する](./optimize-pig-ambari.md)
