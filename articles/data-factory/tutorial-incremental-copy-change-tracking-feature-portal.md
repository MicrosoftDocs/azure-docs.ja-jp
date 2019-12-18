---
title: Change Tracking を使用してデータを増分コピーする
description: このチュートリアルでは、オンプレミスの SQL Server データベースにある複数のテーブルから Azure SQL データベースに差分データを増分コピーする Azure Data Factory パイプラインを作成します。
services: data-factory
ms.author: yexu
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/12/2018
ms.openlocfilehash: 1b619ca7bb3b095a5707077beb3e0750dee1c2b7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923478"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information"></a>変更追跡情報を使用して Azure SQL Database から Azure Blob Storage にデータを増分読み込みする 

このチュートリアルでは、ソース Azure SQL データベースから**変更追跡**情報に基づく差分データを Azure Blob Storage に読み込むパイプラインを使用して Azure Data Factory を作成します。  

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * ソース データ ストアを準備します。
> * データ ファクトリを作成します。
> * リンクされたサービスを作成します。 
> * ソース、シンク、変更追跡の各データセットを作成します。
> * フル コピー パイプラインを作成、実行、監視します。
> * ソース テーブルのデータを追加または更新します。
> * 増分コピー パイプラインを作成、実行、監視します。

## <a name="overview"></a>概要
データ統合ソリューションでは、初回データ読み込みの後、増分データを読み込む手法が広く利用されています。 ソース データ ストアから特定の期間に変更されたデータを簡単に切り出すことができる場合もあります (LastModifyTime、CreationTime など)。 一方、前回データを処理した時点からの差分データを明示的に特定する方法がない場合もあります。 こうした差分データは、Azure SQL Database や SQL Server などのデータ ストアでサポートされる Change Tracking テクノロジを使用して特定することができます。  このチュートリアルでは、Azure Data Factory と SQL Change Tracking テクノロジを使用して、Azure SQL Database から Azure Blob Storage に差分データを増分読み込みする方法について説明します。  SQL Change Tracking テクノロジに関するより具体的な情報については、[SQL Server における変更の追跡](/sql/relational-databases/track-changes/about-change-tracking-sql-server)に関するページを参照してください。 

## <a name="end-to-end-workflow"></a>エンド ツー エンド ワークフロー
ここでは、Change Tracking テクノロジを使用してデータを増分読み込みする一般的なエンド ツー エンドのワークフロー ステップを取り上げます。

> [!NOTE]
> Azure SQL Database と SQL Server は、どちらも Change Tracking テクノロジをサポートしています。 このチュートリアルでは、Azure SQL Database をソース データ ストアとして使用します。 オンプレミスの SQL Server を使用してもかまいません。 

1. **履歴データの初回読み込みを実行する** (1 回実行)。
    1. ソース Azure SQL データベースの Change Tracking テクノロジを有効にします。
    2. Azure SQL データベースから SYS_CHANGE_VERSION の初期値をベースラインとして取得し、変更済みデータをキャプチャします。
    3. Azure SQL データベースから Azure Blob Storage にフル データを読み込みます。 
2. **スケジュールに従って差分データの増分読み込みを実行する** (データの初回読み込み後に定期的に実行)。
    1. 新旧の SYS_CHANGE_VERSION 値を取得します。
    3. 差分データを読み込みます。これは、**sys.change_tracking_tables** から得られる変更済みの行 (2 つの SYS_CHANGE_VERSION 値の間にある行) の主キーと**ソース テーブル**内のデータとを結合した後、その差分データをターゲットに移動することによって行います。
    4. 次回の差分読み込みに備えて SYS_CHANGE_VERSION を更新します。

## <a name="high-level-solution"></a>ソリューションの概略
このチュートリアルでは、次の 2 つの操作を実行する 2 つのパイプラインを作成します。  

1. **初回読み込み:** ソース データ ストア (Azure SQL Database) からターゲット データ ストア (Azure Blob Storage) にデータ全体をコピーするコピー アクティビティを含んだパイプラインを作成します。

    ![データ全体の読み込み](media/tutorial-incremental-copy-change-tracking-feature-portal/full-load-flow-diagram.png)
