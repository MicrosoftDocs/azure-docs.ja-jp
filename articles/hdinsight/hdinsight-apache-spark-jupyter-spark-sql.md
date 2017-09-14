---
title: "Azure HDInsight での Apache Spark クラスターの作成 | Microsoft Docs"
description: "HDInsight で Apache Spark クラスターを作成する方法に関する HDInsight Spark のクイック スタート。"
keywords: "spark クイック スタート,対話型 spark,対話型クエリ,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/21/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: ad4330a1fc7f8de154d9aaa8df3acc2ab59b9dc1
ms.contentlocale: ja-jp
ms.lasthandoff: 07/24/2017

---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Azure HDInsight での Apache Spark クラスターの作成

この記事では、Azure HDInsight で Apache Spark クラスターを作成する方法を説明します。 HDInsight での Spark について詳しくは、「[概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)」を参照してください。

   ![Azure HDInsight に Apache Spark クラスターを作成する手順を説明するクイック スタート図](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-quickstart-interactive-spark-query-flow.png "HDInsight で Apache Spark を使用する Spark のクイック スタート。図に示されている手順: クラスターを作成し、Spark の対話型クエリを実行します")

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 このチュートリアルを開始する前に、Azure サブスクリプションが必要です。 「[無料の Azure アカウントを今すぐ作成しましょう](https://azure.microsoft.com/free)」をご覧ください。

## <a name="create-hdinsight-spark-cluster"></a>HDInsight Spark クラスターの作成

このセクションでは、[Azure Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/)を使用して HDInsight Spark クラスターを作成します。 その他のクラスター作成方法については、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

1. 次の画像をクリックして Azure ポータルでテンプレートを開きます。         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 次の値を入力します。

    ![Azure Resource Manager テンプレートを使用して HDInsight Spark クラスターを作成する](./media/hdinsight-apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Azure Resource Manager テンプレートを使用して HDInsight で Spark クラスターを作成する")

    * **サブスクリプション**: このクラスター向けに Azure サブスクリプションを選択します。
    * **リソース グループ**: リソース グループを作成するか、既存のリソース グループを選択します。 リソース グループは、プロジェクトの Azure リソースを管理するために使用されます。
    * **場所**: リソース グループの場所を選択します。 テンプレートでは、この場所をクラスターの作成および既定のクラスター ストレージに使用します。
    * **クラスター名**: 作成する HDInsight クラスターの名前を入力します。
    * **Spark のバージョン**: クラスターにインストールするバージョンとして **[2.0]** を選択します。
    * **クラスターのログイン名とパスワード**: 既定のログイン名は admin です。
    * **SSH のユーザー名とパスワード**。

   これらの値を書き留めておいてください。  この情報は後で必要になります。

3. **[上記の使用条件に同意する]**、**[ダッシュボードにピン留めする]** の順に選択し、**[購入]** をクリックします。 "Submitting deployment for Template deployment" という新しいタイルが表示されます。 クラスターの作成には約 20 分かかります。

HDInsight クラスターを作成する際に問題が発生した場合は、適切なアクセス許可がない可能性があります。 詳細については、「[アクセス制御の要件](hdinsight-administer-use-portal-linux.md#create-clusters)」を参照してください。

> [!NOTE]
> この記事では、[クラスター ストレージとして Azure Storage BLOB](hdinsight-hadoop-use-blob-storage.md) を使用する Spark クラスターを作成します。 既定のストレージとして [Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md) を使用する Spark クラスターを作成することもできます。 手順については、 [Data Lake Store を使用した HDInsight クラスターの作成](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)に関するページを参照してください。
>
>

## <a name="run-a-hive-query-using-spark-sql"></a>Spark SQL を使用した Hive クエリの実行

HDInsight Spark クラスター用に構成された Jupyter Notebook を使用すると、Spark SQL を使用して Hive クエリを実行するために使用できるプリセット `sqlContext` が手に入ります。 このセクションでは、Jupyter Notebook を起動し、基本的な Hive クエリを実行する方法について説明します。

1. [Azure Portal](https://portal.azure.com/)を開きます。

2. クラスターをダッシュボードにピン留めしている場合は、ダッシュボードから目的のクラスターのタイルをクリックしてクラスター ブレードを起動します。

    クラスターをダッシュボードにピン留めしていない場合は、左側のウィンドウで **[HDInsight クラスター]** をクリックし、作成したクラスターをクリックします。

3. **クイック リンク**で **[クラスター ダッシュボード]** をクリックし、**[Jupyter Notebook]** をクリックします。 入力を求められたら、クラスターの管理者資格情報を入力します。

   ![Jupyter Notebook を開いて対話型の Spark SQL クエリを実行する](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter Notebook を開いて対話型の Spark SQL クエリを実行する")

   > [!NOTE]
   > ブラウザーで次の URL を開くことで、クラスターの Jupyter Notebook にアクセスすることもできます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Notebook を作成します。 **[新規]** をクリックし、**[PySpark]** をクリックします。

   ![対話型の Spark SQL クエリを実行する Jupyter Notebook を作成する](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "対話型の Spark SQL クエリを実行する Jupyter Notebook を作成する")

   Untitled(Untitled.pynb) という名前の新しい Notebook が作成されて開かれます。

4. 上部の Notebook 名をクリックし、目的のわかりやすい名前を入力します。

    ![対話型の Spark クエリを実行する Jupter Notebook に名前を指定する](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "対話型の Spark クエリを実行する Jupter Notebook に名前を指定する")

5.  次のコードを空のセルに貼り付け、**Shift + Enter** キーを押してコードを実行します。 以下のコードでは、`%%sql` (SQL マジックと呼ばれる) により、プリセット `sqlContext` を使用して Hive クエリを実行するよう Jupyter Notebook に指示します。 クエリは、すべての HDInsight クラスターで既定で使用可能な Hive テーブル (**hivesampletable**) から先頭の 10 行を取得します。

        %%sql
        SELECT * FROM hivesampletable LIMIT 10

    ![HDInsight Spark での Hive クエリ](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "HDInsight Spark での Hive クエリ")

    `%%sql` マジックとプリセット コンテキストの詳細については、[HDInsight クラスターで利用可能な Jupyter カーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)に関する記事を参照してください。

    > [!NOTE]
    > Jupyter でクエリを実行するたびに、Web ブラウザー ウィンドウのタイトルに **[(ビジー)]** ステータスと Notebook のタイトルが表示されます。 また、右上隅にある **PySpark** というテキストの横に塗りつぶされた円も表示されます。 ジョブが完了すると、白抜きの円に変化します。
    >
    >
    
6. 画面が更新され、クエリ出力が表示されます。

    ![HDInsight Spark での Hive クエリ出力](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "HDInsight Spark での Hive クエリ出力")

7. アプリケーションの実行後は、Notebook をシャットダウンしてクラスター リソースを解放します。 そのためには、Notebook の **[ファイル]** メニューの **[Close and Halt]** (閉じて停止) をクリックします。

8. 次の手順を後で実行する場合は、必ずこの記事で作成した HDInsight クラスターを削除してください。 

    [!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-step"></a>次のステップ 

この記事では、HDInsight Spark クラスターを作成し、基本的な Spark SQL クエリを実行する方法を学習しました。 HDInsight Spark クラスターを使用してサンプル データに対話型のクエリを実行するには、次の記事に進みます。

> [!div class="nextstepaction"]
>[HDInsight Spark クラスターに対して対話型クエリを実行する](hdinsight-apache-spark-load-data-run-query.md)




