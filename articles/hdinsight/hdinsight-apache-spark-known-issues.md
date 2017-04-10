---
title: "Azure HDInsight における Apache Spark クラスターの既知の問題 | Microsoft Docs"
description: "Azure HDInsight における Apache Spark クラスターの既知の問題"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 610c4103-ffc8-4ec0-ad06-fdaf3c4d7c10
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 2ba5f280b38622b6a0c966d76617cd5698420b92
ms.lasthandoff: 03/25/2017


---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>HDInsight における Apache Spark クラスターの既知の問題

このドキュメントでは、HDInsight Spark パブリック プレビューのすべての既知の問題を追跡します。  

## <a name="livy-leaks-interactive-session"></a>Livy が対話型セッションをリークする
対話型セッションがまだ有効な状態で Livy が再起動されると (Ambari から、またはヘッドノード 0 仮想マシンの再起動のため)、対話型ジョブ セッションがリークされます。 このため、新しいジョブは受け付け済み状態のままになり、起動できません。

**対応策:**

この問題を回避するには、以下の手順を実行します。

1. ヘッドノードに Ssh します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. 次のコマンドを実行して、Livy を通じて開始された対話型ジョブのアプリケーション ID を調べます。 
   
        yarn application –list
   
    明示的に名前を指定せずに、Livy 対話型セッションによってジョブが開始された場合、既定のジョブ名は Livy です。Jupyter Notebook によって開始された Livy セッションでは、ジョブ名は remotesparkmagics_* で始まります。 
3. 次のコマンドを実行して、これらのジョブを強制終了します。 
   
        yarn application –kill <Application ID>

新しいジョブの実行が開始されます。 

## <a name="spark-history-server-not-started"></a>Spark History Server が開始されない
クラスターの作成後、Spark History Server は自動的には開始されません。  

**対応策:** 

Ambari から履歴サーバーを手動で開始します。

## <a name="permission-issue-in-spark-log-directory"></a>Spark ログ ディレクトリでアクセス許可の問題が発生する
hdiuser が spark-submit でジョブを送信すると、java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (アクセス許可が拒否されました) というエラーになり、ドライバー ログは書き込まれません。 

**対応策:**

1. hdiuser を Hadoop グループに追加します。 
2. クラスターの作成後、/var/log/spark に 777 アクセス許可を指定します。 
3. Ambari を使用して、Spark ログの場所を、777 アクセス許可を持つディレクトリに変更します。  
4. sudo として spark-submit を実行します。  

## <a name="spark-phoenix-connector-is-not-supported"></a>Spark-Phoenix コネクタがサポートされない

現時点では、Spark-Phoenix コネクタは HDInsight Spark クラスターではサポートされません。

**対応策:**

代わりに、Spark-HBase コネクタを使用する必要があります。 手順については、[Spark-HBase コネクタの使用方法](https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/)に関するページを参照してください。

## <a name="issues-related-to-jupyter-notebooks"></a>Jupyter Notebook に関連する問題
Jupyter Notebook に関連する既知の問題を以下に示します。

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>ファイル名での非 ASCII 文字の使用
Spark HDInsight クラスターで使用できる Jupyter Notebook では、ファイル名に非 ASCII 文字を使用することはできません。 Jupyter UI を使用して、ファイル名に非 ASCII 文字が含まれたファイルをアップロードしようとすると、通知されずに失敗します (つまり、Jupyter でファイルのアップロード操作ができないわけではありませんが、明確なエラーがスローされるわけでもありません)。 

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>大きなサイズの Notebook の読み込み中のエラー
大きなサイズの Notebook の読み込み中にエラー **`Error loading notebook`** が発生する場合があります。  

**対応策:**

このエラーが発生した場合、データが壊れたり失われたりしているわけではありません。  Notebook はディスク上の `/var/lib/jupyter`に残っているため、クラスターに SSH 接続し、Notebook にアクセスすることができます。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

SSH を使用してクラスターに接続したら、バックアップとしてクラスターの Notebook をローカル コンピューター (SCP または WinSCP を使用) にコピーすることで、Notebook 内の重要なデータが失われるのを防ぐことができます。 ポート 8001 のヘッドノードへの SSH トンネルを使用すると、ゲートウェイを経由せずに Jupyter にアクセスできます。  そこでは、Notebook の出力をクリアしてから再度保存して、Notebook のサイズを最小限に縮小できます。

今後このエラーが発生しないようにするには、次のベスト プラクティスを実行する必要があります。

* Notebook のサイズを小さく保つことが重要です。 Jupyter に返送される、Spark ジョブからの出力は Notebook に保持されます。  Jupyter でのベスト プラクティスは一般に次のとおりです。大規模な RDD またはデータフレームに対して `.collect()` を実行することは避けます。RDD の内容を参照する場合は、出力が大きくなり過ぎないように `.take()` または `.sample()` の実行を検討します。
* また、Notebook を保存する場合は、出力セルをすべてクリアしてサイズを縮小します。

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Notebook の初期スタートアップに予想より時間がかかる
Spark マジックを使用した Jupyter Notebook の最初のコード ステートメントには、1 分以上かかる場合があります。  

**説明:**

これは、最初のコード セルが実行されるタイミングのために発生します。 バック グラウンドで、これにより、セッション構成が開始され、Spark、SQL、および Hive コンテキストが設定されます。 これらのコンテキストが設定された後に、最初のステートメントが実行されるので、ステートメントの完了までに時間がかかるような印象を受けます。

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Jupyter Notebook がセッションの作成中にタイムアウトする
Spark クラスターがリソース不足になると、Jupyter Notebook の Spark カーネルと Pyspark カーネルは、セッションを作成する試行をタイムアウトにします。 

**対応策:** 

1. 以下の方法で、Spark クラスターのリソースを解放します。
   
   * [閉じて停止] メニューに移動するか、Notebook エクスプローラーで [シャットダウン] をクリックして、他の Spark Notebook を停止する。
   * YARN から他の Spark アプリケーションを停止する。
2. スタートアップしようとしていた Notebook を再起動します。 今度は、セッションを作成するために十分なリソースが使用可能になっている必要があります。

## <a name="see-also"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)


