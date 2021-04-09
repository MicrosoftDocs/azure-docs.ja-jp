---
title: Azure portal を使用して SQL Server から Blob Storage にデータをコピーする
description: Azure Data Factory 内のセルフホステッド統合ランタイムを使って、オンプレミスのデータ ストアからクラウドにデータをコピーする方法について説明します。
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: fa251b80f8e44f48de565685a0841f57f48620f1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724967"
---
# <a name="copy-data-from-a-sql-server-database-to-azure-blob-storage"></a>SQL Server データベースから Azure Blob Storage にデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

このチュートリアルでは、Azure Data Factory ユーザー インターフェイス (UI) を使用して、SQL Server データベースから Azure Blob Storage にデータをコピーするデータ ファクトリ パイプラインを作成します。 セルフホステッド統合ランタイムを作成して使用すると、オンプレミス データ ストアとクラウド データ ストア間でデータを移動できます。

> [!NOTE]
> この記事では、Data Factory の概要については詳しく取り上げません。 詳細については、[Data Factory の概要](introduction.md)に関するページをご覧ください。

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * セルフホステッド統合ランタイムを作成します。
> * SQL Server と Azure Storage のリンクされたサービスを作成します。
> * SQL Server と Azure BLOB のデータセットを作成します。
> * コピー アクティビティでデータを移動するパイプラインを作成します。
> * パイプラインの実行を開始します。
> * パイプラインの実行を監視します。

## <a name="prerequisites"></a>前提条件
### <a name="azure-subscription"></a>Azure サブスクリプション
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

### <a name="azure-roles"></a>Azure ロール
Data Factory インスタンスを作成するには、Azure へのサインインに使用するユーザー アカウントが、"*共同作成者*" または "*所有者*" ロールに属しているか、Azure サブスクリプションの "*管理者*" である必要があります。

サブスクリプションで自分が持っているアクセス許可を表示するには、Azure Portal に移動します。 右上隅にあるユーザー名を選択し、 **[アクセス許可]** を選択してください。 複数のサブスクリプションにアクセスできる場合は、適切なサブスクリプションを選択します。 ロールにユーザーを追加する手順の例については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014、2016、2017
このチュートリアルでは、SQL Server データベースを "*ソース*" データ ストアとして使用します。 このチュートリアルで作成するデータ ファクトリ内のパイプラインは、この SQL Server データベース (ソース) から Blob Storage (シンク) にデータをコピーします。 SQL Server データベース内に **emp** という名前のテーブルを作成し、このテーブルにサンプル エントリをいくつか挿入します。

1. SQL Server Management Studio を起動します。 ご使用のマシンにまだインストールされていない場合は、「[SQL Server Management Studio のダウンロード](/sql/ssms/download-sql-server-management-studio-ssms)」にアクセスしてください。

1. 自分の資格情報で SQL Server インスタンスに接続します。

1. サンプル データベースを作成します。 ツリー ビューで **[データベース]** を右クリックし、 **[新しいデータベース]** を選択します。
1. **[新しいデータベース]** ウィンドウで、データベースの名前を入力し、 **[OK]** を選択します。

1. **emp** テーブルを作成していくつかのサンプル データを挿入するために、次のクエリ スクリプトをデータベースに対して実行します。 ツリー ビューで、作成したデータベースを右クリックし、 **[新しいクエリ]** をクリックします。

   ```
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
   ```

### <a name="azure-storage-account"></a>Azure ストレージ アカウント
このチュートリアルでは、コピー先/シンク データ ストアに汎用の Azure Storage アカウント (具体的には Blob Storage) を使用します。 汎用の Azure Storage アカウントがない場合は、「[ストレージ アカウントの作成](../storage/common/storage-account-create.md)」をご覧ください。 このチュートリアルで作成するデータ ファクトリ内のパイプラインは、この SQL Server データベース (ソース) から Blob Storage (シンク) にデータをコピーします。 

#### <a name="get-the-storage-account-name-and-account-key"></a>ストレージ アカウント名とアカウント キーの取得
このチュートリアルでは、ご利用のストレージ アカウントの名前とキーを使用します。 ご利用のストレージ アカウントの名前とキーを取得するには、次の手順を実行します。

