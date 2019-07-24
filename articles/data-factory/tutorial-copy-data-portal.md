---
title: Azure Portal を使用して Data Factory パイプラインを作成する | Microsoft Docs
description: このチュートリアルでは、Azure Portal を使用してパイプラインを備えたデータ ファクトリを作成するための詳細な手順について説明します。 パイプラインでコピー アクティビティを使用して、Azure Blob Storage から SQL データベースにデータをコピーします。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: 38c9c97af0be77bf9ad4bea2d24676c7448b3aea
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447557"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Blob Storage から SQL データベースにデータをコピーする
このチュートリアルでは、Azure Data Factory ユーザー インターフェイス (UI) を使用してデータ ファクトリを作成します。 このデータ ファクトリのパイプラインでは、Azure Blob Storage から SQL データベースにデータをコピーします。 このチュートリアルの構成パターンは、ファイルベースのデータ ストアからリレーショナル データ ストアへのコピーに適用されます。 ソースおよびシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する表を参照してください。

> [!NOTE]
> - Data Factory を初めて使用する場合は、「[Azure Data Factory の概要](introduction.md)」を参照してください。

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * コピー アクティビティを含むパイプラインを作成します。
> * パイプラインをテスト実行します。
> * パイプラインを手動でトリガーします。
> * スケジュールに基づいてパイプラインをトリガーします。
> * パイプラインとアクティビティの実行を監視します。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* **Azure ストレージ アカウント**。 Blob Storage を "*ソース*" データ ストアとして使用します。 ストレージ アカウントがない場合の作成手順については、[Azure のストレージ アカウントの作成](../storage/common/storage-quickstart-create-account.md)に関するページを参照してください。
* **Azure SQL データベース**。 データベースを "*シンク*" データ ストアとして使用します。 SQL データベースがない場合の作成手順については、「[SQL Database の作成](../sql-database/sql-database-get-started-portal.md)」を参照してください。

### <a name="create-a-blob-and-a-sql-table"></a>BLOB と SQL テーブルを作成する

ここからは、次の手順を実行して、チュートリアルで使用する Blob Storage と SQL データベースを準備します。

#### <a name="create-a-source-blob"></a>ソース BLOB を作成する

1. メモ帳を起動します。 次のテキストをコピーし、**emp.txt** ファイルとしてディスクに保存します。

    ```
    John,Doe
    Jane,Doe
    ```

