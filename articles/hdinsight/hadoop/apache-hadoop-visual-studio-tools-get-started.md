---
title: Apache Hadoop および Visual Studio Data Lake Tools - Azure HDInsight
description: Data Lake Tools for Visual Studio をインストールして使用する方法について説明します。 ツールを使用して Azure HDInsight 内の Apache Hadoop クラスターに接続し、Hive クエリを実行します。
keywords: Hadoop ツール, Hive クエリ, Visual Studio, Visual Studio Hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: how-to
ms.date: 04/14/2020
ms.openlocfilehash: c6969c3108fb1465a705d8e61b3a7bdf2083736a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865657"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Data Lake Tools for Visual Studio を使用して Azure HDInsight に接続し、Apache Hive クエリを実行する

Microsoft Azure Data Lake and Stream Analytics Tools for Visual Studio (Data Lake Tools) を使用する方法を説明します。 ツールを使用して [Azure HDInsight 内の Apache Hadoop クラスター](apache-hadoop-introduction.md)に接続し、Hive クエリを送信します。  

HDInsight の使用方法の詳細については、[HDInsight の概要](apache-hadoop-linux-tutorial-get-started.md)に関する記事を参照してください。  

Apache Storm への接続について詳しくは、[Data Lake Tools を使用した Apache Storm の C# トポロジの開発](../storm/apache-storm-develop-csharp-visual-studio-topology.md)に関する記事をご覧ください。

Data Lake Tools for Visual Studio を使用して、Azure Data Lake Analytics と HDInsight にアクセスできます。 Data Lake Tools の詳細については、「[Data Lake Tools for Visual Studio を使用して U-SQL スクリプトを開発する](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

この記事を完了して、Data Lake Tools for Visual Studio を使用するには、次のものが必要です。

* Azure HDInsight クラスター。 HDInsight クラスターを作成するには、[Azure HDInsight での Apache Hadoop の使用](apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。 対話型 Apache Hive クエリを実行するには、[HDInsight 対話型クエリ](../interactive-query/apache-interactive-query-get-started.md) クラスターが必要です。  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/). [Visual Studio Community エディション](https://visualstudio.microsoft.com/vs/community/)は無料です。 ここでは、[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) の手順について説明します。

## <a name="install-data-lake-tools-for-visual-studio"></a>Data Lake Tools for Visual Studio のインストール  

適切な手順に従い、お使いの Visual Studio のバージョン用の Data Lake Tools をインストールします。

* Visual Studio 2017 または Visual Studio 2019 の場合:

    Visual Studio のインストール時に、 **[Azure の開発]** ワークロードまたは **[データの保存と処理]** ワークロードを含めるようにします。  

    既存の Visual Studio インストールがある場合は、IDE メニュー バーに移動し、 **[ツール]**  >  **[ツールと機能を取得]** を選択して Visual Studio インストーラーを開きます。 **[ワークロード]** タブで、少なくとも **[Azure の開発]** ワークロード ( **[Web & クラウド]** の下) を選択します。 または、 **[データの保存と処理]** ワークロード ( **[他のツールセット]** の下) を選択します。

  :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png" alt-text="ワークロードの選択、Visual Studio インストーラー" border="true":::

* Visual Studio 2015 の場合:

    [Data Lake Tools をダウンロードします](https://www.microsoft.com/download/details.aspx?id=49504)。 お使いの Visual Studio バージョンに対応する Data Lake Tools のバージョンを選択します。

## <a name="update-data-lake-tools-for-visual-studio"></a>Data Lake Tools for Visual Studio の更新  

次に、Data Lake Tools が最新のバージョンに更新されていることを確認します。

1. Visual Studio を開きます。

2. **[開始]** ウィンドウで、 **[コードなしで続行]** を選択します。

3. Visual Studio IDE のメニュー バーで、 **[拡張機能]**  >  **[拡張機能の管理]** を選択します。

4. **[拡張機能の管理]** ダイアログ ボックスで **[更新プログラム]** ノードを展開します。

5. 利用可能な更新プログラムの一覧に **[Azure Data Lake and Stream Analytic Tools]\(Azure Data Lake および Stream Analytic Tools\)** が含まれている場合は、それを選択します。 次に、 **[更新]** ボタンを選択します。 **[ダウンロードとインストール]** ダイアログ ボックスが表示され、表示されなくなると、Visual Studio によって、 **[Azure Data Lake and Stream Analytic Tools]\(Azure Data Lake および Stream Analytic Tools\)** の拡張機能が更新スケジュールに追加されます。

6. すべての Visual Studio ウィンドウを閉じます。 **VSIX インストーラー** のダイアログ ボックスが表示されます。

7. **[ライセンス]** を選択してライセンス条項を読み、 **[閉じる]** を選択して **VSIX インストーラー** のダイアログ ボックスに戻ります。

8. **[変更]** を選択します。 拡張機能の更新プログラムのインストールが開始されます。 しばらくすると、変更が加えられたことを示すダイアログ ボックスが表示されます。 **[閉じる]** を選択し、Visual Studio を再起動してインストールを完了します。

> [!NOTE]  
> 対話型クエリ クラスターに接続して対話型 Hive クエリを実行するには、Data Lake Tools のバージョン 2.3.0.0 以降のみを使用できます。

## <a name="connect-to-azure-subscriptions"></a>Azure サブスクリプションに接続する

Data Lake Tools for Visual Studio を使用して、HDInsight クラスターへの接続、いくつかの基本的な管理操作の実行、および Hive クエリの実行が可能です。

> [!NOTE]  
> 汎用の Hadoop クラスターに接続する方法の詳細については、「[How to write and submit Hive queries using Visual Studio](/archive/blogs/xiaoyong/how-to-write-and-submit-hive-queries-using-visual-studio)」(Visual Studio を使用した Hive クエリの書き込みおよび送信方法) を参照してください。

### <a name="connect-to-an-azure-subscription"></a>Azure サブスクリプションに接続する

Azure サブスクリプションに接続するには:

1. Visual Studio を開きます。

2. **[開始]** ウィンドウで、 **[コードなしで続行]** を選択します。

3. IDE のメニュー バーで、 **[表示]**  >  **[サーバー エクスプローラー]** を選択します。

4. **[サーバー エクスプローラー]** で **[Azure]** を右クリックし、 **[Microsoft Azure サブスクリプションへの接続]** を選択して認証処理を完了します。 **サーバー エクスプローラー** から **[Azure]**  >  **[HDInsight]** を展開して、既存の HDInsight クラスターの一覧を表示します。

5. クラスターが 1 つもない場合は、Azure portal、Azure PowerShell、または HDInsight SDK を使用して作成します。 詳細については、[HDInsight でのクラスターの設定](../hdinsight-hadoop-provision-linux-clusters.md)に関する記事を参照してください。

   :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png" alt-text="HDInsight クラスター一覧、サーバー エクスプローラー、Visual Studio" border="true":::

6. HDInsight クラスターを展開します。 クラスターには、**Hive データベース** 用のノードが含まれています。 また、既定のストレージ アカウント、すべての追加のリンクされたストレージ アカウント、および **Hadoop サービス ログ**。 さらに、エンティティを展開できます。

Azure サブスクリプションに接続した後で、次のタスクを実行できます。

### <a name="connect-to-azure-from-visual-studio"></a>Visual Studio から Azure に接続する

Visual Studio から Azure Portal に接続するには:

1. **サーバー エクスプローラー** で、 **[Azure]**  >  **[HDInsight]** を展開し、クラスターを選択します。

2. HDInsight クラスターを右クリックし、 **[Azure Portal でのクラスターの管理]** を選択します。

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Visual Studio から質問とフィードバックを提供する

Visual Studio から質問をしたりフィードバックを提供したりするには:

1. サーバー エクスプローラーから、 **[Azure]**  >  **[HDInsight]** を選択します。

2. **[HDInsight]** を右クリックし、質問をするには **[MSDN フォーラム]** を、フィードバックを提供するには **[フィードバックの送信]** を選択してください。

## <a name="link-to-or-edit-a-cluster"></a>クラスターへのリンクまたは編集

> [!NOTE]
> 現在、リンクできる HDInsight クラスターの種類は Hive のみです。

HDInsight クラスターをリンクするには:

1. **[HDInsight]** を右クリックし、 **[HDInsight クラスターをリンク]** を選択して、 **[HDInsight クラスターをリンク]** ダイアログ ボックスを表示します。

2. `https://CLUSTERNAME.azurehdinsight.net` の形式で **[接続 URL]** を入力します。 別のフィールドに移動すると、 **[クラスター名]** に URL のクラスター名部分が自動的に入力されます。 次に **[ユーザー名]** と **[パスワード]** を入力し、 **[次へ]** を選択します。

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png" alt-text="クラスターのリンク、HDInsight、Visual Studio" border="true":::

3. **[完了]** を選択します。 クラスターのリンクが成功すると、クラスターは **[HDInsight]** ノードの下に一覧表示されます。

リンクされたクラスターを更新するには、クラスターを右クリックし、 **[編集]** を選択します。 その後、クラスターの情報を更新できます。

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png" alt-text="リンクされたクラスターの編集、HDInsight、Visual Studio" border="true":::

## <a name="explore-linked-resources"></a>リンクされたリソースを調べる

サーバー エクスプローラーで、既定のストレージ アカウント、すべてのリンクされたストレージ アカウントを確認できます。 既定のストレージ アカウントを展開すると、そのストレージ アカウントのコンテナーを表示できます。 既定のストレージ アカウントと既定のコンテナーがマークされます。

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png" alt-text="サーバー エクスプローラーでの Visual Studio のリンクされたリソースの Data Lake Tools" border="true":::

コンテナーを右クリックし、 **[コンテナーを表示]** を選択して、コンテナーの内容を表示します。 コンテナーを開いた後は、ツール バーのボタンを使用してコンテンツ リストの **[更新]** 、 **[BLOB のアップロード]** 、 **[選択した BLOB の削除]** 、 **[BLOB を開く]** を行い、選択した BLOB をダウンロードできます ( **[名前を付けて保存]** )。

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png" alt-text="コンテナー一覧と BLOB の操作、HDInsight クラスター、Visual Studio" border="true":::

## <a name="run-interactive-apache-hive-queries"></a>対話型 Apache Hive クエリを実行する

[Apache Hive](https://hive.apache.org) は、Hadoop に組み込まれているデータ ウェアハウス インフラストラクチャです。 Hive は、データの概要、クエリ、および分析に使用されます。 Data Lake Tools for Visual Studio を使用して、Visual Studio から Hive クエリを実行できます。 Hive の詳細については、「[Azure HDInsight における Apache Hive と HiveQL](hdinsight-use-hive.md)」を参照してください。

[Azure HDInsight の対話型クエリ](../interactive-query/apache-interactive-query-get-started.md)では、Apache Hive 2.1 の [LLAP で Hive](https://cwiki.apache.org/confluence/display/Hive/LLAP) を使用します。 対話型クエリでは、格納されている大規模なデータセットに対する複雑なデータ ウェアハウス スタイルのクエリを対話形式で実行できます。 対話型クエリは、従来の Hive バッチ ジョブよりも、Hive クエリの実行速度が格段に速くなっています。 

> [!NOTE]  
> 対話型 Hive クエリは、[HDInsight 対話型クエリ](../interactive-query/apache-interactive-query-get-started.md) クラスターに接続している場合のみ実行できます。

Data Lake Tools for Visual Studio を使用して、Hive ジョブの内容を表示することもできます。 Data Lake Tools for Visual Studio は、特定の Hive ジョブを収集し、Yarn ログを表示できます。

**サーバー エクスプローラー** で、 **[Azure]**  >  **[HDInsight]** を選択し、クラスターを選択します。  このノードが、次に進むセクションの **サーバー エクスプローラー** の開始点になります。

### <a name="view-hivesampletable"></a>hivesampletable を表示する

すべての HDInsight クラスターには、`hivesampletable` という名前の既定のサンプル Hive テーブルが付属します。  

クラスターから **[Hive データベース]**  >  **[既定]**  >  **[hivesampletable]** を選択します。

* `hivesampletable` スキーマを表示するには:

    **[hivesampletable]** を展開します。 `hivesampletable` 列の名前とデータ型が表示されます。

* `hivesampletable` データを表示するには:

    **[hivesampletable]** を右クリックし、 **[上位 100 行を表示]** を選択します。 100 件の結果の一覧が **[Hive テーブル: hivesampletable]** ウィンドウに表示されます。 このアクションは、Hive ODBC ドライバーを使用して、次の Hive クエリを実行することと同等です。

    `SELECT * FROM hivesampletable LIMIT 100`

    行数をカスタマイズするには、 **[行数]** を変更します。ドロップダウン リストから、50、100、200、または 1000 行を選択できます。

### <a name="create-hive-tables"></a>Hive テーブルの作成

Hive テーブルを作成するには、GUI または Hive クエリを使用できます。 Hive クエリの使用方法の詳細については、「[Hive クエリの作成と実行](#create-and-run-hive-queries)」を参照してください。

1. クラスターから、 **[Hive データベース]**  >  **[既定]** を選択します。

2. **[既定]** を右クリックし、 **[テーブルの作成]** を選択します。

3. テーブルを構成します。

4. **[テーブルの作成]** ボタンを選択して、新しい Hive テーブルを作成するジョブを送信します。

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png" alt-text="[テーブルの作成] ウィンドウ、Hive、HDInsight クラスター、Visual Studio" border="true":::

### <a name="create-and-run-hive-queries"></a>Hive クエリの作成と実行

Hive クエリを作成して実行するためのオプションは 2 つあります。

* アドホック クエリを作成する
* Hive アプリケーションを作成する

#### <a name="create-an-ad-hoc-query"></a>アドホック クエリを作成する

アドホック クエリを作成して実行するには:

1. クエリを実行するクラスターを右クリックし、 **[Hive クエリの作成]** を選択します。  

2. Hive クエリを入力します。

    Hive エディターは IntelliSense をサポートしています。 Data Lake Tools for Visual Studio では、Hive スクリプトの編集時にリモート メタデータの読み込みをサポートします。 たとえば、`SELECT * FROM` と入力すると、IntelliSense によってテーブル名の候補が一覧表示されます。 テーブル名を指定すると、Intellisense によって列名が一覧表示されます。 このツールは、Hive の DML ステートメント、サブクエリ、および組み込みの UDF の大半をサポートします。

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png" alt-text="IntelliSense の例 1、Hive アドホック クエリ、HDInsight クラスター、Visual Studio" border="true":::

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png" alt-text="IntelliSense の例 2、Hive アドホック クエリ、HDInsight クラスター、Visual Studio" border="true":::

    > [!NOTE]  
    > IntelliSense は、HDInsight のツール バーで選択されているクラスターのメタデータのみを推奨します。

    使用できるクエリの例を次に示します。

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. 実行モードを選択します。

    * **対話**  

        最初のドロップダウン リストで、 **[対話型]** を選択し、 **[実行]** を選択します。

        :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png" alt-text="対話モード、Hive アドホック クエリ、HDInsight クラスター、Visual Studio" border="true":::  

    * **Batch**  

        最初のドロップダウン リストで、 **[バッチ]** を選択し、 **[送信]** を選択します。 または、 **[送信]** の横にあるドロップダウン アイコンを選択し、 **[詳細]** を選択します。

        :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png" alt-text="バッチ モード、Hive アドホック クエリ、HDInsight クラスター、Visual Studio" border="true":::

        詳細送信オプションを選択すると、 **[スクリプトの送信]** ダイアログ ボックスが表示されます。 スクリプトの **[ジョブ名]** 、 **[引数]** 、 **[追加の構成]** 、および **[状態ディレクトリ]** を構成します。

        :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png" alt-text="[スクリプトの送信] ダイアログ ボックス、Hive アドホック クエリ、HDInsight クラスター、Visual Studio" border="true":::

      > [!NOTE]  
      > 対話型クエリ クラスターにはバッチを送信できません。  対話モードを使用する必要があります。

#### <a name="create-a-hive-application"></a>Hive アプリケーションを作成する

Hive ソリューションを作成して実行するには:

1. メニュー バーで、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** を選択します。

2. **[新しいプロジェクトの作成]** ウィンドウで、検索ボックスを選択し、「**Hive**」と入力します。 次に **[Hive アプリケーション]** を選択し、 **[次へ]** を選択します。

3. **[新しいプロジェクトの構成]** ウィンドウで、 **[プロジェクト名]** を入力し、プロジェクトの **[場所]** を選択または作成し、 **[作成]** を選択します。

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png" alt-text="新しい Hive アプリケーション、新しいプロジェクトの構成ウィンドウ、HDInsight Visual Studio" border="true":::

4. **ソリューション エクスプローラー** で、**Script.hql** をダブルクリックしてスクリプトを開きます。

### <a name="view-job-summary-and-output"></a>ジョブの概要と出力の表示

ジョブの概要は、**バッチ** モードと **対話** モードとで若干異なります。

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png" alt-text="Hive ジョブの概要ウィンドウ、バッチと対話モード、Visual Studio" border="true":::

**[更新]** アイコンを使用して、ジョブのステータスが **[完了]** に変更されるまで、ステータスを更新します。  

* **バッチ** モードのジョブの詳細については、下部にあるリンクを選択して **[ジョブ クエリ]** 、 **[ジョブ出力]** 、 **[ジョブのログ]** を表示するか、 **[Yarn ログの表示]** に移動します。

* **対話** モードのジョブ詳細については、 **[出力]** ペインおよび **[HiveServer2 出力]** ペインを参照してください。

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png" alt-text="Hive 対話型ジョブの出力、HDInsight クラスター、Visual Studio" border="true":::

### <a name="view-job-graph"></a>ジョブ グラフの表示

現在、ジョブ グラフは Tez を実行エンジンとして使用する Hive ジョブの場合のみ表示されます。  Tez を有効にする方法の詳細については、「[Azure HDInsight における Apache Hive と HiveQL](hdinsight-use-hive.md)」を参照してください。  「[Map Reduce の代わりに Apache Tez を使用する](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce)」も参照してください。  

頂点内のすべての演算子を表示するには、ジョブ グラフの頂点をダブルクリックします。 特定の演算子をポイントして、演算子の詳細を表示することもできます。

Tez アプリケーションが起動されていない場合は、実行エンジンとして Tez を指定してもジョブ グラフが表示されないことがあります。  この状況は、ジョブに DML ステートメントが含まれていないために発生する可能性があります。 または、DML ステートメントが Tez アプリケーションを起動せずに戻る可能性があるためです。 たとえば、`SELECT * FROM table1` では Tez アプリケーションが起動されません。

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png" alt-text="Apache Hive ジョブ グラフ、Visual Studio" border="true":::

### <a name="view-task-execution-detail"></a>タスク実行の詳細を表示する

ジョブ グラフから **[タスク実行の詳細]** を選択して、構造化および視覚化された Hive ジョブの情報を取得できます。 さらにジョブの詳細を取得することもできます。 パフォーマンスの問題が発生した場合に、このビューを使用して、問題の詳細を取得できます。 たとえば、各タスクの動作の情報と、各タスクの詳細情報 (データの読み取り/書き込み、開始時刻/終了時刻のスケジュールなど) を取得できます。 これらの情報を使用して、視覚化された情報に基づくジョブの構成やシステム アーキテクチャを調整します。

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png" alt-text="タスク実行ビュー ウィンドウ、Data Lake Visual Studio Tools" border="true":::

### <a name="view-hive-jobs"></a>Hive ジョブの表示

Hive ジョブのジョブ クエリ、ジョブ出力、ジョブのログ、Yarn ログを表示できます。

最新版のツールでは、Yarn ログを収集して表示することで、Hive ジョブの内容を確認できます。 Yarn ログは、パフォーマンス問題の検証に役立ちます。 HDInsight で Yarn ログを収集する方法の詳細については、[Apache Hadoop YARN アプリケーション ログへのアクセス](../hdinsight-hadoop-access-yarn-app-logs-linux.md)に関する記事を参照してください。

Hive ジョブを表示するには:

1. HDInsight クラスターを右クリックし、 **[ジョブの表示]** を選択します。

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png" alt-text="ジョブの表示、Apache Hive、HDInsight クラスター、Visual Studio" border="true":::

    クラスターで実行された Hive ジョブの一覧が表示されます。  

2. ジョブを選択します。 **[Hive ジョブの概要]** ウィンドウで、次のいずれかのリンクを選択します。
    - **ジョブ クエリ**
    - **ジョブの出力**
    - **ジョブのログ**  
    - **Yarn ログ**

## <a name="run-apache-pig-scripts"></a>Apache Pig スクリプトの実行

1. メニュー バーで、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** を選択します。

2. **[スタート]** ウィンドウで、検索ボックスを選択し、「**Pig**」と入力します。 次に、**Pig Application** を選択し、 **[次へ]** を選択します。

3. **[新しいプロジェクトの構成]** ウィンドウで、 **[プロジェクト名]** を入力し、プロジェクトの **[場所]** を選択または作成します。 **[作成]** を選択します。

4. IDE **ソリューション エクスプローラー** ペインで、**Script.pig** をダブルクリックしてスクリプトを開きます。

## <a name="feedback-and-known-issues"></a>フィードバックと既知の問題

* null 値で始まる結果が表示されない問題は修正されています。 この問題が発生している場合は、サポート チームに問い合わせてください。

* ユーザーのローカルのリージョン設定によっては、Visual Studio によって作成された HQL スクリプトがエンコードされます。 このスクリプトをバイナリ ファイルとしてクラスターにアップロードした場合、スクリプトは正しく実行されません。

## <a name="next-steps"></a>次のステップ

この記事では、Data Lake Tools for Visual Studio パッケージを使用して、Visual Studio から HDInsight クラスターに接続する方法を説明しました。 Hive クエリを実行する方法についても説明しました。 

* [Data Lake Tools for Visual Studio を使用して Apache Hive クエリを実行する](apache-hadoop-use-hive-visual-studio.md)
* [Azure HDInsight における Apache Hive と HiveQL](hdinsight-use-hive.md)
* [Apache Hadoop クラスターの作成 - テンプレート](apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight で Apache Hadoop ジョブを送信する](submit-apache-hadoop-jobs-programmatically.md)
* [HDInsight で Apache Hive と Apache Hadoop を使用して Twitter データを分析する](../hdinsight-analyze-twitter-data-linux.md)