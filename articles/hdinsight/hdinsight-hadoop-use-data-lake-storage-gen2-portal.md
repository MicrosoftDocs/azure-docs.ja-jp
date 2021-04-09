---
title: Azure HDInsight を作成する - Azure Data Lake Storage Gen2 - portal
description: ポータルを使用して Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する方法について学習します。
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/17/2020
ms.openlocfilehash: 5eb6cfc7f0dbc0bc886550e1d601dc52dff66267
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866371"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-the-azure-portal"></a>Azure portal を使用して Data Lake Storage Gen2 を使用するクラスターを作成する

Azure Portal は、Microsoft Azure クラウドでホストされるサービスやリソースの Web ベースの管理ツールです。 この記事では、ポータルを使用して Linux ベースの Azure HDInsight クラスターを作成する方法について説明します。 追加の詳細については、[HDInsight クラスターの作成](./hdinsight-hadoop-provision-linux-clusters.md)に関する記事を参照してください。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

ストレージに Data Lake Storage Gen2 を使用する HDInsight クラスターを作成するには、次の手順に従って階層型名前空間があるストレージ アカウントを構成します。

## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

ユーザー割り当てマネージド ID をまだお持ちでない場合には、作成します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左上の **[リソースの作成]** をクリックします。
1. 検索ボックスに「**ユーザー割り当て**」と入力し **[ユーザー割り当てマネージド ID]** をクリックします。
1. **Create** をクリックしてください。
1. ご自身のマネージド ID 名を入力し、適切なサブスクリプション、リソース グループ、および場所を選択します。
1. **Create** をクリックしてください。

Azure HDInsight でマネージド ID がどのように機能するかに関する詳細は、「[Azure HDInsight のマネージド ID](hdinsight-managed-identities.md)」を参照してください。

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png" alt-text="ユーザー割り当てマネージド ID を作成する":::

## <a name="create-a-storage-account-to-use-with-data-lake-storage-gen2"></a>Data Lake Storage Gen2 を使用するストレージ アカウントを作成する

Azure Data Lake Storage Gen2 を使用するストレージ アカウントを作成します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左上の **[リソースの作成]** をクリックします。
1. 検索ボックスに「**ストレージ**」と入力し、 **[ストレージ アカウント]** をクリックします。
1. **Create** をクリックしてください。
1. **[ストレージ アカウントの作成]** 画面で次を実行します。
    1. 正しいサブスクリプションとリソース グループを選択します。
    1. Data Lake Storage Gen2 を使用するストレージ アカウントの名前を入力します。
    1. **[詳細]** タブをクリックします。
    1. **[Data Lake Storage Gen2]** の下の **[階層構造の名前空間]** の横の **[有効]** をクリックします。
    1. **[Review + create]\(レビュー + 作成\)** をクリックします。
    1. **[作成]** をクリックします。

ストレージ アカウントを作成する場合のその他のオプションについては、「[Azure Data Lake Storage Gen2 のストレージ アカウントを作成します](../storage/blobs/create-data-lake-storage-account.md)。

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png" alt-text="Azure portal でのストレージ アカウントの作成を示したスクリーンショット":::

## <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2"></a>Data Lake Storage Gen2 にマネージド ID のアクセス許可を設定する

ストレージ アカウントの **[ストレージ BLOB データ所有者]** ロールにマネージド ID を割り当てます。

1. [Azure portal](https://portal.azure.com) で、ストレージ アカウントに移動します。
1. ストレージ アカウントを選択し、 **[アクセス制御 (IAM)]** を選択して、そのアカウントのアクセス制御設定を表示します。 **[ロールの割り当て]** タブを選択して、ロールの割り当ての一覧を表示します。

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png" alt-text="ストレージのアクセス制御設定を示すスクリーンショット":::

1. **[+ ロールの割り当ての追加]** ボタンを選択して新しいロールを追加します。
1. **[ロールの割り当ての追加]** ウィンドウで、 **[ストレージ BLOB データ所有者]** ロールを選択します。 次に、マネージド ID とストレージ アカウントを持つサブスクリプションを選択します。 次に、以前作成したユーザー割り当てマネージド ID を検索して見つけます。 最後に、マネージド ID を選択すると、その ID が **[選択したメンバー]** の下に一覧表示されます。

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png" alt-text="Azure ロールの割り当て方法を示すスクリーンショット":::

1. **[保存]** を選択します。 選択したユーザー割り当て ID が、選択されたロールの下に表示されるようになります。
1. この初期セットアップを完了すると、ポータルを通じてクラスターを作成できます。 クラスターは、ストレージ アカウントと同じ Azure リージョンに存在する必要があります。 クラスターの作成メニューの **[ストレージ]** タブで、次のオプションを選択します。

    * **[プライマリ ストレージの種類]** で、 **[Azure Data Lake Storage Gen2]** を選択します。
    * **[プライマリ ストレージ アカウント]** で、新しく作成した、Data Lake Storage Gen2 ストレージを使用するストレージ アカウントを探して選択します。

    * **[ID]** から、新しく作成されたユーザー割り当てマネージド ID を選択します。

        :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png" alt-text="Azure HDInsight で Data Lake Storage Gen2 を使用するためのストレージ設定":::

    > [!NOTE]
    > * Data Lake Storage Gen2 を使用するセカンダリ ストレージ アカウントを追加するには、ストレージ アカウント レベルで、先ほど作成したマネージド ID を、追加する新しい Data Lake Storage Gen2 に割り当てます。 Data Lake Storage Gen2 を使用するセカンダリ ストレージ アカウントの追加は、HDInsight の [追加のストレージ アカウント] ブレードからはサポートされていないことに注意してください。
    > * HDInsight で使用する Azure Blob Storage アカウントで、RA-GRS または RA-ZRS を有効にすることができます。 ただし、RA-GRS または RA-ZRS セカンダリ エンドポイントに対するクラスターの作成はサポートされていません。

## <a name="delete-the-cluster"></a>クラスターを削除する

「[ブラウザー、PowerShell、または Azure CLI を使用して HDInsight クラスターを削除する](./hdinsight-delete-cluster.md)」を参照してください。

## <a name="troubleshoot"></a>トラブルシューティング

HDInsight クラスターの作成で問題が発生した場合は、「[アクセス制御の要件](./hdinsight-hadoop-customize-cluster-linux.md#access-control)」を参照してください。

## <a name="next-steps"></a>次のステップ

HDInsight クラスターが正常に作成されました。 次に、クラスターを操作する方法を学習してください。

### <a name="apache-spark-clusters"></a>Apache Spark クラスター

* [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)
* [Scala を使用してスタンドアロン アプリケーションを作成する](spark/apache-spark-create-standalone-application.md)
* [Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](spark/apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Apache Hadoop クラスター

* [HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)
* [HDInsight での MapReduce の使用](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase クラスター

* [HDInsight での Apache HBase の使用](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight での Apache HBase の Java アプリケーションの開発](hbase/apache-hbase-build-java-maven-linux.md)
