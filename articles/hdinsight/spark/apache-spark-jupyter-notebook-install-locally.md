---
title: Jupyter をローカルにインストールして Azure HDInsight の Spark に接続する
description: Jupyter Notebook をローカル コンピューターにインストールして Apache Spark クラスターに接続する方法を説明します。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: hrasheed
ms.openlocfilehash: 7b20f0ec4669b485f87d050fcf597244fb898c85
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58091258"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Jupyter Notebook をコンピューターにインストールして HDInsight の Apache Spark に接続する

この記事では、カスタム PySpark カーネル (Python の場合)、Apache Spark カーネル (Scala の場合)、および Spark マジックと共に Jupyter Notebook をインストールし、そのノートブックを HDInsight クラスターに接続する方法について説明します。 さまざまな理由から、Jupyter をローカル コンピューターにインストールすることが必要になるケースがあります。またローカル コンピューターへのインストールには、いくつかの課題もあります。 この点について詳しくは、この記事の最後のセクション「[Jupyter をローカル コンピューターにインストールする理由](#why-should-i-install-jupyter-on-my-computer)」を参照してください。

Jupyter をインストールして HDInsight の Apache Spark に接続するには、4 つの主要手順があります。

* Spark クラスターを構成する。
* Jupyter Notebook をインストールする。
* PySpark カーネルと Spark カーネルおよび Spark マジックをインストールする。
* HDInsight 上の Spark クラスターにアクセスするように Spark マジックを構成する。

HDInsight クラスターの Jupyter Notebook で使用できるカスタム カーネルと Spark マジックの詳細については、 [HDInsight の Apache Spark Linux クラスターと Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)に関するページを参照してください。

> [!IMPORTANT]  
> この記事の手順が機能するのは、Spark バージョン 2.1.0 までのみです。

## <a name="prerequisites"></a>前提条件
ここに記載したのは、Jupyter のインストールに関する前提条件ではありません。 Jupyter Notebook のインストール後にノートブックを HDInsight クラスターに接続するための前提条件です。

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight 上の Apache Spark クラスター (バージョン 2.1.0 以下)。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。



## <a name="install-jupyter-notebook-on-your-computer"></a>Jupyter Notebook をコンピューターにインストールする

Jupyter Notebook をインストールする前に Python をインストールする必要があります。 Python と Jupyter はどちらも、[Anaconda ディストリビューション](https://www.anaconda.com/download/)に同梱されています。 Anaconda をインストールすると、Python のディストリビューションがインストールされます。 Jupyter 環境は、Anaconda のインストール後に適切なコマンドを実行して追加します。

1. ご使用のプラットフォーム用の [Anaconda インストーラー](https://www.anaconda.com/download/) をダウンロードし、セットアップ プログラムを実行します。 セットアップ ウィザードを実行する過程で、Anaconda を PATH 変数に追加するためのオプションを忘れずに選択してください。

2. 次のコマンドを実行して Jupyter をインストールします。

        conda install jupyter

    Jupyter のインストールについて詳しくは、[Anaconda を使用した Jupyter のインストール](https://jupyter.readthedocs.io/en/latest/install.html)に関するページをご覧ください。

## <a name="install-the-kernels-and-spark-magic"></a>カーネルと Spark マジックをインストールする

Spark マジック、PySpark カーネル、Spark カーネルのインストール方法については、GitHub の [sparkmagic ドキュメント](https://github.com/jupyter-incubator/sparkmagic#installation)で説明されているインストール手順を参照してください。 Spark マジックのドキュメントでは、最初の手順として Spark マジックをインストールするように求められます。 このリンク先のページにある最初の手順を、接続する HDInsight クラスターのバージョンに応じて以下のコマンドに置き換えてください。 その後、Spark マジックのドキュメントにある残りの手順を実行します。 異なるカーネルをインストールする必要がある場合は、Spark マジックのインストール方法を説明したセクションの手順 3. を実行してください。

* クラスター v3.5 と v3.6 の場合は、`pip install sparkmagic==0.11.2` を実行して sparkmagic 0.11.2 をインストールします。

* クラスター v3.4 の場合は、`pip install sparkmagic==0.2.3` を実行して sparkmagic 0.2.3 をインストールします。

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>HDInsight の Spark クラスターに接続するように Spark マジックを構成する

このセクションでは、先ほどインストールした Spark マジックに対し、Apache Spark クラスターに接続するための構成を行います。Apache Spark クラスターは、あらかじめ Azure HDInsight に作成しておく必要があります。

1. 次のコマンドを使用して Python シェルを起動します。

    ```
    python
    ```

2. Jupyter の構成情報は通常、ユーザーの home ディレクトリに格納されます。 次のコマンドを入力して home ディレクトリを識別し、そこに **.sparkmagic** という名前のフォルダーを作成します。  完全なパスが出力されます。

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. フォルダー `.sparkmagic` に **config.json** というファイルを作成し、次の JSON スニペットを追加します。  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```
4. ファイルに次の編集を行います。

    |テンプレート値 | 新しい値 |
    |---|---|
    |{USERNAME}|クラスター ログイン。既定値は admin。|
    |{CLUSTERDNSNAME}|クラスター名|
    |{BASE64ENCODEDPASSWORD}|実際のパスワードの Base64 でエンコードされたパスワード。  Base64 のパスワードは [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/) で生成できます。|
    |`"livy_server_heartbeat_timeout_seconds": 60`|`sparkmagic 0.11.23` (クラスター v3.5 と v3.6) を使用している場合、保持します。  `sparkmagic 0.2.3` (クラスター v3.4) を使用している場合は、`"should_heartbeat": true` に置き換えます。|

    完全なサンプル ファイルは [sample config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json)　で確認できます。

   > [!TIP]  
   > ハートビートが送信され、セッションがリークしないことが確認されます。 コンピューターがスリープ状態になるかシャットダウンすると、ハートビートは送信されず、セッションがクリーンアップされます。 クラスター v3.4 の場合、この動作を無効にするには、Ambari UI から Livy config `livy.server.interactive.heartbeat.timeout` を `0` に設定します。 クラスター v3.5 の場合、上記の 3.5 構成を設定しないと、セッションは削除されません。

5. Jupyter を起動します。 コマンド プロンプトから次のコマンドを使用します。

        jupyter notebook

6. カーネルで使用可能な Spark マジックを使用できることを確認します。 次の手順に従います。

    a. 新しい Notebook を作成します。 右隅から **[新規]** を選択します。 既定のカーネル **Python 2** または **Python 3** と、インストールしたカーネルが表示されるはずです。 実際の値は、インストールの選択に応じて異なる場合があります。  **PySpark** を選択します。

    ![Jupyter Notebook のカーネル](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Jupyter Notebook のカーネル")

    > [!IMPORTANT]  
    > **[新規]** を選択したら、エラーがないかシェルを確認します。  エラー `TypeError: __init__() got an unexpected keyword argument 'io_loop'` が表示される場合は、Tornado の特定のバージョンで既知の問題が発生している可能性があります。  その場合はカーネルを停止し、次のコマンドを使用して Tornado のインストールをダウン グレードします:`pip install tornado==4.5.3`。

    b. 次のコード スニペットを実行します。

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    出力結果が正しく得られた場合、HDInsight クラスターへの接続テストは完了です。

    別のクラスターに接続するようにノートブックの構成を更新する場合は、前の手順 3. を参照して、config.json を新しい値セットで更新してください。

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Jupyter をローカル コンピューターにインストールする理由

Jupyter をコンピューターにインストールしてから HDInsight 上の Apache Spark クラスターに接続することが必要になる理由はいくつか存在します。

* Azure HDInsight の Spark クラスターで Jupyter Notebook が既に使用できる状態になっていても、ご使用のコンピューターに Jupyter をインストールすることで、ノートブックをローカルで作成し、稼働中のクラスターに対してアプリケーションをテストしたうえで、クラスターにノートブックをアップロードするという選択肢が得られます。 ノートブックをクラスターにアップロードする手段としては、クラスターで実行されている Jupyter Notebook を使用してアップロードする方法と、クラスターに関連付けられているストレージ アカウントの /HdiNotebooks フォルダーにそれらを保存する方法とがあります。 クラスターにノートブックを保存する方法の詳細については、 [Jupyter Notebook の格納場所](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)に関するセクションを参照してください。
* ノートブックがローカルで利用できると、アプリケーションの要件に応じて異なる Spark クラスターに接続することができます。
* GitHub を使用してソース管理システムを導入し、ノートブックのバージョン管理を行うことができます。 複数のユーザーが同じノートブックで作業するコラボレーション環境を実現することもできます。
* クラスターをセットアップしなくてもローカルでノートブックを使用できます。 クラスターは、クラスターとの間でノートブックをテストする目的でのみ必要となります。ノートブックや開発環境を手動で管理するうえでクラスターは必要ありません。
* 独自のローカル開発環境の方が、クラスター上の Jupyter 環境よりも構成しやすい場合があります。  リモートのクラスターを構成しなくても、ローカルにインストールされているすべてのソフトウェアを有効活用することができます。

> [!WARNING]  
> Jupyter がローカル コンピューターにインストールされている場合、同じ Spark クラスター上で複数のユーザーが同時に同じノートブックを実行できます。 そのような状況では、複数の Livy セッションが作成されます。 問題が発生してデバッグが必要となった場合、それは Livy セッションとユーザーの対応関係を追跡する複雑な作業となります。  

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
* [HDInsight 用の Apache Spark クラスター内の Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)
