---
title: Hortonworks サンドボックスでの Data Lake Tools for Visual Studio - Azure HDInsight
description: ローカル VM で実行される Hortonworks サンドボックスで Azure Data Lake Tools for Visual Studio を使用する方法について説明します。 このツールを使用すると、サンドボックスで Hive ジョブと Pig ジョブを作成して実行し、ジョブの出力と履歴を表示できます。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: hrasheed
ms.openlocfilehash: 7c2a1c586b4d6f5f9f6e34a091702a35f994544f
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726631"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Hortonworks サンドボックスで Azure Data Lake Tools for Visual Studio を使用する

Azure Data Lake には、汎用の Apache Hadoop クラスターで動作するツールが含まれています。 このドキュメントでは、ローカルの仮想マシンで実行されている Hortonworks サンドボックスで Data Lake ツールを使用するために必要な手順を説明します。

Hortonworks サンドボックスを使用すると、ローカルの開発環境で Hadoop を使用できます。 開発したソリューションを大規模にデプロイしたい場合に、HDInsight クラスターに移行できます。

## <a name="prerequisites"></a>前提条件

* 開発環境の仮想マシンで実行されている Hortonworks サンドボックス。 このドキュメントの執筆とテストには、Oracle VirtualBox で実行されたサンドボックスを使用しています。 サンドボックスをセットアップする方法については、[Hortonworks サンドボックスの概要](hadoop/apache-hadoop-emulator-get-started.md) に関する記事を参照してください。

* Visual Studio 2013、Visual Studio 2015、または Visual Studio 2017 (エディションは任意)。

