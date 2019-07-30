---
title: Data Lake Tools for Visual Studio を使用した Apache Hadoop への接続 - Azure HDInsight
description: Data Lake Tools for Visual Studio をインストールし、それを使用して Azure HDInsight 上の ApacheHadoop クラスターに接続し、Hive クエリを実行する方法について説明します。
keywords: Hadoop ツール, Hive クエリ, Visual Studio, Visual Studio Hadoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 485f3a4b6a5fde532229873fe1f3feaa30ece523
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450199"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Data Lake Tools for Visual Studio を使用して Azure HDInsight に接続し、Apache Hive クエリを実行する

[Microsoft Azure Data Lake and Stream Analytics Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) (Data Lake Tools とも呼ばれます) を使用して [Azure HDInsight](../hdinsight-hadoop-introduction.md) 上の Apache Hadoop クラスターに接続し、Hive クエリを送信する方法について説明します。  

HDInsight の使用に関する詳細については、[HDInsight の概要](../hdinsight-hadoop-introduction.md)と [HDInsight の使用](apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。  

Apache Storm クラスターへの接続に関する詳細については、[Visual Studio を使用した HDInsight での Apache Storm の C# トポロジの開発](../storm/apache-storm-develop-csharp-visual-studio-topology.md)に関するページを参照してください。

Data Lake Tools for Visual Studio を使用して、Azure Data Lake Analytics と HDInsight にアクセスできます。 Data Lake Tools の詳細については、「[Data Lake Tools for Visual Studio を使用して U-SQL スクリプトを開発する](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

この記事を完了して、Data Lake Tools for Visual Studio を使用するには、次のものが必要です。

* Azure HDInsight クラスター。 HDInsight クラスターを作成するには、[Azure HDInsight での Apache Hadoop の使用](apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。 対話型 Apache Hive クエリを実行するには、[HDInsight 対話型クエリ](../interactive-query/apache-interactive-query-get-started.md) クラスターが必要です。  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 以降)。  [Visual Studio Community エディション](https://visualstudio.microsoft.com/vs/community/)は無料です。  「[Visual Studio 2017 のインストール](https://docs.microsoft.com/visualstudio/install/install-visual-studio)」と「[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)」も参照してください。 Visual Studio 2019 にはいくつかのインターフェイスのバリエーションがあります。

  > [!IMPORTANT]  
  > Data Lake Tools は Visual Studio 2013 ではサポートされなくなりました。

## <a name="install-data-lake-tools-for-visual-studio"></a>Data Lake Tools for Visual Studio のインストール  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 または Visual Studio 2019  
  インストール中は、少なくとも **Azure 開発**または**データの保存と処理**のワークロードを含めるようにしてください。  

  既存のインストールの場合は、メニュー バーから **[ツール]**  >  **[ツールと機能を取得...]** に移動し、Visual Studio インストーラーを開きます。  その後、少なくとも **Azure 開発**または**データの保存と処理**のワークロードを選択してください。

  ![Visual Studio インストーラーのスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/vs2017_installation.png)

* Visual Studio 2013 および 2015  
  [Data Lake Tools をダウンロードします](https://www.microsoft.com/download/details.aspx?id=49504)。 お使いの Visual Studio バージョンに対応する Data Lake Tools のバージョンを選択します。  

> [!NOTE]  
> 現時点では、Data Lake Tools for Visual Studio の英語版のみを使用できます。

## <a name="update-data-lake-tools-for-visual-studio"></a>Data Lake Tools for Visual Studio の更新  

1. Visual Studio を開きます。

2. メニュー バーから **[ツール]**  >  **[拡張機能と更新プログラム...]** に移動します。

3. **[拡張機能と更新プログラム]** ウィンドウで、左側の **[更新プログラム]** を展開します。

4. 利用可能な更新プログラムがある場合は、**Azure Data Lake and Stream Analytic Tools** がメイン ウィンドウに表示されます。  **[Update]\(更新\)** を選択します。

> [!NOTE]  
> 対話型クエリ クラスターに接続して対話型 Hive クエリを実行するには、Data Lake Tools のバージョン 2.3.0.0 以降のみを使用できます。

## <a name="connect-to-azure-subscriptions"></a>Azure サブスクリプションに接続する
Data Lake Tools for Visual Studio を使用して、HDInsight クラスターへの接続、いくつかの基本的な管理操作の実行、および Hive クエリの実行が可能です。

> [!NOTE]  
> 汎用の Hadoop クラスターに接続する方法の詳細については、「[Write and submit Hive queries by using Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx)」(Visual Studio を使用した Hive クエリの書き込みと送信) をご覧ください。

Azure サブスクリプションに接続するには:

1. Visual Studio を開きます。

2. メニュー バーから、 **[表示]**  >  **[サーバー エクスプローラー]** に移動します。

3. [サーバー エクスプローラー] で **[Azure]** を右クリックし、 **[Microsoft Azure サブスクリプションへの接続...]** を選択してサインイン処理を完了します。

4. サーバー エクスプローラーで、既存の HDInsight クラスターの一覧が表示されます。 クラスターが 1 つもない場合は、Azure ポータル、Azure PowerShell、または HDInsight SDK を使用して作成できます。 詳細については、[HDInsight クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。

   ![サーバー エクスプローラーの Data Lake Tools for Visual Studio クラスター一覧のスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "サーバー エクスプローラーの Data Lake Tools for Visual Studio クラスター一覧")

5. HDInsight クラスターを展開します。 **Hive データベース**、既定のストレージ アカウント、リンクされたストレージ アカウント、および **Hadoop サービス ログ**が表示されます。 さらに、エンティティを展開できます。

Azure サブスクリプションに接続した後で、次のタスクを実行できます。

Visual Studio から Azure Portal に接続するには:

1. サーバー エクスプローラーから **[Azure]**  >  **[HDInsight]** に移動して、クラスターを選択します。

2. HDInsight クラスターを右クリックし、 **[Azure Portal でのクラスターの管理][sic]** を選択します。

Visual Studio から質問をしたりフィードバックを提供したりするには:

1. サーバー エクスプローラーから **[Azure]**  >  **[HDInsight]** に移動します。

2. **[HDInsight]** を右クリックし、質問をするには **[MSDN フォーラム]** を、フィードバックを提供するには **[フィードバックの送信]** を選択してください。

## <a name="link-a-cluster"></a>クラスターのリンク
クラスターをリンクするには、 **[HDInsight]** を右クリックして **[HDInsight クラスターをリンク]** を選択します。 **[接続 URL]** 、 **[ユーザー名]** 、 **[パスワード]** を入力し、 **[次へ]** をクリックし、 **[完了]** をクリックします。クラスターは HDInsight ノードの下に成功と表示されます。

![Data Lake Tools for Visual Studio のリンク クラスター ダイアログのスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

リンクされたクラスターを右クリックし、 **[編集]** を選択します。ユーザーはクラスター情報を更新できます。 現在、HDInsight クラスターの追加は Hive のみをサポートしています。

![Data Lake Tools for Visual Studio のリンク クラスターの更新のスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>リンクされたリソースを調べる
サーバー エクスプローラーで、既定のストレージ アカウント、すべてのリンクされたストレージ アカウントを確認できます。 既定のストレージ アカウントを展開すると、そのストレージ アカウントのコンテナーを表示できます。 既定のストレージ アカウントと既定のコンテナーがマークされます。 コンテナーのコンテンツを表示するには、コンテナーを右クリックします。

![サーバー エクスプローラーの Data Lake Tools for Visual Studio クラスターのリンクされたリソース一覧のスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "リンクされたリソース一覧")

コンテナーを開くと、次のボタンを使用して、BLOB をアップロード、削除、およびダウンロードすることができます。

![サーバー エクスプローラーでの Data Lake Tools for Visual Studio の BLOB 操作のスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "サーバー エクスプローラーでの BLOB のアップロード、削除、およびダウンロード")

## <a name="run-interactive-apache-hive-queries"></a>対話型 Apache Hive クエリを実行する
[Apache Hive](https://hive.apache.org) は、Hadoop に組み込まれているデータ ウェアハウス インフラストラクチャです。 Hive は、データの概要、クエリ、および分析に使用されます。 Data Lake Tools for Visual Studio を使用して、Visual Studio から Hive クエリを実行できます。 Hive の詳細については、[HDInsight での Apache Hive の使用](hdinsight-use-hive.md)に関するページを参照してください。

[対話型クエリ](../interactive-query/apache-interactive-query-get-started.md)では、Apache Hive 2.1 の [Hive on LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) を使用します。 対話型クエリでは、格納されている大規模なデータセットに対する複雑なデータ ウェアハウス スタイルのクエリを対話形式で実行できます。 対話型クエリは、従来の Hive バッチ ジョブと比べ、Hive クエリの実行速度が格段に速くなっています。 

> [!NOTE]  
> 対話型 Hive クエリは、[HDInsight 対話型クエリ](../interactive-query/apache-interactive-query-get-started.md) クラスターに接続している場合のみ実行できます。

Data Lake Tools for Visual Studio を使用して、Hive ジョブの内容を表示することもできます。 Data Lake Tools for Visual Studio は、特定の Hive ジョブを収集し、Yarn ログを表示できます。

サーバー エクスプローラーから **[Azure]**  >  **[HDInsight]** に移動して、クラスターを選択します。  これが、次に進むセクションのサーバー エクスプローラーの開始点になります。

### <a name="view-hivesampletable"></a>hivesampletable を表示する
すべての HDInsight クラスターには、`hivesampletable` という名前の既定のサンプル Hive テーブルが付属します。  

クラスターから、 **[Hive データベース]**  >  **[既定]**  >  **[hivesampletable]** に移動します。

* `hivesampletable` スキーマを表示するには:  
**[hivesampletable]** を展開します。

* `hivesampletable` データを表示するには:  
**[hivesampletable]** を右クリックし、 **[上位 100 行を表示]** を選択します。  これは、Hive ODBC ドライバーを使用して、次の Hive クエリを実行することと同等です。

   `SELECT * FROM hivesampletable LIMIT 100`

  行カウントをカスタマイズできます。

  ![HDInsight Hive Visual Studio スキーマ クエリのスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Hive クエリの結果")

### <a name="create-hive-tables"></a>Hive テーブルの作成
Hive テーブルを作成するには、GUI または Hive クエリを使用できます。 Hive クエリの使用については、[Apache Hive クエリの実行](#run.queries)に関するページを参照してください。

1. クラスターから、 **[Hive データベース]**  >  **[既定]** に移動します。

2. **[既定]** を右クリックし、 **[テーブルの作成]** を選択します。

3. 目的に応じてテーブルを構成します。  

4. **[テーブルの作成]** を選択して、新しい Hive テーブルを作成するためのジョブを送信します。

    ![HDInsight Visual Studio Tools の テーブル作成ウィンドウのスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "Hive テーブルの作成")

### <a name="run.queries"></a>Hive クエリの作成と実行
Hive クエリを作成して実行するためのオプションは 2 つあります。

* アドホック クエリを作成する
* Hive アプリケーションを作成する

アドホック クエリを作成して実行するには:

1. クエリを実行するクラスターを右クリックし、 **[Hive クエリの作成]** を選択します。  

2. 次の Hive クエリを入力します。

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

    Hive エディターは IntelliSense をサポートしています。 Data Lake Tools for Visual Studio では、Hive スクリプトの編集時にリモート メタデータの読み込みをサポートします。 たとえば、`SELECT * FROM` と入力すると、IntelliSense によってテーブル名の候補が一覧表示されます。 テーブル名を指定すると、Intellisense によって列名が一覧表示されます。 このツールは、Hive の DML ステートメント、サブクエリ、および組み込みの UDF の大半をサポートします。

    ![HDInsight Visual Studio Tools での IntelliSense の例 1 のスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png "U-SQL IntelliSense")

    ![HDInsight Visual Studio Tools での IntelliSense の例 2 のスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png "U-SQL IntelliSense")

   > [!NOTE]  
   > IntelliSense は、HDInsight のツール バーで選択されているクラスターのメタデータのみを推奨します。

3. 実行モードを選択します:

    * **対話**  

      **[対話型]** が選択されていることを確認してから、 **[実行]** を選択します。

      ![クエリと実行のスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/execute.png)  

    * **Batch**  

      **[バッチ]** が選択されていることを確認してから、 **[送信]** を選択します。  高度な送信オプションを選択した場合は、スクリプトの **[ジョブ名]** 、 **[引数]** 、 **[追加の構成]** 、 **[状態ディレクトリ]** を構成します。

      ![クエリとバッチのスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/batch.png)  

      ![HDInsight Hadoop の Hive クエリのスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "クエリの送信")

      > [!NOTE]  
      > 対話型クエリ クラスターにはバッチを送信できません。  対話モードを使用する必要があります。

Hive ソリューションを作成して実行するには:

1. メニュー バーから、 **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト...]** に移動します。

2. 左側のウィンドウで、 **[インストール済み]**  >  **[Azure Data Lake]**  >  **[HIVE (HDInsight)]** に移動します。  

3. 中央のウィンドウで、 **[Hive アプリケーション]** を選択します。 プロパティを入力し、 **[OK]** を選択します。

    ![HDInsight Visual Studio Tools の新しい Hive プロジェクトのスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "Visual Studio から Hive アプリケーションを作成する")

4. **ソリューション エクスプローラー**で、**Script.hql** をダブルクリックしてスクリプトを開きます。

### <a name="view-job-summary-and-output"></a>ジョブの概要と出力の表示

ジョブの概要は、**バッチ** モードと**対話**モードとで若干異なります。

![ジョブの概要](./media/apache-hadoop-visual-studio-tools-get-started/jobSummary.png "Hive ジョブの概要")

**[更新]** ボタンを使用して、ジョブのステータスが **[完了]** に変更されるまで、ステータスを更新します。  

* **バッチ** モードのジョブ詳細については、ウィンドウ下部の該当するリンクを選択して **[ジョブ クエリ]** 、 **[ジョブ出力]** 、 **[ジョブのログ]** または **[Yarn ログ]** を表示します。

* **対話**モードのジョブ詳細については、 **[出力]** タブおよび **[HiveServer 2 出力]** タブを参照してください。

  ![ジョブの詳細](./media/apache-hadoop-visual-studio-tools-get-started/tabs.png "Hive ジョブの詳細")

### <a name="view-job-graph"></a>ジョブ グラフの表示

現在、ジョブ グラフは Tez を実行エンジンとして使用する Hive ジョブの場合のみ表示されます。  Tez の有効化の情報については、[HDInsight での Apache Hive の使用](hdinsight-use-hive.md)に関するページを参照してください。  「[Map Reduce の代わりに Apache Tez を使用する](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce)」も参照してください。  

頂点内のすべての演算子を表示するには、ジョブ グラフの頂点をダブルクリックします。 特定の演算子をポイントして、演算子の詳細を表示することもできます。

Tez アプリケーションが起動されていない場合は、実行エンジンとして Tez を指定してもジョブ グラフが表示されないことがあります。  これは、ジョブに DML ステートメントが含まれていないか、または DML ステートメントが Tez アプリケーションを起動せずに戻ることがあるために発生する可能性があります。 たとえば、`SELECT * FROM table1`は Tez アプリケーションを起動しません。

![ジョブ グラフ](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "Hive ジョブの概要")


### <a name="task-execution-detail"></a>タスク実行の詳細

ジョブ グラフから **[タスク実行の詳細]** を選択して、構造化および視覚化された Hive ジョブの情報を取得できます。 ジョブの詳細を取得することもできます。 パフォーマンスの問題が発生した場合に、このビューを使用して、問題の詳細を取得できます。 たとえば、各タスクの動作の情報と、各タスクの詳細情報 (データの読み取り/書き込み、開始時刻/終了時刻のスケジュールなど) を取得できます。 これらの情報を使用して、視覚化された情報に基づくジョブの構成やシステム アーキテクチャを調整します。

![Data Lake Visual Studio Tools のタスク実行ビュー ウィンドウのスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png "タスク実行ビュー")


### <a name="view-hive-jobs"></a>Hive ジョブの表示
Hive ジョブのジョブ クエリ、ジョブ出力、ジョブのログ、Yarn ログを表示できます。

最新版のツールでは、Yarn ログを収集して表示することで、Hive ジョブの内容を確認できます。 Yarn ログは、パフォーマンス問題の検証に役立ちます。 HDInsight での Yarn ログの収集に関する詳細については、[プログラムによる HDInsight アプリケーション ログへのアクセス](../hdinsight-hadoop-access-yarn-app-logs.md)に関するページを参照してください。

Hive ジョブを表示するには:

1. HDInsight クラスターを右クリックし、 **[ジョブの表示]** を選択します。 クラスターで実行された Hive ジョブの一覧が表示されます。  

2. ジョブを選択します。 **[Hive ジョブの概要]** ウィンドウで、次のいずれかを選択します。
    - **ジョブ クエリ**
    - **ジョブの出力**
    - **ジョブのログ**  
    - **Yarn ログ**

    ![HDInsight Visual Studio Tools の [Hive ジョブの表示] ウィンドウのスクリーンショット](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png "Hive ジョブの表示")


## <a name="run-apache-pig-scripts"></a>Apache Pig スクリプトの実行

1. メニュー バーから、 **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト...]** に移動します。

2. 左側のウィンドウで、 **[インストール済み]**  >  **[Azure Data Lake]**  >  **[Pig (HDInsight)]** に移動します。  

3. 中央のウィンドウで、 **[Pig アプリケーション]** を選択します。 プロパティを入力し、 **[OK]** を選択します。

4. **ソリューション エクスプローラー**で、**Script.pig** をダブルクリックしてスクリプトを開きます。

## <a name="feedback-and-known-issues"></a>フィードバックと既知の問題
* null 値で始まる結果が表示されない問題は修正されています。 この問題が発生している場合は、サポート チームに問い合わせてください。
* ユーザーのローカルのリージョン設定によっては、Visual Studio によって作成された HQL スクリプトがエンコードされます。 このスクリプトをバイナリ ファイルとしてクラスターにアップロードした場合、スクリプトは正しく実行されません。

## <a name="next-steps"></a>次の手順
この記事では、Data Lake Tools for Visual Studio パッケージを使用して、Visual Studio から HDInsight クラスターに接続する方法を説明しました。 Hive クエリを実行する方法についても説明しました。 詳細と例については、次の記事をご覧ください。

* [Data Lake Tools for Visual Studio を使用して Apache Hive クエリを実行する](apache-hadoop-use-hive-visual-studio.md)
* [HDInsight での Hadoop Hive の使用](hdinsight-use-hive.md)
* [HDInsight で Apache Hadoop を使用する](apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight で Apache Hadoop ジョブを送信する](submit-apache-hadoop-jobs-programmatically.md)
* [HDInsight で Apache Hadoop を使用して Twitter データを分析する](../hdinsight-analyze-twitter-data.md)

