---
title: "チュートリアル: コピー アクティビティがあるパイプラインを Visual Studio で作成する | Microsoft Docs"
description: "このチュートリアルでは、Visual Studio を使用して、コピー アクティビティがある Azure Data Factory パイプラインを作成します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1751185b-ce0a-4ab2-a9c3-e37b4d149ca3
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/02/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 39824fa66dee9f1bd57687e59ece97f4f4636b7d
ms.lasthandoff: 03/24/2017


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>チュートリアル: コピー アクティビティがあるパイプラインを Visual Studio で作成する
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

このチュートリアルでは、Visual Studio を使用して Azure データ ファクトリを作成および監視する方法について説明しています。 データ ファクトリのパイプラインは、コピー アクティビティを使用して、Azure Blob Storage から Azure SQL Database にデータをコピーします。

> [!NOTE]
> このチュートリアルのデータ パイプラインでは、ソース データ ストアからターゲット データ ストアにデータをコピーします。 入力データを変換して出力データを生成するのではありません。 Azure Data Factory を使用してデータを変換する方法のチュートリアルについては、「[チュートリアル: Hadoop クラスターを使用してデータを処理する最初のパイプラインを作成する](data-factory-build-your-first-pipeline.md)」を参照してください。
> 
> 2 つのアクティビティを連鎖させる (アクティビティを連続的に実行する) には、一方のアクティビティの出力データセットを、もう一方のアクティビティの入力データセットとして指定します。 詳細については、[Data Factory でのスケジュールと実行](data-factory-scheduling-and-execution.md)に関するページを参照してください。

このチュートリアルの一部として実行する手順を次に示します。

1. リンクされた次の 2 つのサービス、**AzureStorageLinkedService1** と **AzureSqlinkedService1** を作成します。 
   
    AzureStorageLinkedService1 は、Azure ストレージにリンクし、AzureSqlLinkedService1 は、Azure SQL データベースを **ADFTutorialDataFactoryVS** データ ファクトリにリンクします。 パイプラインの入力データは、Azure BLOB ストレージの BLOB コンテナーにあります。また出力データは、Azure SQL Database のテーブルに格納されます。 そのため、これら 2 つのデータ ストアをリンクされたサービスとしてデータ ファクトリに追加します。
2. **InputDataset** と **OutputDataset** の 2 つのデータセットを作成します。これらはデータ ストアに格納されている入力/出力データを表します。 
   
    InputDataset では、ソース データが格納された BLOB を含む BLOB コンテナーを指定します。 OutputDataset では、出力データを格納する SQL テーブルを指定します。 また、構造、可用性、ポリシーなど、他のプロパティも指定します。
3. ADFTutorialDataFactoryVS に **ADFTutorialPipeline** という名前のパイプラインを作成します。 
   
    このパイプラインには、Azure BLOB から Azure SQL 出力テーブルに入力データをコピーする **コピー アクティビティ** があります。 コピー アクティビティにより、Azure Data Factory でデータ移動が実行されます。 このアクティビティは、安全で信頼性の高いスケーラブルな方法によってさまざまなデータ ストア間でデータをコピーできる、グローバルに利用可能なサービスによって動作します。 コピー アクティビティの詳細については、「 [データ移動アクティビティ](data-factory-data-movement-activities.md) 」をご覧ください。 
4. **VSTutorialFactory**という名前のデータ ファクトリを作成します。 データ ファクトリと、すべての Data Factory エンティティ (リンクされたサービス、テーブル、パイプライン) をデプロイします。    

