---
title: "HDInsight (Hadoop) での Hive シェルの使用 | Microsoft Docs"
description: "Linux ベースの HDInsight クラスターで Hive シェルを使用する方法について説明します。 SSh を使用して HDInsight クラスターに接続してから、Hive シェルを使用して対話的にクエリを実行する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0e919171-03e6-4f5a-ab4e-3eec3e34c347
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 237b78f723fe6dbb800f9e0be2314c7c8a5c668e
ms.lasthandoff: 03/25/2017


---
# <a name="use-hive-with-hadoop-in-hdinsight-with-ssh"></a>SSH による HDInsight での Hive と Hadoop の使用
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

この記事では、Secure Shell (SSH) を使用して Azure HDInsight クラスターで Hadoop に接続してから、Hive コマンド ライン インターフェイス (CLI) を使用して Hive クエリを対話的に実行する方法について説明します。

> [!IMPORTANT]
> Hive コマンドは HDInsight クラスターで利用できますが、Beeline の使用を検討してください。 Beeline は Hive を使用するための最新クライアントであり、HDInsight クラスターに付属します。 使用方法に関する詳細については、「 [Beeline による HDInsight での Hive と Hadoop の使用](hdinsight-hadoop-use-hive-beeline.md)」を参照してください。


## <a id="prereq"></a>前提条件
この記事の手順を完了するには、次のものが必要です。

* HDInsight クラスターでの Linux ベースの Hadoop

  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* SSH クライアント SSH クライアントを備えた Linux、Unix、および Mac OS Windows ユーザーは [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) などのクライアントをダウンロードする必要があります。

## <a id="ssh"></a>SSH を使用した接続
SSH コマンドを使用して、HDInsight クラスターの完全修飾ドメイン名 (FQDN) に接続します。 FQDN はクラスターに指定した名前で、その後、 **.azurehdinsight.net**が続きます。 以下の例では、 **myhdinsight**という名前のクラスターに接続します。

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**HDInsight クラスターの作成時に SSH 認証に証明書キーを指定した場合は** 、クライアント システムの秘密キーの場所を指定する必要があることがあります。

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**HDInsight クラスターの作成時に SSH 認証のパスワードを指定した場合は** 、パスワードの入力を求められます。

SSH の使用方法の詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。

## <a id="hive"></a>Hive コマンドの使用
1. 接続したら、次のコマンドを使用して Hive CLI を起動します。
   
        hive
2. CLI を使用して次のステートメントを入力し、サンプル データを使用して **log4jLogs** という名前の新しいテーブルを作成します。
   
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   
    これらのステートメントは次のアクションを実行します。
   
   * **DROP TABLE** - テーブルが既存の場合にテーブルとデータ ファイルを削除します。
   * **CREATE EXTERNAL TABLE** : Hive に新しく '外部' テーブルを作成します。 外部テーブルは Hive にテーブル定義のみを格納します。 データは元の場所に残されます。
   * **ROW FORMAT** - Hive にデータの形式を示します。 ここでは、各ログのフィールドは、スペースで区切られています。
   * **STORED AS TEXTFILE LOCATION** - Hive に、データの格納先 (example/data ディレクトリ) と、データはテキストとして格納されていることを示します。
   * **SELECT** - **t4** 列の値が **[ERROR]** であるすべての行の数を指定します。 ここでは、この値を含む行が 3 行あるため、 **3** という値が返されています。
   * **INPUT__FILE__NAME LIKE '%.log'** - Hive に .log で終わるファイルのデータのみを返す必要があることを示します。 これにより、検索はデータを含む sample.log ファイルに制限され、定義したスキーマに一致しない他のサンプル データ ファイルのデータを返すことができなくなります。
     
     > [!NOTE]
     > 基盤となるデータを外部ソースによって更新する (データの自動アップロード処理など) 場合や別の MapReduce 操作によって更新する場合に、Hive クエリで最新のデータを使用する場合は、外部テーブルを使用する必要があります。
     > 
     > 外部テーブルを削除しても、データは削除**されません**。テーブル定義のみが削除されます。
     > 
     > 
3. 次のステートメントを使用して、 **errorLogs**という名前の新しい "内部" テーブルを作成します。
   
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   
    これらのステートメントは次のアクションを実行します。
   
   * **CREATE TABLE IF NOT EXISTS** - 既存のテーブルがない場合、テーブルを作成します。 **EXTERNAL** キーワードが使用されていないため、これは内部テーブルであり、Hive のデータ保管先に格納され、完全に Hive によって管理されます。
   * **STORED AS ORC** - Optimized Row Columnar (ORC) 形式でデータを格納します。 この形式は、Hive にデータを格納するための、非常に効率的で適切な形式です。
   * **INSERT OVERWRITE ...SELECT** - **[ERROR]** を含む **log4jLogs** テーブルの列を選択し、**errorLogs** テーブルにデータを挿入します。
     
     **errorLogs** テーブルには t4 列に **[ERROR]** を含む行のみが格納されていることを確認するには、次のステートメントを使用して、**errorLogs** 列からすべての列を返します。
     
       SELECT * from errorLogs;
     
     3 つのデータ行が返され、各行の t4 列には **[ERROR]** が含まれます。
     
     > [!NOTE]
     > 外部テーブルとは異なり、内部テーブルを削除すると、基盤となるデータも削除されます。
     > 
     > 

## <a id="summary"></a>概要
このように、Hive コマンドを使用すると、HDInsight クラスターで簡単に対話的に Hive クエリを実行し、ジョブ ステータスを監視し、出力を取得できます。

## <a id="nextsteps"></a>次のステップ
HDInsight での Hive に関する全般的な情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

Hive で Tez を使用する場合、デバッグ情報については、次のドキュメントを参照してください。

* [Windows ベースの HDInsight で Tez UI を使用して Tez ジョブをデバッグする](hdinsight-debug-tez-ui.md)
* [HDInsight で Ambari ビューを使用して Tez ジョブをデバッグする](hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md



[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png


