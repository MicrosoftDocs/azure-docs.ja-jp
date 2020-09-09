---
title: Azure portal を使用してデータ ファクトリ パイプラインを作成する
description: このチュートリアルでは、Azure Portal を使用してパイプラインを備えたデータ ファクトリを作成するための詳細な手順について説明します。 パイプラインでコピー アクティビティを使用して、Azure Blob Storage から Azure SQL Database にデータをコピーします。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 05/28/2020
ms.author: jingwang
ms.openlocfilehash: 16b5eeb33f8be07d6257d8d7957ea2526ab9d3f1
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253967"
---
# <a name="copy-data-from-azure-blob-storage-to-a-database-in-azure-sql-database-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Blob Storage から Azure SQL Database のデータベースにデータをコピーする

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

このチュートリアルでは、Azure Data Factory ユーザー インターフェイス (UI) を使用してデータ ファクトリを作成します。 このデータ ファクトリのパイプラインでは、Azure Blob Storage から Azure SQL Database のデータベースにデータをコピーします。 このチュートリアルの構成パターンは、ファイルベースのデータ ストアからリレーショナル データ ストアへのコピーに適用されます。 ソースおよびシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する表を参照してください。

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
* **Azure ストレージ アカウント**。 Blob Storage を "*ソース*" データ ストアとして使用します。 ストレージ アカウントがない場合の作成手順については、[Azure のストレージ アカウントの作成](../storage/common/storage-account-create.md)に関するページを参照してください。
* **Azure SQL データベース**。 データベースを "*シンク*" データ ストアとして使用します。 Azure SQL Database のデータベースがない場合の作成手順については、[Azure SQL Database のデータベースの作成](../azure-sql/database/single-database-create-quickstart.md)に関するページを参照してください。

### <a name="create-a-blob-and-a-sql-table"></a>BLOB と SQL テーブルを作成する

ここからは、次の手順を実行して、チュートリアルで使用する Blob Storage と SQL データベースを準備します。

#### <a name="create-a-source-blob"></a>ソース BLOB を作成する

