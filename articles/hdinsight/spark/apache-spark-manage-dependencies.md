---
title: Azure HDInsight で Spark アプリケーションの依存関係を管理する
description: この記事では、PySpark および Scala アプリケーションのために HDInsight Spark クラスターで Spark の依存関係を管理する方法の概要について説明します。
author: yanancai
ms.author: yanacai
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: f0673523c74a0ea298e7d2d520952c3e98877e91
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930043"
---
# <a name="manage-spark-application-dependencies"></a>Spark アプリケーションの依存関係を管理する

この記事では、HDInsight で実行される Spark アプリケーションの依存関係を管理する方法について説明します。 Spark アプリケーションとクラスター スコープでの Scala と PySpark の両方について説明します。

ユーザーのケースに基づいてセクションに移動するには、クイック リンクを使用します。
* [Jupyter Notebook を使用して Spark ジョブの jar の依存関係を設定する](#use-jupyter-notebook)
* [Azure Toolkit for IntelliJ を使用して Spark ジョブの jar の依存関係を設定する](#use-azure-toolkit-for-intellij)
* [Spark クラスターの jar の依存関係を構成する](#jar-libs-for-cluster)
* [jar の依存関係を安全に管理する](#safely-manage-jar-dependencies)
* [Jupyter Notebook を使用して Spark ジョブの Python パッケージを設定する](#use-jupyter-notebook-1)
* [Spark クラスターの Python パッケージを安全に管理する](#python-packages-for-cluster)

## <a name="jar-libs-for-one-spark-job"></a>1 つの Spark ジョブの Jar ライブラリ
### <a name="use-jupyter-notebook"></a>Jupyter Notebook を使用する
Spark カーネルの Jupyter Notebook で Scala の Spark セッションが開始されるときに、次のものからパッケージを構成できます。

* [Maven リポジトリ](https://search.maven.org/)、または [Spark パッケージ](https://spark-packages.org/)のコミュニティから提供されたパッケージ。
* クラスターのプライマリ ストレージに格納されている Jar ファイル。

外部のパッケージを使用するようにノートブックを構成するには、`%%configure` マジックを使用します。 外部のパッケージを使用するノートブックでは必ず、最初のコード セルで `%%configure` マジックを呼び出すようにしてください。 そうすることでセッションが開始される前に、指定のパッケージを使用するようにカーネルが構成されます。

>
>[!IMPORTANT]  
>最初のセルでカーネルを構成しなかった場合、`-f` パラメーターを指定して `%%configure` を使用できますが、その場合セッションが最初からやり直しとなり、すべての進捗が失われます。

**Maven リポジトリまたは Spark パッケージのパッケージのサンプル**

Maven リポジトリでパッケージを見つけたら、**GroupId**、**ArtifactId**、および **Version** の値を収集します。 3 つの値をコロン ( **:** ) で区切って連結します。

   ![パッケージ スキーマを連結する](./media/apache-spark-manage-dependencies/spark-package-schema.png "パッケージ スキーマを連結する")

収集した値が、クラスターに一致することを確認します。 この例では、Scala 2.11 用の Spark Cosmos DB コネクタ パッケージと、HDInsight 3.6 Spark クラスター用の Spark 2.3 を使用しています。 わからない場合は、Spark カーネルのコード セルで `scala.util.Properties.versionString` を実行して、クラスターの Scala バージョンを取得します。 `sc.version` を実行して、クラスターの Spark バージョンを取得します。

```
%%configure { "conf": {"spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.3.3" }}
```

**プライマリ ストレージに格納されている Jar のサンプル**

クラスターのプライマリ ストレージの jar ファイルには、[URI スキーム](../hdinsight-hadoop-linux-information.md#URI-and-scheme)を使用します。 Azure Storage では `wasb://`、Azure Data Lake Storage Gen2 では `abfs://`、Azure Data Lake Storage Gen1 では `adl://` です。 Azure Storage または Data Lake Storage Gen2 で安全な転送が有効になっている場合、URI は `wasbs://` または `abfss://` になります。 [安全な転送](../../storage/common/storage-require-secure-transfer.md)に関する記事を参照してください。

複数の jar ファイルの場合は、jar のパスのコンマ区切りのリストを使用します。glob を使用できます。 jar はドライバーおよびエグゼキューターのクラスパスに含まれています。

```
%%configure { "conf": {"spark.jars": "wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/azure-cosmosdb-spark_2.3.0_2.11-1.3.3.jar" }}
```

外部パッケージを構成した後に、コード セルでインポートを実行すると、パッケージが正しく配置されているかどうかを確認できます。

```scala
import com.microsoft.azure.cosmosdb.spark._
```

### <a name="use-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ を使用する
[Azure Toolkit for IntelliJ プラグイン](./apache-spark-intellij-tool-plugin.md)では、Spark Scala アプリケーションを HDInsight クラスターに送信するための UI エクスペリエンスが提供されます。 Spark アプリケーションの送信時の jar ライブラリ パスを構成するために、`Referenced Jars` および `Referenced Files` プロパティが用意されています。 [HDInsight で Azure Toolkit for IntelliJ プラグインを使用する方法](./apache-spark-intellij-tool-plugin.md#run-a-spark-scala-application-on-an-hdinsight-spark-cluster)の詳細を確認してください。

![[Spark Submission]\(Spark 送信\) ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

## <a name="jar-libs-for-cluster"></a>クラスターの Jar ライブラリ
場合によっては、すべてのアプリケーションを既定で同じ依存関係で設定できるように、クラスター レベルで jar の依存関係を構成することがあります。 この方法では、Spark のドライバーおよびエグゼキューターのクラスパスに jar パスを追加します。

1. 次のサンプルのスクリプト操作を実行して、プライマリ ストレージ `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` からクラスターのローカル ファイル システム `/usr/libs/sparklibs` に jar ファイルをコピーします。 Linux では `:` を使用してクラス パス リストを区切るため、この手順が必要になりますが、HDInsight では、`wasb://` のようなスキームを使用したストレージ パスのみがサポートされます。 クラス パスに直接追加した場合、リモート ストレージ パスは正常に機能しません。

    ```bash
    sudo mkdir -p /usr/libs/sparklibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/sparklibs
    ```

2. Ambari から Spark サービスの構成を変更して、クラス パスを更新します。 **[Ambari] > [Spark] > [Configs] > [Custom Spark2-defaults]** にアクセスします。 次のように **[Add Property]** でプロパティを追加します。 複数のパスを追加する場合は、`:` を使用してパスを区切ります。 glob を使用できます。

    ```
    spark.driver.extraClassPath=/usr/libs/sparklibs/*
    spark.executor.extraClassPath=/usr/libs/sparklibs/*
    ```

   ![Spark の既定の構成の変更](./media/apache-spark-manage-dependencies/change-spark-default-config.png "Spark の既定の構成の変更")

3. 変更した構成を保存し、影響を受けるサービスを再起動します。

   ![影響を受けるサービスの再起動](./media/apache-spark-manage-dependencies/restart-impacted-services.png "影響を受けるサービスの再起動")

[スクリプト操作](../hdinsight-hadoop-customize-cluster-linux.md)を使用すると、この手順を自動化できます。 [Hive カスタム ライブラリの追加](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)のスクリプト操作が参考になります。 Spark のサービス構成を変更する場合は、構成ファイルを直接変更するのではなく、Ambari API を使用してください。 

## <a name="safely-manage-jar-dependencies"></a>jar の依存関係を安全に管理する
HDInsight クラスターには jar の依存関係が組み込まれており、これらの jar バージョンの更新が随時行われます。 組み込み jar とユーザーが参照用に導入した jar の間のバージョンの競合を回避するには、[アプリケーションの依存関係をシェーディングする](./safely-manage-jar-dependency.md)ことを検討してください。

## <a name="python-packages-for-one-spark-job"></a>1 つの Spark ジョブの Python パッケージ
### <a name="use-jupyter-notebook"></a>Jupyter Notebook を使用する
HDInsight Jupyter Notebook の PySpark カーネルは、PyPi または Anaconda パッケージ リポジトリから直接 Python パッケージをインストールすることをサポートしていません。 `.zip`、`.egg`、または `.py` の依存関係があり、1 つの Spark セッションでそれらを参照する場合は、次の手順に従います。

1. 次のサンプルのスクリプト操作を実行して、プライマリ ストレージ `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` からクラスターのローカル ファイル システム `/usr/libs/pylibs` に `.zip`、`.egg`、または `.py` ファイルをコピーします。 Linux では `:` を使用して検索パス リストを区切るため、この手順が必要になりますが、HDInsight では、`wasb://` のようなスキームを使用したストレージ パスのみがサポートされます。 `sys.path.insert` を使用すると、リモート ストレージ パスが正しく機能しません。

    ```bash
    sudo mkdir -p /usr/libs/pylibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/pylibs
    ```

2. Notebook で、PySpark カーネルを使用してコード セルで次のコードを実行します。

   ```python
   import sys
   sys.path.insert(0, "/usr/libs/pylibs/pypackage.zip")
   ```

3. `import` を実行して、パッケージが正常に含まれているかどうかを確認します。  

## <a name="python-packages-for-cluster"></a>クラスター用の Python パッケージ
スクリプト操作を介して conda コマンドを使用して、Anaconda からクラスターに Python パッケージをインストールできます。 インストールされるパッケージはクラスター レベルであり、すべてのアプリケーションに適用されます。 

HDInsight Spark クラスターには、2 つの Python インストール (Anaconda Python 2.7 と Anaconda Python 3.5) が組み込まれています。 サービスの既定の Python 設定、およびクラスターを中断せずに外部 Python パッケージを安全にインストールする方法の詳細については、[クラスターの Python の依存関係の安全な管理](./apache-spark-python-package-installation.md)に関する記事を参照してください。
