---
title: データを移動する - Data Management Gateway
description: オンプレミスとクラウドの間でデータを移動するためのデータ ゲートウェイを設定します。 Azure Data Factory で Data Management Gateway を使用してデータを移動します。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 039a19f38da4e651ee35fe60ba2b95a40cf890b0
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931903"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する
> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[Data Factory を使用したオンプレミスとクラウド間でのデータのコピー](../tutorial-hybrid-copy-powershell.md)に関するページを参照してください。

この記事では、Data Factory を使用したオンプレミス データ ストアとクラウド データ ストア間でのデータ統合の概要について説明します。 この記事は、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事と、Data Factory の中核となる概念である[データセット](data-factory-create-datasets.md)と[パイプライン](data-factory-create-pipelines.md)に関する他の記事に基づいています。

## <a name="data-management-gateway"></a>Data Management Gateway
オンプレミス データ ストアとの間でデータを移動できるようにするには、オンプレミス コンピューターに Data Management Gateway をインストールする必要があります。 ゲートウェイはデータ ストアと同じコンピューターにインストールできるほか、ゲートウェイがデータ ストアに接続できれば別のコンピューターにインストールしてもかまいません。

> [!IMPORTANT]
> Data Management Gateway の詳細については、「 [Data Management Gateway](data-factory-data-management-gateway.md) 」をご覧ください。 

以下のチュートリアルでは、オンプレミスの **SQL Server** データベースから Azure BLOB ストレージにデータを移動するパイプラインを備えたデータ ファクトリを作成する方法を説明します。 チュートリアルの一環として、ご使用のコンピューターに Data Management Gateway をインストールして構成します。

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>チュートリアル: オンプレミスのデータをクラウドにコピーする
このチュートリアルでは、次の手順を実行します。 

1. データ ファクトリを作成します。
2. データ管理ゲートウェイを作成します。 
3. ソース データ ストアとシンク データ ストアのリンクされたサービスを作成します。
4. 入力データと出力データを表すデータセットを作成します。
5. コピー アクティビティでデータを移動するパイプラインを作成します。

## <a name="prerequisites-for-the-tutorial"></a>このチュートリアルの前提条件
このチュートリアルを開始する前に、以下の前提条件を満たしている必要があります。

* **Azure サブスクリプション**。  サブスクリプションがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/) のページを参照してください。
* **Azure ストレージ アカウント**。 このチュートリアルでは、BLOB ストレージを**コピー先/シンク** データ ストアとして使用します。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「 [ストレージ アカウントの作成](../../storage/common/storage-quickstart-create-account.md) 」をご覧ください。
* **SQL Server**。 このチュートリアルでは、オンプレミスの SQL Server データベースを**ソース** データ ストアとして使用します。 

## <a name="create-data-factory"></a>データ ファクトリの作成
この手順では、Azure Portal を使用して **ADFTutorialOnPremDF**という名前の Azure Data Factory インスタンスを作成します。

