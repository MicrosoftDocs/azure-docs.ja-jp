---
title: Azure Data Factory を使って複数のテーブルを増分コピーする | Microsoft Docs
description: このチュートリアルでは、オンプレミスの SQL Server データベースにある複数のテーブルから Azure SQL Database に差分データを増分コピーする Azure Data Factory パイプラインを作成します。
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/20/2018
ms.author: yexu
ms.openlocfilehash: b9dafd31ed84298c97932b1cdb5593eb17769ef9
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59566007"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>SQL Server にある複数のテーブルから Azure SQL データベースにデータを増分読み込みする
このチュートリアルでは、オンプレミスの SQL Server にある複数のテーブルから Azure SQL データベースに差分データを読み込むパイプラインを持つ Azure Data Factory を作成します。    

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * ソース データ ストアとターゲット データ ストアを準備します。
> * データ ファクトリを作成します。
> * セルフホステッド統合ランタイムを作成します。
> * 統合ランタイムをインストールします。 
> * リンクされたサービスを作成します。 
> * ソース データセット、シンク データセット、および基準値データセットを作成します。
> * パイプラインを作成、実行、監視します。
> * 結果を確認します。
> * ソース テーブルのデータを追加または更新します。
> * パイプラインを再実行して監視します。
> * 最終結果を確認します。

## <a name="overview"></a>概要
このソリューションを作成するための重要な手順を次に示します。 

1. **基準値列を選択する**。
    
    ソース データ ストアのテーブルごとに、いずれか 1 つの列を選択します。この列は、実行ごとに新しいレコードまたは更新されたレコードを特定する目的で使用されます。 通常、行が作成または更新されたときに常にデータが増える列を選択します (last_modify_time、ID など)。 この列の最大値が基準値として使用されます。

1. **基準値を格納するためのデータ ストアを準備する**。   
    
    このチュートリアルでは、SQL データベースに基準値を格納します。

1. **次のアクティビティを含んだパイプラインを作成する**。 
    
    a. パイプラインにパラメーターとして渡された一連のソース テーブル名を反復処理する ForEach アクティビティを作成する。 このアクティビティが、ソース テーブルごとに次のアクティビティを呼び出して各テーブルの差分読み込みを実行します。

    b. 2 つのルックアップ アクティビティを作成する。 1 つ目のルックアップ アクティビティは、前回の基準値を取得するために使用します。 2 つ目のルックアップ アクティビティは、新しい基準値を取得するために使用します。 これらの基準値は、コピー アクティビティに渡されます。

    c. コピー アクティビティを作成する。これは基準値列の値がかつての基準値より大きく、かつ新しい基準値よりも小さい行をソース データ ストアからコピーするアクティビティです。 その差分データが、ソース データ ストアから新しいファイルとして Azure BLOB ストレージにコピーされます。

    d. ストアド プロシージャ― アクティビティを作成する。これはパイプラインの次回実行に備えて基準値を更新するためのアクティビティです。 

    ソリューションの概略図を次に示します。 

    ![データの増分読み込み](media/tutorial-incremental-copy-multiple-tables-portal/high-level-solution-diagram.png)


Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件
* **SQL Server**。 このチュートリアルでは、オンプレミスの SQL Server データベースをソース データ ストアとして使用します。 
* **Azure SQL データベース**。 シンク データ ストアとして SQL データベースを使用します。 SQL データベースがない場合の作成手順については、「[Azure SQL データベースを作成する](../sql-database/sql-database-get-started-portal.md)」を参照してください。 

### <a name="create-source-tables-in-your-sql-server-database"></a>SQL Server データベースにソース テーブルを作成する

1. SQL Server Management Studio を開き、オンプレミスの SQL Server データベースに接続します。

1. **サーバー エクスプローラー**で目的のデータベースを右クリックし、**[新しいクエリ]** を選択します。

