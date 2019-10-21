---
title: Apache Hive と Data Lake Tools for Visual Studio - Azure HDInsight
description: Data Lake Tools for Visual Studio を使って Azure HDInsight 上の Apache Hadoop で Apache Hive クエリを実行する方法について説明します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 1e5e3854f0b132ede38e182f99435a569c04d49e
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076280"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Data Lake Tools for Visual Studio を使用して Apache Hive クエリを実行する

Data Lake Tools for Visual Studio を使って Apache Hive のクエリを実行する方法について説明します。 Data Lake Tools を使うと、Azure HDInsight 上の Apache Hadoop に対する Hive クエリを簡単に作成、送信、および監視できます。

## <a id="prereq"></a>前提条件

* HDInsight の Apache Hadoop クラスター。 [Linux での HDInsight の概要](./apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。

* 下記のいずれかのバージョンの Visual Studio

    * Visual Studio 2015、2017 (任意のエディション)
    * Visual Studio 2013 Community/Professional/Premium/Ultimate の Update 4

* HDInsight Tools for Visual Studio または Azure Data Lake Tools for Visual Studio ツールのインストールおよび構成については、 [HDInsight Hadoop Tools for Visual Studio の使用開始](apache-hadoop-visual-studio-tools-get-started.md) に関するページをご覧ください。

## <a id="run"></a>Visual Studio を使用して Apache Hive クエリを実行する

Hive クエリを作成して実行するためのオプションは 2 つあります。

* アドホック クエリを作成する
* Hive アプリケーションを作成する

### <a name="ad-hoc"></a>アドホック

アドホック クエリは **[バッチ]** または **[対話型]** モードで実行できます。

1. **Visual Studio** を開きます。

2. **サーバー エクスプローラー**から **[Azure]**  >  **[HDInsight]** に移動します。

3. **[HDInsight]** を展開し、クエリを実行するクラスターを右クリックして、 **[Hive クエリの作成]** を選択します。

4. 次の Hive クエリを入力します。

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. **[実行]** を選択します。 実行モードは既定で **[対話型]** であることに注意してください。

    ![Interactive Hive クエリ実行のスクリーンショット](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. 同じクエリを **[バッチ]** モードで実行するには、ドロップダウン リストを **[対話型]** から **[バッチ]** に切り替えます。 実行ボタンが **[実行]** から **[送信]** に変わることに注意してください。

    ![Hive クエリの送信のスクリーンショット](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    Hive エディターは IntelliSense をサポートしています。 Data Lake Tools for Visual Studio では、Hive スクリプトの編集時にリモート メタデータの読み込みをサポートします。 たとえば、`SELECT * FROM` と入力すると、IntelliSense によってテーブル名の候補が一覧表示されます。 テーブル名を指定すると、Intellisense によって列名が一覧表示されます。 このツールは、Hive の DML ステートメント、サブクエリ、および組み込みの UDF の大半をサポートします。 IntelliSense は、HDInsight のツール バーで選択されているクラスターのメタデータのみを推奨します。

    ![HDInsight Visual Studio Tools での IntelliSense の例 1 のスクリーンショット](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![HDInsight Visual Studio Tools での IntelliSense の例 2 のスクリーンショット](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. **[送信]** または **[Submit (Advanced)]\(送信 (詳細設定)\)** を選択します。

   [Submit (Advanced)]\(送信 (詳細設定)\) オプションを選択した場合は、スクリプトの **[ジョブ名]** 、 **[引数]** 、 **[追加の構成]** 、 **[状態ディレクトリ]** を構成します。

    ![HDInsight Hadoop の Hive クエリのスクリーンショット](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png "クエリの送信")

### <a name="hive-application"></a>Hive アプリケーション

1. **Visual Studio** を開きます。

2. メニュー バーから、 **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** に移動します。

3. **[新規プロジェクト]** ウィンドウから、 **[テンプレート]**  >  **[Azure Data Lake]**  >  **[HIVE (HDInsight)]**  >  **[Hive アプリケーション]** に移動します。 

4. このプロジェクトの名前を入力して、 **[OK]** を選択します。

5. このプロジェクトで作成した **Script.hql** ファイルを開き、次の HiveQL ステートメントを貼り付けます。

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

6. ツール バーで、このクエリに使用する **HDInsight クラスター**を選択します。 **[送信]** を選択して、ステートメントを Hive ジョブとして実行します。

   ![Azure HDInsight ツール バーの送信](./media/apache-hadoop-use-hive-visual-studio/hdinsight-toolbar-submit.png)

7. **[Hive ジョブの概要]** に実行しているジョブに関する情報が表示されます。 **[更新]** リンクを使用して、 **[ジョブのステータス]** が **[完了]** に変更されるまで、ジョブの情報を更新します。

   ![完了したジョブが表示されたジョブの概要](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

8. **[ジョブ出力]** リンクを使用して、このジョブの出力を表示します。 このクエリによって返された値である `[ERROR] 3` が表示されます。

### <a name="additional-example"></a>その他の例

この例は、前の手順で作成された `log4jLogs` テーブルに依存しています。

1. **サーバー エクスプローラー**から、クラスターを右クリックして **[Hive クエリの作成]** を選択します。

2. 次の Hive クエリを入力します。

    ```hql
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

3. **[バッチ]** モードでクエリを実行します。

4. ジョブによってテーブルが作成されたことを確認するには、**サーバー エクスプローラー**で、 **[Azure]**  >  **[HDInsight]** > 対象の HDInsight クラスター > **[Hive データベース]**  >  **[既定]** の順に展開します。 **errorLogs** テーブルと **log4jLogs** テーブルが表示されます。

## <a id="nextsteps"></a>次のステップ

おわかりのように、Visual Studio の HDInsight ツールを使用すると、HDInsight で Hive クエリを簡単に操作できます。

HDInsight での Hive に関する全般的な情報

* [HDInsight 上の Apache Hadoop で Apache Hive を使用する](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight 上の Apache Hadoop で Apache Pig を使用する](hdinsight-use-pig.md)

* [HDInsight 上の Apache Hadoop で MapReduce を使用する](hdinsight-use-mapreduce.md)

Visual Studio の HDInsight ツールに関する詳細情報:

* [Visual Studio の HDInsight ツールの概要](apache-hadoop-visual-studio-tools-get-started.md)