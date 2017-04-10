---
title: "Hortonworks サンドボックスでの Azure Data Lake Tools for Visual Studio | Microsoft Docs"
description: "(ローカル VM で実行される) Hortonworks サンドボックスで Azure Data Lake Tools for Visual Studio を使用する方法について説明します。このツールを使用すると、サンドボックスで Hive ジョブと Pig ジョブを作成して実行し、ジョブの出力と履歴を表示できます。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e3434c45-95d1-4b96-ad4c-fb59870e2ff0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 599aeb1f38c804c2edf6590140e739c9705ab1ab
ms.lasthandoff: 03/07/2017


---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Hortonworks サンドボックスで Azure Data Lake Tools for Visual Studio を使用する

Azure Data Lake Tools for Visual Studio には、Azure Data Lake と HDInsight を操作するためのツールに加えて、汎用的な Hadoop クラスターを操作するためのツールも含まれています。 このドキュメントでは、ローカルの仮想マシンで実行されている Hortonworks サンドボックスで、Azure Data Lake ツールを使用するために必要な手順を説明します。

Hortonworks サンドボックスを使用すると、ローカルの開発環境で Hadoop を使用できます。 開発したソリューションを大規模にデプロイしたい場合に、HDInsight クラスターに移行できます。

## <a name="prerequisites"></a>前提条件

* 開発環境の仮想マシンで実行されている Hortonworks サンドボックス。 このドキュメントに記載されている内容は、Oracle VirtualBox で実行されるサンドボックスを使用してテストされています。また、サンドボックスは、[Hadoop エコシステムの使用方法](hdinsight-hadoop-emulator-get-started.md)に関するドキュメントに記載された情報を使用して構成されています。

* Visual Studio 2013、2015、または 2017 の任意のエディション。