1. Blob Storage に **adftutorial** という名前のコンテナーを作成します。 このコンテナーに **input** という名前のフォルダーを作成します。 次に、**input** フォルダーに **emp.txt** ファイルをアップロードします。 Azure Portal を使用するか、または [Azure Storage Explorer](https://storageexplorer.com/) などのツールを使用して、これらのタスクを実行します。

#### <a name="create-a-sink-sql-table"></a>シンク SQL テーブルを作成する

1. 次の SQL スクリプトを使用して、**dbo.emp** テーブルを SQL データベースに作成します。

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

1. Azure サービスに SQL Server へのアクセスを許可します。 Data Factory から SQL Server にデータを書き込むことができるように、SQL Server で **[Azure サービスへのアクセスを許可]** が**オン**になっていることを確認します。 この設定を確認して有効にするには、次の手順を実行します。

    a. 左側の **[その他のサービス]** > **[SQL Server]** を選択します。

    b. サーバーを選択し、**[設定]** の **[ファイアウォール]** を選択します。

    c. **[ファイアウォールの設定]** ページの **[Azure サービスへのアクセスを許可]** で **[オン]** を選択します。

## <a name="create-a-data-factory"></a>Data Factory を作成する。
この手順では、データ ファクトリを作成するほか、Data Factory UI を起動してそのデータ ファクトリにパイプラインを作成します。 

1. **Microsoft Edge** または **Google Chrome** を開きます。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
2. 左側のメニューで、**[リソースの作成]** > **[データ + 分析]** > **[Data Factory]** の順に選択します。 
  
   ![[新規] ウィンドウでの [Data Factory] の選択](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

3. **[新しいデータ ファクトリ]** ページで、**[名前]** に「**ADFTutorialDataFactory**」と入力します。 
      
     ![新しいデータ ファクトリ](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は *グローバルに一意*にする必要があります。 名前フィールドで次のエラー メッセージが発生した場合は、データ ファクトリの名前を変更してください (yournameADFTutorialDataFactory など)。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関するページを参照してください。
  
   ![エラー メッセージ](./media/tutorial-copy-data-portal/name-not-available-error.png)
4. データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
5. **[リソース グループ]** で、次の手順のいずれかを行います。
     
    a. **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。

    b. **[新規作成]** を選択し、リソース グループの名前を入力します。 
         
    リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。 
6. **[バージョン]** で、**[V2]** を選択します。
7. **[場所]** で、データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストア (Azure Storage、SQL Database など) やコンピューティング (Azure HDInsight など) は、他のリージョンに存在していてもかまいません。
8. **[ダッシュボードにピン留めする]** をオンにします。 
9. **作成**を選択します。 
10. ダッシュボードに、**[Deploying Data Factory]\(Data Factory をデプロイしています\)** というステータスを示した次のタイルが表示されます。 

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-copy-data-portal/deploying-data-factory.png)
1. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
    ![データ ファクトリのホーム ページ](./media/tutorial-copy-data-portal/data-factory-home-page.png)
1. **[Author & Monitor]\(作成と監視\)** を選択して、別のタブで Data Factory (UI) を起動します。

## <a name="create-a-pipeline"></a>パイプラインを作成する。
この手順では、コピー アクティビティが含まれたパイプラインをデータ ファクトリに作成します。 コピー アクティビティによって、Blob Storage から SQL Database にデータがコピーされます。 [クイックスタート チュートリアル](quickstart-create-data-factory-portal.md)では、次の手順でパイプラインを作成しました。

1. リンクされたサービスを作成します。 
1. 入力データセットと出力データセットを作成します。
1. パイプラインを作成します。

このチュートリアルでは、最初にパイプラインを作成します。 その後、パイプラインの構成に必要な場合にリンクされたサービスとデータセットを作成します。 

1. **[Let's get started]\(始めましょう\)** ページで **[Create pipeline]\(パイプラインの作成\)** を選択します。 

   ![パイプラインの作成](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
1. パイプラインの **[全般]** タブで、パイプラインの**名前**として「**CopyPipeline**」と入力します。

1. **[アクティビティ]** ツール ボックスで **[Move and Transform]\(移動と変換\)** カテゴリを展開し、ツール ボックスからパイプライン デザイナー画面に **[データのコピー]** アクティビティをドラッグ アンド ドロップします。 **[名前]** に「**CopyFromBlobToSql**」と指定します。

    ![コピー アクティビティ](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>ソースの構成

1. **[ソース]** タブに移動します。**[+ 新規]** を選択して、ソース データセットを作成します。 

1. **[新しいデータセット]** ウィンドウで **[Azure Blob Storage]** を選択し、**[完了]** を選択します。 ソース データは Blob Storage にあるので、ソース データセットには **Azure Blob Storage** を選択します。 

    ![Storage の選択](./media/tutorial-copy-data-portal/select-azure-blob-dataset.png)

1. BLOB データセット用の新しいタブが開きます。 **[プロパティ]** ウィンドウの下部にある **[全般]** タブで、**[名前]** に「**SourceBlobDataset**」と入力します。

    ![データセットの名前](./media/tutorial-copy-data-portal/dataset-name.png)

1. **[プロパティ]** ウィンドウの **[接続]** タブに移動します。 **[リンクされたサービス]** ボックスの横にある **[+ 新規]** をクリックします。 

    ![新しいリンクされたサービスのボタン](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)

1. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、名前として「**AzureStorageLinkedService**」と入力し、**[ストレージ アカウント名]** の一覧からご自身のストレージ アカウントを選択します。次に、**[保存]** を選択して、リンクされたサービスをデプロイします。

    ![新規のリンクされたサービス](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)

1. リンクされたサービスが作成されると、データセットの設定に戻ります。 **[ファイル パス]** の横にある **[参照]** を選択します。

    ![ファイル パスの [参照] ボタン](./media/tutorial-copy-data-portal/file-browse-button.png)

1. **adftutorial/input** フォルダーに移動し、**emp.txt** ファイルを選択して、**[完了]** を選択します。 

    ![入力ファイルの選択](./media/tutorial-copy-data-portal/select-input-file.png)

1. **[File format]\(ファイル形式\)** が **[Text format]\(テキスト形式\)** に設定されており、**[列区切り記号]** が **[コンマ (`,`)]** に設定されていることを確認します。 ソース ファイルで異なる行および列の区切り記号が使用されている場合、**[File format]\(ファイル形式\)** で **[Detect Text Format]\(テキスト形式の検出\)** を選択できます。 データのコピー ツールによって、ファイル形式と区切り記号が自動で検出されます。 これらの値はオーバーライドすることもできます。 このページのデータをプレビューするには、**[データのプレビュー]** を選択します。

    ![テキスト形式の検出](./media/tutorial-copy-data-portal/detect-text-format.png)

1. **[プロパティ]** ウィンドウの **[スキーマ]** タブに移動し、**[スキーマのインポート]** を選択します。 アプリケーションによってソース ファイル内の 2 つの列が検出されたことがわかります。 ソース データ ストアからシンク データ ストアに列をマップできるように、ここでスキーマをインポートします。 列をマップする必要がない場合、この手順はスキップしてかまいません。 このチュートリアルでは、スキーマをインポートします。

    ![ソース スキーマの検出](./media/tutorial-copy-data-portal/detect-source-schema.png)  

1. 次に、パイプライン -> **[ソース]** タブに戻り、**SourceBlobDataset** が選択されていることを確認します。 このページのデータをプレビューするには、**[データのプレビュー]** を選択します。 
    
    ![ソース データセット](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>シンクの構成

1. **[シンク]** タブに移動し、**[+ 新規]** を選択してシンク データセットを作成します。 

    ![シンク データセット](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
1. **[新しいデータセット]** ウィンドウで、検索ボックスに「SQL」と入力してコネクタをフィルター処理し、**[Azure SQL Database]** を選択して、**[完了]** を選択します。 このチュートリアルでは、SQL データベースにデータをコピーします。 

    ![SQL データベースの選択](./media/tutorial-copy-data-portal/select-azure-sql-dataset.png)
1. **[プロパティ]** ウィンドウの **[General]\(一般\)** タブの **[名前]** に「**OutputSqlDataset**」と入力します。 
    
    ![出力データセットの名前](./media/tutorial-copy-data-portal/output-dataset-name.png)
1. **[接続]** タブに移動し、**[リンクされたサービス]** の横の **[+ 新規]** を選択します。 データセットをリンクされたサービスに関連付ける必要があります。 リンクされたサービスには、Data Factory が実行時に SQL データベースに接続するために使用する接続文字列が含まれています。 データセットは、コンテナー、フォルダー、データのコピー先のファイル (オプション) を指定します。 
    
    ![リンクされたサービス](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
1. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。 

    a. **[名前]** に「**AzureSqlDatabaseLinkedService**」と入力します。

    b. **[サーバー名]** で、使用する SQL Server インスタンスを選択します。

    c. **[データベース名]** で、使用する SQL データベースを選択します。

    d. **[ユーザー名]** に、ユーザーの名前を入力します。

    e. **[パスワード]** に、ユーザーのパスワードを入力します。

    f. **[テスト接続]** を選択して接続をテストします。

    g. **[保存]** を選択して、リンクされたサービスを保存します。 
    
    ![新しいリンクされたサービスの保存](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. **[テーブル]** で **[dbo].[emp]** を選択します。 

    ![テーブル](./media/tutorial-copy-data-portal/select-emp-table.png)
1. **[スキーマ]** タブに移動し、**[スキーマのインポート]** を選択します。 

    ![[スキーマのインポート] を選択](./media/tutorial-copy-data-portal/import-destination-schema.png)
1. **[ID]** 列を選択し、**[削除]** を選択します。 この **ID** 列は、SQL データベース内の ID 列です。そのため、コピー アクティビティでデータをこの列に挿入する必要はありません。

    ![ID 列の削除](./media/tutorial-copy-data-portal/delete-id-column.png)
1. パイプラインがあるタブに移動し、**[Sink Dataset]\(シンク データセット\)** で **OutputSqlDataset** が選択されていることを確認します。

    ![パイプラインのタブ](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        

### <a name="configure-mapping"></a>マッピングを構成する

**[プロパティ]** ウィンドウの一番下にある **[マップ]** タブに移動し、**[スキーマのインポート]** を選択します。 ソース ファイルの最初と 2 番目の列が SQL データベースの **FirstName** と **LastName** にマップされていることがわかります。

![スキーマのマップ](./media/tutorial-copy-data-portal/map-schemas.png)

## <a name="validate-the-pipeline"></a>パイプラインを検証する
パイプラインを検証するには、ツール バーから **[検証]** を選択します。
 
パイプラインに関連付けられている JSON コードを確認するには、右上隅にある **[コード]** をクリックします。

## <a name="debug-and-publish-the-pipeline"></a>パイプラインをデバッグして発行する
Data Factory または独自の Azure Repos Git リポジトリにアーティファクト (リンクされたサービス、データセット、パイプライン) を発行する前に、パイプラインをデバッグできます。 

1. パイプラインをデバッグするには、ツール バーで **[デバッグ]** を選択します。 ウィンドウ下部の **[出力]** タブにパイプラインの実行の状態が表示されます。 

1. パイプラインを適切に実行できたら、上部のツール バーで **[すべて発行]** を選択します。 これにより、作成したエンティティ (データセットとパイプライン) が Data Factory に発行されます。

    ![発行](./media/tutorial-copy-data-portal/publish-button.png)

1. **[正常に発行されました]** というメッセージが表示されるまで待機します。 通知メッセージを表示するには、右上にある **[通知の表示]** (ベル ボタン) をクリックします。 

## <a name="trigger-the-pipeline-manually"></a>パイプラインを手動でトリガーする
この手順では、前の手順で発行したパイプラインを手動でトリガーします。 

1. ツール バーの **[トリガー]** を選択し、**[Trigger Now]\(今すぐトリガー\)** を選択します。 **[Pipeline Run]\(パイプラインの実行\)** ページで **[完了]** を選択します。  

1. 左側の **[監視]** タブに移動します。 手動トリガーによってトリガーされたパイプラインの実行が表示されます。 **[アクション]** 列のリンクを使用して、アクティビティの詳細を表示したりパイプラインを再実行したりできます。

    ![パイプラインの実行を監視する](./media/tutorial-copy-data-portal/monitor-pipeline.png)
1. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、**[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクを選択します。 この例では、アクティビティが 1 つだけなので、一覧に表示されるエントリは 1 つのみです。 コピー操作の詳細を確認するために、**[アクション]** 列にある **[詳細]** リンク (眼鏡アイコン) を選択します。 再度**パイプラインの実行**ビューに移動するには、一番上にある **[パイプライン]** を選択します。 表示を更新するには、**[最新の情報に更新]** を選択します。

    ![アクティビティの実行を監視する](./media/tutorial-copy-data-portal/view-activity-runs.png)
1. SQL データベースの **emp** テーブルに 2 つの行が追加されていることを確認します。 

## <a name="trigger-the-pipeline-on-a-schedule"></a>スケジュールに基づいてパイプラインをトリガーする
このスケジュールでは、パイプラインのスケジュール トリガーを作成します。 このトリガーは、指定されたスケジュール (1 時間に 1 回、毎日など) に基づいてパイプラインを実行します。 この例では、指定された終了日時まで毎分実行されるようトリガーを設定します。 

1. [監視] タブの上にある左側の **[作成者]** タブに移動します。 

1. パイプラインに移動し、ツール バーの **[トリガー]** をクリックして、**[新規/編集]** を選択します。 

1. **[Add Triggers]\(トリガーの追加\)** ウィンドウで、**[Choose trigger]\(トリガーの選択\)** を選択し、**[+ 新規]** を選択します。 

    ![[新規] ボタン](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
1. **[新しいトリガー]** ウィンドウで、次の手順を実行します。 

    a. **[名前]** に「**RunEveryMinute**」と入力します。

    b. **[終了]** で **[指定日]** を選択します。

    c. **[End On]\(終了日\)** のドロップダウン リストを選択します。

    d. **現在の日付**のオプションを選択します。 既定では、終了日は翌日に設定されています。

    e. **分**の部分を現在の日時の数分後に変更します。 トリガーは、変更を発行した後にのみアクティブ化されます。 これをわずか数分後に設定し、それまでに発行しなかった場合、トリガー実行は表示されません。

    f. **[適用]** を選択します。 

    ![トリガーのプロパティ](./media/tutorial-copy-data-portal/set-trigger-properties.png)

    g. **[アクティブ化]** オプションをオンにします。 このチェック ボックスを使用して、後で非アクティブ化したりアクティブ化したりできます。

    h. **[次へ]** を選択します。

    ![[アクティブ化] ボタン](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > パイプラインの実行ごとにコストが関連付けられるため、終了日は適切に設定してください。 
1. **[Trigger Run Parameters]\(トリガー実行のパラメーター\)** ページで、警告を確認し、**[完了]** を選択します。 この例のパイプラインにはパラメーターはありません。 

    ![トリガー実行のパラメーター](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)

1. **[すべて発行]** をクリックして、変更を発行します。 

1. 左側の **[モニター]** タブに移動して、トリガーされたパイプラインの実行を確認します。 

    ![トリガーされたパイプラインの実行](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
1. **パイプラインの実行**ビューから**トリガーの実行**ビューに切り替えるには、**[Pipeline Runs]\(パイプラインの実行\)** を選択して **[Trigger Runs]\(トリガーの実行\)** を選択します。
    
    ![トリガーの実行](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
1. トリガーの実行が一覧で表示されます。 

    ![トリガーの実行の一覧](./media/tutorial-copy-data-portal/trigger-runs-list.png)
1. 指定された終了日時まで、(各パイプライン実行について) 1 分ごとに 2 つの行が **emp** テーブルに挿入されていることを確認します。 

## <a name="next-steps"></a>次の手順
このサンプルのパイプラインは、Blob Storage 内のある場所から別の場所にデータをコピーします。 以下の方法について学習しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * コピー アクティビティを含むパイプラインを作成します。
> * パイプラインをテスト実行します。
> * パイプラインを手動でトリガーします。
> * スケジュールに基づいてパイプラインをトリガーします。
> * パイプラインとアクティビティの実行を監視します。


オンプレミスからクラウドにデータをコピーする方法について学習するには、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
>[オンプレミスからクラウドにデータをコピーする](tutorial-hybrid-copy-portal.md)
