---
title: プライベート エンドポイントを使用して Azure Data Factory パイプラインを作成する
description: このチュートリアルでは、Azure Portal を使用してパイプラインを備えたデータ ファクトリを作成するための詳細な手順について説明します。 パイプラインでコピー アクティビティを使用して、Azure Blob Storage から Azure SQL データベースにデータをコピーします。
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.author: jingwang
ms.openlocfilehash: a3cc3686d4b256fc7b8702f34cdf393dc1b0e25f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740394"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>プライベート エンドポイントを使用して Azure Blob Storage から SQL データベースに安全にデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

このチュートリアルでは、Azure Data Factory ユーザー インターフェイス (UI) を使用してデータ ファクトリを作成します。 *このデータ ファクトリのパイプラインでは、[Azure Data Factory マネージド仮想ネットワーク](managed-virtual-network-private-endpoint.md)のプライベート エンドポイントを使用して、Azure Blob Storage から Azure SQL データベースに安全にデータをコピーします (どちらも、選択したネットワークへのアクセスのみを許可します)。* このチュートリアルの構成パターンは、ファイルベースのデータ ストアからリレーショナル データ ストアへのコピーに適用されます。 ソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](./copy-activity-overview.md)」の表を参照してください。

> [!NOTE]
> Data Factory を初めて使用する場合は、「[Azure Data Factory の概要](./introduction.md)」を参照してください。

このチュートリアルでは、次の手順を実行します。

* データ ファクトリを作成します。
* コピー アクティビティを含むパイプラインを作成します。


## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* **Azure ストレージ アカウント**。 Blob Storage を "*ソース*" データ ストアとして使用します。 ストレージ アカウントがない場合の作成手順については、[Azure のストレージ アカウントの作成](../storage/common/storage-account-create.md?tabs=azure-portal)に関するページを参照してください。 *ストレージ アカウントで、選択したネットワーク からのアクセスのみが許可されていることを確認します。* 
* **Azure SQL データベース**。 データベースを "*シンク*" データ ストアとして使用します。 Azure SQL データベースがない場合の作成手順については、[SQL データベースの作成](../azure-sql/database/single-database-create-quickstart.md)に関するページを参照してください。 *SQL Database アカウントで、選択したネットワーク からのアクセスのみが許可されていることを確認します。* 

### <a name="create-a-blob-and-a-sql-table"></a>BLOB と SQL テーブルを作成する

ここからは、次の手順を実行して、チュートリアル用の Blob Storage と SQL データベースを準備します。

#### <a name="create-a-source-blob"></a>ソース BLOB を作成する

1. メモ帳を開きます。 次のテキストをコピーし、**emp.txt** ファイルとしてディスクに保存します。

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Blob Storage に **adftutorial** という名前のコンテナーを作成します。 このコンテナーに **input** という名前のフォルダーを作成します。 次に、**input** フォルダーに **emp.txt** ファイルをアップロードします。 Azure Portal を使用するか、または [Azure Storage Explorer](https://storageexplorer.com/) などのツールを使用して、これらのタスクを実行します。

#### <a name="create-a-sink-sql-table"></a>シンク SQL テーブルを作成する

次の SQL スクリプトを使用して、**dbo.emp** テーブルを SQL データベースに作成します。

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

## <a name="create-a-data-factory"></a>Data Factory の作成
この手順では、データ ファクトリを作成するほか、Data Factory UI を起動してそのデータ ファクトリにパイプラインを作成します。

1. Microsoft Edge または Google Chrome を開きます。 現在、Data Factory UI をサポートしている Web ブラウザーは Microsoft Edge と Google Chrome のみです。

1. 左側のメニューで、 **[リソースの作成]**  >  **[分析]**  >  **[Data Factory]** の順に選択します。

1. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。

   Azure データ ファクトリの名前は *グローバルに一意* にする必要があります。 データ ファクトリの名前の値に関するエラー メッセージが表示された場合は、別の名前を入力してください(yournameADFTutorialDataFactory など)。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](./naming-rules.md)に関するページを参照してください。