1. Azure のユーザー名とパスワードを使用して、[Azure Portal](https://portal.azure.com) にサインインします。

1. 左側のウィンドウで、 **[すべてのサービス]** を選択します。 「**ストレージ**」というキーワードでフィルタリングして、 **[ストレージ アカウント]** を選択します。

    ![Storage account search](media/doc-common-process/search-storage-account.png)

1. ストレージ アカウントの一覧で、必要に応じてご利用のストレージ アカウントをフィルターで抽出します。 次に、ストレージ アカウントを選択します。

1. **[ストレージ アカウント]** ウィンドウで **[アクセス キー]** を選択します。

1. **[ストレージ アカウント名]** ボックスと **[key1]** ボックスの値をコピーし、メモ帳などのエディターに貼り付けます。これらの値は、後でこのチュートリアルの中で使用します。

#### <a name="create-the-adftutorial-container"></a>adftutorial コンテナーの作成
このセクションでは、**adftutorial** という名前の BLOB コンテナーを Blob Storage に作成します。

1. **[ストレージ アカウント]** ウィンドウで **[概要]** に移動し、 **[コンテナー]** を選択します。

    ![BLOB オプションを選択する](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. **[コンテナー]** ウィンドウで **[+ コンテナー]** を選択して新しいコンテナーを作成します。

1. **[新しいコンテナー]** ウィンドウの **[名前]** に「**adftutorial**」と入力します。 **[作成]** を選択します。

1. コンテナーの一覧で、作成した **[adftutorial]** を選択します。

1. **adftutorial** の **[コンテナー]** ウィンドウを開いたままにしておきます。 チュートリアルの最後で、このページを使用して出力を確認します。 このコンテナーには output フォルダーが Data Factory によって自動的に作成されます。手動で作成する必要はありません。

## <a name="create-a-data-factory"></a>Data Factory の作成
この手順では、データ ファクトリを作成するほか、Data Factory UI を起動してそのデータ ファクトリにパイプラインを作成します。

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を開きます。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
1. 左側のメニューで、 **[リソースの作成]**  >  **[統合]**  >  **[Data Factory]** を選択します。

   ![[新規] ウィンドウでの [Data Factory] の選択](./media/doc-common-process/new-azure-data-factory-menu.png)

1. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。

   データ ファクトリの名前は "*グローバルに一意*" にする必要があります。 名前フィールドで次のエラー メッセージが発生した場合は、データ ファクトリの名前を変更してください (yournameADFTutorialDataFactory など)。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関するページを参照してください。

   ![新しいデータ ファクトリ名](./media/doc-common-process/name-not-available-error.png)

1. データ ファクトリを作成する Azure **サブスクリプション** を選択します。
1. **[リソース グループ]** で、次の手順のいずれかを行います。

   - **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。

   - **[新規作成]** を選択し、リソース グループの名前を入力します。
        
     リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。
1. **[バージョン]** で、 **[V2]** を選択します。
1. **[場所]** で、データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 Data Factory によって使用されるデータ ストア (Storage、SQL Database など) やコンピューティング (Azure HDInsight など) は、他のリージョンに存在していてもかまいません。
1. **［作成］** を選択します

1. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。

    ![データ ファクトリのホーム ページ](./media/doc-common-process/data-factory-home-page.png)
1. **[Author & Monitor]\(作成と監視\)** タイルをクリックして、別のタブで Data Factory (UI) を起動します。


## <a name="create-a-pipeline"></a>パイプラインを作成する

1. **[Let's get started]\(始めましょう\)** ページで **[Create pipeline]\(パイプラインの作成\)** を選択します。 パイプラインが自動的に作成されます。 ツリー ビューのパイプラインとそのエディターが開かれるのがわかります。

   ![[Let's get started]\(始めましょう\) ページ](./media/doc-common-process/get-started-page.png)

1. [全般] パネルの **[プロパティ]** で、 **[名前]** に「**SQLServerToBlobPipeline**」を指定します。 次に、右上隅にある [プロパティ] アイコンをクリックしてパネルを折りたたみます。

1. **[アクティビティ]** ツール ボックスで **[Move & Transform]\(移動と変換\)** を展開します。 パイプライン デザイン サーフェイスに **[コピー]** アクティビティをドラッグ アンド ドロップします。 アクティビティの名前を「**CopySqlServerToAzureBlobActivity**」に設定します。

1. **[プロパティ]** ウィンドウの **[ソース]** タブに移動し、 **[+ 新規]** を選択します。

1. **[新しいデータセット]** ダイアログ ボックスで、**SQL Server** を検索します。 **[SQL Server]** を選択し、 **[続行]** を選択します。
    ![新しい SqlServer データセット](./media/tutorial-hybrid-copy-portal/create-sqlserver-dataset.png)

1. **[プロパティの設定]** ダイアログ ボックスの **[名前]** に、「**SqlServerDataset**」と入力します。 **[リンクされたサービス]** で **[+ 新規]** を選択します。 この手順でソース データ ストア (SQL Server データベース) への接続を作成します。

1. **[New Linked Service]\(新しいリンクされたサービス\)** ダイアログ ボックスで、 **[名前]** に「**SqlServerLinkedService**」と入力します。 **[Connect via integration runtime]\(統合ランタイム経由で接続\)** で **[+新規]** を選択します。  このセクションでは、セルフホステッド統合ランタイムを作成し、SQL Server データベースがあるオンプレミスのマシンに関連付けます。 セルフホステッド統合ランタイムは、マシンの SQL Server データベースから Blob Storage にデータをコピーするコンポーネントです。

1. **[Integration Runtime セットアップ]** ダイアログ ボックスで **[Self-Hosted]\(セルフホステッド\)** を選択し、 **[続行]** を選択します。

1. [名前] に「**TutorialIntegrationRuntime**」と入力します。 **[作成]** を選択します。

1. [設定] の **[Click here to launch the express setup for this computer]\(このコンピューターで高速セットアップを起動するにはここをクリック\)** を選択します。 この操作により、統合ランタイムがマシンにインストールされ、Data Factory に登録されます。 別の方法として、手動セットアップのオプションを使用できます。インストール ファイルをダウンロードして実行し、キーを使用して統合ランタイムを登録します。
    ![統合ランタイムのセットアップ](./media/tutorial-hybrid-copy-portal/intergration-runtime-setup.png)

1. **[Integration Runtime (セルフホステッド) 高速セットアップ]** ウィンドウで、処理が完了したら **[閉じる]** を選択します。

    ![Integration Runtime (セルフホステッド) 高速セットアップ](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. **[New linked service (SQL Server)]\(新しいリンクされたサービス (SQL Server)\)** ダイアログ ボックスで、 **[Connect via integration runtime]\(統合ランタイム経由で接続\)** の **TutorialIntegrationRuntime** が選択されていることを確認します。 その後、次の手順を行います。

    a. **[名前]** に「**SqlServerLinkedService**」と入力します。

    b. **[サーバー名]** に SQL Server インスタンスの名前を入力します。

    c. **[データベース名]** に、**emp** テーブルが含まれたデータベースの名前を入力します。

    d. Data Factory が SQL Server データベースへの接続に使用する適切な認証の種類を **[認証の種類]** で選択します。

    e. **[ユーザー名]** と **[パスワード]** に、ユーザー名とパスワードを入力します。 必要に応じて、ユーザー名として *mydomain\\myuser* を使用します。

    f. **[接続テスト]** を選択します。 この手順は、作成したセルフホステッド統合ランタイムを使用して Data Factory が SQL Server データベースに接続できることを確認するために行います。

    g. リンクされたサービスを保存するには、 **[作成]** を選択します。
 
    ![新しいリンクされたサービス (SQL Server)](./media/tutorial-hybrid-copy-portal/new-sqlserver-linked-service.png)

1. リンクされたサービスが作成されると、SqlServerDataset の **[プロパティの設定]** ページに戻ります。 次の手順を実行します。

    a. **[リンクされたサービス]** に **SqlServerLinkedService** が表示されていることを確認します。

    b. **[テーブル名]** で **[dbo].[emp]** を選択します。
    
    c. **[OK]** を選択します。

1. **SQLServerToBlobPipeline** のタブに移動するか、またはツリービューの **SQLServerToBlobPipeline** を選択します。

1. **[プロパティ]** ウィンドウの一番下にある **[シンク]** タブに移動し、 **[+ 新規]** を選択します。

1. **[新しいデータセット]** ダイアログ ボックスで、 **[Azure Blob Storage]** を選択します。 その後 **[続行]** を選択します。

1. **[形式の選択]** ダイアログ ボックスで、データ形式の種類を選択します。 その後 **[続行]** を選択します。

    ![データ形式の選択](./media/doc-common-process/select-data-format.png)

1. **[プロパティの設定]** ダイアログ ボックスで、[名前] に「**AzureBlobDataset**」と入力します。 **[リンクされたサービス]** ボックスの横にある **[+ 新規]** をクリックします。

1. **[New Linked Service (Azure Blob Storage)]\(新しいリンクされたサービス (Azure Blob Storage)\)** ダイアログ ボックスで、名前として「**AzureStorageLinkedService**」と入力し、 **[ストレージ アカウント]** 名の一覧からご自身のストレージ アカウントを選択します。 接続をテストし、 **[作成]** を選択して、リンクされたサービスをデプロイします。

1. リンクされたサービスが作成されると、 **[プロパティの設定]** ページに戻ります。 **[OK]** を選択します。

1. シンク データセットを開きます。 **[接続]** タブで、次の手順を実行します。

    a. **[リンクされたサービス]** で **AzureStorageLinkedService** が選択されていることを確認します。

    b. **[ファイルのパス]** で、**コンテナーまたはディレクトリ** の部分に「**adftutorial/fromonprem**」と入力します。 adftutorial コンテナーに出力フォルダーが存在しない場合、Data Factory によって自動的に出力フォルダーが作成されます。

    c. **ファイル** 部分については、 **[動的なコンテンツの追加]** を選択します。
    ![ファイル名を解決するための動的な式](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. `@CONCAT(pipeline().RunId, '.txt')` を追加し、 **[完了]** を選択します。 この操作で、ファイルの名前が PipelineRunID.txt に変更されます。

1. パイプラインが開かれているタブに移動するか、ツリー ビューでパイプラインを選択します。 **[Sink Dataset]\(シンク データセット\)** で **AzureBlobDataset** が選択されていることを確認します。

1. パイプライン設定を検証するには、パイプラインのツール バーにある **[検証]** を選択します。 **[Pipe validation output]\(パイプ検証出力\)** を閉じるには、 **>>** アイコンを選択します。
    ![パイプラインの検証](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
    

1. 作成したエンティティを Data Factory に発行するには、 **[すべて発行]** を選択します。

1. **発行が完了** したことを示すポップアップが表示されるまで待ちます。 発行の状態を確認するには、ウィンドウの上部にある **[通知の表示]** リンクを選択します。 通知ウィンドウを閉じるには、 **[閉じる]** を選択します。


## <a name="trigger-a-pipeline-run"></a>パイプラインの実行をトリガーする
パイプラインのツール バーの **[トリガーの追加]** を選択し、 **[Trigger Now]\(今すぐトリガー\)** を選択します。

## <a name="monitor-the-pipeline-run"></a>パイプラインの実行を監視します

1. **[監視]** タブに移動します。前の手順で手動でトリガーしたパイプラインを確認します。

1. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、 *[パイプライン名]* の **[SQLServerToBlobPipeline]** リンクを選択します。 
    ![パイプラインの実行を監視する](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)

1. **[アクティビティの実行]** ページで [詳細] (眼鏡アイコン) リンクを選択し、コピー操作の詳細を確認します。 再度パイプラインの実行ビューに移動するには、一番上にある **[すべてのパイプラインの実行]** を選択します。

## <a name="verify-the-output"></a>出力を検証する
このパイプラインは、`adftutorial` BLOB コンテナーに対して *fromonprem* という名前の出力フォルダーを自動的に作成します。 出力フォルダーに *[pipeline().RunId].txt* ファイルがあることを確認してください。


## <a name="next-steps"></a>次のステップ
このサンプルのパイプラインは、Blob Storage 内のある場所から別の場所にデータをコピーするものです。 以下の方法を学習しました。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * セルフホステッド統合ランタイムを作成します。
> * SQL Server と Storage のリンクされたサービスを作成します。
> * SQL Server と Blob Storage のデータセットを作成します。
> * コピー アクティビティでデータを移動するパイプラインを作成します。
> * パイプラインの実行を開始します。
> * パイプラインの実行を監視します。

Data Factory でサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。

次のチュートリアルに進んで、ソースからコピー先にデータを一括コピーする方法について学習しましょう。

> [!div class="nextstepaction"]
>[データを一括コピーする](tutorial-bulk-copy-portal.md)