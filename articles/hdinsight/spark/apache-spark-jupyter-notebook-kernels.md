---
title: Azure HDInsight の Spark クラスター上の Jupyter Notebook のカーネル
description: Azure HDInsight 上の Spark クラスターで使用可能な Jupyter Notebook 用の PySpark、PySpark3、および Spark カーネルについて説明します。
keywords: spark 上の jupyter notebook,jupyter spark
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: 68581c957e2835aa8841e3ce490c9a7898ed4ff5
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527474"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Azure HDInsight の Apache Spark クラスター上の Jupyter Notebook のカーネル 

HDInsight Spark クラスターは、アプリケーションをテストするために [Apache Spark](https://spark.apache.org/) 上の Jupyter Notebook で使用できるカーネルを提供します。 カーネルは、コードを実行し、解釈するプログラムです。 次の 3 つのカーネルがあります。

- **PySpark** - Python2 で記述されたアプリケーション用。
- **PySpark3** - Python3 で記述されたアプリケーション用。
- **Spark** - Scala で記述されたアプリケーション用。

この記事では、このカーネルの使用方法と、カーネルを使用するメリットについて説明します。

## <a name="prerequisites"></a>前提条件

* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Spark HDInsight での Jupyter Notebook の作成

1. [Azure Portal](https://portal.azure.com/) でクラスターを開きます。  手順については、「[クラスターの一覧と表示](../hdinsight-administer-use-portal-linux.md#showClusters)」を参照してください。 クラスターは新しいポータル ブレードで開きます。

2. **[クイック リンク]** セクションで **[クラスター ダッシュボード]** をクリックして、**[クラスター ダッシュボード]** ブレードを開きます。  **[クイック リンク]** が表示されない場合は、ブレードの左側のメニューで **[概要]** をクリックします。

    ![Spark 上の Jupyter Notebook](./media/apache-spark-jupyter-notebook-kernels/hdinsight-jupyter-notebook-on-spark.png "Spark 上の Jupyter Notebook") 

3. **[Jupyter Notebook]** をクリックします。 入力を求められたら、クラスターの管理者資格情報を入力します。
   
   > [!NOTE]  
   > ブラウザーで次の URL を開き、Spark クラスターの Jupyter Notebook にアクセスすることもできます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`


3. **[新規]** をクリックし、**[Pyspark]**、**[PySpark3]**、または **[Spark]** をクリックして、Notebook を作成します。 Scala アプリケーションには Spark カーネルを、Python2 アプリケーションには PySpark カーネルを、Python3 アプリケーションには PySpark3 カーネルを使用します。
   
    ![Spark 上の Jupyter Notebook のカーネル](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Spark 上の Jupyter Notebook のカーネル") 

4. Notebook が、選択したカーネルで開きます。

## <a name="benefits-of-using-the-kernels"></a>カーネルを使用する利点

Spark HDInsight クラスター上の Jupyter Notebook で新しいカーネルを使用する利点は次のとおりです。

- **コンテキストのプリセット**。 **PySpark**、**PySpark3**、または **Spark** カーネルでは、アプリケーションの操作を開始する前に、Spark または Hive コンテキストを明示的に設定する必要がありません。 これらは既定で利用できます。 各コンテキストは次のとおりです。
   
  * **sc** : Spark コンテキスト用
  * **sqlContext** : Hive コンテキスト用
   
    そのため、コンテキストを設定するための次のようなステートメントを実行する必要はありません。
   
         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)
   
    代わりに、事前に設定されたコンテキストをアプリケーションで直接使用できます。

- **セル マジック**。 PySpark カーネルには、"マジック"、つまり、`%%` で呼び出すことができる特別なコマンドがいくつか事前定義されています (`%%MAGIC` `<args>` など)。 このマジック コマンドはコード セルの最初の単語にする必要があります。また、コンテンツの複数行に対応できる必要があります。 魔法の単語はセルの最初の単語にする必要があります。 その前に他の単語を追加すると、それがコメントであっても、エラーを引き起こします。     マジックの詳細については、[こちら](https://ipython.readthedocs.org/en/stable/interactive/magics.html)をご覧ください。
   
    次の表は、カーネルで使用できるさまざまなマジックを一覧にしたものです。

   | マジック | 例 | 説明 |
   | --- | --- | --- |
   | help |`%%help` |利用できるすべてのマジック、その例と説明から構成されるテーブルを生成します。 |
   | info |`%%info` |現在の Livy エンドポイントのセッション情報を出力します。 |
   | [構成] |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |セッションを作成するためのパラメーターを構成します。 セッションが既に作成されている場合、強制フラグ (-f) は必須です。これにより、セッションの破棄と再作成が保証されます。 有効なパラメーターの一覧については、 [Livy の「POST /sessions」の「Request Body (要求本文)」](https://github.com/cloudera/livy#request-body) をご覧ください。 例の列で示されているように、パラメーターは JSON 文字列として渡し、マジックの後の次の行に置く必要があります。 |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |sqlContext に対して Hive クエリを実行します。 `-o` パラメーターが渡される場合、クエリの結果は、 [Pandas](https://pandas.pydata.org/) データフレームとして %%local Python コンテキストで永続化されます。 |
   | local |`%%local`<br>`a=1` |後続行のすべてのコードがローカルで実行されます。 使用しているカーネルに関係なく、コードは有効な Python2 コードである必要があります。 したがって、Notebook の作成時に **PySpark3** または **Spark** カーネルを選択している場合でも、`%%local` マジックをセルで使用する場合、そのセルには、有効な Python2 コードのみが含まれている必要があります。 |
   | logs |`%%logs` |現在の Livy セッションのログを出力します。 |
   | 削除 |`%%delete -f -s <session number>` |現在 Livy エンドポイントの特定のセッションを削除します。 カーネル自体が開始したセッションを削除することはできません。 |
   | cleanup |`%%cleanup -f` |このノートブックのセッションを含む、現在 Livy エンドポイントのすべてのセッションを削除します。 強制フラグ -f は必須です。 |

   > [!NOTE]  
   > PySpark カーネルによって追加されるマジックに加えて、`%%sh` などの[組み込み IPython](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics) マジックも使用することができます。 `%%sh` マジックは、クラスター ヘッド ノードでスクリプトやコード ブロックを実行する際に使用することができます。

1. **自動視覚化**。 **Pyspark** カーネルは、Hive と SQL のクエリの出力を自動的に視覚化します。 表、円グラフ、面積グラフ、棒グラフなど、さまざまな種類の視覚化から選択できます。

## <a name="parameters-supported-with-the-sql-magic"></a>%%sql マジックでサポートされるパラメーター
`%%sql` マジックでは、クエリの実行時に受け取る出力の種類の制御に使用できる、さまざまなパラメーターがサポートされます。 次の表に、出力を示します。

| パラメーター | 例 | 説明 |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |クエリの結果を [Pandas](https://pandas.pydata.org/) データフレームとして %%local Python コンテキストで永続化するには、このパラメーターを使用します。 データ フレーム変数の名前は、指定した変数の名前です。 |
| パラメーター |`-q` |セルの視覚化をオフにするには、これを使用します。 セルのコンテンツを自動的に視覚化せず、単にデータ フレームとしてキャプチャする場合は、 `-q -o <VARIABLE>`を使用します。 (たとえば、`CREATE TABLE` ステートメントのような、SQL クエリを実行するために) 結果をキャプチャせずに視覚化をオフにする必要がある場合、`-o` 引数を指定せずに `-q` を使用します。 |
| -m |`-m <METHOD>` |ここで **METHOD** は **take** または **sample** です (既定値は **take**)。 メソッドが **take**の場合、カーネルによって、MAXROWS (この表で後述) で指定された結果のデータ セットの先頭から要素が取得されます。 メソッドが **sample** の場合、カーネルによって、この表の次に説明する `-r` パラメーターに従って、データ セットの要素がランダムにサンプリングされます。 |
| -r |`-r <FRACTION>` |ここで **FRACTION** は、0.0 ～ 1.0 の浮動小数点数です。 SQL クエリのサンプル メソッドが `sample` の場合、カーネルは、結果セットの指定された割合の要素をランダムにサンプリングします。 たとえば、`-m sample -r 0.01` 引数を使用して SQL クエリを実行した場合、結果の行の 1% がランダムにサンプリングされます。 |
| -n |`-n <MAXROWS>` |**MAXROWS** は整数値です。 カーネルによって、出力行の数が **MAXROWS** に制限されます。 **MAXROWS** が **-1** など、負の数の場合は、結果セット内の行数は制限されません。 |

**例:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

上記のステートメントによって、次のことが行われます。

* **hivesampletable**からすべてのレコードを選択します。
* -q を使用しているため、自動視覚化をオフにします。
* `-m sample -r 0.1 -n 500` を使用しているため、hivesampletable 内の行の 10% がランダムにサンプリングされ、結果セットのサイズが 500 行に制限されます。
* 最後に、 `-o query2` を使用しているため、 **query2**という名前のデータフレームにも、その出力が保存されます。

## <a name="considerations-while-using-the-new-kernels"></a>新しいカーネルを使用する場合の考慮事項

どのカーネルを使用する場合でも、Notebook を実行したままにしておくと、クラスターのリソースが消費されます。  これらのカーネルでは、コンテキストがプリセットされているため、Notebook を終了するだけではコンテキストは強制終了されません。そのため、クラスターのリソースは消費され続けます。 Notebook の使用が終了したら、Notebook の **[ファイル]** メニューの **[Close and Halt (閉じて停止する)]** オプションを使用することをお勧めします。これにより、コンテキストが強制終了され、Notebook が終了します。     

## <a name="show-me-some-examples"></a>いくつかの例

Jupyter Notebook を開くと、ルート レベルで利用可能な 2 つのフォルダーが表示されます。

* **PySpark** フォルダーには、新しい **Python** カーネルを使用するサンプル Notebook があります。
* **Scala** フォルダーには、新しい **Spark** カーネルを使用するサンプル Notebook があります。

**PySpark** または **Spark** フォルダーから **00 - [READ ME FIRST] Spark Magic Kernel Features** Notebook を開くと、利用できるさまざまなマジックを確認できます。 この 2 つのフォルダーにはサンプル ノードブックが他にもあります。Jupyter ノードブックと HDInsight Spark クラスターを利用したさまざまなシナリオの実現方法について学習できます。

## <a name="where-are-the-notebooks-stored"></a>Notebook の格納場所

クラスターで Azure Storage が既定のストレージ アカウントとして使用されている場合、Jupyter Notebook は **/HdiNotebooks** フォルダーの下のストレージ アカウントに保存されます。  Notebook、テキスト ファイル、および Jupyter 内から作成したフォルダーには、ストレージ アカウントからアクセスできます。  たとえば、Jupyter を使用して **myfolder** フォルダーと Notebook **myfolder/mynotebook.ipynb** を作成した場合、ストレージ アカウントの `/HdiNotebooks/myfolder/mynotebook.ipynb` で Notebook にアクセスできます。  逆の場合も同様です。つまり、Notebook を 自分のストレージ アカウントの `/HdiNotebooks/mynotebook1.ipynb` に直接アップロードした場合、Jupyter からも Notebook を表示することができます。  Notebook は、クラスターが削除された後でも、ストレージ アカウントに保持されます。

> [!NOTE]  
> 既定のストレージが Azure Data Lake Storage である HDInsight クラスターの場合、ノートブックは関連するストレージには保存されません。

Notebook がストレージ アカウントに保存される方法は、[Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) と互換性があります。 そのため、クラスターに SSH で接続する場合は、次のスニペットに示すようにファイル管理コマンドを使用できます。

    hdfs dfs -ls /HdiNotebooks                               # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                    # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

クラスターの既定のストレージ アカウントが Azure Storage か Azure Data Lake Storage かに関わらず、ノートブックはクラスターのヘッド ノード `/var/lib/jupyter` にも保存されます。

## <a name="supported-browser"></a>サポートされているブラウザー

Spark HDInsight クラスター上の Jupyter Notebook は、Google Chrome でのみサポートされます。

## <a name="feedback"></a>フィードバック
新しいカーネルは進化の過程にあり、時間の経過と共に成熟するでしょう。 カーネルが改良されるにつれて、API も変更される可能性があります。 これらの新しいカーネルに関するフィードバックを、ぜひお寄せください。 これらのカーネルの最終リリースの設計に役立ちます。 ご意見やフィードバックは、この記事の下部にある **コメント** のセクションからお寄せください。

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
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Apache Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)
