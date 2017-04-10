---
title: "HDInsight のクエリ コンソールでの Hadoop Hive の使用 | Microsoft Docs"
description: "Web ベースのクエリ コンソールを使用して、お使いのブラウザーから HDInsight Hadoop クラスターで Hive クエリを実行する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5ae074b0-f55e-472d-94a7-005b0e79f779
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: 0c7f570db388b0ed96479e994a4a2f79e7919b17
ms.lasthandoff: 01/18/2017


---
# <a name="run-hive-queries-using-the-query-console"></a>クエリ コンソールを使用して Hive クエリを実行
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

この記事では、HDInsight クエリ コンソールを使用して、お使いのブラウザーから HDInsight Hadoop クラスターで Hive クエリを実行する方法について説明します。

> [!IMPORTANT]
> HDInsight クエリ コンソールは、Windows ベースの HDInsight クラスターでのみ使用できます。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。
>
> HDInsight 3.4 以上での Web ブラウザーからの Hive クエリの実行に関する情報については、[Ambari Hive ビューでの Hive クエリの実行](hdinsight-hadoop-use-hive-ambari-view.md)に関するページを参照してください。

## <a id="prereq"></a>前提条件
この記事の手順を完了するには、次のものが必要です。

* Windows ベースの HDInsight Hadoop クラスター
* 最新の Web ブラウザー

## <a id="run"></a> クエリ コンソールを使用して Hive クエリを実行
1. Web ブラウザーを開いて **https://CLUSTERNAME.azurehdinsight.net** に移動します。**CLUSTERNAME** は実際の HDInsight クラスターの名前です。 プロンプトが表示されたら、クラスターの作成時に使用したユーザー名とパスワードを入力します。
2. ページ上部のリンクから、 **[Hive エディター]**を選択します。 HDInsight クラスターで実行する HiveQL ステートメントの入力に使用できるフォームが表示されます。
   
    ![Hive エディター](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)
   
    テキスト `Select * from hivesampletable` を次の HiveQL ステートメントに置き換えます。
   
        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   
    これらのステートメントは次のアクションを実行します。
   
   * **DROP TABLE**: テーブルが既存の場合にテーブルとデータ ファイルを削除します。
   * **CREATE EXTERNAL TABLE**: Hive に新しく '外部' テーブルを作成します。 外部テーブルは、Hive にテーブル定義のみを格納し、データは、元の場所に残します。
     
     > [!NOTE]
     > 基盤となるデータを外部ソースによって更新する (データの自動アップロード処理など) 場合や別の MapReduce 操作によって更新する場合に、Hive クエリで最新のデータを使用する場合は、外部テーブルを使用する必要があります。
     > 
     > 外部テーブルを削除しても、データは削除**されません**。テーブル定義のみが削除されます。
     > 
     > 
   * **ROW FORMAT**: Hive にデータの形式を示します。 ここでは、各ログのフィールドは、スペースで区切られています。
   * **STORED AS TEXTFILE LOCATION**: Hive に、データの格納先 (example/data directory) と、データはテキストとして格納されていることを示します。
   * **SELECT**: **t4** 列の値が **[ERROR]** であるすべての行の数を指定します。 ここでは、この値を含む行が&3; 行あるため、 **3** という値が返されています。
   * **INPUT__FILE__NAME LIKE '%.log'** - Hive に .log で終わるファイルのデータのみを返す必要があることを示します。 これにより、検索はデータを含む sample.log ファイルに制限され、定義したスキーマに一致しない他のサンプル データ ファイルのデータを返すことができなくなります。
3. **[Submit]**をクリックします。 ページ下部の **[ジョブ セッション]** にジョブの詳細が表示されます。
4. **[ステータス]** フィールドが **Completed** に変わったら、ジョブの **[詳細の表示]** を選択します。 詳細ページの **[ジョブ出力]** に `[ERROR]    3` が含まれます。 このフィールドの下にある **[ダウンロード]** ボタンを使用して、ジョブの出力を含むファイルをダウンロードします。

## <a id="summary"></a>概要
このように、クエリ コンソールを使用すると、HDInsight クラスターで簡単に Hive クエリを実行し、ジョブ ステータスを監視し、出力を取得できます。

Hive クエリ コンソールを使用した Hive ジョブの実行の詳細については、クエリ コンソールの上部にある **[概要]** を選択し、サンプルを使用します。 各サンプルでは順を追って Hive を使用してデータを分析します。また、サンプルで使用されている HiveQL ステートメントについての説明も含まれています。

## <a id="nextsteps"></a>次のステップ
HDInsight での Hive に関する全般的な情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

Hive で Tez を使用する場合、デバッグ情報については、次のドキュメントを参照してください。

* [Windows ベースの HDInsight で Tez UI を使用して Tez ジョブをデバッグする](hdinsight-debug-tez-ui.md)
* [HDInsight で Ambari ビューを使用して Tez ジョブをデバッグする](hdinsight-debug-ambari-tez-view.md)

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

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



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png