1. データ ファクトリを作成する Azure **サブスクリプション** を選択します。

1. **[リソース グループ]** で、次の手順のいずれかを行います。

    - **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。
    - **[新規作成]** を選択し、リソース グループの名前を入力します。 
     
    リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。 

1. **[バージョン]** で、 **[V2]** を選択します。

1. **[場所]** で、データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストア (Azure Storage、SQL Database など) やコンピューティング (Azure HDInsight など) は、他のリージョンに存在していてもかまいません。

1. **［作成］** を選択します

1. 作成が完了すると、その旨が通知センターに表示されます。 **[リソースに移動]** を選択して、 **[Data Factory]** ページに移動します。

1. **[Author & Monitor]\(作成と監視\)** を選択して、別のタブで Data Factory (UI) を起動します。

## <a name="create-an-azure-integration-runtime-in-data-factory-managed-virtual-network"></a>Data Factory マネージド仮想ネットワークに Azure 統合ランタイムを作成する
この手順では、Azure 統合ランタイムを作成し、Data Factory マネージド仮想ネットワークを有効にします。

1. Data Factory ポータルで、 **[管理]** に移動し、 **[新規]** を選択して新しい Azure 統合ランタイムを作成します。

   ![新しい Azure 統合ランタイムの作成を示すスクリーンショット。](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. **[Integration runtime setup]\(統合ランタイムのセットアップ\)** ページで、必要な機能に基づいて作成する統合ランタイムを選択します。 このチュートリアルでは、 **[Azure、セルフホステッド]** を選択し、 **[続行]** をクリックします。 
1. **[Azure]** を選択し、 **[続行]** をクリックして、Azure Integration Runtime を作成します。

   ![新しい Azure 統合ランタイムを示すスクリーンショット。](./media/tutorial-copy-data-portal-private/azure-ir.png)
1. **[仮想ネットワークの構成 (プレビュー)]** で、 **[有効化]** を選択します。

   ![新しい Azure 統合ランタイムの有効化を示すスクリーンショット。](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
1. **［作成］** を選択します

## <a name="create-a-pipeline"></a>パイプラインを作成する
この手順では、コピー アクティビティが含まれたパイプラインをデータ ファクトリに作成します。 コピー アクティビティによって、Blob Storage から SQL Database にデータがコピーされます。 [クイックスタート チュートリアル](./quickstart-create-data-factory-portal.md)では、次の手順でパイプラインを作成しました。

1. リンクされたサービスを作成します。
1. 入力データセットと出力データセットを作成します。
1. パイプラインを作成します。

このチュートリアルでは、最初にパイプラインを作成します。 その後、パイプラインの構成に必要な場合にリンクされたサービスとデータセットを作成します。

1. **[Let's get started]\(始めましょう\)** ページで **[Create pipeline]\(パイプラインの作成\)** を選択します。

   ![パイプラインの作成を示すスクリーンショット。](./media/doc-common-process/get-started-page.png)
1. パイプラインの [プロパティ] ペインで、パイプライン名として「**CopyPipeline**」と入力します。

1. **[アクティビティ]** ツール ボックスで **[Move and Transform]\(移動と変換\)** カテゴリを展開し、ツール ボックスからパイプライン デザイナー画面に **[データのコピー]** アクティビティをドラッグします。 名前に「**CopyFromBlobToSql**」と入力します。

    ![コピー アクティビティを示すスクリーン ショット。](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-a-source"></a>ソースを構成する

>[!TIP]
>このチュートリアルでは、ソース データ ストアの認証の種類として "**アカウント キー**" を使用します。 また、必要に応じて、**SAS URI**、**サービス プリンシパル**、**マネージド ID** など、サポートされている他の認証方法を選ぶこともできます。 詳細については、「[Azure Data Factory を使用して Azure BLOB ストレージのデータをコピーおよび変換する](./connector-azure-blob-storage.md#linked-service-properties)」の対応するセクションを参照してください。
>
>さらに、データ ストアのシークレットを安全に格納するために、Azure Key Vault の使用をお勧めします。 詳細な説明と図解については、「[Azure Key Vault への資格情報の格納](./store-credentials-in-key-vault.md)」を参照してください。

#### <a name="create-a-source-dataset-and-linked-service"></a>ソース データセットおよびリンクされたサービスを作成する

1. **[ソース]** タブに移動します。 **[+ 新規]** を選択して、ソース データセットを作成します。

1. **[新しいデータセット]** ダイアログ ボックスで **[Azure Blob Storage]** を選択し、 **[続行]** をクリックします。 ソース データは Blob Storage にあるので、ソース データセットには **Azure Blob Storage** を選択します。

1. **[形式の選択]** ダイアログ ボックスで、データの形式の種類を選択して、 **[続行]** を選択します。

1. **[プロパティの設定]** ダイアログ ボックスで、 **[名前]** に「**SourceBlobDataset**」を入力します。 **[First row as header]\(先頭の行を見出しとして使用\)** のチェック ボックスをオンにします。 **[リンクされたサービス]** ボックスの下にある **[+ 新規]** を選択します。

1. **[New linked service (Azure Blob Storage)]\(新しいリンクされたサービス (Azure Blob Storage)\)** ダイアログ ボックスで、 **[名前]** として「**AzureStorageLinkedService**」と入力し、 **[ストレージ アカウント名]** の一覧からご自身のストレージ アカウントを選択します。 

1. **[Interactive authoring]\(インタラクティブな作成\)** を必ず有効にしてください。 これは有効になるまでに 1 分程かかる場合があります。

    ![インタラクティブな作成を示すスクリーンショット。](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. **[接続テスト]** を選択します。 これは、ストレージ アカウントが、**選択したネットワーク** からのアクセスのみを許可していて、Data Factory に、使用する前に承認が必要なプライベート エンドポイントの作成を要求する場合、失敗します。 エラー メッセージ内に、プライベート エンドポイントを作成するためのリンクが表示されます。それをたどることで、マネージド プライベート エンドポイントを作成できます。 代わりに、 **[管理]** タブに直接移動し、[次のセクション](#create-a-managed-private-endpoint)の指示に従って、マネージド プライベート エンドポイントを作成する方法もあります。

   > [!NOTE]
   > データ ファクトリ インスタンスでは、そのすべてで **[管理]** タブを使用できない場合があります。 表示されない場合は、 **[作成者]**  >  **[接続]**  >  **[プライベート エンドポイント]** を選択して、プライベート エンドポイントにアクセスできます。
1. ダイアログ ボックスは開いたままにして、ストレージ アカウントに移動します。

1. [このセクション](#approval-of-a-private-link-in-a-storage-account)の手順に従って、プライベート リンクを承認します。

1. ダイアログ ボックスに戻ります。 もう一度 **[接続テスト]** を選択し、 **[作成]** を選択して、リンクされたサービスをデプロイします。

1. リンクされたサービスが作成されると、 **[プロパティの設定]** ページに戻ります。 **[ファイル パス]** の横にある **[参照]** を選択します。

1. **adftutorial/input** フォルダーに移動して **emp.txt** ファイルを選択し、 **[OK]** を選択します。

1. **[OK]** を選択します。 自動的にパイプライン ページに移動します。 **[ソース]** タブで、 **[SourceBlobDataset]** が選択されていることを確認します。 このページのデータをプレビューするには、 **[データのプレビュー]** を選択します。

    ![ソース データセットを示すスクリーンショット。](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>マネージド プライベート エンドポイントを作成する

接続をテストした際にハイパーリンクを選択しなかった場合は、パスに従います。 次に、作成済みのリンクされたサービスに接続するマネージド プライベート エンドポイントを作成する必要があります。

1. **[管理]** タブに移動します。

   > [!NOTE]
   > Data Factory インスタンスでは、そのすべてで **[管理]** タブを使用できない場合があります。 表示されない場合は、 **[作成者]**  >  **[接続]**  >  **[プライベート エンドポイント]** を選択して、プライベート エンドポイントにアクセスできます。

1. **[マネージド プライベート エンドポイント]** セクションに移動します。

1. **[マネージド プライベート エンドポイント]** で、 **[+ 新規]** を選択します。

    ![[マネージド プライベート エンドポイント] の [新規] ボタンを示すスクリーンショット。](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. 一覧から **[Azure Blob Storage]** タイルを選択し、 **[続行]** を選択します。

1. 作成したストレージ アカウントの名前を入力します。

1. **［作成］** を選択します

1. 数秒後に、作成されたプライベート リンクに承認が必要であることが表示されます。

1. 作成したプライベート エンドポイントを選択します。 ストレージ アカウント レベルでプライベート エンドポイントを承認できるハイパーリンクが表示されます。

    ![[マネージド プライベート エンドポイント] ペインを示すスクリーンショット。](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>ストレージ アカウントでプライベート リンクを承認する
1. ストレージ アカウントで、 **[設定]** セクションの **[プライベート エンドポイント接続]** に移動します。

1. 作成したプライベート エンドポイントのチェック ボックスをオンにし、 **[承認]** を選択します。

    ![プライベート エンドポイントの [承認] ボタンを示すスクリーンショット。](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. 説明を追加し、 **[はい]** を選択します。
1. Data Factory の **[管理]** タブにある **[マネージド プライベート エンドポイント]** セクションに戻ります。
1. 約 1 分から 2 分後に、Data Factory UI にプライベート エンドポイントの承認が表示されます。


### <a name="configure-a-sink"></a>シンクを構成する
>[!TIP]
>このチュートリアルでは、シンク データ ストアの認証の種類として **SQL 認証** を使用します。 また、必要に応じて、**サービス プリンシパル**、**マネージド ID** など、サポートされている他の認証方法を選ぶこともできます。 詳細については、「[Azure Data Factory を使用して Azure SQL Database のデータをコピーおよび変換する](./connector-azure-sql-database.md#linked-service-properties)」の対応するセクションを参照してください。
>
>さらに、データ ストアのシークレットを安全に格納するために、Azure Key Vault の使用をお勧めします。 詳細な説明と図解については、「[Azure Key Vault への資格情報の格納](./store-credentials-in-key-vault.md)」を参照してください。

#### <a name="create-a-sink-dataset-and-linked-service"></a>シンク データセットおよびリンクされたサービスを作成する
1. **[シンク]** タブに移動し、 **[+ 新規]** を選択してシンク データセットを作成します。

1. **[新しいデータセット]** ダイアログ ボックスで、検索ボックスに「**SQL**」と入力して、コネクタをフィルター処理します。 **[Azure SQL Database]** を選び、 **[続行]** を選びます。 このチュートリアルでは、SQL データベースにデータをコピーします。

1. **[プロパティの設定]** ダイアログ ボックスで、 **[名前]** に「**OutputSqlDataset**」を入力します。 **[リンクされたサービス]** ドロップダウン リストから **[+ 新規]** を選択します。 データセットをリンクされたサービスに関連付ける必要があります。 リンクされたサービスには、Data Factory が実行時に SQL データベースに接続するために使用する接続文字列が含まれています。 データセットは、コンテナー、フォルダー、データのコピー先のファイル (オプション) を指定します。

1. **[New Linked Service (Azure SQL Database)]\(新しいリンクされたサービス (Azure SQL Database)\)** ダイアログ ボックスで、次の手順を実行します。

    1. **[名前]** に「**AzureSqlDatabaseLinkedService**」と入力します。
    1. **[サーバー名]** で、使用する SQL Server インスタンスを選択します。
    1. **[Interactive authoring]\(インタラクティブな作成\)** を必ず有効にしてください。
    1. **[データベース名]** で、使用する SQL データベースを選択します。
    1. **[ユーザー名]** に、ユーザーの名前を入力します。
    1. **[パスワード]** に、ユーザーのパスワードを入力します。
    1. **[接続テスト]** を選択します。 これは失敗します。SQL サーバーが、**選択したネットワーク** からのアクセスのみを許可し、Data Factory に、使用する前に承認が必要なプライベート エンドポイントの作成を要求するためです。 エラー メッセージ内に、プライベート エンドポイントを作成するためのリンクが表示されます。それをたどることで、マネージド プライベート エンドポイントを作成できます。 代わりに、 **[管理]** タブに直接移動し、次のセクションの指示に従って、マネージド プライベート エンドポイントを作成する方法もあります。
    1. ダイアログ ボックスは開いたままにして、選択した SQL サーバーに移動します。
    1. [このセクション](#approval-of-a-private-link-in-sql-server)の手順に従って、プライベート リンクを承認します。
    1. ダイアログ ボックスに戻ります。 もう一度 **[接続テスト]** を選択し、 **[作成]** を選択して、リンクされたサービスをデプロイします。

1. **[プロパティの設定]** ダイアログ ボックスに自動的に移動します。 **[テーブル]** で **[dbo].[emp]** を選択します。 **[OK]** をクリックします。

1. パイプラインがあるタブに移動し、 **[シンク データセット]** で **OutputSqlDataset** が選択されていることを確認します。

    ![[パイプライン] タブを示すスクリーンショット。](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)

必要に応じて「[コピー アクティビティでのスキーマ マッピング](./copy-activity-schema-and-type-mapping.md)」に従い、コピー元のスキーマをコピー先の対応するスキーマにマッピングすることができます。

#### <a name="create-a-managed-private-endpoint"></a>マネージド プライベート エンドポイントを作成する

接続をテストした際にハイパーリンクを選択しなかった場合は、パスに従います。 次に、作成済みのリンクされたサービスに接続するマネージド プライベート エンドポイントを作成する必要があります。

1. **[管理]** タブに移動します。
1. **[マネージド プライベート エンドポイント]** セクションに移動します。
1. **[マネージド プライベート エンドポイント]** で、 **[+ 新規]** を選択します。

    ![[マネージド プライベート エンドポイント] の [新規] ボタンを示すスクリーンショット。](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. 一覧から **[Azure SQL Database]** タイルを選択し、 **[続行]** を選択します。
1. 選択した SQL サーバーの名前を入力します。
1. **［作成］** を選択します
1. 数秒後に、作成されたプライベート リンクに承認が必要であることが表示されます。
1. 作成したプライベート エンドポイントを選択します。 SQL サーバー レベルでプライベート エンドポイントを承認できるハイパーリンクが表示されます。


#### <a name="approval-of-a-private-link-in-sql-server"></a>SQL サーバーでのプライベート リンクの承認
1. SQL サーバーで、 **[設定]** セクションの **[プライベート エンドポイント接続]** に移動します。
1. 作成したプライベート エンドポイントのチェック ボックスをオンにし、 **[承認]** を選択します。
1. 説明を追加し、 **[はい]** を選択します。
1. Data Factory の **[管理]** タブにある **[マネージド プライベート エンドポイント]** セクションに戻ります。
1. プライベート エンドポイントの承認が表示されるまでに 1 分から 2 分かかります。

#### <a name="debug-and-publish-the-pipeline"></a>パイプラインをデバッグして発行する

Data Factory または独自の Azure Repos Git リポジトリにアーティファクト (リンクされたサービス、データセット、パイプライン) を発行する前に、パイプラインをデバッグできます。

1. パイプラインをデバッグするには、ツール バーで **[デバッグ]** を選択します。 ウィンドウ下部の **[出力]** タブにパイプラインの実行の状態が表示されます。
1. パイプラインを適切に実行できたら、上部のツール バーで **[すべて発行]** を選択します。 これにより、作成したエンティティ (データセットとパイプライン) が Data Factory に発行されます。
1. **[正常に発行されました]** というメッセージが表示されるまで待機します。 通知メッセージを表示するには、右上隅にある **[通知の表示]** (ベル ボタン) を選択 ます。


#### <a name="summary"></a>まとめ
このサンプルのパイプラインでは、Data Factory マネージド仮想ネットワークのプライベート エンドポイントを使用して、Blob Storage から SQL Database にデータをコピーします。 以下の方法を学習しました。

* データ ファクトリを作成します。
* コピー アクティビティを含むパイプラインを作成します。