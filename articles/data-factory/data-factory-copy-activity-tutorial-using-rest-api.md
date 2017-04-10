---
title: "チュートリアル: コピー アクティビティがあるパイプラインを REST API で作成する | Microsoft Docs"
description: "このチュートリアルでは、REST API を使用して、コピー アクティビティがある Azure Data Factory パイプラインを作成します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1704cdf8-30ad-49bc-a71c-4057e26e7350
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/17/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 4b29fd1c188c76a7c65c4dcff02dc9efdf3ebaee
ms.openlocfilehash: c5049cbe98dbb04deae4a2b9dc098938aa65495a


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-rest-api"></a>チュートリアル: コピー アクティビティがあるパイプラインを REST API で作成する
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

このチュートリアルでは、REST API を使用して Azure Data Factory を作成および監視する方法について説明しています。 データ ファクトリのパイプラインは、コピー アクティビティを使用して、Azure Blob Storage から Azure SQL Database にデータをコピーします。

> [!NOTE]
> この記事では、すべての Data Factory REST API を取り上げているわけではありません。 Data Factory REST API に関する包括的なドキュメントについては、 [Data Factory REST API リファレンス](/rest/api/datafactory/) を参照してください。
> 
> このチュートリアルのデータ パイプラインでは、ソース データ ストアからターゲット データ ストアにデータをコピーします。 入力データを変換して出力データを生成するのではありません。 Azure Data Factory を使用してデータを変換する方法のチュートリアルについては、[Hadoop クラスターを使用してデータを変換する最初のパイプラインを作成する方法に関するチュートリアル](data-factory-build-your-first-pipeline.md)を参照してください。

