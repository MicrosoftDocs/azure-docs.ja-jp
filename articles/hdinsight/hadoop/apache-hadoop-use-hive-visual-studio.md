---
title: Apache Hive と Data Lake (Apache Hadoop) tools for Visual Studio - Azure HDInsight
description: Data Lake Tools for Visual Studio を使って Azure HDInsight 上の Apache Hadoop で Apache Hive クエリを実行する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 1412e644a0c824be3612e14a1059661092805682
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447423"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Data Lake Tools for Visual Studio を使用して Apache Hive クエリを実行する

Data Lake Tools for Visual Studio を使って Apache Hive のクエリを実行する方法について説明します。 Data Lake Tools を使うと、Azure HDInsight 上の Apache Hadoop に対する Hive クエリを簡単に作成、送信、および監視できます。

## <a id="prereq"></a>前提条件

* Azure HDInsight (HDInsight 上の Apache Hadoop) クラスター

  > [!IMPORTANT]  
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

* 下記のいずれかのバージョンの Visual Studio

    * Visual Studio 2013 Community/Professional/Premium/Ultimate の Update 4

    * Visual Studio 2015 (任意のエディション)

    * Visual Studio 2017 (任意のエディション)

* HDInsight Tools for Visual Studio または Azure Data Lake Tools for Visual Studio ツールのインストールおよび構成については、 [HDInsight Hadoop Tools for Visual Studio の使用開始](apache-hadoop-visual-studio-tools-get-started.md) に関するページをご覧ください。

## <a id="run"></a>Visual Studio を使用して Apache Hive クエリを実行する

1. **Visual Studio** を開き、**[新規]** > 、**[プロジェクト]** > 、**[Azure Data Lake]** > 、**[Hive]** > 、**[Hive アプリケーション]** の順に選択します。 プロジェクトの名前を指定します。

2. このプロジェクトで作成した **Script.hql** ファイルを開き、次の HiveQL ステートメントを貼り付けます。

   ```hiveql
   set hive.execution.engine=tez;
   DROP TABLE log4jLogs;
   CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
   STORED AS TEXTFILE LOCATION '/example/data/';
   SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   ```

    これらのステートメントは次のアクションを実行します。

   * `DROP TABLE`:テーブルが存在する場合、そのテーブルを削除します。

   * `CREATE EXTERNAL TABLE`:新しい "外部" テーブルを Hive に作成します。 外部テーブルは、Hive にテーブル定義のみを格納し、データは、元の場所に残します。

     > [!NOTE]  
     > 基になるデータが外部ソースによって更新されると考えられる場合は、外部テーブルを使用する必要があります。 たとえば、MapReduce ジョブや Azure サービスなどです。
     >
     > 外部テーブルを削除しても、データは削除**されません**。テーブル定義のみが削除されます。

   * `ROW FORMAT`:データの形式を Hive に伝えます。 ここでは、各ログのフィールドは、スペースで区切られています。

   * `STORED AS TEXTFILE LOCATION`:データの格納先 (example/data ディレクトリ) と、データがテキストとして格納されていることを Hive に伝えます。

   * `SELECT`:`t4` 列の値が `[ERROR]` であるすべての行の数を選択します。 この値を含む行が 3 行あるため、このステートメントでは値 `3` が返されます。

   * `INPUT__FILE__NAME LIKE '%.log'`: .log で終わるファイルのデータだけを返す必要があることを Hive に伝えます。 この句により、データを含む sample.log ファイルに検索が制限されます。

3. ツール バーで、このクエリに使用する **HDInsight クラスター**を選択します。 **[送信]** を選択して、ステートメントを Hive ジョブとして実行します。

   ![[送信] バー](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

4. **[Hive ジョブの概要]** に実行しているジョブに関する情報が表示されます。 **[更新]** リンクを使用して、**[ジョブのステータス]** が **[完了]** に変更されるまで、ジョブの情報を更新します。

   ![完了したジョブが表示されたジョブの概要](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

5. **[ジョブ出力]** リンクを使用して、このジョブの出力を表示します。 このクエリによって返された値である `[ERROR] 3` が表示されます。

6. プロジェクトを作成せずに Hive クエリを実行することもできます。 **サーバー エクスプローラー**を使用して **[Azure]** > **[HDInsight]** の順に展開し、HDInsight サーバーを右クリックして、**[Hive クエリを記述]** を選択します。

7. 表示された **temp.hql** ドキュメントに次の HiveQL ステートメントを追加します。

   ```hiveql
   set hive.execution.engine=tez;
   CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
   INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   ```

    これらのステートメントは次のアクションを実行します。

   * `CREATE TABLE IF NOT EXISTS`:テーブルがまだ存在しない場合に、テーブルを作成します。 `EXTERNAL` キーワードが使用されていないため、このステートメントでは内部テーブルが作成されます。 内部テーブルは Hive データ ウェアハウスに格納され、Hive によって管理されます。

     > [!NOTE]  
     > `EXTERNAL` テーブルとは異なり、内部テーブルを削除すると、基になるデータも削除されます。

   * `STORED AS ORC`:Optimized Row Columnar (ORC) 形式でデータを格納します。 ORC は、Hive データを格納するための高度に最適化された効率的な形式です。

   * `INSERT OVERWRITE ... SELECT`:`[ERROR]` を含む `log4jLogs` テーブルの列を選択し、データを `errorLogs` テーブルに挿入します。

8. ツール バーで **[送信]** を選択し、ジョブを実行します。 **[ジョブ ステータス]** で、ジョブが正常に完了したことを確認します。

9. ジョブによってテーブルが作成されたことを確認するには、**サーバー エクスプローラー**で、**[Azure]** > **[HDInsight]** > 対象の HDInsight クラスター > **[Hive データベース]** > **[既定]** の順に展開します。 **errorLogs** テーブルと **log4jLogs** テーブルが表示されます。

## <a id="nextsteps"></a>次のステップ

おわかりのように、Visual Studio の HDInsight ツールを使用すると、HDInsight で Hive クエリを簡単に操作できます。

HDInsight での Hive に関する全般的な情報

* [HDInsight 上の Apache Hadoop で Apache Hive を使用する](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight 上の Apache Hadoop で Apache Pig を使用する](hdinsight-use-pig.md)

* [HDInsight 上の Apache Hadoop で MapReduce を使用する](hdinsight-use-mapreduce.md)

Visual Studio の HDInsight ツールに関する詳細情報:

* [Visual Studio の HDInsight ツールの概要](apache-hadoop-visual-studio-tools-get-started.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