1.  **増分読み込み:** 次のアクティビティを含んだパイプラインを作成して定期的に実行します。 
    1. **2 つのルックアップ アクティビティ**を作成します。これらのアクティビティによって Azure SQL Database から新旧の SYS_CHANGE_VERSION を取得してコピー アクティビティに渡すことになります。
    2. 2 つの SYS_CHANGE_VERSION 値の間に存在する挿入/更新/削除されたデータを Azure SQL Database から Azure Blob Storage にコピーする**コピー アクティビティを 1 つ**作成します。
    3. 次回のパイプライン実行に備えて SYS_CHANGE_VERSION の値を更新する**ストアド プロシージャ アクティビティを 1 つ**作成します。

    ![増分読み込みのフロー図](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-load-flow-diagram.png)


Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件
* **Azure SQL データベース**。 **ソース** データ ストアとして使うデータベースです。 Azure SQL データベースがない場合は、[Azure SQL データベースの作成](../sql-database/sql-database-get-started-portal.md)に関する記事に書かれている手順を参照して作成してください。
* **Azure Storage アカウント**。 **シンク** データ ストアとして使用する BLOB ストレージです。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「[ストレージ アカウントの作成](../storage/common/storage-quickstart-create-account.md)」を参照してください。 **adftutorial** という名前のコンテナーを作成します。 

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>Azure SQL データベースにデータ ソース テーブルを作成する
1. **SQL Server Management Studio** を起動し、Azure SQL Server に接続します。 
2. **サーバー エクスプローラー**で目的の**データベース**を右クリックして **[新しいクエリ]** を選択します。
3. Azure SQL データベースに対して次の SQL コマンドを実行し、`data_source_table` という名前のテーブルをソース データ ストアとして作成します。  
    
    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```
4. 次の SQL クエリを実行して、データベースとソース テーブル (data_source_table) の **Change Tracking** 機構を有効にします。 

    > [!NOTE]
    > - &lt;your database name&gt; は、data_source_table がある実際の Azure SQL データベースの名前に置き換えてください。 
    > - 現行の例では、変更済みのデータが 2 日間維持されます。 変更済みデータを読み込む間隔を 3 日おき、またはそれ以上にした場合、変更済みデータの一部が読み込まれません。  CHANGE_RETENTION の数値を増やす必要があります。 または、変更済みデータの読み込み間隔を必ず 2 日以内としてください。 詳細については、「[データベースの変更の追跡を有効にする](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database)」を参照してください。
 
    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  
  
    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. 新しいテーブルを作成し、ChangeTracking_version を既定値で格納するために、次のクエリを実行します。 

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```
    
    > [!NOTE]
    > SQL Database の変更追跡を有効にした後、データが変更されていなければ、変更追跡バージョンの値は 0 になります。
6. 次のクエリを実行して、Azure SQL データベースにストアド プロシージャを作成します。 このストアド プロシージャをパイプラインで呼び出すことによって、前の手順で作成したテーブルの変更追跡バージョンを更新します。 

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS
    
    BEGIN
    
        UPDATE table_store_ChangeTracking_version
        SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName
    
    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell のインストールと構成の方法](/powershell/azure/install-Az-ps)に関するページの手順に従って、最新の Azure PowerShell モジュールをインストールしてください。