## <a name="prerequisites"></a>前提条件
* 「 [チュートリアルの概要](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 」に目を通し、 **前提条件** の手順を完了する必要があります。
* コンピューターに [Curl](https://curl.haxx.se/dlwiz/) をインストールします。 データ ファクトリを作成するには、Curl ツールと REST コマンドを使用します。 
* [この記事](../azure-resource-manager/resource-group-create-service-principal-portal.md) の手順に従って、次の操作を行います。 
  1. Azure Active Directory に、 **ADFCopyTutorialApp** という名前の Web アプリケーションを作成します。
  2. **クライアント ID** と**秘密キー**を取得します。 
  3. **テナント ID**を取得します。 
  4. **ADFCopyTutorialApp** アプリケーションを **Data Factory 共同作成者**ロールに割り当てます。  
* [Azure PowerShell](/powershell/azureps-cmdlets-docs)をインストールします。  
* **PowerShell** を起動し、次のコマンドを実行します。 Azure PowerShell は、このチュートリアルが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。
  
  1. 次のコマンドを実行して、Azure ポータルへのサインインに使用するユーザー名とパスワードを入力します。
    
    ```PowerShell 
    Login-AzureRmAccount
    ```   
  2. 次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。

    ```PowerShell     
    Get-AzureRmSubscription
    ``` 
  3. 次のコマンドを実行して、使用するサブスクリプションを選択します。 **&lt;NameOfAzureSubscription**&gt; を自分の Azure サブスクリプションの名前で置き換えます。 
     
    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```
  4. PowerShell で次のコマンドを実行して、 **ADFTutorialResourceGroup** という名前の Azure リソース グループを作成します。  

    ```PowerShell     
      New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
     
      リソース グループが既に存在する場合は、それを更新するか (Y) そのまま保持するか (N) を指定します。 
     
      このチュートリアルの一部の手順は、ADFTutorialResourceGroup という名前のリソース グループを使用することを前提としています。 異なるリソース グループを使用する場合は、このチュートリアルで ADFTutorialResourceGroup の代わりにそのリソース グループの名前を使用する必要があります。

## <a name="create-json-definitions"></a>JSON 定義の作成
curl.exe があるフォルダーに、以下の JSON ファイルを作成します。 

### <a name="datafactoryjson"></a>datafactory.json
> [!IMPORTANT]
> 名前はグローバルに一意である必要があります。一意の名前にするために、ADFCopyTutorialDF にプレフィックス/サフィックスを付けることができます。 
> 
> 

```JSON
{  
    "name": "ADFCopyTutorialDF",  
    "location": "WestUS"
}  
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> **accountname** と **accountkey** を Azure ストレージ アカウントの名前とキーに置き換えます。 ストレージ アクセス キーを取得する方法については、「 [ストレージ アクセス キーの表示、コピーおよび再生成](../storage/storage-create-storage-account.md#manage-your-storage-access-keys)」を参照してください。
> 
> 

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="azuersqllinkedservicejson"></a>azuersqllinkedservice.json
> [!IMPORTANT]
> **servername**、**databasename**、**username**、**password** を、Azure SQL サーバーの名前、SQL データベースの名前、ユーザー アカウント、アカウントのパスワードに置き換えます。  
> 
> 

```JSON
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
{
  "name": "AzureBlobInput",
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
    "linkedServiceName": "AzureStorageLinkedService",
    "typeProperties": {
      "folderPath": "adftutorial/",
      "fileName": "emp.txt",
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

JSON 定義では **AzureBlobInput** という名前のデータセットを定義します。これはパイプラインのアクティビティの入力データを表します。 また、入力データが **emp.txt** ファイルに配置されることを指定します。このファイルは、BLOB コンテナー **adftutorial** にあります。 

 以下の点に注意してください。 

* データセットの **type** は **AzureBlob** に設定されています。
* **linkedServiceName** は **AzureStorageLinkedService** に設定されています。 
* **folderPath** は **adftutorial** コンテナーに設定され、**fileName** は **emp.txt** に設定されています。  
* format の **type** は **TextFormat** に設定されています。
* テキスト ファイル内に&2; つのフィールド (**FirstName** と **LastName**) があり、コンマ (**columnDelimiter**) で区切られています。    
* **availability** が **hourly** に設定されています (frequency は hour、interval は 1 に設定されています)。 そのため、Data Factory は、指定された BLOB コンテナー (**adftutorial**) のルート フォルダーにある入力データを&1; 時間ごとに検索します。 

入力データセット用に **fileName** を指定しない場合、入力フォルダー (**folderPath**) のすべてのファイルまたは BLOB が入力と見なされます。 JSON で fileName を指定した場合は、指定されたファイル/BLOB のみが入力と見なされます。

**出力テーブル**に **fileName** を指定しない場合、**folderPath** に生成されるファイルには Data.&lt;Guid&gt;.txt という形式で名前が付けられます (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt)。

**folderPath** と **fileName** を **SliceStart** 時刻に基づいて動的に設定するには、**partitionedBy** プロパティを使用します。 次の例では、folderPath に SliceStart (処理されるスライスの開始時刻) の年、月、日を使用し、fileName に SliceStart の時間を使用します。 たとえば、スライスが 2014-10-20T08:00:00 に生成されている場合、folderName は wikidatagateway/wikisampledataout/2014/10/20 に設定され、fileName は 08.csv に設定されます。 

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

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
{
  "name": "AzureSqlOutput",
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
```

JSON 定義では **AzureSqlOutput** という名前のデータセットを定義します。これはパイプラインのアクティビティの出力データを表します。 また、結果がテーブル **emp** に格納されることを指定します。このテーブルは、AzureSqlLinkedService によって表されるデータベース内にあります。 **availability** セクションでは、出力データセットが 1 時間ごとに生成されることを指定します (頻度: 時間、間隔: 1)。

以下の点に注意してください。 

* データセットの **type** は **AzureSQLTable** に設定されています。
* **linkedServiceName** は **AzureSqlLinkedService** に設定されています。
* **tablename** は **emp** に設定されています。
* データベース内の emp テーブルには、**ID**、**FirstName**、**LastName** の&3; つの列があります。 ID は ID 列であるため、ここで指定する必要があるのは **FirstName** と **LastName** のみです。
* **availability** は **hourly** に設定されています (**frequency** は **hour**、**interval** は **1** に設定されています)。  Data Factory サービスは、Azure SQL Database 内の **emp** テーブルに&1; 時間ごとに出力データ スライスを生成します。

### <a name="pipelinejson"></a>pipeline.json

```JSON
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
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
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
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-08-12T00:00:00Z",
    "end": "2016-08-13T00:00:00Z"
  }
}
```

以下の点に注意してください。

* activities セクションに、**type** が **CopyActivity** に設定されたアクティビティが&1; つだけあります。
* アクティビティの入力は **AzureBlobInput** に設定され、アクティビティの出力は **AzureSqlOutput** に設定されています。
* **transformation** セクションでは、ソースの種類として **BlobSource** が指定され、シンクの種類として **SqlSink** が指定されています。

**start** プロパティの値を現在の日付に置き換え、**end** プロパティの値を翌日の日付に置き換えます。 日付の部分のみを指定し、時刻の部分をスキップすることもできます。 たとえば、"2015-02-03" と "2015-02-03T00:00:00Z" は同じです。

start と end の日時は、いずれも [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)である必要があります  (例: 2014-10-14T16:32:41Z)。 **end** の時刻は省略可能ですが、このチュートリアルでは使用します。 

**end** プロパティの値を指定しない場合、"**start + 48 時間**" として計算されます。 パイプラインを無期限に実行する場合は、**9999-09-09** を **end** プロパティの値として指定します。

この例では、各データ スライスが 1 時間ごとに生成されるため、データ スライスは 24 個になります。

> [!NOTE]
> 前の例で使用した JSON プロパティの詳細については、「 [パイプラインのしくみ](data-factory-create-pipelines.md) 」を参照してください。
> 
> 

## <a name="set-global-variables"></a>グローバル変数の設定
Azure PowerShell で、値を独自の値に置き換えて、以下のコマンドを実行します。

> [!IMPORTANT]
> クライアント ID、クライアント シークレット、テナント ID、サブスクリプション ID を取得する手順については、「 [前提条件](#prerequisites) 」セクションを参照してください。   
> 
> 

```JSON
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
$adf = "ADFCopyTutorialDF"
```

## <a name="authenticate-with-aad"></a>AAD での認証
Azure Active Directory (AAD) で認証するには、次のコマンドを実行します。 

```PowerShell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken) 
```

## <a name="create-data-factory"></a>データ ファクトリの作成
この手順では、**ADFCopyTutorialDF** という名前の Azure データ ファクトリを作成します。 データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 パイプラインには、1 つまたは複数のアクティビティを含めることができます。 たとえば、コピー元データ ストアからコピー先データ ストアにデータをコピーするコピー アクティビティや、 Hive スクリプトを実行し、入力データを変換して出力データを生成する HDInsight Hive アクティビティなどを含めることができます。 以下のコマンドを実行して、データ ファクトリを作成します。 

1. コマンドを **cmd**という名前の変数に割り当てます。 
   
    ここで指定するデータ ファクトリの名前 (ADFCopyTutorialDF) が、 **datafactory.json**で指定した名前と一致することを確認します。 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF?api-version=2015-10-01};
    ```
2. **Invoke-Command**を使用して、コマンドを実行します。
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 結果を表示します。 データ ファクトリが正常に作成された場合は、**results** にデータ ファクトリの JSON が表示されます。そうでない場合は、エラー メッセージが表示されます。  
   
    ```
    Write-Host $results
    ```

以下の点に注意してください。

* Azure Data Factory の名前はグローバルに一意にする必要があります。 results に **"データ ファクトリ名 "ADFCopyTutorialDF" は利用できません"**というエラーが表示される場合は、次の手順に従います。  
  
  1. **datafactory.json** ファイルで名前を変更します (たとえば、yournameADFCopyTutorialDF)。
  2. **$cmd** 変数に値が割り当てられる最初のコマンドで、ADFCopyTutorialDF を新しい名前に置き換え、コマンドを実行します。 
  3. REST API を呼び出す次の&2; つのコマンドを実行して、データ ファクトリを作成し、操作の結果を出力します。 
     
     Data Factory アーティファクトの名前付け規則については、「 [Azure Data Factory - 名前付け規則](data-factory-naming-rules.md) 」を参照してください。
* Data Factory インスタンスを作成するには、Azure サブスクリプションの共同作成者または管理者である必要があります。
* データ ファクトリの名前は今後、DNS 名として登録される可能性があるため、一般ユーザーに表示される場合があります。
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

パイプラインを作成する前に、まず、Data Factory エンティティをいくつか作成する必要があります。 最初に、ソース データ ストアや保存先データ ストアを自分のデータ ストアにリンクするリンクされたサービスを作成します。 次に、リンクされたデータ ストア内のデータを表す入力データセットと出力データセットを定義します。 最後に、これらのデータセットを使用するアクティビティを含むパイプラインを作成します。

## <a name="create-linked-services"></a>リンクされたサービスの作成
リンクされたサービスは、データ ストアまたはコンピューティング サービスを Azure Data Factory にリンクします。 データ ストアには、Azure Storage、Azure SQL Database、またはオンプレミスの SQL Server データベースを指定できます。これらのデータ ストアには、Data Factory パイプラインの入力データが含まれているか、出力データが格納されています。 コンピューティング サービスは、入力データを処理し、出力データを生成するサービスです。 

この手順では、**AzureStorageLinkedService** と **AzureSqlLinkedService** の&2; つのリンクされたサービスを作成します。 リンクされたサービス AzureStorageLinkedService は Azure ストレージ アカウントを、AzureSqlLinkedService は Azure SQL Database を **ADFCopyTutorialDF**というデータ ファクトリにリンクします。 このチュートリアルの後半では、AzureStorageLinkedService 内の BLOB コンテナーから AzureSqlLinkedService 内の SQL テーブルにデータをコピーするパイプラインを作成します。

### <a name="create-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する
この手順では、Azure ストレージ アカウントをデータ ファクトリにリンクします。 このチュートリアルでは、Azure ストレージ アカウントを使用して、入力データを格納します。 

1. コマンドを **cmd**という名前の変数に割り当てます。 

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. **Invoke-Command**を使用して、コマンドを実行します。
    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 結果を表示します。 リンクされたサービスが正常に作成された場合は、**results** に、リンクされたサービスの JSON が表示されます。そうでない場合は、エラー メッセージが表示されます。

    ```PowerShell   
    Write-Host $results
    ```

### <a name="create-azure-sql-linked-service"></a>Azure SQL のリンクされたサービスの作成
この手順では、Azure SQL データベースをデータ ファクトリにリンクします。 このチュートリアルでは、同じ Azure SQL データベースを使用して、出力データを格納します。

1. コマンドを **cmd**という名前の変数に割り当てます。 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
    ```
2. **Invoke-Command**を使用して、コマンドを実行します。
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 結果を表示します。 リンクされたサービスが正常に作成された場合は、**results** に、リンクされたサービスの JSON が表示されます。そうでない場合は、エラー メッセージが表示されます。
   
    ```PowerShell
    Write-Host $results
    ```

## <a name="create-datasets"></a>データセットを作成する
前の手順では、**AzureStorageLinkedService** と **AzureSqlLinkedService** というリンクされたサービスを作成し、Azure ストレージ アカウントと Azure SQL データベースを **ADFCopyTutorialDF** というデータ ファクトリにリンクしました。 この手順では、次の手順で作成するパイプラインのコピー アクティビティ用に入力データと出力データを表すデータセットを作成します。 

このチュートリアルの入力データセットは、AzureStorageLinkedService がポイントする Azure Storage 内の BLOB コンテナーを参照します。 出力データセットは、AzureSqlLinkedService がポイントする Azure SQL Database 内の SQL テーブルを参照します。  

### <a name="prepare-azure-blob-storage-and-azure-sql-database-for-the-tutorial"></a>Azure Blob Storage と Azure SQL Database をチュートリアル用に準備する
このチュートリアルで使用する Azure BLOB ストレージと Azure SQL Database を準備するには、次の手順を実行します。 

* **AzureStorageLinkedService** がポイントする Azure Blob Storage 内に **adftutorial** という名前の BLOB コンテナーを作成します。 
* **emp.txt** という名前のテキスト ファイルを作成し、BLOB として **adftutorial** コンテナーにアップロードします。 
* **AzureSqlLinkedService** がポイントする Azure SQL データベース内に **emp** という名前のテーブルを作成します。

1. メモ帳を起動し、次のテキストを貼り付け、**emp.txt** という名前でハード ドライブの **C:\ADFGetStartedPSH** フォルダーに保存します。 

    ```   
    John, Doe
    Jane, Doe
    ```
2. [Azure Storage エクスプローラー](https://azurestorageexplorer.codeplex.com/)などのツールを使用して **adftutorial** コンテナーを作成し、このコンテナーに **emp.txt** ファイルをアップロードします。
   
    ![Azure ストレージ エクスプローラー](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
3. 次の SQL スクリプトを使用して、 **emp** テーブルを Azure SQL Database に作成します。  

    ```SQL
    CREATE TABLE dbo.emp 
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 
    ```

    SQL Server 2014 がコンピューターにインストールされている場合は、[「SQL Server Management Studio を使用した Azure SQL データベースの管理」の手順 2. の SQL データベースへの接続][sql-management-studio]の手順に従い、Azure SQL サーバーに接続して SQL スクリプトを実行します。

    クライアントから Azure SQL サーバーへのアクセスが許可されていない場合は、コンピューター (IP アドレス) からのアクセスを許可するように、Azure SQL サーバーのファイアウォールを構成する必要があります。 Azure SQL サーバーのファイアウォールを構成する手順については、 [こちらの記事](../sql-database/sql-database-configure-firewall-settings.md) を参照してください。

### <a name="create-input-dataset"></a>入力データセットの作成
この手順では、リンクされたサービス **AzureStorageLinkedService** が表す Azure Storage 内の BLOB コンテナーをポイントする **AzureBlobInput** という名前のデータセットを作成します。 この BLOB コンテナー (**adftutorial**) には、**emp.txt** ファイルの入力データが含まれています。 

1. コマンドを **cmd**という名前の変数に割り当てます。 

    ```PowerSHell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. **Invoke-Command**を使用して、コマンドを実行します。
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 結果を表示します。 データセットが正常に作成された場合は、**results** にデータセットの JSON が表示されます。そうでない場合は、エラー メッセージが表示されます。
   
    ```PowerShell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>出力データセットの作成
この手順では、 **AzureSqlOutput**という名前の出力テーブルを作成します。 このデータセットは、**AzureSqlLinkedService** で表される Azure SQL データベース内の SQL テーブル (**emp**) をポイントします。 パイプラインで入力 BLOB から **emp** テーブルにデータがコピーされます。 

1. コマンドを **cmd**という名前の変数に割り当てます。

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
    ```
2. **Invoke-Command**を使用して、コマンドを実行します。
    
    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 結果を表示します。 データセットが正常に作成された場合は、**results** にデータセットの JSON が表示されます。そうでない場合は、エラー メッセージが表示されます。
   
    ```PowerShell
    Write-Host $results
    ``` 

## <a name="create-pipeline"></a>パイプラインの作成
この手順では、**AzureBlobInput** を入力、**AzureSqlOutput** を出力として使用する**コピー アクティビティ**を持つパイプラインを作成します。

1. コマンドを **cmd**という名前の変数に割り当てます。

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. **Invoke-Command**を使用して、コマンドを実行します。

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 結果を表示します。 データセットが正常に作成された場合は、**results** にデータセットの JSON が表示されます。そうでない場合は、エラー メッセージが表示されます。  

    ```PowerShell   
    Write-Host $results
    ```

**お疲れさまでした。** Azure Blob Storage から Azure SQL データベースにデータをコピーするパイプラインを持つ Azure データ ファクトリが正常に作成されました。

## <a name="monitor-pipeline"></a>パイプラインを監視する
この手順では、Data Factory REST API を使用して、パイプラインによって生成されるスライスを監視します。

```PowerShell
$ds ="AzureSqlOutput"
```

```PowerShell
$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};
```

```PowerShell
$results2 = Invoke-Command -scriptblock $cmd;
```

```PowerShell
IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

スライスが **Ready** 状態または **Failed** 状態として表示されるまで、これらのコマンドを実行します。 スライスが Ready 状態のときは、出力データ用の Azure SQL データベース内の **emp** テーブルを確認します。 

スライスごとに、ソース ファイルのデータの&2; つの行が、Azure SQL データベースの emp テーブルにコピーされます。 そのため、すべてのスライスが正常に処理されると (Ready 状態になると)、emp テーブルに 24 個の新しいレコードが表示されます。 

## <a name="summary"></a>まとめ
このチュートリアルでは、REST API を使用して Azure データ ファクトリを作成し、Azure BLOB から Azure SQL Database にデータをコピーしました。 以下は、このチュートリアルで実行した手順の概要です。  

1. Azure **データ ファクトリ**を作成しました。
2. 次の **リンクされたサービス**を作成しました。
   1. 入力データを保持する Azure ストレージ アカウントをリンクするための、Azure Storage のリンクされたサービス。     
   2. 出力データを保持する Azure SQL データベースをリンクするための、Azure SQL のリンクされたサービス。 
3. パイプラインの入力データと出力データを記述する **データセット**を作成しました。
4. ソースとして BlobSource、シンクとして SqlSink を持つコピー アクティビティを含む **パイプライン** を作成しました。 

## <a name="see-also"></a>関連項目
| トピック | 説明 |
|:--- |:--- |
| [データ移動アクティビティ](data-factory-data-movement-activities.md) |この記事には、このチュートリアルで使用したコピー アクティビティの詳細な情報が記載されています。 |
| [スケジュールと実行](data-factory-scheduling-and-execution.md) |この記事では、Azure Data Factory アプリケーション モデルのスケジュール設定と実行の側面について説明します。 |
| [パイプライン](data-factory-create-pipelines.md) |この記事では、Azure Data Factory のパイプラインとアクティビティの概要、およびそれらを利用して実際のシナリオやビジネスのためにエンド ツー エンドのデータ主導ワークフローを作成する方法を説明します。 |
| [データセット](data-factory-create-datasets.md) |この記事では、Azure Data Factory のデータセットについて説明します。 |
| [監視アプリを使用したパイプラインの監視と管理に関する記事](data-factory-monitor-manage-app.md) |この記事では、監視と管理アプリを使用してパイプラインを監視、管理、デバッグする方法について説明します。 |

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: /powershell/azureps-cmdlets-docs
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md



<!--HONumber=Feb17_HO1-->


