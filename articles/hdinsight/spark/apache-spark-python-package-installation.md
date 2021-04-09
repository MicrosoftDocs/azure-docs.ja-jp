---
title: Azure HDInsight の Jupyter を使用した Python パッケージに対するスクリプト アクション
description: スクリプト アクションを使用して HDInsight の Spark clusters で Jupyter Notebook を構成し、外部の Python パッケージを使用する方法に関する詳細な手順。
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/29/2020
ms.openlocfilehash: c3f912b4f4c2e78c44425f489927cee185b3d312
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868717"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>スクリプト アクションを使用して Azure HDInsight で Python 環境を安全に管理する

HDInsight では、Spark クラスターに 2 つの Python のインストール (Anaconda Python 2.7 と Python 3.5) が組み込まれています。 外部 Python パッケージのインストールなど、Python 環境のカスタマイズは、お客様が実行する必要があります。 ここでは、HDInsight 上の Apache Spark クラスターで Python 環境を安全に管理するためのベスト プラクティスについて説明します。

## <a name="prerequisites"></a>前提条件

HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。 HDInsight に Spark クラスターがない場合は、クラスターの作成時にスクリプト アクションを実行できます。 [カスタム スクリプト アクションの使用方法](../hdinsight-hadoop-customize-cluster-linux.md)に関するドキュメントを参照してください。

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>HDInsight クラスターで使用するオープン ソース ソフトウェアのサポート