## <a name="prerequisites"></a>前提条件
1. 「 [チュートリアルの概要](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 」に目を通し、 **前提条件** の手順を完了する必要があります。 
2. Data Factory エンティティを Azure Data Factory に発行できる **Azure サブスクリプションの管理者** である必要があります。  
3. コンピューターに以下がインストールされている必要があります。 
   * Visual Studio 2013 または Visual Studio 2015
   * Azure SDK for Visual Studio 2013 または Visual Studio 2015 をダウンロードします。 [Azure ダウンロード ページ](https://azure.microsoft.com/downloads/)に移動し、**.NET** セクションの **[VS 2013]** または **[VS 2015]** をクリックします。
   * Visual Studio 用の最新の Azure Data Factory プラグイン ([VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) または [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)) をダウンロードします。 メニューで **[ツール]** -> **[拡張機能と更新プログラム]** -> **[オンライン]** -> **[Visual Studio ギャラリー]** -> **[Microsoft Azure Data Factory Tools for Visual Studio]** -> **[更新]** の順にクリックして、プラグインを更新することもできます。

## <a name="create-visual-studio-project"></a>Visual Studio プロジェクトの作成
1. **Visual Studio 2013**を起動します。 **[ファイル]** をクリックし、**[新規作成]** をポイントして、**[プロジェクト]** をクリックします。 **[新しいプロジェクト]** ダイアログ ボックスが表示されます。  
2. **[新しいプロジェクト]** ダイアログ ボックスで、**[DataFactory]** テンプレートを選択し、**[Empty Data Factory Project (空の Data Factory プロジェクト)]** をクリックします。 DataFactory テンプレートが表示されない場合は、Visual Studio を終了し、Azure SDK for Visual Studio 2013 をインストールし、Visual Studio を再度開きます。  
   
    ![[新しいプロジェクト] ダイアログ ボックス
](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)
3. プロジェクトの**名前**、**場所**、**ソリューション**の名前を入力し、**[OK]** をクリックします。
   
    ![ソリューション エクスプローラー
](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>リンクされたサービスの作成
リンクされたサービスは、データ ストアまたはコンピューティング サービスを Azure Data Factory にリンクします。 コピー アクティビティでサポートされているすべてのソースとシンクについては、 [サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats) に関する記事を参照してください。 Data Factory でサポートされているコンピューティング サービスの一覧については、「 [コンピューティングのリンクされたサービス](data-factory-compute-linked-services.md) 」を参照してください。 このチュートリアルでは、コンピューティング サービスは使用しません。 

この手順では、**AzureStorageLinkedService1** と **AzureSqlLinkedService1** の 2 つのリンクされたサービスを作成します。 リンクされたサービス AzureStorageLinkedService1 は Azure ストレージ アカウントをリンクし、AzureSqlLinkedService は Azure SQL Database を **ADFTutorialDataFactory**という Data Factory にリンクします。 

### <a name="create-the-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成します。
1. ソリューション エクスプローラーの **[リンクされたサービス]** を右クリックして **[追加]** をポイントし、**[新しい項目]** をクリックします。      
2. **[新しい項目の追加]** ダイアログ ボックスで、一覧から **[Azure Storage Linked Service (Azure Storage のリンクされたサービス)]** を選択し、**[追加]** をクリックします。 
   
    ![新規のリンクされたサービス](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
3. `<accountname>` と `<accountkey>`* を Azure ストレージ アカウントの名前とキーで置き換えます。 
   
    ![Azure Storage のリンクされたサービス](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)
4. **AzureStorageLinkedService1.json** ファイルを保存します。

> JSON プロパティの詳細については、 [Azure BLOB に対するデータの移動](data-factory-azure-blob-connector.md#azure-storage-linked-service) に関するページを参照してください。
> 
> 

### <a name="create-the-azure-sql-linked-service"></a>Azure SQL のリンクされたサービスを作成します。
1. **ソリューション エクスプローラー**で **[リンクされたサービス]** ノードをもう一度右クリックして **[追加]** をポイントし、**[新しい項目]** をクリックします。 
2. このとき、**[Azure SQL Linked Service (Azure SQL のリンクされたサービス)]** を選択し、**[追加]** をクリックします。 
3. **AzureSqlLinkedService1.json** ファイルで、`<servername>`、`<databasename>`、`<username@servername>`、`<password>` を Azure SQL のサーバー名、データベース名、ユーザー アカウント、パスワードに置き換えます。    
4. **AzureSqlLinkedService1.json** ファイルを保存します。 

> [!NOTE]
> JSON プロパティの詳細については、 [Azure SQL Database に対するデータの移動](data-factory-azure-sql-connector.md#linked-service-properties) に関するページを参照してください。
> 
> 

## <a name="create-datasets"></a>データセットを作成する
前の手順では、**AzureStorageLinkedService1** と **AzureSqlLinkedService1** というリンクされたサービスを作成し、Azure ストレージ アカウントと Azure SQL データベースを **ADFTutorialDataFactory** というデータ ファクトリにリンクしました。 この手順では、**InputDataset** と **OutputDataset** の 2 つのデータセットを定義します。これらはそれぞれ AzureStorageLinkedService1 と AzureSqlLinkedService1 が参照するデータ ストアに格納されている入力/出力データを表します。 InputDataset では、ソース データが格納された BLOB を含む BLOB コンテナーを指定します。 OutputDataset では、出力データを格納する SQL テーブルを指定します。

### <a name="create-input-dataset"></a>入力データセットの作成
この手順では、リンクされたサービス **AzureStorageLinkedService1** が表す Azure Storage 内の BLOB コンテナーをポイントする **InputDataset** という名前のデータセットを作成します。 テーブルは四角形のデータセットであり、現時点でサポートされている唯一の種類のデータセットです。 

1. **ソリューション エクスプローラー**の **[テーブル]** を右クリックして **[追加]** をポイントし、**[新しい項目]** をクリックします。
2. **[新しい項目の追加]** ダイアログ ボックスで、**[Azure BLOB]** をクリックし、**[追加]** をクリックします。   
3. JSON テキストを次のテキストで置き換え、 **AzureBlobLocation1.json** ファイルを保存します。 

  ```json   
  {
    "name": "InputDataset",
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
      "linkedServiceName": "AzureStorageLinkedService1",
      "typeProperties": {
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
  ``` 
    以下の点に注意してください。 
   
   * データセットの **type** は **AzureBlob** に設定されています。
   * **linkedServiceName** は **AzureStorageLinkedService** に設定されています。 このリンクされたサービスは手順 2. で作成しました。
   * **folderPath** は **adftutorial** コンテナーに設定されています。 **fileName** プロパティを使用して、フォルダー内の BLOB の名前を指定することもできます。 BLOB の名前を指定しない場合、コンテナー内のすべての BLOB からのデータが入力データと見なされます。  
   * format の **type** は **TextFormat** に設定されています。
   * テキスト ファイル内に 2 つのフィールド (**FirstName** と **LastName**) があり、コンマ (**columnDelimiter**) で区切られています。    
   * **availability** が **hourly** に設定されています (**frequency** は **hour**、**interval** は **1** に設定されています)。 そのため、Data Factory は、指定された BLOB コンテナー (**adftutorial**) のルート フォルダーにある入力データを 1 時間ごとに検索します。 
   
   **入力**データセット用に **fileName** を指定しない場合、入力フォルダー (**folderPath**) のすべてのファイルまたは BLOB が入力と見なされます。 JSON で fileName を指定した場合は、指定されたファイル/BLOB のみが入力と見なされます。
   
   **出力テーブル**に **fileName** を指定しない場合、**folderPath** に生成されるファイルには Data.&lt;Guid&gt;.txt という形式で名前が付けられます (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt)。
   
   **folderPath** と **fileName** を **SliceStart** 時刻に基づいて動的に設定するには、**partitionedBy** プロパティを使用します。 次の例では、folderPath に SliceStart (処理されるスライスの開始時刻) の年、月、日を使用し、fileName に SliceStart の時間を使用します。 たとえば、スライスが 2016-09-20T08:00:00 に生成されている場合、folderName は wikidatagateway/wikisampledataout/2016/09/20 に設定され、fileName は 08.csv に設定されます。 
  
    ```json   
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ```
            
> [!NOTE]
> JSON プロパティの詳細については、 [Azure BLOB に対するデータの移動](data-factory-azure-blob-connector.md#dataset-properties) に関するページを参照してください。
> 
> 

### <a name="create-output-dataset"></a>出力データセットの作成
この手順では、 **OutputDataset**という名前の出力データセットを作成します。 このデータセットは、 **AzureSqlLinkedService1**で表される Azure SQL データベース内の SQL テーブルをポイントします。 

1. **ソリューション エクスプローラー**の **[テーブル]** をもう一度右クリックし、**[追加]** をポイントし、**[新しい項目]** をクリックします。
2. **[新しい項目の追加]** ダイアログ ボックスで、**[Azure SQL]** を選択し、**[追加]** をクリックします。 
3. JSON テキストを次の JSON テキストで置き換え、 **AzureSqlTableLocation1.json** ファイルを保存します。

    ```json
    {
     "name": "OutputDataset",
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
       "linkedServiceName": "AzureSqlLinkedService1",
       "typeProperties": {
         "tableName": "emp"
       },
       "availability": {
         "frequency": "Hour",
         "interval": 1
       }
     }
    }
    ```
   
    以下の点に注意してください。 
   
   * データセットの **type** は **AzureSQLTable** に設定されています。
   * **linkedServiceName** は **AzureSqlLinkedService** (手順 2. で作成した、リンクされたサービス) に設定されています。
   * **tablename** は **emp** に設定されています。
   * データベース内の emp テーブルには、**ID**、**FirstName**、**LastName** の 3 つの列があります。 ID は ID 列であるため、ここで指定する必要があるのは **FirstName** と **LastName** のみです。
   * **availability** は **hourly** に設定されています (**frequency** は **hour**、**interval** は **1** に設定されています)。  Data Factory サービスは、Azure SQL Database 内の **emp** テーブルに 1 時間ごとに出力データ スライスを生成します。

> [!NOTE]
> JSON プロパティの詳細については、 [Azure SQL Database に対するデータの移動](data-factory-azure-sql-connector.md#linked-service-properties) に関するページを参照してください。
> 
> 

## <a name="create-pipeline"></a>パイプラインの作成
入力/出力のリンクされたサービスとテーブルの作成は完了しました。 次に、Azure BLOB から Azure SQL Database にデータをコピーするための **コピー アクティビティ** を持つパイプラインを作成します。 

1. **ソリューション エクスプローラー**の **[パイプライン]** を右クリックして **[追加]** をポイントし、**[新しい項目]** をクリックします。  
2. **[新しい項目の追加]** ダイアログ ボックスで **[データ パイプラインのコピー]** を選択し、**[追加]** をクリックします。 
3. JSON テキストを次の JSON テキストで置き換え、 **CopyActivity1.json** ファイルを保存します。

    ```json   
    {
     "name": "ADFTutorialPipeline",
     "properties": {
       "description": "Copy data from a blob to Azure SQL table",
       "activities": [
         {
           "name": "CopyFromBlobToSQL",
           "type": "Copy",
           "inputs": [
             {
               "name": "InputDataset"
             }
           ],
           "outputs": [
             {
               "name": "OutputDataset"
             }
           ],
           "typeProperties": {
             "source": {
               "type": "BlobSource"
             },
             "sink": {
               "type": "SqlSink",
               "writeBatchSize": 10000,
               "writeBatchTimeout": "60:00:00"
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
       "start": "2015-07-12T00:00:00Z",
       "end": "2015-07-13T00:00:00Z",
       "isPaused": false
     }
    }
    ```   
   以下の点に注意してください。
   
   * activities セクションに、**type** が **Copy** に設定されたアクティビティが 1 つだけあります。
   * アクティビティの入力を **InputDataset** に設定し、出力を **OutputDataset** に設定します。
   * **typeProperties** セクションでは、ソースの種類として **BlobSource** が指定され、シンクの種類として **SqlSink** が指定されています。
   
   **start** プロパティの値を現在の日付に置き換え、**end** プロパティの値を翌日の日付に置き換えます。 日付の部分のみを指定し、時刻の部分をスキップすることもできます。 たとえば、"2016-02-03" と "2016-02-03T00:00:00Z" は同じです。
   
   start と end の日時は、いずれも [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)である必要があります。 たとえば、2016-10-14T16:32:41Z とします。 **end** の時刻は省略可能ですが、このチュートリアルでは使用します。 
   
   **end** プロパティの値を指定しない場合、"**start + 48 時間**" として計算されます。 パイプラインを無期限に実行する場合は、**9999-09-09** を **end** プロパティの値として指定します。
   
   前の例では、各データ スライスが 1 時間ごとに生成されるため、データ スライスは 24 個になります。

## <a name="publishdeploy-data-factory-entities"></a>Data Factory エンティティの発行/デプロイ
この手順では、先ほど作成した Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) を発行します。 また、これらのエンティティを保持するために、新しいデータ ファクトリの名前を指定して作成します。  

1. ソリューション エクスプローラーでプロジェクトを右クリックし、 **[発行]**をクリックします。 
2. **[Microsoft アカウントへのサインイン]** ダイアログ ボックスが表示されたら、Azure サブスクリプションを所有するアカウントの資格情報を入力し、**[サインイン]** をクリックします。
3. 次のダイアログ ボックスが表示されます。
   
   ![[発行] ダイアログ ボックス](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. データ ファクトリの構成ページで、次の手順を行います。 
   
   1. **[Data Factory の新規作成]** オプションを選択します。
   2. **[名前]** に「**VSTutorialFactory**」と入力します。  
      
      > [!IMPORTANT]
      > Azure Data Factory の名前はグローバルに一意にする必要があります。 発行時にデータ ファクトリの名前に関するエラーが発生した場合は、そのデータ ファクトリの名前を変更して (yournameVSTutorialFactory など)、発行し直してください。 Data Factory アーティファクトの名前付け規則については、「 [Azure Data Factory - 名前付け規則](data-factory-naming-rules.md) 」を参照してください。        
      > 
      > 
   3. **[サブスクリプション]** フィールドで Azure サブスクリプションを選択します。
      
      > [!IMPORTANT]
      > サブスクリプションが表示されない場合は、サブスクリプションの管理者または共同管理者のアカウントを使用してログインしたことを確認します。  
      > 
      > 
   4. 作成するデータ ファクトリの **リソース グループ** を選択します。 
   5. データ ファクトリの **リージョン** を選択します。 Data Factory サービスによってサポートされているリージョンのみ、ドロップダウン リストに表示されます。
   6. **[次へ]** をクリックし、**[項目の発行]** ページに切り替えます。
      
       ![Configure data factory page](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. **[項目の発行]** ページで、すべての Data Factory エンティティが選択されていることを確認し、**[次へ]** をクリックして **[概要]** ページに切り替えます。
   
   ![Publish items page](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. 概要を確認してから **[次へ]** をクリックし、デプロイ プロセスを開始して **[デプロイ ステータス]** を表示します。
   
   ![Publish summary page](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. **[デプロイ ステータス]** ページに、デプロイメント プロセスのステータスが表示されます。 デプロイメントが完了したら、[完了] をクリックします。
 
   ![Deployment status page](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)

以下の点に注意してください。 

* "**サブスクリプションが名前空間 Microsoft.DataFactory を使用するように登録されていません**" というエラー メッセージが表示されたら、以下のいずれかの操作をしてから、もう一度発行してみます。 
  
  * Azure PowerShell で次のコマンドを実行して、Data Factory プロバイダーを登録します。 

    ```PowerShell    
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Data Factory プロバイダーが登録されたことを確認するには、次のコマンドを実行します。 
    
    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * Azure サブスクリプションを使用して [Azure ポータル](https://portal.azure.com) にログインし、[Data Factory] ブレードに移動するか、Azure ポータルでデータ ファクトリを作成します。 この操作によって、プロバイダーが自動的に登録されます。
* データ ファクトリの名前は今後、DNS 名として登録される可能性があるため、一般ユーザーに表示される場合があります。

> [!IMPORTANT]
> Data Factory インスタンスを作成するには、Azure サブスクリプションの管理者または共同管理者である必要があります。
> 
> 

## <a name="summary"></a>概要
このチュートリアルでは、Azure Data Factory を作成し、Azure BLOB から Azure SQL Database にデータをコピーしました。 また、Visual Studio を使用して、データ ファクトリ、リンクされたサービス、データセット、パイプラインを作成しました。 以下は、このチュートリアルで実行した手順の概要です。  

1. Azure **データ ファクトリ**を作成しました。
2. 次の **リンクされたサービス**を作成しました。
   1. 入力データを保持する Azure ストレージ アカウントをリンクするための、 **Azure Storage** のリンクされたサービス。     
   2. 出力データを保持する Azure SQL データベースをリンクするための、 **Azure SQL** のリンクされたサービス。 
3. パイプラインの入力データと出力データを記述する **データセット**を作成しました。
4. ソースとして **BlobSource**、シンクとして **SqlSink** を持つ**コピー アクティビティ**がある**パイプライン**を作成しました。 

## <a name="use-server-explorer-to-view-data-factories"></a>サーバー エクスプローラーを使用して Data Factory を表示する
1. **Visual Studio** のメニューで **[ビュー]** をクリックし、**[サーバー エクスプローラー]** をクリックします。
2. [サーバー エクスプローラー] ウィンドウで、**[Azure]**、**[Data Factory]** の順に展開します。 **[Visual Studio にサインイン]** が表示されたら、Azure サブスクリプションに関連付けられている**アカウント**を入力し、**[続行]** をクリックします。 **パスワード**を入力し、**[サインイン]** をクリックします。 Visual Studio は、サブスクリプション内のすべての Azure データ ファクトリに関する情報を取得しようとします。 **[Data Factory Task List (Data Factory タスク リスト)]** ウィンドウで、この操作のステータスを確認します。

    ![[サーバー エクスプローラー]](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)
3. データ ファクトリを右クリックし、[Data Factory を新しいプロジェクトにエクスポートする] を選択して、既存のデータ ファクトリに基づいて Visual Studio プロジェクトを作成します。

    ![Data Factory の Visual Studio プロジェクトへのエクスポート](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>Visual Studio の Data Factory ツールを更新する
Visual Studio の Azure Data Factory ツールを更新するには、次の手順に従います。

1. メニューで **[ツール]** をクリックし、**[拡張機能と更新プログラム]** を選択します。 
2. 左ウィンドウで **[更新]** を選択し、**[Visual Studio ギャラリー]** を選択します。
3. **[Azure Data Factory tools for Visual Studio]** を選択して、**[更新]** をクリックします。 このエントリが表示されない場合は、ツールは既に最新バージョンです。 

Azure ポータルを使用して、このチュートリアルで作成したパイプラインとデータセットを監視する方法については、 [データセットとパイプラインの監視](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) に関するセクションを参照してください。

## <a name="see-also"></a>関連項目
| トピック | 説明 |
|:--- |:--- |
| [パイプライン](data-factory-create-pipelines.md) |この記事では、Azure Data Factory のパイプラインとアクティビティについて説明します。 |
| [データセット](data-factory-create-datasets.md) |この記事では、Azure Data Factory のデータセットについて説明します。 |
| [スケジュールと実行](data-factory-scheduling-and-execution.md) |この記事では、Azure Data Factory アプリケーション モデルのスケジュール設定と実行の側面について説明します。 |
| [監視アプリを使用したパイプラインの監視と管理に関する記事](data-factory-monitor-manage-app.md) |この記事では、監視と管理アプリを使用してパイプラインを監視、管理、デバッグする方法について説明します。 |