1. [Azure Portal](https://portal.azure.com) にログインします。
2. **[リソースの作成]** 、 **[インテリジェンス + 分析]** 、 **[データ ファクトリ]** の順にクリックします。

   ![New->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. **[新しいデータ ファクトリ]** ページで、[名前] に「**ADFTutorialOnPremDF**」と入力します。

    ![スタート画面への追加](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Azure Data Factory の名前はグローバルに一意にする必要があります。 エラー "**Data factory name "ADFTutorialOnPremDF" is not available (データ ファクトリ名 "ADFTutorialOnPremDF" は使用できません)** " が発生した場合は、データ ファクトリの名前を変更して (yournameADFTutorialOnPremDF など) 作成し直してください。 このチュートリアルでは以降の手順の実行中に、この名前を ADFTutorialOnPremDF の代わりに使用します。
   >
   > データ ファクトリの名前は今後、**DNS** 名として登録される可能性があるため、一般ユーザーに表示される場合があります。
   >
   >
4. データ ファクトリを作成する **Azure サブスクリプション** を選択します。
5. 既存の **リソース グループ** を選択するか、新しいリソース グループを作成します。 このチュートリアルでは、**ADFTutorialResourceGroup** という名前のリソース グループを作成します。
6. **[新しい Data Factory]** ページで **[作成]** をクリックします。

   > [!IMPORTANT]
   > Data Factory インスタンスを作成するには、サブスクリプション/リソース グループ レベルで [Data Factory の共同作業者](../../role-based-access-control/built-in-roles.md#data-factory-contributor) ロールのメンバーである必要があります。
   >
   >
7. 作成が完了すると、次の図に示すような **[Data Factory]** ページが表示されます。

   ![Data Factory ホーム ページ](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>ゲートウェイの作成
1. **[Data Factory]** ページで、 **[作成およびデプロイ]** タイルをクリックして、データ ファクトリ用の**エディター**を起動します。

    ![[作成とデプロイ] タイル](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. Data Factory Editor のツール バーで **[...More (...詳細)]** をクリックし、 **[新しいデータ ゲートウェイ]** をクリックします。 または、ツリー ビューの **[データ ゲートウェイ]** を右クリックして、 **[新しいデータ ゲートウェイ]** をクリックします。

   ![ツールバーでのデータ ゲートウェイの新規作成](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. **[作成]** ページで、 **[名前]** に「**adftutorialgateway**」と入力し、 **[OK]** をクリックします。     

    ![[ゲートウェイの作成] ページ](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > このチュートリアルでは、ノードが 1 つだけある論理ゲートウェイを作成します (ノードはオンプレミスの Windows マシン です)。 データ管理ゲートウェイは、複数のオンプレミス マシンをゲートウェイに関連付けることによって、スケールアウトできます。 1 つのノードで同時に実行できるデータ移動ジョブ数を増やすことで、スケールアップできます。 この機能は、単一のノードを持つ論理ゲートウェイでも使用できます。 詳細については、[Azure Data Factory でのデータ管理ゲートウェイのスケーリング](data-factory-data-management-gateway-high-availability-scalability.md)に関する記事をご覧ください。  
4. **[構成]** ページで、 **[このコンピューターに直接インストール]** をクリックします。 この操作により、ゲートウェイのインストール パッケージのダウンロードと、コンピューターへのインストール、構成、および登録が行われます。  

   > [!NOTE]
   > Internet Explorer または Microsoft の ClickOnce と互換性のある Web ブラウザーを使用します。
   >
   > Chrome を使用する場合は、 [Chrome Web ストア](https://chrome.google.com/webstore/)に移動し、"ClickOnce" キーワードで検索して、ClickOnce 拡張機能のいずれかを選択してインストールします。
   >
   > Firefox についても、同じ操作を実行します (アドインをインストール)。 ツール バーの **[メニューを開く]** ボタン (右上隅にある **3 本の横線**) をクリックして、 **[アドオン]** をクリックし、"ClickOnce" キーワードを使用して検索し、ClickOnce の拡張機能のいずれかを選択してインストールします。    
   >
   >

    ![ゲートウェイ - [構成] ページ](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    これは、たった 1 つの手順 (クリック 1 回) でゲートウェイのダウンロード、インストール、構成、および登録を行う、最も簡単な方法です。 **Microsoft Data Management Gateway 構成マネージャー** アプリケーションがコンピューターにインストールされていることがわかります。 また、実行可能ファイル **ConfigManager.exe** は、**C:\Program Files\Microsoft Data Management Gateway\2.0\Shared** フォルダー内にあります。

    このページのリンクを使用してゲートウェイのダウンロードとインストールを手動で行い、 **[新しいキー]** ボックスに表示されるキーを使用して登録することもできます。

    ゲートウェイの詳細については、「 [Data Management Gateway](data-factory-data-management-gateway.md) 」をご覧ください。

   > [!NOTE]
   > Data Management Gateway を正常にインストールして構成するには、ローカル コンピューターの管理者である必要があります。 他のユーザーをローカル Windows グループの **Data Management Gateway Users** に追加できます。 このグループのメンバーは、Data Management Gateway 構成マネージャー ツールを使用して、ゲートウェイを構成できます。
   >
   >
5. 2 ～ 3 分待つか、次のような通知メッセージが表示されるまで待ちます。

    ![ゲートウェイのインストールが正常に完了しました](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. コンピューターで **Data Management Gateway 構成マネージャー** アプリケーションを起動します。 **[検索]** ウィンドウに、このユーティリティにアクセスする **Data Management Gateway** を入力します。 また、実行可能ファイル **ConfigManager.exe** は、**C:\Program Files\Microsoft Data Management Gateway\2.0\Shared** フォルダー内にあります

    ![Gateway Configuration Manager](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. "`adftutorialgateway is connected to the cloud service`" というメッセージが表示されていることを確認します。 下部のステータス バーに、**緑色のチェック マーク**と共に "**Connected to the cloud service (クラウド サービスに接続済み)** " と表示されます。

    **[ホーム]** タブで、次の操作を行うこともできます。

   * [登録] ボタンで、Azure Portal からキーを使用してゲートウェイを**登録**する。
   * ゲートウェイ コンピューターで実行されている Data Management Gateway ホスト サービスを**停止**する。
   * 1 日の特定の時刻に更新がインストールされるように**スケジュールを設定**する。
   * ゲートウェイの**最終更新日時**を確認する。
   * ゲートウェイへの更新プログラムをインストールできる時刻を指定します。
8. **[設定]** タブに切り替えます。 **[証明書]** セクションに示されている証明書は、ポータルで指定したオンプレミス データ ストアの資格情報の暗号化/暗号化解除に使用されます。 独自の証明書を使用するには、 **[変更]** をクリックします (省略可能)。 既定では、ゲートウェイは Data Factory サービスによって自動生成される証明書を使用します。

    ![ゲートウェイ証明書の構成](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    **[設定]** タブで、以下の操作を行うこともできます。

   * ゲートウェイで使用されている証明書を表示またはエクスポートする。
   * ゲートウェイで使用される HTTPS エンドポイントを変更する。    
   * ゲートウェイで使用される HTTP プロキシを設定する。     
9. (省略可能) **[診断]** タブに切り替え、ゲートウェイで発生した問題のトラブルシューティングに使用できる詳細なログ記録を有効にする場合は、 **[詳細なログ記録]** をオンにします。 ログ情報については、 **[アプリケーションとサービス ログ]**  ->  **[Data Management Gateway]** ノードの順に進み、 **[イベント ビューアー]** を参照してください。

    ![[診断] タブ](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    **[診断]** タブでは、次の操作を行うこともできます。

   * ゲートウェイを使用して、オンプレミスのデータ ソースに **[接続のテスト]** セクションを使用します。
   * **[ログの表示]** をクリックして、[イベント ビューアー] ウィンドウの Data Management Gateway のログを確認します。
   * **[ログを送信]** をクリックして過去 7 日間のログの zip ファイルを Microsoft にアップロードし、問題のトラブルシューティングを容易にします。
10. ゲートウェイがデータベースに接続できるかどうかをテストするには、 **[診断]** タブの **[接続のテスト]** セクションで、データ ストアの種類として **[SqlServer]** を選択し、データベース サーバーの名前とデータベースの名前を入力します。さらに認証の種類を指定し、ユーザー名とパスワードを入力して、 **[テスト]** をクリックします。
11. Web ブラウザーに切り替え、**Azure ポータル**の **[構成]** ページと **[新しいデータ ゲートウェイ]** ページで **[OK]** をクリックします。
12. 左側のツリービューの **[データ ゲートウェイ]** に **[adftutorialgateway]** と表示されます。  クリックすると、関連する JSON が表示されます。

## <a name="create-linked-services"></a>リンクされたサービスを作成します
この手順では、2 つのリンクされたサービスを作成します (**AzureStorageLinkedService**、**SqlServerLinkedService**)。 **SqlServerLinkedService** はオンプレミスの SQL Server Database をリンクし、**AzureStorageLinkedService** リンク サービスは Azure BLOB ストアを Data Factory にリンクします。 このチュートリアルの後半で、オンプレミスの SQL Server Database から Azure BLOB ストアにデータをコピーするパイプラインを作成します。

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>オンプレミスの SQL Server Database にリンクされたサービスを追加する
1. **Data Factory エディター**のツール バーにある **[新しいデータ ストア]** をクリックし、 **[SQL Server]** を選択します。

   ![SQL Server のリンクされているサービス](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. 右側の **JSON エディター**で、次の操作を実行します。

   1. **[gatewayName]** に **[adftutorialgateway]** を指定します。    
   2. **[connectionString]** で、次の操作を実行します。    

      1. **[servername]** で、SQL Server データベースをホストするサーバーの名前を入力します。
      2. **[databasename]** で、データベースの名前を入力します。
      3. ツール バーの **[暗号化]** をクリックします。 資格情報マネージャー アプリケーションが表示されます。

         ![資格情報マネージャー アプリケーション](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. **[資格情報の設定]** ダイアログ ボックスで、認証の種類、ユーザー名、およびパスワードを指定し、 **[OK]** をクリックします。 接続が成功すると、暗号化された資格情報は JSON に格納され、ダイアログ ボックスが閉じられます。
      5. ダイアログ ボックスを起動した空のブラウザー タブが自動的に閉じられない場合は手動で閉じて、Azure Portal のタブに戻ります。

         ゲートウェイ コンピューターで、これらの資格情報は、Data Factory サービスが所有する証明書を使用して**暗号化**されます。 Data Management Gateway に関連付けられている証明書を使用する場合は、「Set credentials securely (資格情報を安全に設定する)」を参照してください。    
   3. コマンド バーの **[デプロイ]** をクリックして、SQL Server リンク サービスをデプロイします。 リンクされたサービスは、ツリー ビューに表示されます。

      ![ツリー ビューでの SQL Server のリンクされたサービス](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Azure ストレージ アカウント用のリンクされたサービスを追加する
1. **Data Factory エディター**で、コマンド バーの **[新しいデータ ストア]** をクリックし、 **[Azure ストレージ]** をクリックします。
2. Azure ストレージ アカウントの名前を **[アカウント名]** に入力します。
3. Azure ストレージ アカウントのキーを **[アカウント キー]** に入力します。
4. **[デプロイ]** をクリックして **AzureStorageLinkedService** をデプロイします。

## <a name="create-datasets"></a>データセットを作成する
このステップでは、コピー操作 (オンプレミスの SQL Server Database => Azure BLOB ストレージ) の入力および出力データを表す入出力データ セットを作成します。 データセットを作成する前に、以下の手順を実行します (詳細な手順はリストの後にあります)。

* リンクされたサービスとしてデータ ファクトリに追加した SQL Server Database 内に "**emp**" という名前のテーブルを作成し、このテーブルにサンプル エントリをいくつか挿入します。
* Data Factory にリンクされたサービスとして追加した Azure BLOB ストレージ アカウントに **adftutorial** という名前の BLOB コンテナーを作成します。

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>チュートリアル用にオンプレミスの SQL Server を用意します。
1. オンプレミスの SQL Server リンク サービス (**SqlServerLinkedService**) 用に指定したデータベースで、次の SQL スクリプトを使用して、データベースに **emp** テーブルを作成します。

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. テーブルにサンプルをいくつか挿入します。

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>入力データセットの作成

1. **Data Factory エディター**のコマンド バーで **[...More (...詳細)]** をクリックし、 **[新しいデータセット]** 、 **[SQL Server テーブル]** の順にクリックします。
2. 右側のウィンドウの JSON を次のテキストに置き換えます。

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }     
    ```     
   以下の点に注意してください。

   * **type** は **SqlServerTable** に設定されています。
   * **tablename** は **emp** に設定されています。
   * **linkedServiceName** が **SqlServerLinkedService** (このチュートリアルの前の方で作成したリンクされたサービス) に設定されます。
   * Azure Data Factory の別のパイプラインでは生成されない入力データセットの場合、**external** を **true** に設定する必要があります。 これは、入力データが Azure Data Factory サービスの外部で生成されることを意味します。 必要に応じて、**Policy** セクションの **externalData** 要素を使用して外部データ ポリシーを指定できます。    

   JSON プロパティの詳細については、[SQL Server に対するデータの移動](data-factory-sqlserver-connector.md)に関するページを参照してください。
3. コマンド バーの **[デプロイ]** をクリックしてデータセットをデプロイします。  

### <a name="create-output-dataset"></a>出力データセットの作成

1. **Data Factory エディター**で、コマンド バーの **[新しいデータセット]** をクリックし、 **[Azure BLOB ストレージ]** をクリックします。
2. 右側のウィンドウの JSON を次のテキストに置き換えます。

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   以下の点に注意してください。

   * **type** は **AzureBlob** に設定されています。
   * **linkedServiceName** を **AzureStorageLinkedService** (手順 2. で作成したリンクされたサービス) に設定します。
   * **folderPath** を **adftutorial/outfromonpremdf** に設定します。outfromonpremdf は adftutorial コンテナー内のフォルダーです。 **adftutorial** コンテナーを作成します (既に存在していない場合)。
   * **availability** が **hourly** に設定されています (**frequency** は **hour**、**interval** は **1** に設定されています)。  Data Factory サービスは、Azure SQL Database 内の **emp** テーブルに 1 時間ごとに出力データ スライスを生成します。

   **出力テーブル**に **fileName** を指定しない場合、**folderPath** に生成されるファイルの名前は次の形式になります。`Data.<Guid>.txt` (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.)。

   **folderPath** と **fileName** を **SliceStart** 時刻に基づいて動的に設定するには、partitionedBy プロパティを使用します。 次の例では、folderPath に SliceStart (処理されるスライスの開始時刻) の年、月、日を使用し、fileName に SliceStart の時間を使用します。 たとえば、スライスが 2014-10-20T08:00:00 に生成されている場合、folderName は wikidatagateway/wikisampledataout/2014/10/20 に設定され、fileName は 08.csv に設定されます。

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    JSON プロパティの詳細については、[Azure Blob Storage に対するデータの移動](data-factory-azure-blob-connector.md)に関するページを参照してください。
3. コマンド バーの **[デプロイ]** をクリックしてデータセットをデプロイします。 ツリー ビューに両方のデータセットが表示されることを確認します。  

## <a name="create-pipeline"></a>パイプラインの作成
この手順では、**EmpOnPremSQLTable** を入力として使用し、**OutputBlobTable** を出力として使用する**コピー アクティビティ**を 1 つ使用する**パイプライン**を作成します。

1. Data Factory エディターで **[...More (...詳細)]** 、 **[新しいパイプライン]** の順にクリックします。
2. 右側のウィンドウの JSON を次のテキストに置き換えます。    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on premises SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on premises SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
               }
             },
             "Policy": {
               "concurrency": 1,
               "executionPriorityOrder": "NewestFirst",
               "style": "StartOfInterval",
               "retry": 0,
               "timeout": "01:00:00"
             }
           }
         ],
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > **start** プロパティの値を現在の日付に置き換え、**end** プロパティの値を翌日の日付に置き換えます。
   >
   >

   以下の点に注意してください。

   * activities セクションに、**type** が **Copy** に設定されたアクティビティが 1 つだけあります。
   * アクティビティの**入力**を **EmpOnPremSQLTable** に設定し、**出力**を **OutputBlobTable** に設定します。
   * **typeProperties** セクションでは、**ソースの種類**として **SqlSource** が指定され、**シンクの種類**として **BlobSink** が指定されています。
   * **SqlSource** の **sqlReaderQuery** プロパティに、SQL クエリ `select * from emp` を指定します。

   start と end の日時は、いずれも [ISO 形式](https://en.wikipedia.org/wiki/ISO_8601)である必要があります。 例: 2014-10-14T16:32:41Z。 **end** の時刻は省略可能ですが、このチュートリアルでは使用します。

   **end** プロパティの値を指定しない場合、"**start + 48 時間**" として計算されます。 無期限でパイプラインを実行するには、**end** プロパティの値として **9/9/9999** を指定します。

   Azure Data Factory データセットごとに定義された **Availability** プロパティに基づいてデータ スライスが処理される期間を定義します。

   この例では、各データ スライスが 1 時間ごとに生成されるため、データ スライスは 24 個になります。        
3. コマンド バーの **[デプロイ]** をクリックして、データセットをデプロイします (テーブルは四角形のデータセットです)。 ツリー ビューの **[パイプライン]** ノードの下に、パイプラインが表示されることを確認します。  
4. ここで **[X]** を 2 回クリックしてページを閉じ、**ADFTutorialOnPremDF** の **[データ ファクトリ]** ページに戻ります。

**お疲れさまでした。** これで、Azure データ ファクトリ、リンクされたサービス、データセット、およびパイプラインの作成と、パイプラインのスケジュール設定が完了しました。

#### <a name="view-the-data-factory-in-a-diagram-view"></a>ダイアグラム ビューでの Data Factory の表示
1. **Azure Portal** の **ADFTutorialOnPremDF** データ ファクトリのホーム ページで、 **[ダイアグラム]** タイルをクリックします。 :

    ![[ダイアグラム] リンク](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. 以下の図のようなダイアグラムが表示されるはずです。

    ![Diagram view](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    パイプラインとデータセットは、拡大、縮小、100% に拡大、ウィンドウのサイズに合わせて大きさを変更、自動的に配置などの表示が可能です。また、系列情報を表示 (選択した項目の上位項目や下位項目を強調表示) することもできます。  オブジェクト (入力/出力データセットまたはパイプライン) をダブルクリックすると、そのオブジェクトのプロパティを表示できます。

## <a name="monitor-pipeline"></a>パイプラインを監視する
この手順では、Azure ポータルを使用して、Azure データ ファクトリの状況を監視します。 PowerShell コマンドレットを使用して、データセットとパイプラインを監視することもできます。 監視の詳細については、「 [パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)」という記事を参照してください。

1. ダイアグラムで **EmpOnPremSQLTable** をダブルクリックします。  

    ![EmpOnPremSQLTable slices](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. パイプラインの期間 (開始時刻から終了時刻まで) が過去であるため、すべてのデータ スライスが **[準備完了]** 状態になっていることに注意してください。 これは、SQL Server データベースに挿入したデータが、現在まで残っているためでもあります。 下部の **[問題のあるスライス]** セクションにスライスが表示されていないことを確認します。 すべてのスライスを表示するには、スライスの一覧の下部にある **[もっと見る]** をクリックします。
3. **[データセット]** ページで、 **[OutputBlobTable]** をクリックします。

    ![OputputBlobTable slices](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. 一覧で任意のデータ スライスをクリックすると、 **[データ スライス]** ページが表示されます。 そのスライスのアクティビティの実行が表示されます。 通常は、1 つのアクティビティの実行のみが表示されます。  

    ![Data Slice Blade](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    スライスが **[準備完了]** 状態でない場合、現在のスライスの実行をブロックしている準備完了でない上位スライスが、 **[準備完了でない上位スライス]** の一覧に表示されます。
5. 下部にある一覧の **[アクティビティの実行]** をクリックして、 **[アクティビティの実行の詳細]** を表示します。

   ![[アクティビティ実行の詳細] ページ](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   スループット、期間、データの転送に使用されるゲートウェイなどの情報が表示されます。
6. **[X]** をクリックしてすべてのページを閉じ、
7. **ADFTutorialOnPremDF** のホーム ページに戻ります。
8. (省略可能) **[パイプライン]** をクリックし、 **[ADFTutorialOnPremDF]** をクリックして、入力テーブル (**Consumed**) または出力データセット (**Produced**) をドリル スルーします。
9. [Microsoft ストレージ エクスプローラー](https://storageexplorer.com/)などのツールを使用して、1 時間ごとに BLOB/ファイルが作成されることを確認します。

   ![Azure ストレージ エクスプローラー](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>次の手順
* Data Management Gateway の詳細については、「 [Data Management Gateway](data-factory-data-management-gateway.md) 」をご覧ください。
* コピー アクティビティを使用して、ソース データ ストアからシンク データ ストアにデータを移動する方法については、 [Azure BLOB から Azure SQL へのデータのコピー](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) に関する記事をご覧ください。