1. データベースに対して次の SQL コマンドを実行し、`customer_table` および `project_table` という名前のテーブルを作成します。

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Azure SQL データベースにターゲット テーブルを作成する
1. SQL Server Management Studio を開き、Azure SQL データベースに接続します。

1. **サーバー エクスプローラー**で目的のデータベースを右クリックし、**[新しいクエリ]** を選択します。

1. SQL データベースに対して次の SQL コマンドを実行し、`customer_table` と `project_table` という名前のテーブルを作成します。  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>高基準値の格納用としてもう 1 つテーブルを Azure SQL データベースに作成する
1. SQL データベースに対して次の SQL コマンドを実行し、基準値の格納先として `watermarktable` という名前のテーブルを作成します。 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. 両方のソース テーブルの初期基準値を基準値テーブルに挿入します。

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Azure SQL データベースにストアド プロシージャを作成する 

次のコマンドを実行して、SQL データベースにストアド プロシージャを作成します。 パイプラインの実行後は都度、このストアド プロシージャによって基準値が更新されます。 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Azure SQL データベースにデータ型と新たなストアド プロシージャを作成する
次のクエリを実行して、2 つのストアド プロシージャと 2 つのデータ型を SQL データベースに作成します。 それらは、ソース テーブルから宛先テーブルにデータをマージするために使用されます。

作業工程を簡単に始められるように、差分データをテーブル変数を介して渡すこのようなストアド プロシージャを直接使用し、それらを宛先ストアにマージします。 テーブル変数には、"多数" の差分行 (100 を超える行) が格納されることが想定されていない点に注意してください。  

多数の差分行を宛先ストアにマージする必要がある場合は、まずコピー アクティビティを使用してすべての差分データを宛先ストアの一時的な "ステージング" テーブルにコピーしてから、テーブル変数を使用せずに独自のストアド プロシージャを構築し、"ステージング" テーブルから "最終的な" テーブルにマージすることをお勧めします。 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
1. 左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。 
   
   ![New->DataFactory](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory-menu.png)