## <a name="create-a-data-factory"></a>Data Factory の作成

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
1. 左側のメニューで、 **[リソースの作成]**  >  **[データ + 分析]**  >  **[Data Factory]** の順に選択します。 
   
   ![[新規] ウィンドウでの [Data Factory] の選択](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。 
      
     ![[新しいデータ ファクトリ] ページ](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更して (yournameADFTutorialDataFactory など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
       `Data factory name “ADFTutorialDataFactory” is not available`
3. データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
4. **[リソース グループ]** について、次の手順のいずれかを行います。
     
      - **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
      - **[新規作成]** を選択し、リソース グループの名前を入力します。   
         
        リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
4. **バージョン**として **[V2 (プレビュー)]** を選択します。
5. データ ファクトリの **場所** を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。
6. **[ダッシュボードにピン留めする]** をオンにします。     
7. **Create** をクリックしてください。      
8. ダッシュボードに、 **[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。 

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-incremental-copy-change-tracking-feature-portal/deploying-data-factory.png)
9. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
   ![データ ファクトリのホーム ページ](./media/tutorial-incremental-copy-change-tracking-feature-portal/data-factory-home-page.png)
10. **[Author & Monitor]\(作成と監視\)** タイルをクリックして、別のタブで Azure Data Factory ユーザー インターフェイス (UI) を起動します。
11. **開始**ページで、次の図に示すように、左パネルの **[編集]** タブに切り替えます。 

    ![[Create pipeline]\(パイプラインの作成\) ボタン](./media/tutorial-incremental-copy-change-tracking-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>リンクされたサービスを作成します
データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このセクションでは、Azure ストレージ アカウントと Azure SQL データベースに対するリンクされたサービスを作成します。 

### <a name="create-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する
この手順では、Azure ストレージ アカウントをデータ ファクトリにリンクします。

1. **[接続]** をクリックし、 **[+ 新規]** をクリックします。

   ![新しい接続ボタン](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-connection-button-storage.png)
2. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで **[Azure Blob Storage]** を選択し、 **[続行]** をクリックします。 

   ![[Azure Blob Storage] の選択](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-storage.png)
3. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。 

    1. **[名前]** に「**AzureStorageLinkedService**」と入力します。 
    2. **[ストレージ アカウント名]** で、使用する Azure ストレージ アカウントを選択します。 
    3. **[Save]** をクリックします。 
    
   ![Azure Storage アカウントの設定](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-database-linked-service"></a>Azure SQL Database のリンクされたサービスを作成する
この手順では、Azure SQL データベースをデータ ファクトリにリンクします。

1. **[接続]** をクリックし、 **[+ 新規]** をクリックします。
2. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで **[Azure SQL Database]** を選択し、 **[続行]** をクリックします。 
3. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。 

    1. **[名前]** フィールドに「**AzureSqlDatabaseLinkedService**」と入力します。 
    2. **[サーバー名]** フィールドで Azure SQL サーバーを選択します。
    4. **[データベース名]** フィールドで Azure SQL データベースを選択します。 
    5. **[ユーザー名]** フィールドにユーザーの名前を入力します。 
    6. **[パスワード]** フィールドに、ユーザーのパスワードを入力します。 
    7. **[テスト接続]** をクリックして接続をテストします。
    8. **[保存]** をクリックして、リンクされたサービスを保存します。 
    
       ![Azure SQL Database のリンクされたサービスの設定](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-sql-database-linked-service-settings.png)

## <a name="create-datasets"></a>データセットを作成する
この手順では、データのコピー元とコピー先、 SYS_CHANGE_VERSION の格納場所を表すデータセットを作成します。

### <a name="create-a-dataset-to-represent-source-data"></a>ソース データを表すデータセットを作成する 
この手順では、ソース データを表すデータセットを作成します。 

1. ツリー ビューで **[+]\(プラス記号\)** をクリックし、 **[データセット]** をクリックします。 

   ![[新しいデータセット] メニュー](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. **[Azure SQL Database]** を選択し、 **[完了]** をクリックします。 

   ![ソース データセットの種類 - Azure SQL Database](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-sql-database.png)
3. データセットを構成するための新しいタブが表示されます。 ツリー ビューにもデータセットが表示されます。 **[プロパティ]** ウィンドウで、データセットの名前を「**SourceDataset**」に変更します。

   ![ソース データセット名](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-name.png)    
4. **[接続]** タブに切り替えて、次の手順を実行します。 
    
    1. **[リンクされたサービス]** で **[AzureSqlDatabaseLinkedService]** を選択します。 
    2. **[テーブル]** で **[dbo].[data_source_table]** を選択します。 

   ![ソースの接続](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-connection.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>シンク データ ストアにコピーされるデータを表すデータセットを作成します。 
この手順では、ソース データ ストアからコピーされたデータを表すデータセットを作成します。 前提条件の 1 つとして adftutorial コンテナーを Azure Blob Storage に作成しました。 このコンテナーが存在しない場合は作成するか、または既存のコンテナーの名前に設定してください。 このチュートリアルでは、`@CONCAT('Incremental-', pipeline().RunId, '.txt')` という式を使って出力ファイル名が動的に生成されます。

1. ツリー ビューで **[+]\(プラス記号\)** をクリックし、 **[データセット]** をクリックします。 

   ![[新しいデータセット] メニュー](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. **[Azure Blob Storage]** を選択し、 **[完了]** をクリックします。 

   ![シンク データセットの種類 - Azure Blob Storage](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-type.png)
3. データセットを構成するための新しいタブが表示されます。 ツリー ビューにもデータセットが表示されます。 **[プロパティ]** ウィンドウで、データセットの名前を「**SinkDataset**」に変更します。

   ![シンク データセット - 名前](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-name.png)
4. プロパティ ウィンドウで **[接続]** タブに切り替え、以下の手順を実行します。

    1. **[リンクされたサービス]** で **[AzureStorageLinkedService]** を選択します。
    2. **filePath** の**フォルダー**部分として「**adftutorial/incchgtracking**」と入力します。
    3. **filePath** の**ファイル**部分として「 **\@CONCAT('Incremental-', pipeline().RunId, '.txt')** 」と入力します。  

       ![シンク データセット - 接続](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-connection.png)

### <a name="create-a-dataset-to-represent-change-tracking-data"></a>変更追跡データを表すデータセットを作成する 
この手順では、変更追跡バージョンを格納するためのデータセットを作成します。  前提条件の 1 つとして table_store_ChangeTracking_version テーブルを作成しました。

1. ツリー ビューで **[+]\(プラス記号\)** をクリックし、 **[データセット]** をクリックします。 
2. **[Azure SQL Database]** を選択し、 **[完了]** をクリックします。 
3. データセットを構成するための新しいタブが表示されます。 ツリー ビューにもデータセットが表示されます。 **[プロパティ]** ウィンドウで、データセットの名前を「**ChangeTrackingDataset**」に変更します。
4. **[接続]** タブに切り替えて、次の手順を実行します。 
    
    1. **[リンクされたサービス]** で **[AzureSqlDatabaseLinkedService]** を選択します。 
    2. **[テーブル]** で **[dbo].[table_store_ChangeTracking_version]** を選択します。 

## <a name="create-a-pipeline-for-the-full-copy"></a>完全コピーのパイプラインを作成する
この手順では、ソース データ ストア (Azure SQL Database) からターゲット データ ストア (Azure Blob Storage) にデータ全体をコピーするコピー アクティビティを含んだパイプラインを作成します。

1. 左ウィンドウで **[+]\(プラス記号\)** をクリックし、 **[パイプライン]** をクリックします。 

    ![新しいパイプライン メニュー](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu.png)
2. パイプラインを構成するための新しいタブが表示されます。 ツリー ビューにもパイプラインが表示されます。 **[プロパティ]** ウィンドウで、パイプラインの名前を「**FullCopyPipeline**」に変更します。

    ![新しいパイプライン メニュー](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-name.png)
3. **[アクティビティ]** ツールボックスで **[データ フロー]** を展開し、パイプライン デザイナー画面に **[コピー]** アクティビティをドラッグ アンド ドロップして、名前を「**FullCopyActivity**」に設定します。 

    ![完全コピー アクティビティ名](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-activity-name.png)
4. **[ソース]** タブに切り替えて、 **[Source Dataset]\(ソース データセット\)** フィールドで **[SourceDataset]** を選択します。 

    ![コピー アクティビティ - ソース](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-source.png)
5. **[シンク]** タブに切り替えて、 **[Sink Dataset]\(シンク データセット\)** フィールドで **[SinkDataset]** を選択します。 

    ![コピー アクティビティ - シンク](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-sink.png)
6. パイプライン定義を検証するために、ツール バーの **[検証]** をクリックします。 検証エラーがないことを確認します。 **[>>]** をクリックして、 **[Pipeline Validation Report]\(パイプライン検証レポート\)** を閉じます。 

    ![パイプラインを検証する](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-validate.png)
7. エンティティ (リンクされたサービス、データセット、およびパイプライン) を発行するには、 **[発行]** をクリックします。 発行が成功するまで待機します。 

    ![[発行] ボタン](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button.png)
8. **[正常に発行されました]** というメッセージが表示されるまで待機します。 

    ![発行は成功しました](./media/tutorial-incremental-copy-change-tracking-feature-portal/publishing-succeeded.png)
9. 左側の **[通知の表示]** ボタンをクリックして、通知を確認することもできます。 通知ウィンドウを閉じるには、 **[X]** をクリックします。

    ![通知の表示](./media/tutorial-incremental-copy-change-tracking-feature-portal/show-notifications.png)


### <a name="run-the-full-copy-pipeline"></a>フル コピー パイプラインを実行する
パイプラインのツール バーの **[トリガー]** をクリックし、 **[Trigger Now]\(今すぐトリガー\)** をクリックします。 

![[Trigger Now]\(今すぐトリガー\) メニュー](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu.png)

### <a name="monitor-the-full-copy-pipeline"></a>フル コピー パイプラインを監視する

1. 左側の **[監視]** タブをクリックします。 一覧にパイプラインの実行とその状態が表示されます。 一覧を更新するには、 **[最新の情報に更新]** をクリックします。 [アクション] 列のリンクを使用すると、パイプラインの実行に関連付けられているアクティビティの実行を表示し、パイプラインを再実行することができます。 

    ![パイプライン実行](./media/tutorial-incremental-copy-change-tracking-feature-portal/monitor-full-copy-pipeline-run.png)
2. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、 **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクをクリックします。 パイプライン内のアクティビティは 1 つだけであるため、一覧に表示されるエントリは 1 つのみです。 [Pipeline Runs]\(パイプラインの実行\) ビューに戻るには、上部の **[パイプライン]** リンクをクリックします。 

    ![アクティビティの実行](./media/tutorial-incremental-copy-change-tracking-feature-portal/activity-runs-full-copy.png)

### <a name="review-the-results"></a>結果の確認
`adftutorial` コンテナーの `incchgtracking` フォルダーに、`incremental-<GUID>.txt` という名前のファイルが確認できます。 

![フル コピーからの出力ファイル](media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-output-file.png)

このファイルには、Azure SQL データベースからのデータが存在します。

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>ソース テーブルにデータを追加する

Azure SQL データベースに次のクエリを実行して行の追加と更新を行います。 

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

``` 

## <a name="create-a-pipeline-for-the-delta-copy"></a>差分コピーのパイプラインを作成する
この手順では、次のアクティビティを含んだパイプラインを作成して定期的に実行します。 **ルックアップ アクティビティ**は、Azure SQL Database から新旧の SYS_CHANGE_VERSION を取得してコピー アクティビティに渡します。 **コピー アクティビティ**は、2 つの SYS_CHANGE_VERSION 値の間に存在する挿入/更新/削除されたデータを Azure SQL Database から Azure Blob Storage にコピーします。 **ストアド プロシージャ アクティビティ**は、次回のパイプライン実行に備えて SYS_CHANGE_VERSION の値を更新します。

1. Data Factory の UI で、 **[編集]** タブに切り替えます。左ウィンドウで **[+]\(プラス記号\)** をクリックし、 **[パイプライン]** をクリックします。 

    ![新しいパイプライン メニュー](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu-2.png)
2. パイプラインを構成するための新しいタブが表示されます。 ツリー ビューにもパイプラインが表示されます。 **[プロパティ]** ウィンドウで、パイプラインの名前を「**IncrementalCopyPipeline**」に変更します。

    ![パイプライン名](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-pipeline-name.png)
3. **[アクティビティ]** ツール ボックスの **[General]\(一般\)** を展開し、 **[検索]** アクティビティをパイプライン デザイナー画面にドラッグ アンド ドロップします。 アクティビティの名前を「**LookupLastChangeTrackingVersionActivity**」に設定します。 このアクティビティは、直前のコピー操作で使用された変更追跡バージョンを取得します。この値は、テーブル **table_store_ChangeTracking_version** に格納されています。

    ![検索アクティビティ - 名前](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-name.png)
4. **[プロパティ]** ウィンドウで **[設定]** に切り替えて、 **[Source Dataset]\(ソース データセット\)** フィールドで **[ChangeTrackingDataset]** を選択します。 

    ![検索アクティビティ - 設定](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-settings.png)
5. **[アクティビティ]** ツールボックスからパイプライン デザイナー画面に **[検索]** アクティビティをドラッグ アンド ドロップします。 アクティビティの名前を「**LookupCurrentChangeTrackingVersionActivity**」に設定します。 このアクティビティは、現在の変更追跡バージョンを取得します。

    ![検索アクティビティ - 名前](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-name.png)
6. **[プロパティ]** ウィンドウで **[設定]** に切り替え、以下の手順を実行します。

   1. **[Source Dataset]\(ソース データセット\)** フィールドで **[SourceDataset]** を選択します。
   2. **[クエリの使用]** で **[クエリ]** を選択します。 
   3. **[クエリ]** に次の SQL クエリを入力します。 

       ```sql
       SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion
       ```

      ![検索アクティビティ - 設定](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-settings.png)
7. **[アクティビティ]** ツールボックスで **[データ フロー]** を展開し、パイプライン デザイナー画面に **[コピー]** アクティビティをドラッグ アンド ドロップします。 アクティビティの名前を「**IncrementalCopyActivity**」に設定します。 このアクティビティは、直前の変更追跡バージョンと現在の変更追跡バージョンとの間のデータをターゲット データ ストアにコピーします。 

    ![コピー アクティビティ - 名前](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-activity-name.png)
8. **[プロパティ]** ウィンドウで **[ソース]** タブに切り替え、以下の手順を実行します。

   1. **[Source Dataset]\(ソース データセット\)** で **[SourceDataset]** を選択します。 
   2. **[クエリの使用]** で **[クエリ]** を選択します。 
   3. **[クエリ]** に次の SQL クエリを入力します。 

       ```sql
       select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}
       ```
    
      ![コピー アクティビティ - ソースの設定](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-source-settings.png)
9. **[シンク]** タブに切り替えて、 **[Sink Dataset]\(シンク データセット\)** フィールドで **[SinkDataset]** を選択します。 

    ![コピー アクティビティ - シンクの設定](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-sink-settings.png)
10. 1 つずつ、**両方の検索アクティビティをコピー アクティビティに接続**します。 **[検索]** アクティビティにアタッチされている**緑**のボタンを **[コピー]** アクティビティにドラッグします。 

    ![検索アクティビティとコピー アクティビティを接続する](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-lookup-and-copy.png)
11. **[アクティビティ]** ツールボックスからパイプライン デザイナー画面に **[ストアド プロシージャ]** アクティビティをドラッグ アンド ドロップします。 アクティビティの名前を「**StoredProceduretoUpdateChangeTrackingActivity**」に設定します。 このアクティビティは、**table_store_ChangeTracking_version** テーブル内の変更追跡バージョンを更新します。

    ![ストアド プロシージャ アクティビティ - 名前](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-activity-name.png)
12. *[SQL Account\*]\(SQL アカウント\)* タブに切り替えて、 **[リンクされたサービス]** で **[AzureSqlDatabaseLinkedService]** を選択します。 

    ![ストアド プロシージャ アクティビティ - SQL アカウント](./media/tutorial-incremental-copy-change-tracking-feature-portal/sql-account-tab.png)
13. **[ストアド プロシージャ]** タブに切り替えて、次の手順を実行します。 

    1. **[ストアド プロシージャ名]** に **[Update_ChangeTracking_Version]** を選択します。  
    2. **[Import parameter]\(インポート パラメーター\)** を選択します。 
    3. **[ストアド プロシージャのパラメーター]** セクションで、各パラメーターに次の値を指定します。 

        | 名前 | 種類 | 値 | 
        | ---- | ---- | ----- | 
        | CurrentTrackingVersion | Int64 | @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion} | 
        | TableName | string | @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName} | 
    
        ![ストアド プロシージャ アクティビティ - パラメーター](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-parameters.png)
14. **コピー アクティビティをストアド プロシージャ アクティビティに接続します**。 コピー アクティビティにアタッチされている**緑**のボタンをストアド プロシージャ アクティビティにドラッグ アンド ドロップします。 

    ![コピー アクティビティとストアド プロシージャ アクティビティを接続する](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-copy-stored-procedure.png)
15. ツール バーの **[検証]** をクリックします。 検証エラーがないことを確認します。 **[>>]** をクリックして、 **[Pipeline Validation Report]\(パイプライン検証レポート\)** ウィンドウを閉じます。 

    ![検証ボタン](./media/tutorial-incremental-copy-change-tracking-feature-portal/validate-button.png)
16. **[すべて公開]** ボタンをクリックして、エンティティ (リンクされたサービス、データセット、およびパイプライン) を Data Factory サービスに発行します。 **[発行は成功しました]** というメッセージが表示されるまで待機します。 

       ![[発行] ボタン](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button-2.png)    

### <a name="run-the-incremental-copy-pipeline"></a>増分コピー パイプラインを実行する
1. パイプラインのツール バーの **[トリガー]** をクリックし、 **[Trigger Now]\(今すぐトリガー\)** をクリックします。 

    ![[Trigger Now]\(今すぐトリガー\) メニュー](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu-2.png)
2. **[Pipeline Run]\(パイプラインの実行\)** ウィンドウで **[完了]** を選択します。

### <a name="monitor-the-incremental-copy-pipeline"></a>増分コピー パイプラインを監視する
1. 左側の **[監視]** タブをクリックします。 一覧にパイプラインの実行とその状態が表示されます。 一覧を更新するには、 **[最新の情報に更新]** をクリックします。 **[アクション]** 列のリンクを使用すると、パイプラインの実行に関連付けられているアクティビティの実行を表示し、パイプラインを再実行することができます。 

    ![パイプライン実行](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-pipeline-runs.png)
2. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、 **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクをクリックします。 パイプライン内のアクティビティは 1 つだけであるため、一覧に表示されるエントリは 1 つのみです。 [Pipeline Runs]\(パイプラインの実行\) ビューに戻るには、上部の **[パイプライン]** リンクをクリックします。 

    ![アクティビティの実行](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-activity-runs.png)


### <a name="review-the-results"></a>結果の確認
`adftutorial` コンテナーの `incchgtracking` フォルダーにもう 1 つファイルが確認できます。 

![増分コピーからの出力ファイル](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-output-file.png)

このファイルに含まれているのは、Azure SQL データベースからの差分データのみです。 `U` と記録されているレコードはデータベース内の更新された行で、`I` は追加された行です。 

```
1,update,10,2,U
6,new,50,1,I
```
最初の 3 つの列は、data_source_table 内の変更済みデータです。 最後の 2 つの列は、変更追跡システム テーブルからのメタデータです。 4 つ目の列は、変更済みの各行の SYS_CHANGE_VERSION です。 5 つ目の列は実行された操作で、U は更新 (Update) を、I は挿入 (Insert) を表します。  変更追跡情報の詳細については、「[CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql)」を参照してください。 

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10     2                     U
6        new     50     1                     I
```

    
## <a name="next-steps"></a>次の手順
次のチュートリアルに進んで、LastModifiedDate に基づいて新規ファイルおよび変更されたファイルのみをコピーする方法について学習してください。

> [!div class="nextstepaction"]
>[lastmodifieddate を基準に新しいファイルをコピーする](tutorial-incremental-copy-lastmodified-copy-data-tool.md)



