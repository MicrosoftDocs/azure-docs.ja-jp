---
title: Azure HDInsight を使用した Hive のトラブルシューティング
description: Apache Hive と Azure HDInsight の操作についてよく寄せられる質問とその回答を示します。
keywords: Azure HDInsight、Hive、よくある質問、トラブルシューティング ガイド、一般的な質問
ms.service: hdinsight
author: dharmeshkakadia
ms.author: dkakadia
ms.topic: conceptual
ms.date: 11/2/2017
ms.openlocfilehash: 91e6803e0a1302a33a3bf176ad84d0b0e0c8c5b6
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875940"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Azure HDInsight を使用した Apache Hive のトラブルシューティング

Apache Ambari で Apache Hive ペイロードを操作するときに発生する主な質問とその解決策について説明します。


## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Hive metastore をエクスポートして別のクラスターにインポートする方法


### <a name="resolution-steps"></a>解決手順

1. Secure Shell (SSH) クライアントで HDInsight クラスターに接続します。 詳細については、「[その他の情報](#additional-reading-end)」をご覧ください。

2. metastore をエクスポートする HDInsight クラスターで次のコマンドを実行します。

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   このコマンドによって、allatables.sql という名前のファイルが生成されます。

3. 新しい HDInsight クラスターに alltables.sql ファイルをコピーし、次のコマンドを実行します。

   ```apache
   hive -f alltables.sql
   ```

この解決手順のコードでは、新しいクラスターのデータ パスが元のクラスターのデータ パスと同じであると想定しています。 データ パスが異なる場合は、生成された alltables.sql ファイルを手動で編集して、変更を反映できます。

### <a name="additional-reading"></a>その他の情報

- [SSH を使用して HDInsight クラスターに接続する](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>クラスターにある Hive のログを特定する方法

### <a name="resolution-steps"></a>解決手順

1. SSH を使用して HDInsight クラスターに接続します。 詳細については、「**その他の情報**」をご覧ください。

2. Hive クライアント ログを表示するには、次のコマンドを使用します。

   ```apache
   /tmp/<username>/hive.log 
   ```

3. Hive metastore ログを表示するには、次のコマンドを使用します。

   ```apache
   /var/log/hive/hivemetastore.log 
   ```

4. Hiveserver ログを表示するには、次のコマンドを使用します。

   ```apache
   /var/log/hive/hiveserver2.log 
   ```

### <a name="additional-reading"></a>その他の情報

- [SSH を使用して HDInsight クラスターに接続する](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>クラスターで特定の構成を使って Hive シェルを起動する方法

### <a name="resolution-steps"></a>解決手順

1. Hive シェルの開始時に構成のキーと値のペアを指定します。 詳細については、「[その他の情報](#additional-reading-end)」をご覧ください。

   ```apache
   hive -hiveconf a=b 
   ```

2. 次のコマンドを使用して、Hive シェルの効果的な構成をすべて一覧表示します。

   ```apache
   hive> set;
   ```

   たとえば、デバッグ ログの記録が有効になっている Hive シェルを開始するには、コンソールで次のコマンドを使用します。

   ```apache
   hive -hiveconf hive.root.logger=ALL,console 
   ```

### <a name="additional-reading"></a>その他の情報

- [Hive 構成のプロパティ](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>クラスターのクリティカル パスで Apache Tez DAG データを分析する方法


### <a name="resolution-steps"></a>解決手順
 
1. クラスターのクリティカル パスで Apache Tez 有向非巡回グラフ (DAG) を分析するには、SSH を使用して HDInsight クラスターに接続します。 詳細については、「[その他の情報](#additional-reading-end)」をご覧ください。

2. コマンド プロンプトで、次のコマンドを実行します。
   
   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. 次のコマンドを使用して、Tez DAG の分析に使用できる他のアナライザーの一覧を表示します。

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   サンプル プログラムを最初の引数として指定する必要があります。

   有効なプログラム名は次のとおりです。
    - **ContainerReuseAnalyzer**: DAG のコンテナーの再利用の詳細を出力します。
    - **CriticalPath**: DAG のクリティカル パスを検出します。
    - **LocalityAnalyzer**: DAG の局所性の詳細を出力します。
    - **ShuffleTimeAnalyzer**: DAG のシャッフル時間の詳細を分析します。
    - **SkewAnalyzer**: DAG の傾きの詳細を分析します。
    - **SlowNodeAnalyzer**: DAG のノードの詳細を出力します。
    - **SlowTaskIdentifier**: DAG の遅いタスクの詳細を出力します。
    - **SlowestVertexAnalyzer**: DAG の最も遅い頂点の詳細を出力します。
    - **SpillAnalyzer**: DAG の書き込みの詳細を出力します。
    - **TaskConcurrencyAnalyzer**: DAG のタスク コンカレンシーの詳細を出力します。
    - **VertexLevelCriticalPathAnalyzer**: Dag の頂点レベルのクリティカル パスを検出します。


### <a name="additional-reading"></a>その他の情報

- [SSH を使用して HDInsight クラスターに接続する](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>クラスターから Tez DAG データをダウンロードする方法


#### <a name="resolution-steps"></a>解決手順

Tez DAG データは次の 2 つの方法で収集できます。

- コマンドラインから:
 
    SSH を使用して HDInsight クラスターに接続します。 コマンド プロンプトで、次のコマンドを実行します。

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
  ```

- Ambari Tez ビューを使用して:
   
  1. Ambari に移動します。 
  2. Tez ビューに移動します (右上隅のタイル アイコンの裏に隠れています)。 
  3. 表示する DAG をクリックします。
  4. **[データのダウンロード]** を選択します。

### <a name="additional-reading-end"></a>その他の情報

[SSH を使用して HDInsight クラスターに接続する](hdinsight-hadoop-linux-use-ssh-unix.md)


### <a name="see-also"></a>関連項目
[Azure HDInsight を使用したトラブルシューティング](hdinsight-troubleshoot-guide.md)




