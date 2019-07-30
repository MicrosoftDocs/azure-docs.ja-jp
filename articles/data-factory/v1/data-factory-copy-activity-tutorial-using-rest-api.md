---
title: チュートリアル:REST API を使用して Azure Data Factory パイプラインを作成する | Microsoft Docs
description: このチュートリアルでは、REST API を使用してコピー アクティビティが含まれた Azure Data Factory パイプラインを作成し、Azure Blob Storage から Azure SQL データベースにデータをコピーします。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 1704cdf8-30ad-49bc-a71c-4057e26e7350
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6b5698d94a09096d58b316ca3b23bead5b1a39a7
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839405"
---
# <a name="tutorial-use-rest-api-to-create-an-azure-data-factory-pipeline-to-copy-data"></a>チュートリアル:REST API を使用して、データをコピーする Azure Data Factory パイプラインを作成する 
> [!div class="op_single_selector"]
> * [概要と前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [コピー ウィザード](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager テンプレート](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[コピー アクティビティのチュートリアル](../quickstart-create-data-factory-rest-api.md)に関するページを参照してください。 

この記事では、REST API を使用して、Azure Blob Storage から Azure SQL データベースにデータをコピーするパイプラインを備えたデータ ファクトリを作成します。 Azure Data Factory の使用経験がない場合は、このチュートリアルを実行する前に、「[Azure Data Factory の概要](data-factory-introduction.md)」を参照してください。   

このチュートリアルでは、1 つのアクティビティ (コピー アクティビティ) が含まれたパイプラインを作成します。 コピー アクティビティは、サポートされているデータ ストアからサポートされているシンク データ ストアにデータをコピーします。 ソースおよびシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に関するセクションを参照してください。 このアクティビティは、安全で信頼性の高いスケーラブルな方法によってさまざまなデータ ストア間でデータをコピーできる、グローバルに利用可能なサービスによって動作します。 コピー アクティビティの詳細については、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事を参照してください。

1 つのパイプラインには複数のアクティビティを含めることができます。 また、1 つのアクティビティの出力データセットを別のアクティビティの入力データセットとして指定することで、2 つのアクティビティを連鎖させる (アクティビティを連続的に実行する) ことができます。 詳細については、「[パイプライン内の複数アクティビティ](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)」を参照してください。

> [!NOTE]
> この記事では、すべての Data Factory REST API を取り上げているわけではありません。 Data Factory REST API に関する包括的なドキュメントについては、 [Data Factory REST API リファレンス](/rest/api/datafactory/) を参照してください。
>  
> このチュートリアルのデータ パイプラインでは、ソース データ ストアからターゲット データ ストアにデータをコピーします。 Azure Data Factory を使用してデータを変換する方法のチュートリアルについては、[Hadoop クラスターを使用してデータを変換するパイプラインを作成する方法のチュートリアル](data-factory-build-your-first-pipeline.md)を参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* 「 [チュートリアルの概要](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 」に目を通し、 **前提条件** の手順を完了する必要があります。
* コンピューターに [Curl](https://curl.haxx.se/dlwiz/) をインストールします。 データ ファクトリを作成するには、Curl ツールと REST コマンドを使用します。 
* [この記事](../../active-directory/develop/howto-create-service-principal-portal.md) の手順に従って、次の操作を行います。 
  1. Azure Active Directory に、 **ADFCopyTutorialApp** という名前の Web アプリケーションを作成します。
  2. **クライアント ID** と**秘密キー**を取得します。 
  3. **テナント ID**を取得します。 
  4. **ADFCopyTutorialApp** アプリケーションを **Data Factory 共同作成者**ロールに割り当てます。  
* [Azure PowerShell](/powershell/azure/overview)をインストールします。  
* **PowerShell** を起動し、次の手順を実行します。 Azure PowerShell は、このチュートリアルが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。
  
  1. 次のコマンドを実行して、Azure Portal へのサインインに使用するユーザー名とパスワードを入力します。
    
     ```PowerShell 
     Connect-AzAccount
     ```   
  2. 次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。

     ```PowerShell     
     Get-AzSubscription
     ``` 
  3. 次のコマンドを実行して、使用するサブスクリプションを選択します。 **&lt;NameOfAzureSubscription**&gt; を自分の Azure サブスクリプションの名前で置き換えます。 
     
     ```PowerShell
     Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
     ```
  4. PowerShell で次のコマンドを実行して、 **ADFTutorialResourceGroup** という名前の Azure リソース グループを作成します。  

     ```PowerShell     
      New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
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
> **accountname** と **accountkey** を Azure ストレージ アカウントの名前とキーに置き換えます。 ストレージ アクセス キーを取得する方法については、「 [ストレージ アクセス キーの表示、コピーおよび再生成](../../storage/common/storage-account-manage.md#access-keys)」を参照してください。

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

JSON プロパティの詳細については、「[Azure Storage のリンクされたサービス](data-factory-azure-blob-connector.md#azure-storage-linked-service)」を参照してください。

### <a name="azuresqllinkedservicejson"></a>azuresqllinkedservice.json
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

JSON プロパティの詳細については、[Azure SQL のリンクされたサービス](data-factory-azure-sql-connector.md#linked-service-properties)に関するセクションを参照してください。

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

次の表に、このスニペットで使用される JSON プロパティの説明を示します。

| プロパティ | 説明 |
|:--- |:--- |
| type | データは Azure Blob Storage に存在するため、type プロパティを **AzureBlob** に設定しています。 |
| linkedServiceName | 前に作成した **AzureStorageLinkedService** を参照します。 |
| folderPath | BLOB **コンテナー**と、入力 BLOB を格納する**フォルダー**を指定します。 このチュートリアルでは、adftutorial は BLOB コンテナーで、フォルダーはルート フォルダーです。 | 
| fileName | このプロパティは省略可能です。 このプロパティを省略した場合は、folderPath のすべてのファイルが取得されます。 このチュートリアルでは fileName に **emp.txt** が指定されているため、このファイルのみが処理のために取得されます。 |
| format -> type |入力ファイルはテキスト形式のため、**TextFormat** を使用します。 |
| columnDelimiter | 入力ファイル内の列は**コンマ (`,`)** で区切られています。 |
| frequency/interval | frequency を **Hour** に設定し、interval を **1** に設定しています。つまり、**1 時間ごと**に入力スライスが取得されます。 言い換えると、Data Factory サービスは、指定された BLOB コンテナー (**adftutorial**) のルート フォルダーにある入力データを 1 時間ごとに検索します。 パイプラインの開始時刻の前または終了時刻の後ではなく、開始時刻と終了時刻の間のデータが検索されます。  |
| external | このパイプラインによってデータが生成されない場合は、このプロパティを **true** に設定します。 このチュートリアルの入力データは emp.txt ファイルに存在し、このパイプラインで生成されるわけではないため、このプロパティを true に設定します。 |

これらの JSON プロパティの詳細については、[Azure BLOB コネクタ](data-factory-azure-blob-connector.md#dataset-properties)に関する記事を参照してください。

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
次の表に、このスニペットで使用される JSON プロパティの説明を示します。

| プロパティ | 説明 |
|:--- |:--- |
| type | type プロパティを **AzureSqlTable** に設定します。これは、データを Azure SQL データベースのテーブルにコピーするためです。 |
| linkedServiceName | 前に作成した **AzureSqlLinkedService** を参照します。 |
| tableName | データのコピー先となる**テーブル**を指定します。 | 
| frequency/interval | frequency は **Hour**、interval は **1** に、それぞれ設定されています。これは、出力スライスがパイプラインの開始時刻から終了時刻までの間 **1 時間ごと**に生成されることを表します (出力スライスは、開始時刻の前および終了時刻の後には生成されません)。  |

データベース内の emp テーブルには、**ID**、**FirstName**、**LastName** の 3 つの列があります。 ID は ID 列であるため、ここで指定する必要があるのは **FirstName** と **LastName** のみです。

これらの JSON プロパティの詳細については、[Azure SQL コネクタ](data-factory-azure-sql-connector.md#dataset-properties)に関する記事を参照してください。

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
    "start": "2017-05-11T00:00:00Z",
    "end": "2017-05-12T00:00:00Z"
  }
}
```

以下の点に注意してください。

- activities セクションに、**type** が **Copy** に設定されたアクティビティが 1 つだけあります。 コピー アクティビティの詳細については、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事を参照してください。 Data Factory ソリューションでは、[データ変換アクティビティ](data-factory-data-transformation-activities.md)を使用することもできます。
- アクティビティの入力は **AzureBlobInput** に設定され、アクティビティの出力は **AzureSqlOutput** に設定されています。 
- **typeProperties** セクションでは、ソースの種類として **BlobSource** が指定され、シンクの種類として **SqlSink** が指定されています。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの完全な一覧については、[サポートされるデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に関するセクションを参照してください。 サポートされているデータ ストアをソースおよびシンクとして使用する方法については、表内のリンクをクリックしてください。  
 
**start** プロパティの値を現在の日付に置き換え、**end** プロパティの値を翌日の日付に置き換えます。 日付の部分のみを指定し、時刻の部分をスキップすることもできます。 たとえば、"2017-02-03" と "2017-02-03T00:00:00Z" は同じです。
 
start と end の日時は、いずれも [ISO 形式](https://en.wikipedia.org/wiki/ISO_8601)である必要があります。 例: 2016-10-14T16:32:41Z。 **end** の時刻は省略可能ですが、このチュートリアルでは使用します。 
 
**end** プロパティの値を指定しない場合、"**start + 48 時間**" として計算されます。 パイプラインを無期限に実行する場合は、**9999-09-09** を **end** プロパティの値として指定します。
 
前の例では、各データ スライスが 1 時間ごとに生成されるため、データ スライスは 24 個になります。

パイプライン定義内の JSON プロパティの説明については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 コピー アクティビティ定義内の JSON プロパティの説明については、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事を参照してください。 BlobSource でサポートされる JSON プロパティの説明については、[Azure BLOB コネクタ](data-factory-azure-blob-connector.md)に関する記事を参照してください。 SqlSink でサポートされる JSON プロパティの説明については、[Azure SQL Database コネクタ](data-factory-azure-sql-connector.md)に関する記事を参照してください。

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
```

使用しているデータ ファクトリの名前を更新した後、次のコマンドを実行します。 

```
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
   
    > [!IMPORTANT]
    > ここで指定するデータ ファクトリの名前 (ADFCopyTutorialDF) が、 **datafactory.json**で指定した名前と一致することを確認します。 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF0411?api-version=2015-10-01};
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

* Azure Data Factory の名前はグローバルに一意にする必要があります。 results に"**データ ファクトリ名 "ADFCopyTutorialDF" は使用できません**" というエラーが表示される場合は、次の手順に従います。  
  
  1. **datafactory.json** ファイルで名前を変更します (たとえば、yournameADFCopyTutorialDF)。
  2. **$cmd** 変数に値が割り当てられる最初のコマンドで、ADFCopyTutorialDF を新しい名前に置き換え、コマンドを実行します。 
  3. REST API を呼び出す次の 2 つのコマンドを実行して、データ ファクトリを作成し、操作の結果を出力します。 
     
     Data Factory アーティファクトの名前付け規則については、 [Data Factory - 名前付け規則](data-factory-naming-rules.md) に関するトピックを参照してください。
* Data Factory インスタンスを作成するには、Azure サブスクリプションの共同作成者または管理者である必要があります。
* データ ファクトリの名前は今後、DNS 名として登録される可能性があるため、一般ユーザーに表示される場合があります。
* エラー "**This subscription is not registered to use namespace Microsoft.DataFactory (このサブスクリプションは、名前空間 Microsoft.DataFactory を使用するように登録されていません)** " が表示された場合は、以下のいずれかの操作を行ってから、もう一度発行してみます。 
  
  * Azure PowerShell で次のコマンドを実行して、Data Factory プロバイダーを登録します。 

    ```PowerShell    
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Data Factory プロバイダーが登録されたことを確認するには、次のコマンドを実行します。 
    
    ```PowerShell
    Get-AzResourceProvider
    ```
  * Azure サブスクリプションを使用して [Azure ポータル](https://portal.azure.com) にログインし、[Data Factory] ブレードに移動するか、Azure ポータルでデータ ファクトリを作成します。 この操作によって、プロバイダーが自動的に登録されます。

パイプラインを作成する前に、まず、Data Factory エンティティをいくつか作成する必要があります。 最初に、ソース データ ストアや保存先データ ストアを自分のデータ ストアにリンクするリンクされたサービスを作成します。 次に、リンクされたデータ ストア内のデータを表す入力データセットと出力データセットを定義します。 最後に、これらのデータセットを使用するアクティビティを含むパイプラインを作成します。

## <a name="create-linked-services"></a>リンクされたサービスを作成します
データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このチュートリアルでは、Azure HDInsight、Azure Data Lake Analytics などのコンピューティング サービスを使用しません。 ここでは、Azure Storage (ソース) と Azure SQL Database (ターゲット) の 2 種類のデータ ストアを使用します。 したがって、AzureStorageLinkedService と AzureSqlLinkedService という名前の 2 つのリンクされたサービスを作成します (タイプ: AzureStorage、AzureSqlDatabase)。  

AzureStorageLinkedService は、Azure ストレージ アカウントをデータ ファクトリにリンクします。 このストレージ アカウントは、[前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)の一部としてコンテナーを作成し、データをアップロードしたストレージ アカウントです。   

AzureSqlLinkedService は、Azure SQL データベースをデータ ファクトリにリンクします。 Blob Storage からコピーされたデータは、このデータベースに格納されます。 [前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)の一部として、このデータベースに emp テーブルを作成しました。  

### <a name="create-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する
この手順では、Azure ストレージ アカウントをデータ ファクトリにリンクします。 このセクションで、Azure Storage アカウントの名前とキーを指定します。 Azure Storage のリンクされたサービスの定義に使用する JSON プロパティの詳細については、「[Azure Storage のリンクされたサービス](data-factory-azure-blob-connector.md#azure-storage-linked-service)」を参照してください。  

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
この手順では、Azure SQL データベースをデータ ファクトリにリンクします。 このセクションで、Azure SQL サーバー名、データベース名、ユーザー名、ユーザー パスワードを指定します。 Azure SQL のリンクされたサービスの定義に使用する JSON プロパティの詳細については、[Azure SQL のリンクされたサービス](data-factory-azure-sql-connector.md#linked-service-properties)に関するセクションをご覧ください。

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
前の手順では、Azure ストレージ アカウントと Azure SQL データベースをデータ ファクトリにリンクするためのリンクされたサービスを作成しました。 この手順では、AzureBlobInput と AzureSqlOutput という名前の 2 つのデータセットを定義します。これらはそれぞれ、AzureStorageLinkedService と AzureSqlLinkedService が参照するデータ ストアに格納されている入力データと出力データを表します。

Azure Storage のリンクされたサービスは、Data Factory サービスが実行時に Azure ストレージ アカウントへの接続に使用する接続文字列を指定します。 また、入力 BLOB データセット (AzureBlobInput) は、コンテナーと、入力データが含まれているフォルダーを指定します。  

同様に、Azure SQL Database のリンクされたサービスは、Data Factory サービスが実行時に Azure SQL データベースへの接続に使用する接続文字列を指定します。 出力 SQL テーブル データセット (OututDataset) は、BLOB ストレージのデータのコピー先となるデータベース内のテーブルを指定します。 

### <a name="create-input-dataset"></a>入力データセットの作成
この手順では、BLOB ファイル (emp.txt) を参照する AzureBlobInput という名前のデータセットを作成します。このファイルは、リンクされたサービス AzureStorageLinkedService が表す Azure Storage 内の BLOB コンテナー (adftutorial) のルート フォルダーにあります。 fileName の値を指定しなかった場合やこれをスキップした場合、入力フォルダー内のすべての BLOB のデータがターゲットにコピーされます。 このチュートリアルでは、fileName の値を指定します。 

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
Azure SQL Database のリンクされたサービスは、Data Factory サービスが実行時に Azure SQL データベースへの接続に使用する接続文字列を指定します。 この手順で作成する出力 SQL テーブル データセット (OututDataset) は、Blob Storage のデータのコピー先となるデータベース内のテーブルを指定します。

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
この手順では、**AzureBlobInput** を入力、**AzureSqlOutput** を出力として使用する**コピー アクティビティ**が含まれたパイプラインを作成します。

現在、スケジュールは出力データセットによって開始されます。 このチュートリアルでは、1 時間ごとにスライスを生成するように出力データセットを構成します。 パイプラインの開始時刻と終了時刻の差は 1 日 (24 時間) です。 したがって、24 個の出力データセットのスライスがパイプラインによって生成されます。 

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

> [!IMPORTANT] 
> 次のコマンドで指定される開始時刻と終了時刻が、パイプラインの開始時刻と終了時刻に一致するようにしてください。 

```PowerShell
$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=2017-05-11T00%3a00%3a00.0000000Z"&"end=2017-05-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};
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

**準備完了**状態または**失敗**状態のスライスが見つかるまで、順番に Invoke-Command を実行します。 スライスが Ready 状態のときは、出力データ用の Azure SQL データベース内の **emp** テーブルを確認します。 

スライスごとに、ソース ファイルのデータの 2 つの行が、Azure SQL データベースの emp テーブルにコピーされます。 そのため、すべてのスライスが正常に処理されると (Ready 状態になると)、emp テーブルに 24 個の新しいレコードが表示されます。 

## <a name="summary"></a>まとめ
このチュートリアルでは、REST API を使用して Azure データ ファクトリを作成し、Azure BLOB から Azure SQL データベースにデータをコピーしました。 以下は、このチュートリアルで実行した手順の概要です。  

1. Azure **データ ファクトリ**を作成しました。
2. 次の **リンクされたサービス**を作成しました。
   1. 入力データを保持する Azure ストレージ アカウントをリンクするための、Azure Storage のリンクされたサービス。     
   2. 出力データを保持する Azure SQL データベースをリンクするための、Azure SQL のリンクされたサービス。 
3. パイプラインの入力データと出力データを記述する **データセット**を作成しました。
4. ソースとして BlobSource、シンクとして SqlSink を持つコピー アクティビティを含む **パイプライン** を作成しました。 

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure Blob Storage をコピー操作のソース データ ストア、Azure SQL データベースをターゲット データ ストアとして使用しました。 次の表は、コピー アクティビティによってソースおよびターゲットとしてサポートされているデータ ストアの一覧です。 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

データ ストアにデータをコピーしたり、データ ストアからデータをコピーしたりする方法を確認するには、表のデータ ストアのリンクをクリックしてください。
