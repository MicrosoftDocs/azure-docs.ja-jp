---
title: 変更データ キャプチャを使用してデータを増分コピーする
description: このチュートリアルでは、Azure SQL Managed Instance データベースから Azure Storage に差分データを増分コピーする Azure Data Factory パイプラインを作成します。
ms.author: nihurt
author: hurtn
ms.service: data-factory
ms.topic: tutorial
ms.date: 02/18/2021
ms.openlocfilehash: a00ec8698b188b8fa87935e498e8cfab3aeab5aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724984"
---
# <a name="incrementally-load-data-from-azure-sql-managed-instance-to-azure-storage-using-change-data-capture-cdc"></a>変更データ キャプチャ (CDC) を使用して Azure SQL Managed Instance から Azure Storage へのデータの増分読み込みを行う

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

このチュートリアルでは、ソース Azure SQL Managed Instance データベースの **変更データ キャプチャ (CDC)** 情報に基づいて、差分データを Azure Blob Storage に読み込むパイプラインを使用して Azure データ ファクトリを作成します。  

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * ソース データ ストアを準備します。
> * データ ファクトリを作成します。
> * リンクされたサービスを作成します。
> * ソース データセットとシンク データセットを作成します。
> * パイプラインを作成、デバッグ、実行して、変更されたデータを確認します
> * ソース テーブルのデータを変更します
> * 完全な増分コピー パイプラインを完成させ、実行して監視します

## <a name="overview"></a>概要
Azure SQL Managed Instances (MI) や SQL Server などのデータ ストアでサポートされる変更データ キャプチャ テクノロジを使用して、変更されたデータを識別できます。  このチュートリアルでは、Azure Data Factory と SQL 変更データ キャプチャ テクノロジを使用して、Azure SQL Managed Instance から Azure Blob Storage に差分データの増分読み込みを行う方法について説明します。  SQL 変更データ キャプチャ テクノロジに関するより具体的な情報については、[SQL Server での変更データ キャプチャ](/sql/relational-databases/track-changes/about-change-data-capture-sql-server)に関するページを参照してください。

## <a name="end-to-end-workflow"></a>エンド ツー エンド ワークフロー
ここでは、変更データ キャプチャ テクノロジを使用してデータの増分読み込みを行う一般的なエンドツーエンドのワークフロー ステップを取り上げます。

> [!NOTE]
> Azure SQL MI と SQL Server のどちらでも、変更データ キャプチャ テクノロジがサポートされています。 このチュートリアルでは、Azure SQL Managed Instance をソース データ ストアとして使用します。 オンプレミスの SQL Server を使用してもかまいません。

## <a name="high-level-solution"></a>ソリューションの概略
このチュートリアルでは、次の操作を実行するパイプラインを作成します。  

   1. SQL Database CDC テーブル内の変更されたレコードの数をカウントして IF 条件アクティビティに渡す **検索アクティビティ** を作成します。
   2. 変更されたレコードがあるかどうかを調べて、ある場合はコピー アクティビティを呼び出す **If 条件** を作成します。
   3. 挿入、更新、削除されたデータを CDC テーブルから Azure Blob Storage にコピーする **コピー アクティビティ** を作成します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件
* **Azure SQL Database Managed Instance**。 **ソース** データ ストアとして使うデータベースです。 Azure SQL Database Managed Instance がない場合は、[Azure SQL Database Managed Instance の作成](../azure-sql/managed-instance/instance-create-quickstart.md)に関する記事に書かれている手順を参照して作成してください。
* **Azure Storage アカウント**。 **シンク** データ ストアとして使用する BLOB ストレージです。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「[ストレージ アカウントの作成](../storage/common/storage-account-create.md)」を参照してください。 **raw** という名前のコンテナーを作成します。 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Azure SQL Database にデータ ソース テーブルを作成する