Microsoft Azure HDInsight サービスは、Apache Hadoop を中心に形成されたオープン ソース テクノロジの環境を利用します。 Microsoft Azure は、オープン ソース テクノロジの一般的なレベルのサポートを提供します。 詳細については、[Azure サポート FAQ Web サイト](https://azure.microsoft.com/support/faq/)を参照してください。 HDInsight サービスでは、組み込みのコンポーネントに対してさらに高いレベルのサポートを提供しています。

HDInsight サービスで利用できるオープン ソース コンポーネントには、2 つの種類があります。

|コンポーネント |説明 |
|---|---|
|組み込み|これらのコンポーネントは、HDInsight クラスターにプレインストールされており、クラスターの主要な機能を提供します。 たとえば、Apache Hadoop YARN リソース マネージャー、Apache Hive クエリ言語 (HiveQL)、Mahout ライブラリがこのカテゴリに属します。 クラスター コンポーネントの完全な一覧は、「[HDInsight で提供されるApache Hadoop クラスター バージョンの新機能](../hdinsight-component-versioning.md)」から入手できます。|
|Custom|クラスターのユーザーは、コミュニティで入手できるコンポーネントや自作のコンポーネントを、インストールするか、ワークロード内で使用することができます。|

> [!IMPORTANT]
> HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、 これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 Microsoft サポートによって問題が解決する場合もあれば、オープン ソース テクノロジに関する深い専門知識を入手できる場所への参加をお願いする場合もあります。 たとえば、[HDInsight に関する Microsoft Q&A 質問ページ](/answers/topics/azure-hdinsight.html)や `https://stackoverflow.com` などの多くのコミュニティ サイトを利用できます。 また、Apache プロジェクトには `https://apache.org` にプロジェクト サイトもあります。

## <a name="understand-default-python-installation"></a>Python の既定のインストールを理解する

HDInsight Spark クラスターは、Anaconda のインストール付きで作成されます。 クラスターには 2 つの Python インストールが存在します (Anaconda Python 2.7 と Python 3.5)。 次の表に、Spark、Livy、および Jupyter 向けの既定の Python 設定を示します。

|設定 |Python 2.7|Python 3.5|
|----|----|----|
|Path|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark のバージョン|既定で 2.7 に設定|構成を 3.5 に変更可能|
|Livy バージョン|既定で 2.7 に設定|構成を 3.5 に変更可能|
|Jupyter|PySpark カーネル|PySpark3 カーネル|

## <a name="safely-install-external-python-packages"></a>外部 Python パッケージを安全にインストールする

HDInsight クラスターは、組み込みの Python 環境 (Python 2.7 と Python 3.5 の両方) に依存しています。 これらの既定の組み込み環境にカスタム パッケージを直接インストールすると、予期しないライブラリ バージョンの変更が生じ、 クラスターがさらに壊れる可能性があります。 Spark アプリケーションのカスタム外部 Python パッケージを安全にインストールするには、次の手順に従います。

1. conda を使用して Python 仮想環境を作成します。 仮想環境によって、他を壊すことなく、プロジェクト用の分離された空間が用意されます。 Python 仮想環境を作成するときに、使用する Python のバージョンを指定できます。 Python 2.7 および 3.5 を使用する場合でも、仮想環境を作成する必要があります。 この要件は、クラスターの既定の環境を壊さないようにするためです。 次のスクリプトを使用して、Python 仮想環境を作成するクラスターのすべてのノードで、スクリプト アクションを実行します。

    -   `--prefix` には、conda 仮想環境を作成するパスを指定します。 ここで指定したパスに基づいて、さらに変更する必要がある構成がいくつかあります。 この例では、クラスターに py35 という名前の既存の仮想環境が既に存在するため、py35new が使用されています。
    -   `python=` には、仮想環境用の Python のバージョンを指定します。 この例では、バージョン3.5 が使用されています。これは、クラスターに組み込まれているものと同じバージョンです。 Python の他のバージョンを使用して仮想環境を作成することもできます。
    -   `anaconda` は、package_spec を anaconda として指定して、仮想環境に Anaconda パッケージをインストールします。
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. 必要に応じて、作成した仮想環境に外部の Python パッケージをインストールします。 次のスクリプトを使用して、クラスターのすべてのノードでスクリプト アクションを実行して、外部の Python パッケージをインストールします。 仮想環境フォルダーにファイルを書き込むために、ここでは sudo 特権が必要です。

    [パッケージ インデックス](https://pypi.python.org/pypi)で、利用できるすべてのパッケージを検索します。 公開されているパッケージの一覧を他のソースから入手してもかまいません。 たとえば、[conda-forge](https://conda-forge.org/feedstocks/) で使用できるパッケージをインストールできます。

    最新バージョンのライブラリをインストールする場合は、次のコマンドを使用します。

    - Conda チャネルを使用します

        -   `seaborn` は、インストールするパッケージの名前です。
        -   `-n py35new` には、先ほど作成した仮想環境の名前を指定します。 仮想環境の作成に応じて、名前を適宜変更してください。

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - または、PyPi リポジトリを使用し、`seaborn` と `py35new` を適切に変更します。
        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install seaborn
        ```

    特定のバージョンのライブラリをインストールする場合は、次のコマンドを使用します。

    - Conda チャネルを使用します

        -   `numpy=1.16.1` は、インストールするパッケージの名前とバージョンです。
        -   `-n py35new` には、先ほど作成した仮想環境の名前を指定します。 仮想環境の作成に応じて、名前を適宜変更してください。

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - または、PyPi リポジトリを使用し、`numpy==1.16.1` と `py35new` を適切に変更します。

        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install numpy==1.16.1
        ```

    仮想環境名がわからない場合は、クラスターのヘッド ノードに SSH 接続し、`/usr/bin/anaconda/bin/conda info -e` を実行して、すべての仮想環境を表示できます。

3. Spark と Livy の構成を変更して、作成した仮想環境をポイントします。

    1. Ambari UI を開き、[Spark2] ページの [Configs]\(構成\) タブに移動します。

        :::image type="content" source="./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png" alt-text="Ambari を使用して Spark と Livy の構成を変更する" border="true":::

    2. [Advanced livy2] を展開し、下のステートメントを末尾に追加します。 仮想環境を別のプレフィックスを使用してインストールした場合は、パスを適宜変更します。

        ```bash
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        :::image type="content" source="./media/apache-spark-python-package-installation/ambari-livy-config.png" alt-text="Ambari を使用して Livy 構成を変更する" border="true":::

    3. [Advanced spark2-env] を展開し、末尾にある既存の export PYSPARK_PYTHON ステートメントを置き換えます。 仮想環境を別のプレフィックスを使用してインストールした場合は、パスを適宜変更します。

        ```bash
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        :::image type="content" source="./media/apache-spark-python-package-installation/ambari-spark-config.png" alt-text="Ambari を通して Spark 構成を変更する" border="true":::

    4. 変更を保存し、影響を受けるサービスを再起動します。 これらの変更では、Spark2 サービスを再起動する必要があります。 Ambari UI によって、再起動が必要であることを示すリマインダーが表示されます。[再起動] をクリックして、影響を受けるすべてのサービスを再起動します。

        :::image type="content" source="./media/apache-spark-python-package-installation/ambari-restart-services.png" alt-text="Restart services" border="true":::

    5. Spark セッションに 2 つのプロパティを設定して、ジョブが更新された spark 構成 (`spark.yarn.appMasterEnv.PYSPARK_PYTHON` と `spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON`) を指すようにします。 

        ターミナルまたはノートブックを使用して、`spark.conf.set` 関数を使用します。

        ```spark
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        ```

        Livy を使用している場合は、次のプロパティを要求本文に追加します。

        ```
        “conf” : {
        “spark.yarn.appMasterEnv.PYSPARK_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”,
        “spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”
        }
        ```

4. 新しく作成された仮想環境を Jupyter で使用する場合は、 Jupyter の構成を変更し、Jupyter を再起動します。 次のステートメントを使用して、すべてのヘッダー ノードでスクリプト アクションを実行して、新たに作成された仮想環境に対して Jupyter をポイントします。 パスを仮想環境用に指定したプレフィックスに必ず変更してください。 このスクリプト アクションを実行した後、Ambari UI から Jupyter サービスを再起動して、この変更を使用できるようにします。

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    次のコードを実行して、Jupyter Notebook の Python 環境を再確認できます。

    :::image type="content" source="./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png" alt-text="Jupyter Notebook の Python のバージョンを確認する" border="true":::

## <a name="known-issue"></a>既知の問題

Anaconda のバージョン `4.7.11`、`4.7.12`、`4.8.0` には既知のバグがあります。 スクリプト アクションが `"Collecting package metadata (repodata.json): ...working..."` で応答を停止し、`"Python script has been killed due to timeout after waiting 3600 secs"` で失敗した場合は、 [このスクリプト](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh)をダウンロードし、すべてのノードでスクリプト アクションとして実行することで問題を解決できます。

Anaconda のバージョンを確認するには、クラスターのヘッダー ノードに SSH 接続し、`/usr/bin/anaconda/bin/conda --v` を実行します。

## <a name="next-steps"></a>次のステップ

* [概要:Azure HDInsight での Apache Spark](apache-spark-overview.md)
* [Apache Spark の Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)
