---
title: スクリプト アクション - Azure HDInsight の Jupyter で Python パッケージをインストールする
description: スクリプト アクションを使用して HDInsight の Spark clusters で Jupyter notebooks を構成し、外部の Python パッケージを使用する方法に関する詳細な手順。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: ce5dc7e17020e1e4564ebe1f531645f7329718dc
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900689"
---
# <a name="script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-on-hdinsight"></a>HDInsight の Apache Spark の Jupyter Notebook に外部の Python パッケージをインストールするスクリプト アクション

> [!div class="op_single_selector"]
> * [cell magic の使用](apache-spark-jupyter-notebook-use-external-packages.md)
> * [スクリプト アクションの使用](apache-spark-python-package-installation.md)

スクリプト アクションを使用して、HDInsight 上の [Apache Spark](https://spark.apache.org/) クラスターを、そのクラスターの標準では搭載されていない外部のコミュニティから提供されている **python** パッケージを使用するよう構成する方法について説明します。

> [!NOTE]  
> `%%configure` マジックを使用して Jupyter Notebook を構成して外部パッケージを使用することもできます。 手順については、「[HDInsight の Apache Spark クラスターの Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)」を参照してください。

[パッケージ インデックス](https://pypi.python.org/pypi)で、利用できるすべてのパッケージを検索できます。 公開されているパッケージの一覧を他のソースから入手してもかまいません。 たとえば、[conda-forge](https://conda-forge.org/feedstocks/) で使用できるパッケージをインストールできます。

この記事では、例として、クラスターでスクリプト アクションを使用して [TensorFlow](https://www.tensorflow.org/) パッケージをインストールし、Jupyter Notebook で使用する方法について説明します。

## <a name="prerequisites"></a>前提条件
次のものが必要です。

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。

   > [!NOTE]  
   > HDInsight Linux に Spark クラスターがない場合は、クラスターの作成時にスクリプト アクションを実行できます。 [カスタム スクリプト アクションの使用方法](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)に関するドキュメントを参照してください。
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>HDInsight クラスターで使用するオープン ソース ソフトウェアのサポート

Microsoft Azure HDInsight サービスは Apache Hadoop を中心に形成されたオープン ソース テクノロジのエコシステムを利用します。 Microsoft Azure は、オープン ソース テクノロジの一般的なレベルのサポートを提供します。 詳細については、「[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」の**サポート範囲**に関するセクションを参照してください。 HDInsight サービスでは、組み込みのコンポーネントに対してさらに高いレベルのサポートを提供しています。

HDInsight サービスで利用できるオープン ソース コンポーネントには、2 つの種類があります。

* **組み込みコンポーネント** - これらのコンポーネントは、HDInsight クラスターにプレインストールされており、クラスターの主要な機能を提供します。 たとえば、Apache Hadoop YARN リソース マネージャー、 Apache Hive クエリ言語 (HiveQL)、Mahout ライブラリなどがこのカテゴリに属します。 クラスター コンポーネントの完全な一覧は、「[HDInsight で提供されるApache Hadoop クラスター バージョンの新機能](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)」から入手できます。
* **カスタム コンポーネント** - クラスターのユーザーは、コミュニティで入手できるコンポーネントや自作のコンポーネントを、インストールするか、ワークロード内で使用することができます。

> [!IMPORTANT]   
> HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、 これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 Microsoft サポートによって問題が解決する場合もあれば、オープン ソース テクノロジに関する深い専門知識を入手できる場所への参加をお願いする場合もあります。 たとえば、次のような数多くのコミュニティ サイトを利用できます: [HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)や [https://stackoverflow.com](https://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。 また、Apache プロジェクトには、[https://apache.org](https://apache.org) に[Hadoop](https://hadoop.apache.org/) などのプロジェクト サイトもあります。


## <a name="use-external-packages-with-jupyter-notebooks"></a>Jupyter Notebook で外部のパッケージを使用する

1. [Azure portal](https://portal.azure.com/) で、お使いのクラスターに移動します。  

2. お使いのクラスターを選択して、左側のウィンドウの **[設定]** で **[スクリプト アクション]** を選択します。

3. **[+新規で送信]** を選択します。

4. **[スクリプト アクションの送信]** ウィンドウで次の値を入力します。  


    |パラメーター | 値 |
    |---|---|
    |スクリプトの種類 | ドロップダウン リストから **[- カスタム]** を選択します。|
    |名前 |テキスト ボックスに「`tensorflow`」と入力します。|
    |Bash スクリプト URI |テキスト ボックスに「`https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh`」と入力します。 |
    |ノードの種類 | **[ヘッド]** を選択し、 **[ワーカー]** チェック ボックスをオンにします。 |

    `tensorflowinstall.sh` には次のコマンドが含まれます。

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install --yes tensorflow
    ```

5. **作成** を選択します。  [カスタム スクリプト アクションの使用方法](../hdinsight-hadoop-customize-cluster-linux.md)に関するドキュメントを参照してください。

6. スクリプトが完了するのを待ちます。  **[スクリプト アクション]** ウィンドウには、スクリプトの実行中、"**新しいスクリプト アクションは、現在のクラスター操作の完了後に送信できます**" というメッセージが表示されます。  進捗状況バーが、Ambari UI の **[バック グラウンド操作]** ウィンドウに表示されます。

7. PySpark Jupyter Notebook を開きます。  手順については、「[Spark HDInsight での Jupyter Notebook の作成](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight)」を参照してください。

    ![新しい Jupyter Notebook の作成](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "新しい Jupyter Notebook の作成")

8. 次に `import tensorflow` によって、hello world の例を実行します。 次のコードを入力します。

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    結果は次のようになります。
    
    ![TensorFlow コード実行](./media/apache-spark-python-package-installation/tensorflow-execution.png "TensorFlow コードの実行")

> [!NOTE]  
> クラスターには 2 つの Python インストールがあります。 Spark では、`/usr/bin/anaconda/bin` の Anaconda Python インストールが使用され、これは既定で Python 2.7 環境になります。 Python 3.x を使用し、PySpark3 カーネルにパッケージをインストールするには、その環境用の `conda` 実行可能ファイルへのパスを使用し、`-n` パラメーターを使用して、環境を指定します。 たとえば、コマンド `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35` は、`conda-forge` チャネルを使用して `ggplot` パッケージを Python 3.5 環境にインストールします。

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 上での Apache Spark を使用した Web サイト ログ分析](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [HDInsight の Apache Spark クラスターの Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Apache Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Apache Spark クラスター内の Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)
