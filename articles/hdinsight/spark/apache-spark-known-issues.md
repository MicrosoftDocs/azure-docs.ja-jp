---
title: Azure HDInsight における Apache Spark クラスターの問題のトラブルシューティング
description: Azure HDInsight における Apache Spark のクラスターに関連する問題と、それらを解決する方法について説明します。
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: c92b55d3ac7f4476b7b74d25b40150a74c6ea1cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930148"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>HDInsight における Apache Spark クラスターの既知の問題

このドキュメントでは、HDInsight Spark パブリック プレビューのすべての既知の問題を追跡します。  

## <a name="apache-livy-leaks-interactive-session"></a>Apache Livy が対話型セッションをリークする
対話型セッションがまだ有効な状態で [Apache Livy](https://livy.incubator.apache.org/) が再起動されると ([Apache Ambari](https://ambari.apache.org/) から、またはヘッドノード 0 仮想マシンの再起動のため)、対話型ジョブ セッションがリークされます。 その結果、新しいジョブは、承諾済み状態で停止されます。

**対応策:**

この問題を回避するには、以下の手順を実行します。

1. ヘッドノードに Ssh します。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. 次のコマンドを実行して、Livy を通じて開始された対話型ジョブのアプリケーション ID を調べます。

   ```bash
   yarn application –list
   ```

    明示的な名前が指定されていない Livy 対話型セッションでジョブが開始された場合、既定のジョブ名は Livy になります。 [Jupyter Notebook](https://jupyter.org/) によって開始された Livy セッションの場合、ジョブ名は `remotesparkmagics_*` で始まります。

3. 次のコマンドを実行して、これらのジョブを強制終了します。

   ```bash
   yarn application –kill <Application ID>
   ```

新しいジョブの実行が開始されます。

## <a name="spark-history-server-not-started"></a>Spark History Server が開始されない
クラスターの作成後、Spark History Server は自動的には開始されません。  

**対応策:**

Ambari から履歴サーバーを手動で開始します。

## <a name="permission-issue-in-spark-log-directory"></a>Spark ログ ディレクトリでアクセス許可の問題が発生する
spark-submit を使用してジョブを送信するとき、hdiuser は次のエラーを取得します。

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```

また、ドライバー ログは書き込まれません。

**対応策:**

1. hdiuser を Hadoop グループに追加します。
2. クラスターの作成後、/var/log/spark に 777 アクセス許可を指定します。
3. Ambari を使用して、Spark ログの場所を、777 アクセス許可を持つディレクトリに変更します。  
4. sudo として spark-submit を実行します。  

## <a name="spark-phoenix-connector-is-not-supported"></a>Spark-Phoenix コネクタがサポートされない

HDInsight Spark クラスターは、Spark-Phoenix コネクターをサポートしていません。

**対応策:**

代わりに、Spark-HBase コネクタを使用する必要があります。 手順については、[Spark-HBase コネクタの使用方法](https://web.archive.org/web/20190112153146/https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/)に関するページをご覧ください。

## <a name="issues-related-to-jupyter-notebooks"></a>Jupyter Notebook に関連する問題

Jupyter Notebook に関連する既知の問題を以下に示します。

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>ファイル名での非 ASCII 文字の使用

Jupyter Notebook のファイル名に ASCII 以外の文字を使用しないでください。 ファイル名に ASCII 以外を含むファイルのアップロードを Jupyter UI 経由で試行すると、エラー メッセージが表示されないまま、エラーになります。 Jupyter を使ってファイルをアップロードすることはできませんが、表示可能なエラーもスローされません。

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>大きなサイズの Notebook の読み込み中のエラー

大きなサイズの Notebook の読み込み中にエラー **`Error loading notebook`** が発生する場合があります。  

**対応策:**

このエラーが発生した場合、データが壊れたり失われたりしているわけではありません。  Notebook はディスク上の `/var/lib/jupyter`に残っているため、クラスターに SSH 接続し、Notebook にアクセスすることができます。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

SSH を使用してクラスターに接続したら、バックアップとしてクラスターの Notebook をローカル コンピューター (SCP または WinSCP を使用) にコピーすることで、Notebook 内の重要なデータが失われるのを防ぐことができます。 ポート 8001 のヘッドノードへの SSH トンネルを使用すると、ゲートウェイを経由せずに Jupyter にアクセスできます。  そこでは、Notebook の出力をクリアしてからもう一度保存して、Notebook のサイズを最小限に縮小できます。

今後このエラーが発生しないようにするには、次のベスト プラクティスを実行する必要があります。

* Notebook のサイズを小さく保つことが重要です。 Jupyter に返送される、Spark ジョブからの出力は Notebook に保持されます。  Jupyter でのベスト プラクティスは一般に次のとおりです。大規模な RDD またはデータフレームに対して `.collect()` を実行することは避けます。RDD の内容を参照する場合は、出力が大きくなりすぎないように `.take()` または `.sample()` の実行を検討します。
* また、Notebook を保存する場合は、出力セルをすべてクリアしてサイズを縮小します。

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Notebook の初期スタートアップに予想より時間がかかる

Spark マジックを使用した Jupyter Notebook の最初のコード ステートメントには、1 分以上かかる場合があります。  

**説明:**

これは、最初のコード セルが実行されるタイミングのために発生します。 バック グラウンドで、これにより、セッション構成が開始され、Spark、SQL、および Hive コンテキストが設定されます。 これらのコンテキストが設定された後に、最初のステートメントが実行されるので、ステートメントの完了までに時間がかかるような印象を受けます。

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Jupyter Notebook がセッションの作成中にタイムアウトする

Spark クラスターがリソース不足になると、Jupyter Notebook の Spark カーネルと PySpark カーネルは、セッションを作成する試行をタイムアウトにします。

**対応策:**

1. 以下の方法で、Spark クラスターのリソースを解放します。

   * [閉じて停止] メニューに移動するか、Notebook エクスプローラーで [シャットダウン] をクリックして、他の Spark Notebook を停止する。
   * YARN から他の Spark アプリケーションを停止する。

2. スタートアップしようとしていた Notebook を再起動します。 今度は、セッションを作成するために十分なリソースが使用可能になっている必要があります。

## <a name="see-also"></a>関連項目

* [概要: Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ

* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning: HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
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
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理

* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)