* [Azure SDK for .NET](https://azure.microsoft.com/downloads/) 2.7.1 以降。

* [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)。

## <a name="configure-passwords-for-the-sandbox"></a>サンドボックスのパスワードを構成する

Hortonworks サンドボックスが実行中であることを確認します。 次に、[Hortonworks サンドボックスの概要](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords)に関する記事の手順に従います。 これにより、SSH `root` アカウントと Apache Ambari `admin` アカウントのパスワードを構成します。 これらのパスワードは、Visual Studio からサンドボックスに接続する際に使用されます。

## <a name="connect-the-tools-to-the-sandbox"></a>サンドボックスにツールを接続する

1. Visual Studio を開き、**[表示]**、**[サーバー エクスプローラー]** の順に選択します。

2. **[サーバー エクスプローラー]** の **[HDInsight]** エントリをクリックし、**[Connect to HDInsight Emulator (HDInsight Emulator に接続)]** を選択します。

    ![サーバー エクスプローラーのスクリーンショット ([HDInsight Emulator に接続する] を強調表示)](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. **[HDInsight Emulator に接続する]** ダイアログ ボックスで、Ambari 用に構成したパスワードを入力します。

    ![ダイアログ ボックスのスクリーンショット (パスワード テキスト ボックスを強調表示)](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    **[次へ]** をクリックして続行します。

4. **[パスワード]** フィールドに、`root` アカウント用に構成したパスワードを入力します。 他のフィールドは既定値のままにします。

    ![ダイアログ ボックスのスクリーンショット (パスワード テキスト ボックスを強調表示)](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    **[次へ]** をクリックして続行します。

5. サービスの検証が完了するのを待ちます。 検証が失敗し、構成を更新するよう求められることがあります。 検証が失敗した場合は、**[更新]** をクリックし、サービスの構成と検証が完了するのを待ちます。

    ![ダイアログ ボックスのスクリーンショット ([更新] ボタンを強調表示)](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]  
    > 更新プロセスでは、Ambari を使用して、Data Lake Tools for Visual Studio で必要とされる要件に合わせて Hortonworks サンドボックス構成が変更されます。

6. 検証が終わったら、**[完了]** をクリックして、構成を完了します。
    ![ダイアログ ボックスのスクリーンショット ([完了] ボタンを強調表示)](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

     >[!NOTE]  
     > 開発環境の処理速度と仮想マシンに割り当てられたメモリの量によっては、サービスの構成と検証に数分かかることがあります。

上記の手順を終了すると、サーバー エクスプローラーの **[HDInsight]** セクションに **[HDInsight local cluster]\(HDInsight ローカル クラスター\)** エントリが表示されます。

## <a name="write-an-apache-hive-query"></a>Apache Hive クエリを記述する

Hive には、構造化データを操作するための、SQL に似たクエリ言語 (HiveQL) が用意されています。 次の手順は、ローカル クラスターに対してオンデマンド クエリを実行する方法を示しています。

1. **サーバー エクスプローラー**で、前の手順で追加したローカル クラスターのエントリを右クリックし、**[Hive クエリの作成]** を選択します。

    ![サーバー エクスプローラーのスクリーンショット ([Hive クエリの作成] を強調表示)](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    新しいクエリ ウィンドウが表示されます。 このウィンドウでローカル クラスターに対するクエリをすぐに作成して送信できます。

2. 新しいクエリ ウィンドウに、次のコマンドを入力します。

        select count(*) from sample_08;

    クエリを実行するには、ウィンドウの上部にある **[送信]** を選択します。 他の値 (**[バッチ]** とサーバー名) は既定値のままにします。

    ![クエリ ウィンドウのスクリーンショット ([送信] ボタンを強調表示)](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    **[送信]** の横にあるドロップダウン メニューで **[詳細設定]** を選択することもできます。 [詳細設定] オプションでは、ジョブを送信するときに利用できる追加のオプションを指定できます。

    ![[スクリプトの送信] ダイアログ ボックスのスクリーンショット](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. クエリを送信すると、ジョブの状態が表示されます。 ここでは、Hadoop で処理されるジョブの情報が表示されます。 **[ジョブの状態]** は、ジョブの状態を示します。 状態は定期的に更新されます。更新アイコンを使用して、状態を手動で更新することもできます。

    ![[ジョブ ビュー] ダイアログ ボックスのスクリーンショット ([ジョブの状態] を強調表示)](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    **[ジョブの状態]** が **[完了]** に変わったら、有向非巡回グラフ (DAG) が表示されます。 このダイアグラムは、Hive クエリを処理するときに Tez によって決定された実行パスを示しています。 Tez は、ローカル クラスター上の Hive の既定の実行エンジンです。

    > [!NOTE]  
    > Linux ベースの HDInsight クラスターを使用する場合も、Apache Tez が既定の実行エンジンです。 Windows ベースの HDInsight では既定ではありません。 Tez を使用するには、Hive クエリの先頭に `set hive.execution.engine = tez;` という行を追加する必要があります。

    **[ジョブの出力]** リンクをクリックすると、出力が表示されます。 この例では、"823" と表示されます。これは、sample_08 テーブル内の行の数です。 **[ジョブのログ]** リンクと **[Download YARN Log (YARN ログのダウンロード)]** リンクを使用すると、ジョブに関する診断情報を表示できます。

4. また、対話形式で Hive ジョブを実行することもできます。その場合は、**[バッチ]** フィールドを **[対話型]** に変更し、 **[実行]** をクリックしてください。

    ![スクリーンショット ([対話型] ボタンと [実行] ボタンを強調表示)](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    これによって、処理中に生成された出力ログが **[HiveServer2 Output (HiveServer2 出力)]** ウィンドウにストリーム表示されます。

    > [!NOTE]  
    > これは、ジョブが完了した後に **[ジョブのログ]** リンクから取得できるのと同じ情報です。

    ![出力ログのスクリーンショット](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Hive プロジェクトを作成する

複数の Hive スクリプトを含むプロジェクトを作成することもできます。 関連するスクリプトがある場合やバージョン管理システムにスクリプトを格納する場合は、プロジェクトを使用します。

1. Visual Studio で、**[ファイル]**、**[新規]**、**[プロジェクト]** の順に選択します。

2. プロジェクトの一覧から、**[テンプレート]**、**[Azure Data Lake]** の順に展開し、**[HIVE (HDInsight)]** を選択します。 テンプレートの一覧から **[Hive Sample (Hive サンプル)]** を選択します。 名前と場所を入力し、**[OK]** を選択します。

    ![[新しいプロジェクト] ウィンドウのスクリーンショット ([Azure Data Lake]、[HIVE]、[Hive Sample]\(Hive サンプル\)、[OK] を強調表示)](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

**[Hive Sample (Hive サンプル)]** プロジェクトには、**WebLogAnalysis.hql** と **SensorDataAnalysis.hql** という 2 つのスクリプトが含まれています。 これらのスクリプトを送信する際も、ウィンドウの上部にある **[送信]** ボタンを使用できます。

## <a name="create-an-apache-pig-project"></a>Apache Pig プロジェクトを作成する

Hive には構造化データを操作するための SQL に似た言語が用意されていますが、Pig はデータで変換を実行することによって機能します。 Pig は変換のパイプラインを作成できる言語 (Pig Latin) を備えています。 ローカル クラスターで Pig を使用するには、次の手順に従います。

1. Visual Studio を開いて、**[ファイル]**、**[新規]**、**[プロジェクト]** の順に選択します。 プロジェクトの一覧から、**[テンプレート]**、**[Azure Data Lake]** の順に展開し、**[Pig (HDInsight)]** を選択します。 テンプレートの一覧から、**[Pig Application (Pig アプリケーション)]** を選択します。 名前と場所を入力し、**[OK]** を選択します。

    ![[新しいプロジェクト] ウィンドウのスクリーンショット ([Azure Data Lake]、[Pig]、[Pig Application]\(Pig アプリケーション\)、[OK] を強調表示)](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

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

    Pig では Hive とは異なる言語を使用しますが、ジョブを実行する方法はどちらの言語でも同じで、**[送信]** ボタンをクリックします。 **[送信]** の横にあるドロップダウンを選択すると、Pig の詳細な送信設定用のダイアログ ボックスが表示されます。

    ![[スクリプトの送信] ダイアログ ボックスのスクリーンショット](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. 表示されるジョブの状態と出力は、Hive クエリの場合と同じです。

    ![完了した Pig ジョブのスクリーンショット](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>ジョブを表示する

Data Lake Tools では、Hadoop で実行されたジョブに関する情報も簡単に表示できます。 ローカル クラスターで実行されたジョブを表示するには、次の手順に従います。

1. **サーバー エクスプローラー**で、ローカル クラスターを右クリックし、**[ジョブの表示]** を選択します。 クラスターに送信されたジョブの一覧が表示されます。

    ![サーバー エクスプローラーのスクリーンショット ([ジョブの表示] を強調表示)](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. ジョブの一覧から、詳細を表示するジョブを選択します。

    ![ジョブ ブラウザーのスクリーンショット (1 つのジョブを強調表示)](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    表示される情報は、出力とログ情報を表示するためのリンクを含め、Hive や Pig のクエリを実行した後に表示されるものとほとんど同じです。

3. また、ここからジョブの変更と再送信を実行することもできます。

## <a name="view-hive-databases"></a>Hive データベースを表示する

1. **[サーバー エクスプローラー]** で、**[HDInsight local cluster (HDInsight ローカル クラスター)]** エントリ、**[Hive Databases (Hive データベース)]** の順に展開します。 ローカル クラスター上の **[既定]** データベースと **[xademo]** データベースが表示されます。 データベースを展開すると、データベース内のテーブルが表示されます。

    ![サーバー エクスプローラーのスクリーンショット (データベースを展開)](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. テーブルを展開すると、そのテーブルの列が表示されます。 すぐにデータを表示するには、テーブルを右クリックし、**[上位 100 行を表示 ]** を選択します。

    ![サーバー エクスプローラーのスクリーンショット (テーブルを展開し、[上位 100 行を表示] を選択)](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>データベースとテーブルのプロパティ

データベースまたはテーブルのプロパティを表示することができます。 **[プロパティ]** を選択すると、選択したアイテムの詳細が [プロパティ] ウィンドウに表示されます。 その例については、次のスクリーンショットに表示されている情報をご覧ください。

![[プロパティ] ウィンドウのスクリーンショット](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>テーブルを作成する

テーブルを作成するには、データベースを右クリックし、**[テーブルの作成]** を選択します。

![サーバー エクスプローラーのスクリーンショット ([テーブルの作成] を強調表示)](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

ここでフォームを使用してテーブルを作成できます。 次のスクリーンショットの一番下を見ると、テーブルの作成に使用された生の HiveQL が確認できます。

![テーブルの作成に使用されたフォームのスクリーンショット](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>次の手順

* [Hortonworks Sandbox の使い方のヒント](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Apache Hadoop チュートリアル: HDP の概要](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