1. **SQL Server Management Studio** を起動し、Azure SQL Managed Instance サーバーに接続します。
2. **サーバー エクスプローラー** で目的の **データベース** を右クリックして **[新しいクエリ]** を選択します。
3. Azure SQL Managed Instance データベースに対して次の SQL コマンドを実行し、ソース データ ストアとして `customers` という名前のテーブルを作成します。  

    ```sql
    create table customers 
    (
    customer_id int, 
    first_name varchar(50), 
    last_name varchar(50), 
    email varchar(100), 
    city varchar(50), CONSTRAINT "PK_Customers" PRIMARY KEY CLUSTERED ("customer_id") 
     );
    ```
4. 次の SQL クエリを実行して、データベースとソース テーブル (customers) で **変更データ キャプチャ** 機構を有効にします。

    > [!NOTE]
    > - &lt;your source schema name&gt; は、customers テーブルが含まれる Azure SQL MI のスキーマに置き換えます。
    > - 変更データ キャプチャでは、追跡対象テーブルを変更するトランザクションの一部としては何も行われません。 代わりに、挿入、更新、削除の各操作がトランザクション ログに書き込まれます。 変更テーブルに格納されるデータは、定期的かつ体系的にクリーンアップしないと、増大して管理しきれなくなります。 詳細については、「[データベースでの変更データ キャプチャの有効化](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server#enable-change-data-capture-for-a-database)」を参照してください

    ```sql
    EXEC sys.sp_cdc_enable_db 
    
    EXEC sys.sp_cdc_enable_table
    @source_schema = 'dbo',
    @source_name = 'customers', 
    @role_name = 'null',
    @supports_net_changes = 1
    ```
5. 次のコマンドを実行して、customers テーブルにデータを挿入します。

    ```sql
     insert into customers 
         (customer_id, first_name, last_name, email, city) 
     values 
         (1, 'Chevy', 'Leward', 'cleward0@mapy.cz', 'Reading'),
         (2, 'Sayre', 'Ateggart', 'sateggart1@nih.gov', 'Portsmouth'),
        (3, 'Nathalia', 'Seckom', 'nseckom2@blogger.com', 'Portsmouth');
    ```

    > [!NOTE]
    > 変更データ キャプチャを有効にする前のテーブルに対する履歴変更はキャプチャされません。

## <a name="create-a-data-factory"></a>Data Factory の作成

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
1. 左側のメニューで、 **[リソースの作成]**  >  **[データ + 分析]**  >  **[Data Factory]** の順に選択します。

   ![[新規] ウィンドウでの [Data Factory] の選択](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory-menu.png)

2. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。

     ![[新しいデータ ファクトリ] ページ](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory.png)

   Azure データ ファクトリの名前は **グローバルに一意** にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更して (yournameADFTutorialDataFactory など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。

    "*データ ファクトリ名 "ADFTutorialDataFactory" は利用できません。* "
3. **バージョン** として **[V2]** を選択します。
4. データ ファクトリを作成する Azure **サブスクリプション** を選択します。
5. **[リソース グループ]** について、次の手順のいずれかを行います。

   1. **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。
   2. **[新規作成]** を選択し、リソース グループの名前を入力します。   
         
    リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。  
5. データ ファクトリの **場所** を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。
6. **[Git を有効にする]** をオフにします。     
7. **Create** をクリックしてください。
8. デプロイが完了したら、 **[リソースに移動]** をクリックします

   ![スクリーンショットは、デプロイが完了したというメッセージと、リソースに移動するオプションを示しています。](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-deploy-complete.png)
9. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。

   ![スクリーンショットは、デプロイしたデータ ファクトリを示しています。](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-home-page.png)
10. **[Author & Monitor]\(作成と監視\)** タイルをクリックして、別のタブで Azure Data Factory ユーザー インターフェイス (UI) を起動します。
11. **開始** ページで、次の図に示すように、左パネルの **[編集]** タブに切り替えます。

    ![[Create pipeline]\(パイプラインの作成\) ボタン](./media/tutorial-incremental-copy-change-data-capture-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>リンクされたサービスを作成します
データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このセクションでは、Azure ストレージ アカウントと Azure SQL MI に対するリンクされたサービスを作成します。

### <a name="create-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する
この手順では、Azure ストレージ アカウントをデータ ファクトリにリンクします。

1. **[接続]** をクリックし、 **[+ 新規]** をクリックします。

   ![新しい接続ボタン](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-connection-button-storage.png)
2. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで **[Azure Blob Storage]** を選択し、 **[続行]** をクリックします。

   ![[Azure Blob Storage] の選択](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-storage.png)
3. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。

   1. **[名前]** に「**AzureStorageLinkedService**」と入力します。
   2. **[ストレージ アカウント名]** で、使用する Azure ストレージ アカウントを選択します。
   3. **[保存]** をクリックします。

   ![Azure Storage アカウントの設定](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-mi-database-linked-service"></a>Azure SQL MI データベースのリンクされたサービスを作成する
この手順では、Azure SQL MI データベースをデータ ファクトリにリンクします。

> [!NOTE]
> SQL MI を使用している場合、パブリック エンドポイントとプライベート エンドポイントを経由したアクセスの詳細については、[こちら](./connector-azure-sql-managed-instance.md#prerequisites)を参照してください。 プライベート エンドポイントを使用する場合は、セルフホステッド統合ランタイムを使用してこのパイプラインを実行する必要があります。 オンプレミス、VM、または VNet で SQL Server を実行するシナリオに対しても、同じことが当てはまります。

1. **[接続]** をクリックし、 **[+ 新規]** をクリックします。
2. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで **[Azure SQL Database Managed Instance]** を選択し、 **[続行]** をクリックします。
3. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。

   1. **[名前]** フィールドに「**AzureSqlMI1**」と入力します。
   2. **[サーバー名]** フィールドでお使いの SQL サーバーを選択します。
   4. **[データベース名]** フィールドでお使いの SQL データベースを選択します。
   5. **[ユーザー名]** フィールドにユーザーの名前を入力します。
   6. **[パスワード]** フィールドに、ユーザーのパスワードを入力します。
   7. **[テスト接続]** をクリックして接続をテストします。
   8. **[保存]** をクリックして、リンクされたサービスを保存します。

   ![Azure SQL MI データベースのリンクされたサービスの設定](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-sql-managed-instance-database-linked-service-settings.png)

## <a name="create-datasets"></a>データセットを作成する
このステップでは、データのコピー元とコピー先を表すデータセットを作成します。

### <a name="create-a-dataset-to-represent-source-data"></a>ソース データを表すデータセットを作成する
この手順では、ソース データを表すデータセットを作成します。

1. ツリー ビューで **[+]\(プラス記号\)** をクリックし、 **[データセット]** をクリックします。

   ![[新しいデータセット] メニュー](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. **[Azure SQL Database Managed Instance]** を選択し、 **[続行]** をクリックします。

   ![ソース データセットの種類 - Azure SQL Database](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-sql-database.png)
   
3. **[プロパティの設定]** タブで、データセット名と接続情報を設定します。
 
   1. **[リンクされたサービス]** で **AzureSqlMI1** を選択します。
   2. **[テーブル名]** で **[dbo].[dbo_customers_CT]** を選択します。  注: このテーブルは、customers テーブルで CDC を有効にしたときに自動的に作成されたものです。 変更されたデータは、このテーブルから直接クエリされることはなく、代わりに [CDC 関数](/sql/relational-databases/system-functions/change-data-capture-functions-transact-sql)を使用して抽出されます。

   ![ソースの接続](./media/tutorial-incremental-copy-change-data-capture-feature-portal/source-dataset-configuration.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>シンク データ ストアにコピーされるデータを表すデータセットを作成します。
この手順では、ソース データ ストアからコピーされたデータを表すデータセットを作成します。 前提条件の 1 つとして、データ レイク コンテナーを Azure Blob Storage に作成しました。 このコンテナーが存在しない場合は作成するか、または既存のコンテナーの名前に設定してください。 このチュートリアルでは、トリガー時刻を使用して出力ファイル名を動的に生成します。これは後で構成します。

1. ツリー ビューで **[+]\(プラス記号\)** をクリックし、 **[データセット]** をクリックします。

   ![[新しいデータセット] メニュー](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. **[Azure Blob Storage]** を選択し、 **[続行]** をクリックします。

   ![シンク データセットの種類 - Azure Blob Storage](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-type.png)
3. **[DelimitedText]** を選択し、 **[続行]** をクリックします。

   ![コピー先データセットの形式 - DelimitedText](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-format.png)
4. **[プロパティの設定]** タブで、データセット名と接続情報を設定します。

   1. **[リンクされたサービス]** で **[AzureStorageLinkedService]** を選択します。
   2. **[ファイル パス]** の **コンテナー** 部分には「**raw**」と入力します。
   3. **[First row as header]\(先頭の行を見出しとして使用する\)** を有効にします
   4. **[OK]** をクリックします。

   ![シンク データセット - 接続](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration.png)

## <a name="create-a-pipeline-to-copy-the-changed-data"></a>変更されたデータをコピーするパイプラインを作成する
このステップで作成するパイプラインでは、最初に、**検索アクティビティ** を使用して、変更テーブルに存在する変更されたレコードの数を調べます。 IF 条件アクティビティでは、変更されたレコードの数が 0 より大きいかどうかが確認され、**コピー アクティビティ** が実行されて、挿入、更新、削除されたデータが Azure SQL Database から Azure Blob Storage にコピーされます。 最後に、タンブリング ウィンドウ トリガーが構成され、開始時刻と終了時刻がウィンドウの開始および終了パラメーターとしてアクティビティに渡されます。 

1. Data Factory の UI で、 **[編集]** タブに切り替えます。左ウィンドウで **[+]\(プラス記号\)** をクリックし、 **[パイプライン]** をクリックします。

    ![新しいパイプライン メニュー](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-pipeline-menu.png)
2. パイプラインを構成するための新しいタブが表示されます。 ツリー ビューにもパイプラインが表示されます。 **[プロパティ]** ウィンドウで、パイプラインの名前を「**IncrementalCopyPipeline**」に変更します。

    ![パイプライン名](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-name.png)
3. **[アクティビティ]** ツール ボックスの **[General]\(一般\)** を展開し、 **[検索]** アクティビティをパイプライン デザイナー画面にドラッグ アンド ドロップします。 アクティビティの名前を **GetChangeCount** に設定します。 このアクティビティを使って、指定した期間における変更テーブル内のレコード数を取得します。

    ![検索アクティビティ - 名前](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-name.png)
4. **[プロパティ]** ウィンドウで **[設定]** に切り替えます。
   1. **[Source Dataset]\(コピー元データセット\)** フィールドで SQL MI データセットの名前を指定します。
   2. クエリ オプションを選択し、クエリ ボックスに次のように入力します。
    ```sql
    DECLARE  @from_lsn binary(10), @to_lsn binary(10);  
    SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers');  
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal',  GETDATE());
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
    ```
   3. **[First row only]\(先頭行のみ\)** を有効にします

    ![検索アクティビティ - 設定](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-settings.png)
5. **[データのプレビュー]** ボタンをクリックし、検索アクティビティによって確実に有効な出力が取得されるようにします

    ![検索アクティビティ - プレビュー](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-preview.png)
6. **[アクティビティ]** ツール ボックスで **[Iteration & Conditionals]\(繰り返しと条件\)** を展開し、パイプライン デザイナー画面に **[If Condition]\(If 条件\)** アクティビティをドラッグ アンド ドロップします。 アクティビティの名前を **HasChangedRows** に設定します。 

    ![If 条件アクティビティ - 名前](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-name.png)
7. **[プロパティ]** ウィンドウで **[アクティビティ]** に切り替えます。

   1. 次の **式** を入力します
   
    ```adf
    @greater(int(activity('GetChangeCount').output.firstRow.changecount),0)
    ```

   2. 鉛筆アイコンをクリックして、True 条件を編集します。

   ![If 条件アクティビティ - 設定](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-setting.png)

   3. **[アクティビティ]** ツール ボックスの **[General]\(一般\)** を展開し、 **[Wait]\(待機\)** アクティビティをパイプライン デザイナー画面にドラッグ アンド ドロップします。 これは、If 条件をデバッグするための一時的なアクティビティであり、チュートリアルの後半で変更します。 

   ![If 条件 True - 待機](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-wait.png)

   4. IncrementalCopyPipeline 階層リンクをクリックして、メイン パイプラインに戻ります。

8. パイプラインを **デバッグ** モードで実行し、パイプラインが正常に実行されることを確認します。 

   ![パイプライン - デバッグ](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug.png)
9. 次に、True 条件のステップに戻り、**待機** アクティビティを削除します。 **[アクティビティ]** ツール ボックスで **[Move & Transform]\(移動と変換\)** を展開し、 **[コピー]** アクティビティをパイプライン デザイナー画面にドラッグ アンド ドロップします。 アクティビティの名前を「**IncrementalCopyActivity**」に設定します。 

   ![コピー アクティビティ - 名前](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-name.png)
10. **[プロパティ]** ウィンドウで **[ソース]** タブに切り替え、以下の手順を実行します。

   1. **[Source Dataset]\(コピー元データセット\)** フィールドで SQL MI データセットの名前を指定します。 
   2. **[クエリの使用]** で **[クエリ]** を選択します。
   3. **[クエリ]** に次のように入力します。

      ```sql
      DECLARE @from_lsn binary(10), @to_lsn binary(10); 
      SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers'); 
      SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal', GETDATE());
      SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
      ```

   ![コピー アクティビティ - ソースの設定](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-settings.png)

11. プレビューをクリックし、クエリから変更された行数が正しく返されることを確認します。

    ![スクリーンショットは、クエリを確認するためのプレビューを示しています。](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-preview.png)
12. **[Sink]\(コピー先\)** タブに切り替えて、 **[Sink Dataset]\(コピー先データセット\)** フィールドで Azure Storage データセットを指定します。

    ![スクリーンショットは [シンク] タブを示しています。](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-sink-settings.png)
13. クリックしてメイン パイプライン キャンバスに戻り、**検索** アクティビティを **If 条件** アクティビティに 1 つずつ接続します。 **[検索]** アクティビティに付いている **緑** のボタンを **[If Condition]\(If 条件\)** アクティビティにドラッグします。

    ![検索アクティビティとコピー アクティビティを接続する](./media/tutorial-incremental-copy-change-data-capture-feature-portal/connect-lookup-if.png)
14. ツール バーの **[検証]** をクリックします。 検証エラーがないことを確認します。 **[>>]** をクリックして、 **[Pipeline Validation Report]\(パイプライン検証レポート\)** ウィンドウを閉じます。

    ![検証ボタン](./media/tutorial-incremental-copy-change-data-capture-feature-portal/validate-button.png)
15. [デバッグ] をクリックしてパイプラインをテストし、ファイルが保存場所に生成されることを確認します。

    ![増分パイプライン デバッグ - 2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-2.png)
16. **[すべて公開]** ボタンをクリックして、エンティティ (リンクされたサービス、データセット、およびパイプライン) を Data Factory サービスに発行します。 **[発行は成功しました]** というメッセージが表示されるまで待機します。

    ![[発行] ボタン](./media/tutorial-incremental-copy-change-data-capture-feature-portal/publish-button-2.png)    

### <a name="configure-the-tumbling-window-trigger-and-cdc-window-parameters"></a>タンブリング ウィンドウ トリガーと CDC ウィンドウ パラメーターを構成する 
このステップでは、頻繁なスケジュールでジョブを実行するためのタンブリング ウィンドウ トリガーを作成します。 タンブリング ウィンドウ トリガーの WindowStart および WindowEnd システム変数を使用し、CDC クエリで使用するパラメーターとしてパイプラインに渡します。

1. **IncrementalCopyPipeline** パイプラインの **[パラメーター]** タブに移動し、 **[+ 新規]** ボタンを使用して、2 つのパラメーター (**triggerStartTime** と **triggerEndTime**) をパイプラインに追加します。これは、タンブリング ウィンドウの開始時刻と終了時刻を表します。 デバッグのため、**YYYY-MM-DD HH24:MI:SS.FFF** という形式で既定値を追加します。ただし、triggerStartTime がテーブルで CDC を有効にする時刻より前にならないようにします。そうしないと、エラーが発生します。

    ![[Trigger Now]\(今すぐトリガー\) メニュー](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-parameters.png)
2. **検索** アクティビティの設定タブをクリックし、開始パラメーターと終了パラメーターを使用するようにクエリを構成します。 以下をクエリにコピーします。
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```

3. **If 条件** アクティビティの True の場合の **コピー** アクティビティに移動し、 **[ソース]** タブをクリックします。以下をクエリにコピーします。
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```
4. **コピー** アクティビティの **[Sink]\(コピー先\)** タブをクリックし、 **[開く]** をクリックして、データセットのプロパティを編集します。 **[パラメーター]** タブをクリックし、**triggerStart** という名前の新しいパラメーターを追加します    

    ![スクリーンショットは、[パラメーター] タブへの新しいパラメーターの追加を示しています。](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-2.png)
5. 次に、日付ベースのパーティションで **customers/incremental** サブディレクトリにデータを格納するように、データセットのプロパティを構成します。
   1. データセットのプロパティの **[接続]** タブをクリックし、 **[ディレクトリ]** と **[ファイル]** の両方のセクションに動的なコンテンツを追加します。 
   2. テキスト ボックスの下にある動的コンテンツ リンクをクリックして、 **[ディレクトリ]** セクションに次の式を入力します。
    
    ```sql
    @concat('customers/incremental/',formatDateTime(dataset().triggerStart,'yyyy/MM/dd'))
    ```
   3. **[ファイル]** セクションに次の式を入力します。 これにより、トリガーの開始日時を基にしてファイル名が作成され、csv 拡張子がサフィックスとして付けられます。
    
    ```sql
    @concat(formatDateTime(dataset().triggerStart,'yyyyMMddHHmmssfff'),'.csv')
    ```
    ![コピー先データセットの構成 - 3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-3.png)

   4. **IncrementalCopyPipeline** タブをクリックして **コピー** アクティビティの **[Sink]\(コピー先\)** 設定に戻ります。 
   5. データセットのプロパティを展開し、次の式を使用して、triggerStart パラメーター値に動的コンテンツを入力します。
     ```sql
     @pipeline().parameters.triggerStartTime
     ```
    ![コピー先データセットの構成 - 4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-4.png)

6. [デバッグ] をクリックしてパイプラインをテストし、確実にフォルダー構造と出力ファイルが想定どおりに生成されるようにします。 ファイルをダウンロードして開き、内容を確認します。 

    ![増分コピーのデバッグ - 3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-3.png)
7. パイプライン実行の入力パラメーターを調べて、パラメーターがクエリに確実に挿入されているようにします。

    ![増分コピーのデバッグ - 4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-4.png)
8. **[すべて公開]** ボタンをクリックして、エンティティ (リンクされたサービス、データセット、およびパイプライン) を Data Factory サービスに発行します。 **[発行は成功しました]** というメッセージが表示されるまで待機します。
9. 最後に、一定の間隔でパイプラインが実行されるようにタンブリング ウィンドウ トリガーを構成し、開始時刻と終了時刻のパラメーターを設定します。 
   1. **[トリガーの追加]** ボタンをクリックし、 **[New/Edit]\(新規作成/編集\)** を選択します

   ![新しいトリガーを追加する](./media/tutorial-incremental-copy-change-data-capture-feature-portal/add-trigger.png)

   2. トリガー名を入力し、上のデバッグ ウィンドウの終了時刻と同じ時刻に開始時刻を指定します。

   ![タンブリング ウィンドウ トリガー](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger.png)

   3. 次の画面で、開始および終了パラメーターにそれぞれ次の値を指定します。
    ```sql
    @formatDateTime(trigger().outputs.windowStartTime,'yyyy-MM-dd HH:mm:ss.fff')
    @formatDateTime(trigger().outputs.windowEndTime,'yyyy-MM-dd HH:mm:ss.fff')
    ```

   ![タンブリング ウィンドウ トリガー - 2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger-2.png)

> [!NOTE]
> トリガーは、公開された後でのみ実行されることにご注意ください。 さらに、タンブリング ウィンドウの予想される動作では、開始日から現在までのすべての履歴間隔が実行されます。 タンブリング ウィンドウ トリガーに関する詳細については、[こちら](./how-to-create-tumbling-window-trigger.md)を参照してください。 
  
10. **SQL Server Management Studio** を使用し、次の SQL を実行して、customer テーブルにいくつかの追加の変更を行います。
    ```sql
    insert into customers (customer_id, first_name, last_name, email, city) values (4, 'Farlie', 'Hadigate', 'fhadigate3@zdnet.com', 'Reading');
    insert into customers (customer_id, first_name, last_name, email, city) values (5, 'Anet', 'MacColm', 'amaccolm4@yellowbook.com', 'Portsmouth');
    insert into customers (customer_id, first_name, last_name, email, city) values (6, 'Elonore', 'Bearham', 'ebearham5@ebay.co.uk', 'Portsmouth');
    update customers set first_name='Elon' where customer_id=6;
    delete from customers where customer_id=5;
    ```
11. **[すべて公開]** ボタンをクリックします。 **[発行は成功しました]** というメッセージが表示されるまで待機します。  
12. 数分後にパイプラインがトリガーされ、新しいファイルが Azure Storage に読み込まれます


### <a name="monitor-the-incremental-copy-pipeline"></a>増分コピー パイプラインを監視する
1. 左側の **[監視]** タブをクリックします。 一覧にパイプラインの実行とその状態が表示されます。 一覧を更新するには、 **[最新の情報に更新]** をクリックします。 再実行アクションと消費量レポートにアクセスするには、パイプラインの名前の近くをポイントします。

    ![パイプライン実行](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-pipeline-runs.png)
2. パイプラインの実行に関連付けられたアクティビティの実行を表示するには、パイプラインの名前をクリックします。 変更されたデータが検出された場合は、コピー アクティビティを含む 3 つのアクティビティがリストに存在し、それ以外の場合は 2 つのエントリしか存在しません。 パイプライン実行ビューに戻るには、上部の **[すべてのパイプライン]** リンクをクリックします。

    ![アクティビティの実行](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-activity-runs.png)


### <a name="review-the-results"></a>結果の確認
`raw` コンテナーの `customers/incremental/YYYY/MM/DD` フォルダーにもう 1 つファイルが確認できます。

![増分コピーからの出力ファイル](media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-run.png)
 

## <a name="next-steps"></a>次のステップ
次のチュートリアルに進んで、LastModifiedDate に基づいて新規ファイルおよび変更されたファイルのみをコピーする方法について学習してください。

> [!div class="nextstepaction"]
>[lastmodifieddate を基準に新しいファイルをコピーする](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