1. メモ帳を起動します。 次のテキストをコピーし、**emp.txt** ファイルとしてディスクに保存します。

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Blob Storage に **adftutorial** という名前のコンテナーを作成します。 このコンテナーに **input** という名前のフォルダーを作成します。 次に、**input** フォルダーに **emp.txt** ファイルをアップロードします。 Azure Portal を使用するか、または [Azure Storage Explorer](https://storageexplorer.com/) などのツールを使用して、これらのタスクを実行します。

#### <a name="create-a-sink-sql-table"></a>シンク SQL テーブルを作成する

1. 次の SQL スクリプトを使用して、**dbo.emp** テーブルをデータベースに作成します。

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

1. Azure サービスに SQL Server へのアクセスを許可します。 Data Factory から SQL Server にデータを書き込むことができるように、SQL Server で **[Azure サービスへのアクセスを許可]** が**オン**になっていることを確認します。 この設定を確認して有効にするには、論理 SQL サーバーで [概要] > [サーバー ファイアウォールの設定] に移動し、 **[Azure サービスへのアクセスを許可]** を **[オン]** に設定します。

## <a name="create-a-data-factory"></a>Data Factory の作成
この手順では、データ ファクトリを作成するほか、Data Factory UI を起動してそのデータ ファクトリにパイプラインを作成します。

1. **Microsoft Edge** または **Google Chrome** を開きます。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
2. 左側のメニューで、 **[リソースの作成]**  >  **[分析]**  >  **[Data Factory]** の順に選択します。
3. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。

   Azure データ ファクトリの名前は *グローバルに一意*にする必要があります。 データ ファクトリの名前の値に関するエラー メッセージが表示された場合は、別の名前を入力してください。 (yournameADFTutorialDataFactory など)。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関するページを参照してください。

     ![新しいデータ ファクトリ](./media/doc-common-process/name-not-available-error.png)
4. データ ファクトリを作成する Azure **サブスクリプション**を選択します。
5. **[リソース グループ]** で、次の手順のいずれかを行います。

    a. **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。

    b. **[新規作成]** を選択し、リソース グループの名前を入力します。 
         
    リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。 
6. **[バージョン]** で、 **[V2]** を選択します。
7. **[場所]** で、データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストア (Azure Storage、SQL Database など) やコンピューティング (Azure HDInsight など) は、他のリージョンに存在していてもかまいません。
8. **［作成］** を選択します
9. 作成が完了すると、その旨が通知センターに表示されます。 **[リソースに移動]** を選択して、Data factory ページに移動します。
10. **[Author & Monitor]\(作成と監視\)** を選択して、別のタブで Data Factory (UI) を起動します。


## <a name="create-a-pipeline"></a>パイプラインを作成する
この手順では、コピー アクティビティが含まれたパイプラインをデータ ファクトリに作成します。 コピー アクティビティによって、Blob Storage から SQL Database にデータがコピーされます。 [クイックスタート チュートリアル](quickstart-create-data-factory-portal.md)では、次の手順でパイプラインを作成しました。

1. リンクされたサービスを作成します。
1. 入力データセットと出力データセットを作成します。
1. パイプラインを作成します。

このチュートリアルでは、最初にパイプラインを作成します。 その後、パイプラインの構成に必要な場合にリンクされたサービスとデータセットを作成します。

1. **[Let's get started]\(始めましょう\)** ページで **[Create pipeline]\(パイプラインの作成\)** を選択します。

   ![パイプラインの作成](./media/doc-common-process/get-started-page.png)

1. 1. [全般] パネルの **[プロパティ]** の下で、 **[名前]** に **CopyPipeline** を指定します。 次に、右上隅にある [プロパティ] アイコンをクリックしてパネルを折りたたみます。

1. **[アクティビティ]** ツール ボックスで **[Move and Transform]\(移動と変換\)** カテゴリを展開し、ツール ボックスからパイプライン デザイナー画面に **[データのコピー]** アクティビティをドラッグ アンド ドロップします。 **[名前]** に「**CopyFromBlobToSql**」と指定します。

    ![コピー アクティビティ](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>ソースの構成

>[!TIP]
>このチュートリアルでは、ソース データ ストアの認証の種類として "*アカウント キー*" を使用しますが、サポートされている他の認証方法を選ぶこともできます。"*SAS URI*"、"*サービス プリンシパル*"、"*マネージド ID*" を必要に応じて使用してください。 詳細については、[この記事](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#linked-service-properties)の対応するセクションを参照してください。
>さらに、データ ストアのシークレットを安全に格納するために、Azure Key Vault の使用をお勧めします。 詳細については、[この記事](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)を参照してください。

1. **[ソース]** タブに移動します。 **[+ 新規]** を選択して、ソース データセットを作成します。

1. **[新しいデータセット]** ダイアログ ボックスで **[Azure Blob Storage]** を選択し、 **[続行]** をクリックします。 ソース データは Blob Storage にあるので、ソース データセットには **Azure Blob Storage** を選択します。

1. **[形式の選択]** ダイアログ ボックスで、データの形式の種類を選択して、 **[続行]** を選択します。

1. **[プロパティの設定]** ダイアログ ボックスで、[名前] に「**SourceBlobDataset**」を入力します。 **[First row as header]\(先頭の行を見出しとして使用\)** のチェック ボックスをオンにします。 **[リンクされたサービス]** ボックスの下にある **[+ 新規]** を選択します。

1. **[New Linked Service (Azure Blob Storage)]\(新しいリンクされたサービス (Azure Blob Storage)\)** ダイアログ ボックスで、名前として「**AzureStorageLinkedService**」と入力し、 **[ストレージ アカウント名]** の一覧からご自身のストレージ アカウントを選択します。 接続をテストし、 **[作成]** を選択して、リンクされたサービスをデプロイします。

1. リンクされたサービスが作成されると、 **[プロパティの設定]** ページに戻ります。 **[ファイル パス]** の横にある **[参照]** を選択します。

1. **adftutorial/input** フォルダーに移動し、**emp.txt** ファイルを選択して、 **[OK]** を選択します。

1. **[OK]** を選択します。 自動的にパイプライン ページに移動します。 **[ソース]** タブで、 **[SourceBlobDataset]** が選択されていることを確認します。 このページのデータをプレビューするには、 **[データのプレビュー]** を選択します。

    ![ソース データセット](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>シンクの構成
>[!TIP]
>このチュートリアルでは、シンク データ ストアの認証の種類として "*SQL 認証*" を使用しますが、サポートされている他の認証方法を選ぶこともできます。必要に応じて、"*サービス プリンシパル*" と "*マネージド ID*" を使用できます。 詳細については、[この記事](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties)の対応するセクションを参照してください。
>さらに、データ ストアのシークレットを安全に格納するために、Azure Key Vault の使用をお勧めします。 詳細については、[この記事](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)を参照してください。

1. **[シンク]** タブに移動し、 **[+ 新規]** を選択してシンク データセットを作成します。

1. **[新しいデータセット]** ダイアログ ボックスで、検索ボックスに「SQL」と入力してコネクタをフィルター処理し、 **[Azure SQL Database]** を選択して、 **[続行]** を選択します。 このチュートリアルでは、SQL データベースにデータをコピーします。

1. **[プロパティの設定]** ダイアログ ボックスで、[名前] に「**OutputSqlDataset**」を入力します。 **[リンクされたサービス]** ボックスの一覧から **[+ 新規]** を選択します。 データセットをリンクされたサービスに関連付ける必要があります。 リンクされたサービスには、Data Factory が実行時に SQL Database に接続するために使用する接続文字列が含まれています。 データセットは、コンテナー、フォルダー、データのコピー先のファイル (オプション) を指定します。

1. **[New Linked Service (Azure SQL Database)]\(新しいリンクされたサービス (Azure SQL Database)\)** ダイアログ ボックスで、次の手順を実行します。

    a. **[名前]** に「**AzureSqlDatabaseLinkedService**」と入力します。

    b. **[サーバー名]** で、使用する SQL Server インスタンスを選択します。

    c. **[データベース名]** で、使用するデータベースを選択します。

    d. **[ユーザー名]** に、ユーザーの名前を入力します。

    e. **[パスワード]** に、ユーザーのパスワードを入力します。

    f. **[テスト接続]** を選択して接続をテストします。

    g. **[作成]** を選択して、リンクされたサービスをデプロイします。

    ![新しいリンクされたサービスの保存](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. **[プロパティの設定]** ダイアログ ボックスに自動的に移動します。 **[テーブル]** で **[dbo].[emp]** を選択します。 **[OK]** をクリックします。

1. パイプラインがあるタブに移動し、 **[Sink Dataset]\(シンク データセット\)** で **OutputSqlDataset** が選択されていることを確認します。

    ![パイプラインのタブ](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

必要に応じて「[コピー アクティビティでのスキーマ マッピング](copy-activity-schema-and-type-mapping.md)」に従い、コピー元のスキーマをコピー先の対応するスキーマにマッピングすることができます。

## <a name="validate-the-pipeline"></a>パイプラインを検証する
パイプラインを検証するには、ツール バーから **[検証]** を選択します。

パイプラインに関連付けられている JSON コードを確認するには、右上にある **[コード]** をクリックします。

## <a name="debug-and-publish-the-pipeline"></a>パイプラインをデバッグして発行する
Data Factory または独自の Azure Repos Git リポジトリにアーティファクト (リンクされたサービス、データセット、パイプライン) を発行する前に、パイプラインをデバッグできます。

1. パイプラインをデバッグするには、ツール バーで **[デバッグ]** を選択します。 ウィンドウ下部の **[出力]** タブにパイプラインの実行の状態が表示されます。

1. パイプラインを適切に実行できたら、上部のツール バーで **[すべて発行]** を選択します。 これにより、作成したエンティティ (データセットとパイプライン) が Data Factory に発行されます。

1. **[正常に発行されました]** というメッセージが表示されるまで待機します。 通知メッセージを表示するには、右上にある **[通知の表示]** (ベル ボタン) をクリックします。

## <a name="trigger-the-pipeline-manually"></a>パイプラインを手動でトリガーする
この手順では、前の手順で発行したパイプラインを手動でトリガーします。

1. ツール バーの **[トリガー]** を選択し、 **[Trigger Now]\(今すぐトリガー\)** を選択します。 **[Pipeline Run]\(パイプラインの実行\)** ページで **[OK]** を選択します。  

1. 左側の **[監視]** タブに移動します。 手動トリガーによってトリガーされたパイプラインの実行が表示されます。 **[パイプライン名]** 列のリンクを使用して、アクティビティの詳細を表示したりパイプラインを再実行したりできます。

    [![パイプラインの実行を監視する](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png)](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png#lightbox)

1. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、 **[パイプライン名]** 列の **[CopyPipeline]** リンクを選択します。 この例では、アクティビティが 1 つだけなので、一覧に表示されるエントリは 1 つのみです。 コピー操作の詳細を確認するには、 **[ACTIVITY NAME]\(アクティビティ名\)** 列の **[詳細]** リンク (眼鏡アイコン) を選択します。 再度パイプラインの実行ビューに移動するには、一番上にある **[すべてのパイプラインの実行]** を選択します。 表示を更新するには、 **[最新の情報に更新]** を選択します。

    [![アクティビティの実行を監視する](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png#lightbox)

1. データベースの **emp** テーブルに 2 つの行が追加されていることを確認します。

## <a name="trigger-the-pipeline-on-a-schedule"></a>スケジュールに基づいてパイプラインをトリガーする
このスケジュールでは、パイプラインのスケジュール トリガーを作成します。 このトリガーは、指定されたスケジュール (1 時間に 1 回、毎日など) に基づいてパイプラインを実行します。 ここでは、指定された終了日時まで毎分実行されるようトリガーを設定します。

1. [監視] タブの上にある左側の **[作成者]** タブに移動します。

1. パイプラインに移動し、ツール バーの **[トリガー]** をクリックして、 **[新規/編集]** を選択します。

1. **[トリガーの追加]** ダイアログ ボックスで、 **[Choose trigger]\(トリガーの選択\)** 領域の **[+ 新規]** を選択します。

1. **[新しいトリガー]** ウィンドウで、次の手順を実行します。

    a. **[名前]** に「**RunEveryMinute**」と入力します。

    b. **[終了]** で **[指定日]** を選択します。

    c. **[End On]\(終了日\)** のドロップダウン リストを選択します。

    d. **現在の日付**のオプションを選択します。 既定では、終了日は翌日に設定されています。

    e. **[終了時刻]** の部分を現在の日時の数分後に変更します。 トリガーは、変更を発行した後にのみアクティブ化されます。 これをわずか数分後に設定し、それまでに発行しなかった場合、トリガー実行は表示されません。

    f. **[OK]** を選択します。

    g. **[アクティブ化]** オプションで **[はい]** を選択します。

    h. **[OK]** を選択します。

    > [!IMPORTANT]
    > パイプラインの実行ごとにコストが関連付けられるため、終了日は適切に設定してください。

1. **[トリガーの編集]** ページで警告を確認し、 **[保存]** を選択します。 この例のパイプラインにはパラメーターはありません。

1. **[すべて発行]** をクリックして、変更を発行します。

1. 左側の **[モニター]** タブに移動して、トリガーされたパイプラインの実行を確認します。

    [![トリガーされたパイプラインの実行](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png#lightbox)

1. **パイプラインの実行**ビューから**トリガーの実行**ビューに切り替えるには、ウィンドウの左側の **[Trigger Runs]\(トリガーの実行\)** を選択します。

1. トリガーの実行が一覧で表示されます。

1. 指定された終了日時まで、(各パイプライン実行について) 1 分ごとに 2 つの行が **emp** テーブルに挿入されていることを確認します。

## <a name="next-steps"></a>次のステップ
このサンプルのパイプラインは、Blob Storage 内のある場所から別の場所にデータをコピーします。 以下の方法を学習しました。

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
