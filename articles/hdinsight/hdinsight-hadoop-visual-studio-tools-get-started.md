---
title: "Data Lake (HDInsight) Tools for Visual Studio を使用する方法 | Microsoft Docs"
description: "Data Lake (HDInsight) Tools for Visual Studio をインストールして、Hadoop クラスターに接続し、Hive クエリを実行する方法について説明します。"
keywords: "Hadoop ツール, Hive クエリ, Visual Studio"
services: HDInsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ce9c572a-1e98-46bf-9581-13a9767f1fa5
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/07/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 8bea5c13b8657be737cdc135b2d46bd0068b6b88
ms.lasthandoff: 03/07/2017


---
# <a name="get-started-using-azure-data-lake-hdinsight-tools-for-visual-studio-to-run-a-hive-query"></a>Azure Data Lake (HDInsight) Tools for Visual Studio を使用して Hive クエリを実行する
Data Lake (HDInsight) Tools for Visual Studio を使用して HDInsight クラスターに接続し、Hive クエリを送信する方法について説明します。 HDInsight の使用に関する詳細については、[HDInsight の概要][hdinsight.introduction]と [HDInsight の使用][hdinsight.get.started]に関するページを参照してください。 Storm クラスターへの接続に関する詳細については、[Visual Studio を使用した HDInsight での Apache Storm の C# トポロジの開発][hdinsight.storm.visual.studio.tools]に関するページを参照してください。

Data Lake Tools for Visual Studio は、Data Lake Analytics と HDInsight の両方へのアクセスに使用できます。  Data Lake Tools の詳細については、「[チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md)」を参照してください。

**前提条件**

このチュートリアルを完了して、Visual Studio で Data Lake Tools を使用するには、次が必要になります。

* Azure HDInsight クラスター: 作成方法については、[Linux ベースの HDInsight を使用する方法](hdinsight-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。
* 次のソフトウェアを搭載したワークステーション
  
  * Windows 10、Windows 8.1、Windows 8、Windows 7
  * Visual Studio 2013/2015/2017
    
    > [!NOTE]
    > 現時点では Data Lake Tools for Visual Studio は英語版のみになります。
    > 
    > 

## <a name="install-data-lake-tools-for-visual-studio"></a>Data Lake Tools for Visual Studio のインストール

Visual Studio 2017 では、Data Lake Tools が既定でインストールされています。 それ以前のバージョンでは、[Web Platform Installer](https://www.microsoft.com/web/downloads/) を使用してインストールできます。 お使いの Visual Studio バージョンに対応するものを選択する必要があります。 Visual Studio がインストールされていない場合は、[Web Platform Installer](https://www.microsoft.com/web/downloads/) を使用して、最新の Visual Studio Community と Azure SDK をインストールできます。

![Data Lake Tools for Visual Studio Web Platform installer][1]

## <a name="connect-to-azure-subscriptions"></a>Azure サブスクリプションに接続する
Data Lake Tools for Visual Studio を使用して、HDInsight クラスターへの接続、いくつかの基本的な管理操作の実行、Hive クエリの実行が可能です。

> [!NOTE]
> 汎用の Hadoop クラスターに接続する方法の詳細については、「 [Visual Studio を使用した Hive クエリの書き込みと送信 (ブログの投稿)](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx)」をご覧ください。
> 
> 

**Azure サブスクリプションに接続するには**

1. Visual Studio を開きます。
2. **[ビュー]** メニューで、**[サーバー エクスプローラー]** をクリックして、サーバー エクスプローラー ウィンドウを開きます。
3. **[Azure]** を展開して、**[HDInsight]** を展開します。
   
   > [!NOTE]
   > **[HDInsight タスク一覧]** ウィンドウが開きます。 ウィンドウが表示されない場合は、**[ビュー]** メニューで **[その他のウィンドウ]** をクリックし、次に **[HDInsight タスク一覧ウィンドウ]** をクリックします。  
   > 
   > 
4. Azure サブスクリプションの資格情報を入力し、 **[サインイン]**をクリックします。 この操作は、このワークステーションで、まだ一度も Visual Studio から Azure サブスクリプションに接続していない場合にのみ必要です。
5. サーバー エクスプローラーに、既存の HDInsight クラスターの一覧が表示されます。 クラスターが&1; つもない場合は、Azure ポータル、Azure PowerShell、または HDInsight SDK を使用して作成できます。 詳細については、[HDInsight クラスターの作成][hdinsight-create-clusters]に関するページを参照してください。
   
   ![Data Lake Tools for Visual Studio サーバー エクスプローラー クラスターの一覧][5]
6. HDInsight クラスターを展開します。 **Hive データベース**、既定のストレージ アカウント、リンクされたストレージ アカウント、**Hadoop サービス ログ**が表示されます。 さらに、エンティティを展開できます。

Azure サブスクリプションに接続した後で、次を実行できます。

**Visual Studio から Azure ポータルに接続するには**

* サーバー エクスプローラーで、**[Azure]**、 > **[HDInsight]** の順に展開し、[HDInsight クラスター] を右クリックして、**[Manage Cluster in Azure portal (Azure Portal でのクラスターの管理)]** をクリックします。

**Visual Studio から質問をしたりフィードバックを提供したりするには**

* **[ツール]** メニューで、**[HDInsight]**、**[MSDN フォーラム]** の順にクリックして質問するか、**[フィードバックの送信]** をクリックします。

## <a name="navigate-the-linked-resources"></a>リンクしているリソースへの移動
サーバー エクスプローラーで、既定のストレージ アカウント、すべてのリンクされたストレージ アカウントを確認できます。 既定のストレージ アカウントを展開すると、そのストレージ アカウントのコンテナーを表示できます。 既定のストレージ アカウントと既定のコンテナーがマークされます。 コンテナーのいずれかの右クリックしてコンテンツを表示できます。

![Data Lake Tools for Visual Studio サーバー エクスプローラー クラスターの一覧][2]

コンテナーを開くと、次のボタンを使用して、BLOB をアップロード、削除、およびダウンロードすることができます。

![Data Lake Tools for Visual Studio サーバー エクスプローラーでの BLOB 操作](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png)

## <a name="run-a-hive-query"></a>Hive クエリを実行する
[Apache Hive][apache.hive] は Hadoop に構築されるデータ ウェアハウス基盤であり、データを集約、照会、分析できます。 Data Lake Tools for Visual Studio は Visual Studio からの Hive クエリの実行をサポートします。 Hive の詳細については、[HDInsight での Hive の使用][hdinsight.hive]に関するページを参照してください。

HDInsight クラスターに対して Hive スクリプトをテストするには、 数分以上かかる場合があります。 Data Lake Tools for Visual Studio では、ライブ クラスターに接続しなくても、Hive スクリプトをローカルで検証できます。

加えて、Data Lake Tools for Visual Studio では、特定の Hive ジョブの YARN ログを収集して表示することで、Hive ジョブの内容を見ることができます。

### <a name="view-the-hivesampletable"></a>**hivesampletable**
すべての HDInsight クラスターには、 *hivesampletable*という Hive テーブルのサンプルが付属します。 このテーブルを使用して Hive テーブルの一覧表示、テーブル スキーマの表示、Hive テーブル内の行の一覧表示を行う方法を説明します。

**Hive テーブルを一覧表示し、Hive テーブル スキーマを表示するには**

1. **サーバー エクスプローラー**から、**[Azure]**、 > **[HDInsight]**、任意のクラスター、**[Hive データベース]**、 > **[既定]**、 > **[hivesampletable]** の順に展開し、テーブル スキーマを表示します。
2. **[hivesampletable]** を右クリックし、**[上位 100 行を表示]** をクリックして、行を一覧表示します。 これは、Hive ODBC ドライバーを使用して、次の Hive クエリを実行することと同等です。
   
     SELECT * FROM hivesampletable LIMIT 100
   
   行カウントをカスタマイズできます。
   
   ![Data Lake Tools: HDInsight Hive Visual Studio スキーマ クエリ][6]

### <a name="create-hive-tables"></a>Hive テーブルの作成
GUI を使用して Hive テーブルを作成するか、Hive クエリを使用できます。 Hive クエリの使用については、 [Hive クエリの実行](#run.queries)をご覧ください。

**Hive テーブルを作成するには**

1. **サーバー エクスプローラー**から、**[Azure]**、 > **[HDInsight クラスター]**、HDInsight クラスター、**[Hive データベース]** の順に展開し、**[既定]** を右クリックして、**[テーブルの作成]** をクリックします。
2. テーブルを構成します。
3. **[テーブルの作成]** をクリックして、新しい Hive テーブルを作成するためのジョブを送信します。
   
    ![Data Lake Tools: HDInsight Visual Studio Tools で Hive テーブルを作成する][7]

### <a name="run.queries"></a>Hive クエリの検証と実行
Hive クエリを作成して実行するには次の&2; つの方法があります。

* アドホック クエリを作成する
* Hive アプリケーションを作成する

**アドホック クエリを作成、検証、実行するには**

1. **サービス エクスプローラー**から、**[Azure]**、**[HDInsight クラスター]** の順に展開します。
2. クエリを実行するクラスターを右クリックして、 **[Hive クエリの作成]**をクリックします。
3. Hive クエリを入力します。 Hive エディターは Intellisense をサポートしています。 Data Lake Tools for Visual Studio で、Hive スクリプトの編集時にリモート メタデータの読み込みがサポートされます。 たとえば、"SELECT * FROM" と入力すると、Intellisense には推奨されるテーブル名が一覧表示されます。 テーブル名を指定すると、列名が Intellisense に一覧表示されます。 ツールは、ほとんどすべての Hive の DML ステートメント、サブクエリ、および組み込みの UDF をサポートします。
   
    ![Data Lake Tools: HDInsight Visual Studio Tools IntelliSense][13]
   
    ![Data Lake Tools: HDInsight Visual Studio Tools IntelliSense][14]
   
   > [!NOTE]
   > クラスターのメタデータのうち、HDInsight のツール バーで選択したものだけが推奨として表示されます。
   > 
   > 
4. (任意) **[Validate Script]** をクリックして、スクリプトの構文エラーを確認します。
   
    ![Data Lake Tools: Data Lake Tools for Visual Studio ローカル検証][10]
5. **[送信]** または **[(高度な) 送信]** をクリックします。 高度な送信オプションの場合は、スクリプトの**ジョブ名**、**引数**、**追加の構成**、**ステータス ディレクトリ**を構成します。
   
    ![HDInsight Hadoop の Hive クエリ][9]
   
    ジョブを送信すると、 **[Hive ジョブの概要]** ウィンドウが表示されます。
   
    ![HDInsight Hadoop の Hive クエリの概要][8]
6. **[更新]** ボタンを使用して、ジョブのステータスが **[完了]** に変更されるまで、ステータスを更新します。
7. ウィンドウ下部のリンクをクリックして次の **[ジョブ クエリ]**、**[ジョブ出力]**、**[ジョブのログ]** または **[Yarn ログ]** を表示します。

**Hive ソリューションを作成し、実行するには**

1. **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。
2. 左側のウィンドウから **[HDInsight]** を選択し、中央のウィンドウで **[Hive アプリケーション]** を選択して、プロパティを入力し、**[OK]** をクリックします。
   
    ![Data Lake Tools: HDInsight Visual Studio Tools 新しい Hive プロジェクト][11]
3. **ソリューション エクスプローラー**で、**Script.hql** をダブルクリックして開きます。
4. Hive スクリプトを検証するには、**[Validate Script]** ボタンをクリックするか、Hive エディターでスクリプトを右クリックしてコンテキスト メニューから **[Validate Script]** をクリックします。

### <a name="view-hive-jobs"></a>Hive ジョブの表示
Hive ジョブのジョブ クエリ、ジョブ出力、ジョブのログ、Yarn ログを表示できます。 詳細については、先のスクリーンショットをご覧ください。

最新版のツールでは、YARN ログを収集して表示することで、Hive ジョブの内容を確認できます。 YARN ログは、パフォーマンス問題の検証に役立ちます。 HDInsight での YARN ログの収集に関する詳細については、[プログラムによる HDInsight アプリケーション ログへのアクセス][hdinsight.access.application.logs]に関するページを参照してください。

**Hive ジョブの表示**

1. **サーバー エクスプローラー**から、**[Azure]**、**[HDInsight]** の順に展開します。
2. HDInsight クラスターを右クリックし、 **[ジョブの表示]**をクリックします。 クラスター上で実行した Hive ジョブの一覧が表示されます。
3. ジョブの一覧でジョブをクリックして選択し、**[Hive ジョブの概要]** ウィンドウを使用して **[ジョブ クエリ]**、**[ジョブ出力]**、**[ジョブのログ]**、または **[Yarn ログ]** を開きます。
   
    ![Data Lake Tools: HDInsight Visual Studio ツールで新しい Hive ジョブを表示する][12]

### <a name="faster-path-hive-execution-via-hiveserver2"></a>HiveServer2 による Hive 実行の高速化
> [!NOTE]
> この機能は、HDInsight クラスター バージョン 3.2 以降のみで動作します。
> 
> 

Data Lake Tools は以前、[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (Templeton とも呼ばれます) を介して Hive ジョブを送信していました。 そのためジョブの詳細やエラー情報を返すのに長い時間がかかっていました。
このようなパフォーマンスの問題を解決するために、Data Lake Tools では、HiveServer2 を通して直接クラスターで Hive ジョブを実行することにより RDP/SSH をバイパスすることができるようになりました。
パフォーマンスが向上するだけでなく、ユーザーは、Hive on Tez のグラフやタスクの詳細を表示することもできます。

HDInsight クラスター バージョン 3.2 以降では、**[HiveServer2 から実行]** ボタンが表示されます。

![hiveserver2 を使用した Data Lake Visual Studio ツールの実行](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png)

ログがリアルタイムでストリーム バックしていることを確認できます。また、Hive クエリが Tez で実行されている場合はジョブ グラフも確認できます。

![Data Lake Visual Studio ツールの高速パスでの Hive 実行](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png)

**HiveServer2 を使用したクエリの実行と WebHCat を使用したクエリの送信の違い**

HiveServer2 を使用してクエリを実行した場合、パフォーマンス上の利点は多数ありますが、いくつかの制限もあります。 制限の一部は、運用環境での使用に適していません。 その違いを次の表に示します。

|  | HiveServer2 を使用して実行した場合 | WebHCat を使用して送信した場合 |
| --- | --- | --- |
| クエリの実行 |WebHCat のオーバーヘッドが解消されます (これにより、"TempletonControllerob" という名前の MapReduce ジョブが開始されます)。 |クエリが WebHCat を使用して実行される間、WebHCat によって MapReduce ジョブが開始されるため、待機時間がさらに長くなります。 |
| ログの再ストリーミング |ほぼリアルタイムで行われます。 |ジョブの実行ログを利用できるのは、ジョブが完了したときのみです。 |
| ジョブ履歴の表示 |HiveServer2 を使用してクエリを実行する場合、そのジョブ履歴 (ジョブのログ、ジョブの出力) は保持されません。 アプリケーションは YARN UI で表示できますが、情報は限定的です。 |WebHCat を使用してクエリを実行する場合、そのジョブ履歴 (ジョブのログ、ジョブの出力) は保持され、Visual Studio、HDInsight SDK、PowerShell を使用して表示できます。 |
| ウィンドウを閉じる |HiveServer2 を使用した実行は "同期的な" 方法であるため、ウィンドウを開いたままにしておく必要があります。ウィンドウを閉じると、クエリの実行は取り消されます。 |WebHCat を使用した送信は "非同期的な" 方法であるため、WebHCat を使用してクエリを送信し、Visual Studio を終了することができます。 結果は、いつでも戻って確認できます。 |

### <a name="tez-hive-job-performance-graph"></a>Tez Hive ジョブのパフォーマンス グラフ
Data Lake Tools for Visual Studio は Tez 実行エンジンで実行された Hive ジョブのパフォーマンス グラフの表示をサポートしています。 Tez を有効にする方法については、[HDInsight での Hive の使用][hdinsight.hive]に関するページを参照してください。 Visual Studio で Hive ジョブを送信した後、ジョブが完了すると、Visual Studio にグラフが表示されます。  最新のジョブの状態を取得するには、 **[更新]** ボタンをクリックする必要があります。

> [!NOTE]
> この機能は、3.2.4.593 以上のバージョンの HDInsight クラスターでのみ使用でき、完了したジョブに対してのみ機能します (WebHCat を使用してジョブを送信した場合。このグラフは HiveServer2 を使用してクエリを実行した場合に表示されます)。 これは、Windows ベースと Linux ベースの両方のクラスターで機能します。
> 
> 

![hadoop hive tez パフォーマンス グラフ](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png)

Hive クエリを理解しやすくするために、今回のリリースでツールに Hive 演算子表示機能が追加されました。 ジョブ グラフの頂点をダブルクリックするだけで、頂点内のすべての演算子を表示できます。 特定の演算子の上にマウス ポインターを置きことで、その演算子の詳細を表示することもできます。

### <a name="task-execution-view-for-hive-on-tez-jobs"></a>Hive on Tez ジョブのタスク実行ビュー
Hive on Tez ジョブのタスク実行ビューは、Hive ジョブの構造化および視覚化された情報の取得と、ジョブの詳細の取得に使用できます。 パフォーマンスの問題が存在する場合は、このビューを使用して、さらなる詳細を確認することができます。 たとえば、各タスクの動作や各タスクの詳細 (読み取られた/書き込まれたデータ、スケジュール時刻/開始時刻/終了時刻など) を確認できるため、視覚化された情報に基づいて、ジョブ構成やシステム アーキテクチャを調整できます。

![Data Lake Visual Studio Toolsの タスク実行ビュー](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png)

## <a name="run-pig-scripts"></a>Pig のスクリプトを実行する
Data Lake Tools for Visual Studio は、Pig スクリプトの作成と、HDInsight クラスターへの送信をサポートしています。 ユーザーは、テンプレートから Pig プロジェクトを作成して、HDInsight クラスターにスクリプトを送信できます。

## <a name="feedbacks--known-issues"></a>フィードバックと既知の問題
* 現在 HiveServer2 の結果は、ピュア テキスト形式で表示され、これは最適とは言えません。 Microsoft では、この問題の解決に取り組んでいます。
* 結果が NULL 値で始まっている場合、現在その結果は表示されません。 この問題は修正されており、この問題で支障が出ている場合は、電子メールでお知らせいただくか、サポート チームにお問い合わせください。
* ユーザーのローカルのリージョン設定によっては、Visual Studio によって作成された HQL スクリプトがエンコードされます。 ユーザーがスクリプトをバイナリとしてクラスターにアップロードする場合、これは正常に実行されないことがあります。

## <a name="next-steps"></a>次のステップ
この記事では、Data Lake (HDInsight) Tools パッケージを使用して Visual Studio から HDInsight クラスターに接続し、Hive クエリを実行する方法を説明しました。 詳細については、次を参照してください。

* [HDInsight での Hadoop Hive の使用][hdinsight.hive]
* [HDInsight での Hadoop の使用][hdinsight.get.started]
* [HDInsight での Hadoop ジョブの送信][hdinsight.submit.jobs]
* [HDInsight での Hadoop を使用した Twitter データの分析][hdinsight.analyze.twitter.data]

<!--Anchors-->
[Installation]: #installation
[Connect to your Azure subscription]: #connect-to-your-azure-subscription
[Navigate the linked resources]: #navigate-the-linked-resources
[Run Hive queries]: #run-hive-queries
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png
[11]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png
[12]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png
[13]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png
[14]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png


<!--Link references-->
[hdinsight-create-clusters]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight.introduction]: hdinsight-hadoop-introduction.md
[hdinsight.get.started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight.hive]: hdinsight-use-hive.md
[hdinsight.submit.jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight.analyze.twitter.data]: hdinsight-analyze-twitter-data.md
[hdinsight.storm.visual.studio.tools]: hdinsight-storm-develop-csharp-visual-studio-topology.md
[hdinsight.access.application.logs]: hdinsight-hadoop-access-yarn-app-logs.md

[apache.hive]: http://hive.apache.org