1. **[新しいデータ ファクトリ]** ページで、**[名前]** に「**ADFMultiIncCopyTutorialDF**」と入力します。 
      
     ![[新しいデータ ファクトリ] ページ](./media/tutorial-incremental-copy-multiple-tables-portal/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更して (yournameADFMultiIncCopyTutorialDF など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
       `Data factory name ADFMultiIncCopyTutorialDF is not available`
1. データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
1. **[リソース グループ]** について、次の手順のいずれかを行います。
     
      - **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
      - **[新規作成]** を選択し、リソース グループの名前を入力します。   
         
        リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
1. **バージョン**として **[V2 (プレビュー)]** を選択します。
1. データ ファクトリの **場所** を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。
1. **[ダッシュボードにピン留めする]** をオンにします。     
1. **Create** をクリックしてください。      
1. ダッシュボードに、**[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。 

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-incremental-copy-multiple-tables-portal/deploying-data-factory.png)
1. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
   ![データ ファクトリのホーム ページ](./media/tutorial-incremental-copy-multiple-tables-portal/data-factory-home-page.png)
1. **[Author & Monitor]\(作成と監視\)** タイルをクリックして、別のタブで Azure Data Factory ユーザー インターフェイス (UI) を起動します。
1. Azure Data Factory UI の開始ページで **[Create pipeline]\(パイプラインの作成\)** タイルをクリックするか、**[編集]** タブに切り替えます。 

   ![開始ページ](./media/tutorial-incremental-copy-multiple-tables-portal/get-started-page.png)

## <a name="create-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムを作成する
プライベート ネットワーク (オンプレミス) のデータ ストアから Azure データ ストアにデータを移動するときに、セルフホステッド統合ランタイム (IR) をオンプレミス環境にインストールします。 セルフホステッド IR を使用すると、プライベート ネットワークと Azure との間でデータを移動できます。 

1. 左ウィンドウの下部にある **[接続]** をクリックし、**[接続]** ウィンドウの **[Integration Runtimes]\(統合ランタイム\)** に切り替えます。 

   ![[接続] タブ](./media/tutorial-incremental-copy-multiple-tables-portal/connections-tab.png)
1. **[Integration Runtimes]\(統合ランタイム\)** タブで、**[+ 新規]** をクリックします。 

   ![新規統合ランタイム - ボタン](./media/tutorial-incremental-copy-multiple-tables-portal/new-integration-runtime-button.png)
1. **[Integration Runtime Setup]\(統合ランタイムの設定\)** ウィンドウで、**[Perform data movement and dispatch activities to external computes]\(データの移動を実行し、アクティビティを外部コンピューティングにディスパッチする\)** を選択し、**[次へ]** をクリックします。 

   ![統合ランタイムの種類を選択する](./media/tutorial-incremental-copy-multiple-tables-portal/select-integration-runtime-type.png)
1. **[プライベート ネットワーク]** を選択し、**[次へ]** をクリックします。 

   ![[プライベート ネットワーク] の選択](./media/tutorial-incremental-copy-multiple-tables-portal/select-private-network.png)
1. **[名前]** に「**MySelfHostedIR**」と入力し、**[次へ]** をクリックします。 

   ![セルフホステッド IR 名](./media/tutorial-incremental-copy-multiple-tables-portal/self-hosted-ir-name.png)
1. **[Option 1: Express setup]\(オプション 1: 高速セットアップ\)** セクションの **[Click here to launch the express setup for this computer]\(このコンピューターで高速セットアップを起動するにはここをクリック\)** をクリックします。 

   ![高速セットアップのリンクのクリック](./media/tutorial-incremental-copy-multiple-tables-portal/click-express-setup.png)
1. **[Integration Runtime (セルフホステッド) 高速セットアップ]** ウィンドウで、**[閉じる]** をクリックします。 

   ![統合ランタイムのセットアップ - 成功](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
1. Web ブラウザーの **[Integration Runtime Setup]\(統合ランタイムのセットアップ\)** ウィンドウで、**[完了]** をクリックします。 

   ![統合ランタイムのセットアップ - 完了](./media/tutorial-incremental-copy-multiple-tables-portal/click-finish-integration-runtime-setup.png)
1. 統合ランタイムの一覧に **MySelfHostedIR** が含まれていることを確認します。

    ![統合ランタイム - 一覧](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtimes-list.png)

## <a name="create-linked-services"></a>リンクされたサービスを作成します
データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このセクションでは、オンプレミスの SQL Server データベースと SQL データベースに対するリンクされたサービスを作成します。 

### <a name="create-the-sql-server-linked-service"></a>SQL Server のリンクされたサービスを作成する
この手順では、オンプレミス SQL Server データベースをデータ ファクトリにリンクします。

1. **[接続]** ウィンドウで、**[Integration Runtimes]\(統合ランタイム\)** タブから **[リンクされたサービス]** タブに切り替え、**[+ 新規]** をクリックします。

    ![新しいリンクされたサービスのボタン](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
1. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで **[SQL Server]** を選択し、**[続行]** をクリックします。 

    ![SQL Server の選択](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server.png)
1. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。

    1. **[名前]** に「**SqlServerLinkedService**」と入力します。 
    1. **[Connect via integration runtime]\(統合ランタイム経由で接続\)** で **[MySelfHostedIR]** を選択します。 これは**重要な**手順です。 既定の統合ランタイムでは、オンプレミスのデータ ストアに接続できません。 そこで、前に作成したセルフホステッド統合ランタイムを使用します。 
    1. **[サーバー名]** に、SQL Server データベースを保持するサーバーの名前を入力します。
    1. **[データベース名]** に、SQL Server にソース データを含むデータベースの名前を入力します。 テーブルを作成してこのデータベースにデータを挿入する作業は、既に前提条件の一部として行っています。 
    1. **[認証の種類]** で、データベースへの接続に使用する**認証の種類**を選択します。 
    1. **[ユーザー名]** に、SQL Server データベースにアクセスできるユーザーの名前を入力します。 ユーザー アカウントまたはサーバー名にスラッシュ文字 (`\`) を使用する必要がある場合は、エスケープ文字 (`\`) を使用します。 例: `mydomain\\myuser`。
    1. **[パスワード]** に、ユーザーの**パスワード**を入力します。 
    1. Data Factory が SQL Server データベースに接続できるかどうかをテストするために、**[Test connection]\(テスト接続\)** をクリックします。 エラーがあれば修正して、正常に接続できるようにします。 
    1. リンクされたサービスを保存するために、**[保存]** をクリックします。

        ![SQL Server のリンクされたサービス - 設定](./media/tutorial-incremental-copy-multiple-tables-portal/sql-server-linked-service-settings.png)

### <a name="create-the-azure-sql-database-linked-service"></a>Azure SQL Database のリンクされたサービスを作成する
最後の手順では、ソース SQL Server データベースをデータ ファクトリに接続するためのリンクされたサービスを作成します。 この手順では、ターゲット/シンク Azure SQL データベースをデータ ファクトリにリンクします。 

1. **[接続]** ウィンドウで、**[Integration Runtimes]\(統合ランタイム\)** タブから **[リンクされたサービス]** タブに切り替え、**[+ 新規]** をクリックします。

    ![新しいリンクされたサービスのボタン](./media/tutorial-incremental-copy-multiple-tables-portal/new-sql-server-linked-service-button.png)
1. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで **[Azure SQL Database]** を選択し、**[続行]** をクリックします。 
1. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。

    1. **[名前]** に「**AzureSqlDatabaseLinkedService**」と入力します。 
    1. **[サーバー名]** で、ドロップダウン リストから Azure SQL サーバーの名前を選択します。 
    1. **[データベース名]** で、前提条件の一部として customer_table と project_table を作成した Azure SQL データベースを選択します。 
    1. **[ユーザー名]** に、Azure SQL データベースにアクセスできるユーザーの名前を入力します。 
    1. **[パスワード]** に、ユーザーの**パスワード**を入力します。 
    1. Data Factory が SQL Server データベースに接続できるかどうかをテストするために、**[Test connection]\(テスト接続\)** をクリックします。 エラーがあれば修正して、正常に接続できるようにします。 
    1. リンクされたサービスを保存するために、**[保存]** をクリックします。

        ![Azure SQL のリンクされたサービス - 設定](./media/tutorial-incremental-copy-multiple-tables-portal/azure-sql-linked-service-settings.png)
1. 2 つのリンクされたサービスが一覧に表示されていることを確認します。 
   
    ![2 つのリンクされたサービス](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>データセットを作成する
この手順では、データ ソース、データの宛先、および基準値の格納場所を表すデータセットを作成します。

### <a name="create-a-source-dataset"></a>ソース データセットを作成する

1. 左ウィンドウで **[+] (プラス記号)** をクリックし、**[データセット]** をクリックします。

   ![[新しいデータセット] メニュー](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. **[新しいデータセット]** ウィンドウで、**[SQL Server]** を選択し、**[完了]** をクリックします。 

   ![SQL Server の選択](./media/tutorial-incremental-copy-multiple-tables-portal/select-sql-server-for-dataset.png)
1. データセットを設定するために、Web ブラウザーで新しいタブが開かれます。 さらに、ツリービューにデータセットが表示されます。 下部にあるプロパティ ウィンドウの **[全般]** タブで、**[名前]** に「**SourceDataset**」と入力します。 

   ![ソース データセット - 名前](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-general.png)
1. プロパティ ウィンドウの **[接続]** タブに切り替えて、**[リンクされたサービス]** で **[SqlServerLinkedService]** を選択します。 ここではテーブルを選択しません。 このパイプライン内のコピー アクティビティは、テーブル全体を読み込むことはせずに、SQL クエリを使用してデータを読み込みます。

   ![ソース データセット - 接続](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>シンク データセットを作成する
1. 左ウィンドウで **[+] (プラス記号)** をクリックし、**[データセット]** をクリックします。

   ![[新しいデータセット] メニュー](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. **[新しいデータセット]** ウィンドウで **[Azure SQL Database]** を選択し、**[完了]** をクリックします。 

   ![Azure SQL Database の選択](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
1. データセットを設定するために、Web ブラウザーで新しいタブが開かれます。 さらに、ツリービューにデータセットが表示されます。 下部にあるプロパティ ウィンドウの **[全般]** タブで、**[名前]** に「**SinkDataset**」と入力します。

   ![シンク データセット - 全般](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-general.png)
1. プロパティ ウィンドウの **[ソース]** タブに切り替え、以下の手順を実行します。 

    1. **[Create/update parameters]\(パラメーターの作成/更新\)** セクションで、**[新規]** をクリックします。 
    1. **[名前]** に「**SinkTableName**」と入力し、**[type]\(型\)** として **[文字列]** を指定します。 このデータセットは、**SinkTableName** をパラメーターとして受け取ります。 SinkTableName パラメーターは、実行時にパイプラインによって動的に設定されます。 パイプライン内の ForEach アクティビティは、一連のテーブル名を反復処理しながら、各イテレーションの中でこのデータセットにテーブル名を渡します。
   
       ![シンク データセット - プロパティ](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)
1. プロパティ ウィンドウの **[接続]** タブに切り替えて、**[リンクされたサービス]** で **[AzureSqlLinkedService]** を選択します。 **[テーブル]** プロパティで、**[動的なコンテンツの追加]** をクリックします。 

   ![シンク データセット - 接続](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection.png)
    
    
1. **[パラメーター]** セクションで **[SinkTableName]** をクリックします
   
   ![シンク データセット - 接続](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-dynamicContent.png)

   
 1. **[完了]** をクリックした後、テーブル名として **@dataset().SinkTableName** が表示されます。
   
   ![シンク データセット - 接続](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-completion.png)

### <a name="create-a-dataset-for-a-watermark"></a>基準値用のデータセットを作成する
この手順では、高基準値を格納するためのデータセットを作成します。 

1. 左ウィンドウで **[+] (プラス記号)** をクリックし、**[データセット]** をクリックします。

   ![[新しいデータセット] メニュー](./media/tutorial-incremental-copy-multiple-tables-portal/new-dataset-menu.png)
1. **[新しいデータセット]** ウィンドウで **[Azure SQL Database]** を選択し、**[完了]** をクリックします。 

   ![Azure SQL Database の選択](./media/tutorial-incremental-copy-multiple-tables-portal/select-azure-sql-database.png)
1. 下部にあるプロパティ ウィンドウの **[全般]** タブで、**[名前]** に「**WatermarkDataset**」と入力します。
1. **[接続]** タブに切り替えて、次の手順を実行します。 

    1. **[リンクされたサービス]** で **[AzureSqlDatabaseLinkedService]** を選択します。
    1. **[テーブル]** で **[dbo].[watermarktable]** を選択します。

       ![基準値データセット - 接続](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>パイプラインを作成する。
このパイプラインは、一連のテーブル名をパラメーターとして受け取ります。 ForEach アクティビティは、一連のテーブル名を反復処理しながら、次の操作を実行します。 

1. ルックアップ アクティビティを使用して古い基準値 (初期値または前回のイテレーションで使用された値) を取得します。

1. ルックアップ アクティビティを使用して新しい基準値 (ソース テーブルの基準値列の最大値) を取得します。

1. コピー アクティビティを使用して、この 2 つの基準値の間に存在するデータをソース データベースからターゲット データベースにコピーします。

1. ストアド プロシージャ アクティビティを使用して古い基準値を更新します。この値が、次のイテレーションの最初のステップで使用されます。 

### <a name="create-the-pipeline"></a>パイプラインを作成する

1. 左ウィンドウで **[+]\(プラス記号\)** をクリックし、**[パイプライン]** をクリックします。

    ![新しいパイプライン - メニュー](./media/tutorial-incremental-copy-multiple-tables-portal/new-pipeline-menu.png)
1. **プロパティ** ウィンドウの **[全般]** タブで、**[名前]** に「**IncrementalCopyPipeline**」と入力します。 

    ![パイプライン名](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-name.png)
1. **プロパティ** ウィンドウで、以下の手順を実行します。 

    1. **[+ 新規]** をクリックします。 
    1. **[名前]** に、パラメーター名として「**tableList**」と入力します。 
    1. パラメーターの **[型]** として **[オブジェクト]** を選択します。

    ![パイプラインのパラメーター](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-parameters.png) 
1. **[アクティビティ]** ツール ボックスで **[Iteration & Conditionals]\(繰り返しと条件\)** を展開し、パイプライン デザイナー画面に **[ForEach]** アクティビティをドラッグ アンド ドロップします。 **プロパティ** ウィンドウの **[全般]** タブで、「**IterateSQLTables**」と入力します。 

    ![ForEach アクティビティ - 名前](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-name.png)
1. **プロパティ** ウィンドウで **[設定]** タブに切り替え、**[項目]** に「`@pipeline().parameters.tableList`」と入力します。 ForEach アクティビティは、一連のテーブルを反復処理しながら、増分コピー操作を実行します。 

    ![ForEach アクティビティ - 設定](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)
1. パイプラインの **ForEach** アクティビティが選択されていない場合はこれを選択します。 **[編集] (鉛筆アイコン)** ボタンをクリックします。

    ![ForEach アクティビティ - 編集](./media/tutorial-incremental-copy-multiple-tables-portal/edit-foreach.png)
1. **[アクティビティ]** ツールボックスで **[General]\(一般\)** を展開し、パイプライン デザイナー画面に **[検索]** アクティビティをドラッグ アンド ドロップし、**[名前]** に「**LookupOldWaterMarkActivity**」を入力します。

    ![最初の検索アクティビティ - 名前](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-name.png)
1. **プロパティ** ウィンドウで **[設定]** タブに切り替え、以下の手順を実行します。 

    1. **[Source Dataset]\(ソース データセット\)** で **[WatermarkDataset]** を選択します。
    1. **[クエリの使用]** で **[クエリ]** を選択します。 
    1. **[クエリ]** に次の SQL クエリを入力します。 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![最初の検索アクティビティ - 設定](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
1. **[アクティビティ]** ツールボックスから**検索**アクティビティをドラッグアンドドロップし、**[名前]** に「**LookupNewWaterMarkActivity**」と入力します。
        
    ![2 つ目の検索アクティビティ - 名前](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-name.png)
1. **[設定]** タブに切り替えます。

    1. **[Source Dataset]\(ソース データセット\)** で **[SourceDataset]** を選択します。 
    1. **[クエリの使用]** で **[クエリ]** を選択します。
    1. **[クエリ]** に次の SQL クエリを入力します。

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![2 つ目の検索アクティビティ - 設定](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
1. **[アクティビティ]** ツールボックスから**コピー** アクティビティをドラッグアンドドロップし、**[名前]** に「**IncrementalCopyActivity**」と入力します。 

    ![コピー アクティビティ - 名前](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-name.png)
1. 1 つずつ、**検索**アクティビティを**コピー** アクティビティに接続します。 接続するには、**検索**アクティビティの横の**緑**のボックスをドラッグして**コピー** アクティビティにドロップします。 コピー アクティビティの境界線の色が**青**に変わったら、マウス ボタンを離します。

    ![検索アクティビティをコピー アクティビティに接続する](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
1. パイプラインの**コピー** アクティビティを選択します。 **プロパティ** ウィンドウで **[ソース]** タブに切り替えます。 

    1. **[Source Dataset]\(ソース データセット\)** で **[SourceDataset]** を選択します。 
    1. **[クエリの使用]** で **[クエリ]** を選択します。 
    1. **[クエリ]** に次の SQL クエリを入力します。

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![コピー アクティビティ - ソースの設定](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
1. **[シンク]** タブに切り替えて、**[Sink Dataset]\(シンク データセット\)** で **[SinkDataset]** を選択します。 
        
    ![コピー アクティビティ - シンクの設定](./media/tutorial-incremental-copy-multiple-tables-portal/copy-sink-settings.png)
1. 次の手順を実行します。

    1. **[データセット]** プロパティで、**[SinkTableName]** パラメーターに「`@{item().TABLE_NAME}`」と入力します。
    1. **[ストアド プロシージャ名]** プロパティに「`@{item().StoredProcedureNameForMergeOperation}`」と入力します。
    1. **[テーブルの種類]** プロパティに「`@{item().TableType}`」と入力します。


        ![コピー アクティビティ - パラメーター](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
1. **[アクティビティ]** ツールボックスからパイプライン デザイナー画面に **[ストアド プロシージャ]** アクティビティをドラッグ アンド ドロップします。 **コピー** アクティビティを**ストアド プロシージャ** アクティビティに接続します。 

    ![コピー アクティビティ - パラメーター](./media/tutorial-incremental-copy-multiple-tables-portal/connect-copy-to-sproc.png)
1. パイプラインの **ストアド プロシージャ** アクティビティを選択します。**プロパティ** ウィンドウの **[全般]** タブで、**[名前]** に「**StoredProceduretoWriteWatermarkActivity**」と入力します。 

    ![ストアド プロシージャ アクティビティ - 名前](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-name.png)
1. **[SQL アカウント]** タブに切り替えて、**[リンクされたサービス]** で **[AzureSqlDatabaseLinkedService]** を選択します。

    ![ストアド プロシージャ アクティビティ - SQL アカウント](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
1. **[ストアド プロシージャ]** タブに切り替えて、次の手順を実行します。

    1. **[ストアド プロシージャ名]** に `usp_write_watermark` を選択します。 
    1. **[Import parameter]\(インポート パラメーター\)** を選択します。 
    1. 各パラメーターの値を次のように指定します。 

        | Name | Type | 値 | 
        | ---- | ---- | ----- |
        | LastModifiedtime | DateTime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | String | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![ストアド プロシージャ アクティビティ - ストアド プロシージャの設定](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
1. 左側のウィンドウで **[発行]** をクリックします。 これにより、作成されたエンティティが Data Factory サービスに発行されます。 

    ![[発行] ボタン](./media/tutorial-incremental-copy-multiple-tables-portal/publish-button.png)
1. **[正常に発行されました]** というメッセージが表示されるまで待機します。 通知を表示するには、**[通知の表示]** リンクをクリックします。 **[X]** をクリックして通知ウィンドウを閉じます。

    ![通知の表示](./media/tutorial-incremental-copy-multiple-tables-portal/notifications.png)

 
## <a name="run-the-pipeline"></a>パイプラインを実行する

1. パイプラインのツール バーの **[トリガー]** をクリックし、**[Trigger Now]\(今すぐトリガー\)** をクリックします。     

    ![[Trigger Now]\(今すぐトリガー\)](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. **[Pipeline Run]\(パイプラインの実行\)** ウィンドウで、**[tableList]** パラメーターに次の値を入力し、**[完了]** をクリックします。 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

    ![パイプラインの実行の引数](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>パイプラインの監視

1. 左側で **[監視]** タブに切り替えます。 **手動トリガー**によってトリガーされたパイプラインの実行を確認できます。 一覧を更新するには、**[最新の情報に更新]** ボタンをクリックします。 **[アクション]** 列のリンクを使用すると、パイプラインの実行に関連付けられているアクティビティの実行を表示したり、パイプラインを再実行したりできます。 

    ![パイプライン実行](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクをクリックします。 選択したパイプラインの実行に関連付けられているすべてのアクティビティの実行が表示されます。 

    ![アクティビティの実行](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png)

## <a name="review-the-results"></a>結果の確認
SQL Server Management Studio からターゲット SQL データベースに対して次のクエリを実行し、ソース テーブルからターゲット テーブルにデータがコピーされていることを確かめます。 

**クエリ** 
```sql
select * from customer_table
```

**Output**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**クエリ**

```sql
select * from project_table
```

**Output**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**クエリ**

```sql
select * from watermarktable
```

**Output**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

2 つのテーブルの基準値が更新されたことがわかります。 

## <a name="add-more-data-to-the-source-tables"></a>ソース テーブルにデータを追加する

コピー元の SQL Server データベースに次のクエリを実行して、customer_table 内の既存の行を更新します。 project_table に新しい行を挿入します。 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>パイプラインを再実行する
1. Web ブラウザー ウィンドウで、左側の **[編集]** タブに切り替えます。 
1. パイプラインのツール バーの **[トリガー]** をクリックし、**[Trigger Now]\(今すぐトリガー\)** をクリックします。   

    ![[Trigger Now]\(今すぐトリガー\)](./media/tutorial-incremental-copy-multiple-tables-portal/trigger-now.png)
1. **[Pipeline Run]\(パイプラインの実行\)** ウィンドウで、**[tableList]** パラメーターに次の値を入力し、**[完了]** をクリックします。 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline-again"></a>パイプラインを再度監視する

1. 左側で **[監視]** タブに切り替えます。 **手動トリガー**によってトリガーされたパイプラインの実行を確認できます。 一覧を更新するには、**[最新の情報に更新]** ボタンをクリックします。 **[アクション]** 列のリンクを使用すると、パイプラインの実行に関連付けられているアクティビティの実行を表示したり、パイプラインを再実行したりできます。 

    ![パイプライン実行](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクをクリックします。 選択したパイプラインの実行に関連付けられているすべてのアクティビティの実行が表示されます。 

    ![アクティビティの実行](./media/tutorial-incremental-copy-multiple-tables-portal/activity-runs.png) 

## <a name="review-the-final-results"></a>最終結果を確認する
SQL Server Management Studio からターゲット データベースに対して次のクエリを実行し、更新されたデータや新しいデータがソース テーブルからターゲット テーブルにコピーされていることを確かめます。 

**クエリ** 
```sql
select * from customer_table
```

**Output**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**PersonID** 3 を見ると、**Name** と **LastModifytime** が新しい値であることがわかります。 

**クエリ**

```sql
select * from project_table
```

**Output**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

project_table に **NewProject** というエントリが追加されていることがわかります。 

**クエリ**

```sql
select * from watermarktable
```

**Output**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

2 つのテーブルの基準値が更新されたことがわかります。
     
## <a name="next-steps"></a>次の手順
このチュートリアルでは、以下の手順を実行しました。 

> [!div class="checklist"]
> * ソース データ ストアとターゲット データ ストアを準備します。
> * データ ファクトリを作成します。
> * 自己ホスト型統合ランタイム (IR) を作成します。
> * 統合ランタイムをインストールします。
> * リンクされたサービスを作成します。 
> * ソース データセット、シンク データセット、および基準値データセットを作成します。
> * パイプラインを作成、実行、監視します。
> * 結果を確認します。
> * ソース テーブルのデータを追加または更新します。
> * パイプラインを再実行して監視します。
> * 最終結果を確認します。

次のチュートリアルに進み、Azure 上の Spark クラスターを使ってデータを変換する方法について学習しましょう。

> [!div class="nextstepaction"]
>[Change Tracking テクノロジを使用して Azure SQL Database から Azure BLOB ストレージにデータを増分読み込みする](tutorial-incremental-copy-change-tracking-feature-portal.md)


