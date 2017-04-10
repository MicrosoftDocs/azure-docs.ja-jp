---
title: "チュートリアル: データを移動するパイプラインを Azure PowerShell で作成する | Microsoft Docs"
description: "このチュートリアルでは、Azure PowerShell を使用して、コピー アクティビティがある Azure Data Factory パイプラインを作成します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 71087349-9365-4e95-9847-170658216ed8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/02/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: fbf77e9848ce371fd8d02b83275eb553d950b0ff
ms.openlocfilehash: a95e65db804f1c6cc2927901216ee7a287a911ee


---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>チュートリアル: データを移動する Data Factory パイプラインを Azure PowerShell で作成する
> [!div class="op_single_selector"]
> * [概要と前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [コピー ウィザード](data-factory-copy-data-wizard-tutorial.md)
> * [Azure ポータル](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager テンプレート](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
>
>

このチュートリアルでは、Azure PowerShell コマンドレットを使用して、Azure Data Factory のインスタンスを作成して監視します。 このチュートリアルで作成するデータ ファクトリのパイプラインは、コピー アクティビティを使用して、Azure BLOB から Azure SQL データベースにデータをコピーします。

コピー アクティビティ機能により、Data Factory でデータ移動が実行されます。 このアクティビティは、安全で信頼性の高いスケーラブルな方法によってさまざまなデータ ストア間でデータをコピーできる、グローバルに利用可能なサービスによって動作します。 コピー アクティビティの詳細については、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事を参照してください。   

> [!NOTE]
> この記事では、すべての Data Factory コマンドレットを取り上げているわけではありません。 これらのコマンドレットに関する包括的なドキュメントについては、[Data Factory コマンドレット リファレンス](/powershell/resourcemanager/azurerm.datafactories/v2.5.0/azurerm.datafactories)を参照してください。
>
> このチュートリアルのデータ パイプラインでは、ソース データ ストアからターゲット データ ストアにデータをコピーします。 入力データを変換して出力データを生成するのではありません。 Azure Data Factory を使用してデータを変換する方法のチュートリアルについては、[Hadoop クラスターを使用してデータを変換する最初のパイプラインを作成する方法に関するチュートリアル](data-factory-build-your-first-pipeline.md)を参照してください。

## <a name="prerequisites"></a>前提条件
- [チュートリアルの概要と前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) のページに目を通して、チュートリアルの概要を理解し、 **前提条件** の手順を完了します。
- Azure PowerShell をインストールします。 [Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)に関するページに記載されている手順に従います。

## <a name="in-this-tutorial"></a>このチュートリアルの内容
次の表に、このチュートリアルの一環として実行する手順を示します。

| 手順 | 説明 |
| --- | --- |
| [Azure データ ファクトリを作成する](#create-data-factory) |この手順では、 **ADFTutorialDataFactoryPSH**という名前の Azure データ ファクトリを作成します。 |
| [リンクされたサービスの作成](#create-linked-services) |この手順では、**StorageLinkedService** と **AzureSqlLinkedService** の&2; つのリンクされたサービスを作成します。 StorageLinkedService は Azure Storage サービスを、AzureSqlLinkedService は Azure SQL Database を、それぞれ ADFTutorialDataFactoryPSH にリンクします。 |
| [入力データセットと出力データセットを作成する](#create-datasets) |この手順では、2 つのデータセット (**EmpTableFromBlob** と **EmpSQLTable**) を定義します。 これらのデータセットは、次の手順で作成する ADFTutorialPipeline の**コピー アクティビティ**の入力テーブルと出力テーブルとして使用されます。 |
| [パイプラインを作成して実行する](#create-pipeline) |この手順では、**ADFTutorialPipeline** という名前のパイプラインを ADFTutorialDataFactoryPSH というデータ ファクトリに作成します。 このパイプラインでは、コピー アクティビティを使用して、Azure BLOB から Azure データベース出力テーブルにデータをコピーします。 |
| [データセットとパイプラインを監視する](#monitor-pipeline) |この手順では、Azure PowerShell を使用してデータセットとパイプラインを監視します。 |

## <a name="create-a-data-factory"></a>Data Factory を作成する。
この手順では、Azure PowerShell を使用して、**ADFTutorialDataFactoryPSH** という名前の Azure データ ファクトリを作成します。

1. **PowerShell**を起動します。 Azure PowerShell は、このチュートリアルが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。

   a. 次のコマンドを実行して、Azure Portal へのサインインに使用するユーザー名とパスワードを入力します。

           Login-AzureRmAccount   
   b. 次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。

           Get-AzureRmSubscription
   c. 次のコマンドを実行して、使用するサブスクリプションを選択します。 **&lt;NameOfAzureSubscription**&gt; を自分の Azure サブスクリプションの名前で置き換えます。

           Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
2. 次のコマンドを実行して、 **ADFTutorialResourceGroup** という名前の Azure リソース グループを作成します。

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    このチュートリアルの一部の手順は、 **ADFTutorialResourceGroup**という名前のリソース グループを使用することを前提としています。 異なるリソース グループを使用する場合は、このチュートリアルで ADFTutorialResourceGroup の代わりにそのリソース グループを使用する必要があります。
3. **New-AzureRmDataFactory** コマンドレットを実行し、**ADFTutorialDataFactoryPSH** という名前のデータ ファクトリを作成します。  

        New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"

以下の点に注意してください。

* Azure Data Factory の名前はグローバルに一意にする必要があります。 次のエラーが表示される場合は、名前を変更します (yournameADFTutorialDataFactoryPSH など)。 このチュートリアルの手順の実行中に、この名前を ADFTutorialFactoryPSH の代わりに使用します。 Data Factory アーティファクトについては、[Data Factory - 名前付け規則](data-factory-naming-rules.md)に関するトピックを参照してください。

        Data factory name “ADFTutorialDataFactoryPSH” is not available
* Data Factory インスタンスを作成するには、Azure サブスクリプションの共同作成者または管理者である必要があります。
* データ ファクトリの名前は今後、DNS 名として登録される可能性があるため、一般ユーザーに表示される場合があります。
* "**サブスクリプションが名前空間 Microsoft.DataFactory を使用するように登録されていません**" というエラーが表示される可能性があります。 次のいずれかを実行し、もう一度発行してみてください。

  * Azure PowerShell で次のコマンドを実行して、Data Factory プロバイダーを登録します。

          Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory

      Data Factory プロバイダーが登録されたことを確認するには、次のコマンドを実行します。

          Get-AzureRmResourceProvider
  * Azure サブスクリプションを使用して、[Azure Portal](https://portal.azure.com) にサインインします。 Data Factory のブレードに移動するか、Azure Portal でデータ ファクトリを作成します。 この操作によって、プロバイダーが自動的に登録されます。

## <a name="create-linked-services"></a>リンクされたサービスの作成
リンクされたサービスは、データ ストアまたはコンピューティング サービスを Azure Data Factory にリンクします。 データ ストアには、Azure Storage サービス、Azure SQL Database、またはオンプレミスの SQL Server データベースを指定できます。これらのデータ ストアには、Data Factory パイプラインの入力データが含まれているか、出力データが格納されています。 コンピューティング サービスは、入力データを処理し、出力データを生成するサービスです。

この手順では、**StorageLinkedService** と **AzureSqlLinkedService** の&2; つのリンクされたサービスを作成します。 StorageLinkedService は Azure ストレージ アカウントを、AzureSqlLinkedService は Azure SQL データベースを **ADFTutorialDataFactoryPSH** というデータ ファクトリにリンクします。 このチュートリアルの後半では、StorageLinkedService 内の BLOB コンテナーから AzureSqlLinkedService 内の SQL テーブルにデータをコピーするパイプラインを作成します。

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Azure ストレージ アカウント用にリンクされたサービスを作成する
1. 次の内容を記述した **StorageLinkedService.json** という名前の JSON ファイルを **C:\ADFGetStartedPSH** フォルダーに作成します  (ADFGetStartedPSH フォルダーがない場合は作成します)。

         {
               "name": "StorageLinkedService",
               "properties": {
                 "type": "AzureStorage",
                 "typeProperties": {
                       "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                 }
               }
         }

   **accountname** と **accountkey** を Azure ストレージ アカウントの名前とキーに置き換えます。
2. **Azure PowerShell** で **ADFGetStartedPSH** フォルダーに切り替えます。
3. **New-AzureRmDataFactoryLinkedService** コマンドレットを使用して、リンクされたサービスを作成できます。 このコマンドレットと、このチュートリアルで使用する他の Data Factory コマンドレットでは、**ResourceGroupName** パラメーターと **DataFactoryName** パラメーターの値を渡す必要があります。 または、**Get-AzureRmDataFactory** を使用して DataFactory オブジェクトを取得すると、コマンドレットを実行するたびに ResourceGroupName と DataFactoryName を入力しなくてもオブジェクトを渡すことができます。 **Get-AzureRmDataFactory** コマンドレットの出力を変数 **$df** に割り当てるには、次のコマンドを実行します。

    ```   
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
    ```

4. これで、**New-AzureRmDataFactoryLinkedService** コマンドレットを実行して、リンクされたサービス **StorageLinkedService** を作成できます。

    ```
    New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```

    **Get-AzureRmDataFactory** コマンドレットを実行して出力を **$df** 変数に割り当てていない場合、ResourceGroupName パラメーターと DataFactoryName パラメーターの値を次のように指定する必要があります。   

    ```
    New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json
    ```

チュートリアルの途中で Azure PowerShell を閉じた場合、次に Azure PowerShell を起動したときに、Get-AzureRmDataFactory コマンドレットを実行してチュートリアルを完了する必要があります。

### <a name="create-a-linked-service-for-an-azure-sql-database"></a>Azure SQL データベース用にリンクされたサービスを作成する
1. 次の内容を記述した AzureSqlLinkedService.json という名前の JSON ファイルを作成します。

         {
             "name": "AzureSqlLinkedService",
             "properties": {
                 "type": "AzureSqlDatabase",
                 "typeProperties": {
                       "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                 }
               }
         }

   **servername**、**databasename**、**username@servername**、**password** を、Azure SQL のサーバー名、データベース名、ユーザー アカウント、パスワードに置き換えます。
2. 次のコマンドを実行して、リンクされたサービスを作成します。

    ```
    New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```

   SQL データベース サーバーで **[Azure サービスへのアクセスを許可]** の設定がオンになっていることを確認します。 この設定を確認してオンにするには、次の手順を実行します。

   1. 左側にある **[参照]** ハブをクリックし、**[SQL Server]** をクリックします。
   2. お使いのサーバーを選択し、**[SQL Server]** ブレードで **[設定]** をクリックします。
   3. **[設定]** ブレードで **[ファイアウォール]** をクリックします。
   4. **[ファイアウォールの設定]** ブレードの **[Azure サービスへのアクセスを許可]** で **[オン]** をクリックします。
   5. 左側の **[アクティブ]** ハブをクリックして、元の **[Data Factory]** ブレードに切り替えます。

## <a name="create-datasets"></a>データセットを作成する
前の手順では、Azure ストレージ アカウントと Azure SQL データベースをデータ ファクトリにリンクするためのサービスを作成しました。 この手順では、次の手順で作成するパイプラインのコピー アクティビティ用に入力データと出力データを表すデータセットを作成します。

テーブルとは四角形のデータセットです。 現時点でサポートされているデータセットの種類はテーブルのみです。 このチュートリアルの入力テーブルは、Azure Storage 内の BLOB コンテナーを参照します。 出力テーブルは、Azure SQL データベース内の SQL テーブルを参照します。  

### <a name="prepare-azure-blob-storage-and-azure-sql-database-for-the-tutorial"></a>Azure Blob Storage と Azure SQL Database をチュートリアル用に準備する
[Blob Storage から SQL Database へのデータのコピー](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関する記事のチュートリアルを完了している方は、この手順をスキップしてください。

このチュートリアル用に Azure Blob Storage と SQL Database を準備するには、次の手順を実行します。

1. **StorageLinkedService** がポイントする Blob Storage 内に **adftutorial** という名前の BLOB コンテナーを作成します。
2. **emp.txt** という名前のテキスト ファイルを作成し、BLOB として **adftutorial** コンテナーにアップロードします。
3. **AzureSqlLinkedService** がポイントする SQL データベース内に **emp** という名前のテーブルを作成します。

4. メモ帳を起動し、次のテキストを貼り付け、**emp.txt** という名前でハード ドライブの **C:\ADFGetStartedPSH** フォルダーに保存します。

        John, Doe
        Jane, Doe
5. [Azure ストレージ エクスプローラー](https://azurestorageexplorer.codeplex.com/)などのツールを使用して **adftutorial** コンテナーを作成し、このコンテナーに **emp.txt** ファイルをアップロードします。

    ![Azure ストレージ エクスプローラー](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
6. 次の SQL スクリプトを使用して、**emp** テーブルを SQL データベースに作成します。  

        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

    SQL Server 2014 がコンピューターにインストールされている場合は、[「SQL Server Management Studio を使用した Azure SQL データベースの管理」の手順 2. SQL データベースへの接続](../sql-database/sql-database-manage-azure-ssms.md)の手順に従い、SQL データベース サーバーに接続して SQL スクリプトを実行します。

    クライアントから SQL データベース サーバーへのアクセスが許可されていない場合は、コンピューター (IP アドレス) からのアクセスを許可するように、SQL データベース サーバーのファイアウォールを設定する必要があります。 手順については、[こちらの記事](../sql-database/sql-database-configure-firewall-settings.md)を参照してください。

### <a name="create-an-input-dataset"></a>入力データセットの作成
テーブルとは四角形のデータセットで、スキーマを持っています。 この手順では、**EmpBlobTable** という名前のテーブルを作成します。 この手順では、リンクされたサービス **StorageLinkedService** が表す Azure Storage 内の BLOB コンテナーをポイントします。 この BLOB コンテナー (**adftutorial**) には、**emp.txt** ファイルの入力データが含まれています。

1. 以下の内容を記述した **EmpBlobTable.json** という名前の JSON ファイルを **C:\ADFGetStartedPSH** フォルダー内に作成します。

         {
           "name": "EmpTableFromBlob",
           "properties": {
             "structure": [
               {
                 "name": "FirstName",
                 "type": "String"
               },
               {
                 "name": "LastName",
                 "type": "String"
               }
             ],
             "type": "AzureBlob",
             "linkedServiceName": "StorageLinkedService",
             "typeProperties": {
               "fileName": "emp.txt",
               "folderPath": "adftutorial/",
               "format": {
                 "type": "TextFormat",
                 "columnDelimiter": ","
               }
             },
             "external": true,
             "availability": {
               "frequency": "Hour",
               "interval": 1
             }
           }
         }

   以下の点に注意してください。

   * データセットの **type** は **AzureBlob** に設定されています。
   * **linkedServiceName** は **StorageLinkedService** に設定されています。
   * **folderPath** は **adftutorial** コンテナーに設定されています。
   * **fileName** は **emp.txt** に設定されています。 BLOB の名前を指定しない場合、コンテナー内のすべての BLOB からのデータが入力データと見なされます。  
   * 形式の **type** は **TextFormat** に設定されています。
   * テキスト ファイル内に&2; つのフィールド (**FirstName** と **LastName**) があり、コンマ (**columnDelimiter**) で区切られています。    
   * **availability** は **hourly** に設定されています (**frequency** は **hour**、**interval** は **1** に設定されています)。 そのため、Data Factory は BLOB コンテナー (**adftutorial**) のルート フォルダーで、入力データを&1; 時間ごとに検索します。

   **入力テーブル**に **fileName** を指定しない場合、入力フォルダー (**folderPath**) のすべてのファイルまたは BLOB が入力と見なされます。 JSON で fileName を指定した場合は、指定されたファイルまたは BLOB のみが入力と見なされます。

   **出力テーブル**に **fileName** を指定しない場合、**folderPath** に生成されるファイルには Data.<Guid\>.txt という形式で名前が付けられます (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt)。

   **folderPath** と **fileName** を **SliceStart** 時刻に基づいて動的に設定するには、**partitionedBy** プロパティを使用します。 次の例では、folderPath に SliceStart (処理されるスライスの開始時刻) の年、月、日を使用し、fileName に SliceStart の時間を使用します。 たとえば、スライスが 2016-10-20T08:00:00 に生成されている場合、folderName は wikidatagateway/wikisampledataout/2016/10/20 に設定され、fileName は 08.csv に設定されます。

         "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
         "fileName": "{Hour}.csv",
         "partitionedBy":
         [
             { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
             { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
             { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
             { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
         ],

   JSON のプロパティの詳細については、[JSON スクリプティング リファレンス](data-factory-data-movement-activities.md)を参照してください。
2. 次のコマンドを実行して、Data Factory データセットを作成します。

    ```  
    New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json
    ```

### <a name="create-an-output-dataset"></a>出力データセットの作成
この手順では、 **EmpSQLTable**という名前の出力データセットを作成します。 このデータセットは、**AzureSqlLinkedService** で表される Azure SQL データベース内の SQL テーブル (**emp**) をポイントします。 パイプラインで入力 BLOB から **emp** テーブルにデータがコピーされます。

1. 以下の内容を記述した **EmpSQLTable.json** という名前の JSON ファイルを **C:\ADFGetStartedPSH** フォルダー内に作成します。

         {
           "name": "EmpSQLTable",
           "properties": {
             "structure": [
               {
                 "name": "FirstName",
                 "type": "String"
               },
               {
                 "name": "LastName",
                 "type": "String"
               }
             ],
             "type": "AzureSqlTable",
             "linkedServiceName": "AzureSqlLinkedService",
             "typeProperties": {
               "tableName": "emp"
             },
             "availability": {
               "frequency": "Hour",
               "interval": 1
             }
           }
         }

   以下の点に注意してください。

   * データセットの **type** は **AzureSqlTable** に設定されています。
   * **linkedServiceName** は **AzureSqlLinkedService** に設定されています。
   * **tablename** は **emp** に設定されています。
   * データベース内の emp テーブルには、**ID**、**FirstName**、**LastName** の&3; つの列があります。 ID は ID 列であるため、ここで指定する必要があるのは **FirstName** と **LastName** のみです。
   * **availability** は **hourly** に設定されています (**frequency** は **hour**、**interval** は **1** に設定されています)。 Data Factory サービスは、Azure SQL Database 内の **emp** テーブルに&1; 時間ごとに出力データ スライスを生成します。
2. 次のコマンドを実行して、データ ファクトリ データセットを作成します。

    ```   
    New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json
    ```

## <a name="create-a-pipeline"></a>パイプラインを作成する。
この手順で、**コピー アクティビティ**のあるパイプラインを作成します。 このパイプラインでは、入力として **EmpTableFromBlob**、出力として **EmpSQLTable** を使用します。

1. 以下の内容を記述した **ADFTutorialPipeline.json** という名前の JSON ファイルを **C:\ADFGetStartedPSH** フォルダー内に作成します。

          {
           "name": "ADFTutorialPipeline",
           "properties": {
             "description": "Copy data from a blob to Azure SQL table",
             "activities": [
               {
                 "name": "CopyFromBlobToSQL",
                 "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
                 "type": "Copy",
                 "inputs": [
                   {
                     "name": "EmpTableFromBlob"
                   }
                 ],
                 "outputs": [
                   {
                     "name": "EmpSQLTable"
                   }
                 ],
                 "typeProperties": {
                   "source": {
                     "type": "BlobSource"
                   },
                   "sink": {
                     "type": "SqlSink"
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
             "start": "2016-08-09T00:00:00Z",
             "end": "2016-08-10T00:00:00Z",
             "isPaused": false
           }
         }

   以下の点に注意してください。

   * activities セクションに、**type** が **Copy** に設定されたアクティビティが&1; つだけあります。
   * アクティビティの入力は **EmpTableFromBlob** に設定され、アクティビティの出力は **EmpSQLTable** に設定されています。
   * **transformation** セクションでは、ソースの種類として **BlobSource** が指定され、シンクの種類として **SqlSink** が指定されています。

   **start** プロパティの値を現在の日付に置き換え、**end** プロパティの値を翌日の日付に置き換えます。 start と end の日時は、いずれも [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)である必要があります。 たとえば、2016-10-14T16:32:41Z とします。 このチュートリアルでは **end** の時刻が使用されていますが、省略可能です。

   **end** プロパティの値を指定しない場合、"**start + 48 時間**" として計算されます。 パイプラインを無期限に実行する場合は、**9/9/9999** を **end** プロパティの値として指定します。

   この例では、各データ スライスが 1 時間ごとに生成されるため、データ スライスは 24 個になります。

   JSON のプロパティの詳細については、[JSON スクリプティング リファレンス](data-factory-data-movement-activities.md)を参照してください。
2. 次のコマンドを実行して、データ ファクトリ テーブルを作成します。

    ```   
    New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

ご利用ありがとうございます。 これで、Azure データ ファクトリ、リンクされたサービス、テーブル、およびパイプラインの作成が完了しました。 パイプラインのスケジュールも設定しました。

## <a name="monitor-the-pipeline"></a>パイプラインの監視
この手順では、Azure PowerShell を使用して、Azure データ ファクトリの状況を監視します。

1. **Get-AzureRmDataFactory** を実行し、出力を変数 $df に割り当てます。

    ```  
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
    ```

2. **Get-AzureRmDataFactorySlice** を実行し、**EmpSQLTable** のすべてのスライスの詳細を表示します。これは、パイプラインの出力テーブルです。  

    ```   
    Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
    ```

   **StartDateTime** パラメーターで、年、月、日の部分を現在の年、月、日に置き換えます。 この設定は、JSON パイプラインの **Start** の値と一致します。

   現在の日付の 12 AM から次の日の 12 AM までの 1 時間ごとに 1 つずつ、合計 24 個のスライスが表示されます。

   **サンプル出力:**

    ```   
     ResourceGroupName : ADFTutorialResourceGroup
     DataFactoryName   : ADFTutorialDataFactoryPSH
     TableName         : EmpSQLTable
     Start             : 8/9/2016 12:00:00 AM
     End               : 8/9/2016 1:00:00 AM
     RetryCount        : 0
     Status            : Waiting
     LatencyStatus     :
     LongRetryCount    : 0
    ```
3. **Get-AzureRmDataFactoryRun** を実行して、**特定の**スライスに関するアクティビティの実行の詳細を取得します。 出力のスライスの **Start** 時間と一致するように、**StartDateTime** パラメーターの値を変更します。 **StartDateTime** の値は [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)である必要があります。

    ```  
    Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
    ```

   次のサンプルのように出力が表示されます。

    ```   
     Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
     ResourceGroupName   : ADFTutorialResourceGroup
     DataFactoryName     : ADFTutorialDataFactoryPSH
     TableName           : EmpSQLTable
     ProcessingStartTime : 8/9/2016 11:03:28 PM
     ProcessingEndTime   : 8/9/2016 11:04:36 PM
     PercentComplete     : 100
     DataSliceStart      : 8/9/2016 10:00:00 PM
     DataSliceEnd        : 8/9/2016 11:00:00 PM
     Status              : Succeeded
     Timestamp           : 8/9/2016 11:03:28 PM
     RetryAttempt        : 0
     Properties          : {}
     ErrorMessage        :
     ActivityName        : CopyFromBlobToSQL
     PipelineName        : ADFTutorialPipeline
     Type                : Copy
    ```

Data Factory コマンドレットに関する包括的なドキュメントについては、[Data Factory コマンドレット リファレンス][cmdlet-reference]を参照してください。

## <a name="summary"></a>まとめ
このチュートリアルでは、Azure Data Factory を作成し、Azure BLOB から Azure SQL Database にデータをコピーしました。 また、PowerShell を使用して、データ ファクトリ、リンクされたサービス、データセット、パイプラインを作成しました。 以下は、このチュートリアルで実行した手順の概要です。  

1. Azure **データ ファクトリ**を作成しました。
2. 次の **リンクされたサービス**を作成しました。

   a. 入力データを保持する Azure ストレージ アカウントをリンクするための、**Azure Storage** のリンクされたサービス。     
   b. 出力データを保持する Azure SQL データベースをリンクするための、**Azure SQL** のリンクされたサービス。
3. パイプラインの入力データと出力データを記述する **データセット** を作成しました。
4. ソースとして **BlobSource**、シンクとして **SqlSink** を持つ**コピー アクティビティ**がある**パイプライン**を作成しました。

## <a name="see-also"></a>関連項目
| トピック | 説明 |
|:--- |:--- |
| [Data Factory コマンドレット リファレンス](/powershell/resourcemanager/azurerm.datafactories/v2.5.0/azurerm.datafactories) | このセクションでは、すべての Data Factory コマンドレットに関する情報を提供します |
| [パイプライン](data-factory-create-pipelines.md) |この記事では、Azure Data Factory のパイプラインとアクティビティについて説明します。 |
| [datasets](data-factory-create-datasets.md) |この記事では、Azure Data Factory のデータセットについて説明します。 |
| [スケジュールと実行](data-factory-scheduling-and-execution.md) |この記事では、Azure Data Factory アプリケーション モデルのスケジュール設定と実行の側面について説明します。 |

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md



<!--HONumber=Feb17_HO1-->