* [Azure SDK for .NET](https://azure.microsoft.com/downloads/) 2.7.1 以降。

* [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)

## <a name="configure-passwords-for-the-sandbox"></a>サンドボックスのパスワードを構成する

Hortonworks サンドボックスが実行されていることを確認したら、[Hadoop エコシステムの使用方法](hdinsight-hadoop-emulator-get-started.md#set-sandbox-passwords)に関するドキュメントに記載された手順に従います。 これにより、SSH `root` アカウントと Ambari `admin` アカウントのパスワードを構成します。 これらのパスワードは、Visual Studio からサンドボックスに接続する際に使用されます。

## <a name="connect-the-tools-to-the-sandbox"></a>サンドボックスにツールを接続する

1. Visual Studio を開き、**[表示]**、**[サーバー エクスプローラー]** の順に選択します。

2. **[サーバー エクスプローラー]** の **[HDInsight]** エントリをクリックし、**[Connect to HDInsight Emulator (HDInsight Emulator に接続)]** を選択します。

    ![[Connect to HDInsight Emulator (HDInsight Emulator に接続)]](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. **[Connect to HDInsight Emulator (HDInsight Emulator に接続)]** ダイアログ ボックスで、Ambari 用に構成したパスワードを入力します。

    ![Enter Ambari password](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    **[次へ]** をクリックして続行します。

4. **[パスワード]** フィールドに、`root` アカウント用に構成したパスワードを入力します。 他のフィールドは既定値のままにします。

    ![Enter root password](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    **[次へ]** をクリックして続行します。

5. サービスの検証が完了するのを待ちます。 検証が失敗し、構成を更新するよう求められることがあります。 検証が失敗した場合は、**[更新]** をクリックし、サービスの構成と検証が完了するのを待ちます。

    ![Errors and update button](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]
    > 更新プロセスでは、Ambari を使用して、Azure Data Lake Tools for Visual Studio で必要とされる要件に合わせて Hortonworks サンドボックス構成が変更されます。

    検証が終わったら、**[完了]** をクリックして、構成を完了します。

    ![Finish connecting](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

    > [!NOTE]
    > 開発環境の処理速度と仮想マシンに割り当てられたメモリの量によっては、サービスの構成と検証に数分かかることがあります。

上記の手順を終了すると、[サーバー エクスプローラー] の [HDInsight] セクションに [HDInsight local cluster (HDInsight ローカル クラスター)] エントリが表示されます。

## <a name="write-a-hive-query"></a>Hive クエリを記述する

Hive には、構造化データを操作するための、SQL に似たクエリ言語 (HiveQL) が用意されています。 次の手順では、ローカル クラスターにアドホック クエリを実行する方法を示します。

1. **[サーバー エクスプローラー]** で、前の手順で追加したローカル クラスターのエントリを右クリックし、**[Write a Hive query (Hive クエリの記述)]** を選択します。

    ![Hive クエリを記述する](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    新しいクエリ ウィンドウが開き、短時間でクエリを入力して、ローカル クラスターに送信できます。

2. 新しいクエリ ウィンドウに、次のコマンドを入力します。

        select count(*) from sample_08;

    クエリ ウィンドウの上部で、ローカル クラスターの構成が選択されていることを確認し、 **[送信]**をクリックします。 他の値 (**[バッチ]** とサーバー名) は既定値のままにします。

    ![query window and submit button](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    **[送信]** の横にあるドロップダウン メニューで **[詳細設定]** を選択することもできます。 [詳細設定] オプションでは、ジョブを送信するときに利用できる追加のオプションを指定できます。

    ![advanced submit](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. クエリを送信すると、ジョブの状態が表示されます。 ここでは、Hadoop で処理されるジョブの情報が表示されます。 **[ジョブの状態]** エントリは、ジョブの状態を示します。 状態は定期的に更新されます。更新アイコンを使用して、状態を手動で更新することもできます。

    ![Job state](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    **[ジョブの状態]** が **[完了]** に変わったら、有向非巡回グラフ (DAG) が表示されます。 このダイアグラムは、Tez (ローカル クラスター上の Hive の既定の実行エンジン) によって決定された実行パスを示しています。

    > [!NOTE]
    > Linux ベースの HDInsight クラスターを使用する場合も、Tez が既定の実行エンジンです。 Windows ベースの HDInsight では既定ではありません。Tez を使用するには、Hive クエリの先頭に `set hive.execution.engine = tez;` という行を追加する必要があります。

    **[ジョブの出力]** リンクをクリックすると、出力が表示されます。 この例では、**823** と表示されます。これは、sample_08 テーブル内の行の数です。 **[ジョブのログ]** リンクと **[Download YARN Log (YARN ログのダウンロード)]** リンクを使用すると、ジョブに関する診断情報を表示できます。

4. また、**[バッチ]** フィールドを **[対話型]** に変更し、**[実行]** をクリックすると、対話形式で Hive ジョブを実行できます。

    ![Interactive query](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    これによって、処理中に生成された出力ログが **[HiveServer2 Output (HiveServer2 出力)]** ウィンドウにストリーム表示されます。

    > [!NOTE]
    > これは、ジョブが完了した後に **[ジョブのログ]** リンクから取得できるものと同じ情報です。

    ![[HiveServer2 Output (HiveServer2 出力)]](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Hive プロジェクトを作成する

複数の Hive スクリプトを含むプロジェクトを作成することもできます。 プロジェクトは、複数の関連スクリプトを&1; つにまとめておきたい場合や、バージョン管理システムを使用して管理する必要がある場合に便利です。

1. Visual Studio で、**[ファイル]**、**[新規]**、__[プロジェクト]__ の順に選択します。

2. プロジェクトの一覧から、**[テンプレート]**、**[Azure Data Lake]** の順に展開し、**[HIVE (HDInsight)]** を選択します。 テンプレートの一覧から **[Hive Sample (Hive サンプル)]** を選択します。 名前と場所を入力し、**[OK]** を選択します。

    ![HIVE (HDInsight) template](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

**[Hive Sample (Hive サンプル)]** プロジェクトには、**WebLogAnalysis.hql** と **SensorDataAnalysis.hql** という&2; つのスクリプトが含まれています。 これらのスクリプトを送信する際も、ウィンドウの上部にある **[送信]** ボタンをクリックします。

## <a name="create-a-pig-project"></a>Pig プロジェクトを作成する

Hive には構造化データを操作するための SQL に似た言語が用意されていますが、Pig はデータで変換を実行することによって機能します。 Pig は変換のパイプラインを作成できる言語 (Pig Latin) を備えています。 ローカル クラスターで Pig を使用するには、次の手順に従います。

1. Visual Studio を開いて、**[ファイル]**、**[新規]**、**[プロジェクト]** の順に選択します。 プロジェクトの一覧から、**[テンプレート]**、**[Azure Data Lake]** の順に展開し、**[Pig (HDInsight)]** を選択します。 テンプレートの一覧から、**[Pig Application (Pig アプリケーション)]** を選択します。 名前と場所を入力し、**[OK]** を選択します。

    ![Pig (HDInsight) project](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. このプロジェクトで作成した **script.pig** ファイルの内容として、次のテキストを入力します。

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Pig では Hive とは異なる言語を使用しますが、ジョブを実行する方法はどちらの言語でも同じで、**[送信]** ボタンをクリックします。 **[送信]** の横にあるドロップダウンを選択すると、Pig の詳細な送信設定のためのダイアログが表示されます。

    ![Pig advanced submit](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. 表示されるジョブの状態と出力は、Hive クエリの場合と同じです。

    ![image of a completed pig job](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>ジョブを表示する

Azure Data Lake Tools では、Hadoop で実行されたジョブに関する情報も簡単に表示できます。 ローカル クラスターで実行されたジョブを表示するには、次の手順に従います。

1. **[サーバー エクスプローラー]** で、ローカル クラスターを右クリックし、**[ジョブの表示]** を選択します。 クラスターに送信されたジョブの一覧が表示されます。

    ![ジョブを表示する](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. ジョブの一覧から、詳細を表示するジョブを選択します。

    ![select a job](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    表示される情報は、Hive や Pig のクエリを実行した後に出力とログ情報を表示するためのリンクを使用して得られるものとほとんど同じです。

3. また、ここからジョブの変更と再送信を実行することもできます。

## <a name="view-hive-databases"></a>Hive データベースを表示する

1. **[サーバー エクスプローラー]** で、**[HDInsight local cluster (HDInsight ローカル クラスター)]** エントリ、**[Hive Databases (Hive データベース)]** の順に展開します。 ローカル クラスター上の **[既定]** データベースと **[xademo]** データベースが表示されます。 データベースを展開すると、データベース内のテーブルが表示されます。

    ![expanded databases](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. テーブルを展開すると、そのテーブルの列が表示されます。 テーブルを右クリックし、**[View Top 100 Rows (上位 100 行の表示)]** を選択すると、すぐにデータが表示されます。

    ![hive databases view](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>データベースとテーブルのプロパティ

お気付きかもしれませんが、データベースまたはテーブルで **[プロパティ]** を選択して表示することができます。 **[プロパティ]** を選択すると、選択したアイテムの詳細が [プロパティ] ウィンドウに表示されます。

![プロパティ](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>テーブルを作成する

新しいテーブルを作成するには、データベースを右クリックし、**[テーブルの作成]** を選択します。

![[テーブルの作成]](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

ここでフォームを使用してテーブルを作成できます。 このページの下部に表示されている未加工の HiveQL を使用してテーブルを作成します。

![create table form](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>次のステップ

* [Hortonworks Sandbox の使い方のヒント](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop チュートリアル: HDP の概要](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

