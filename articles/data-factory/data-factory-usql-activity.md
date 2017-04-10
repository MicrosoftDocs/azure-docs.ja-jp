---
title: "U-SQL スクリプトを使用したデータ変換 - Azure | Microsoft Docs"
description: "Azure Data Lake Analytics コンピューティング サービスで、U-SQL スクリプトを実行してデータを処理または変換する方法について説明します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: b41d906d6948f0f9e3cdb38b4a478b39f55ce219
ms.lasthandoff: 03/14/2017


---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Azure Data Lake Analytics で U-SQL スクリプトを実行してデータを変換 
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive アクティビティ](data-factory-hive-activity.md) 
> * [Pig アクティビティ](data-factory-pig-activity.md)
> * [MapReduce アクティビティ](data-factory-map-reduce.md)
> * [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)
> * [Spark アクティビティ](data-factory-spark.md)
> * [Machine Learning バッチ実行アクティビティ](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning 更新リソース アクティビティ](data-factory-azure-ml-update-resource-activity.md)
> * [ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL アクティビティ](data-factory-usql-activity.md)
> * [.NET カスタム アクティビティ](data-factory-use-custom-activities.md)

Azure Data Factory のパイプラインは、リンクされたコンピューティング サービスを使用して、リンクされたストレージ サービス内のデータを処理します。 パイプラインは、一連のアクティビティで構成されます。各アクティビティは、特定の処理操作を実行します。 この記事では、**Azure Data Lake Analytics** コンピューティング リンク サービスで **U-SQL** スクリプトを実行する **Data Lake Analytics U-SQL アクティビティ**について説明します。 

> [!NOTE]
> Data Lake Analytics U-SQL アクティビティでパイプラインを作成する前に、Azure Data Lake Analytics アカウントを作成します。 Azure Data Lake Analytics の詳細については、 [Azure Data Lake Analytics の使用開始](../data-lake-analytics/data-lake-analytics-get-started-portal.md)に関するページをご覧ください。
> 
> Data Factory、リンクされたサービス、データセット、およびパイプラインを作成する詳細な手順については、 [最初のパイプラインを作成するチュートリアル](data-factory-build-your-first-pipeline.md) に関するページを確認してください。 Data Factory エディター、Visual Studio、または Azure PowerShell と JSON のスニペットを使用して、Data Factory エンティティを作成できます。
> 
> 

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics リンク サービス
**Azure Data Lake Analytics** リンク サービスを作成して、Azure Data Lake Analytics コンピューティング サービスを Azure Data Factory にリンクします。 パイプラインの Data Lake Analytics U-SQL アクティビティは、このリンク サービスを参照します。 

次の例は、Azure Data Lake Analytics リンク サービスの JSON 定義です。 

```JSON
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

次の表は、JSON 定義で使用されるプロパティの説明です。 

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| 型 |type プロパティは **AzureDataLakeAnalytics**に設定する必要があります。 |はい |
| accountName |Azure Data Lake Analytics アカウント名。 |はい |
| dataLakeAnalyticsUri |Azure Data Lake Analytics URI。 |なし |
| authorization |Data Factory Editor で **[承認]** ボタンをクリックし、OAuth ログインを完了すると、承認コードが自動的に取得されます。 |はい |
| subscriptionId |Azure サブスクリプション ID |いいえ (指定されていない場合、Data Factory のサブスクリプションが使用されます)。 |
| resourceGroupName |Azure リソース グループ名 |いいえ (指定されていない場合は Data Factory のリソース グループが使用されます)。 |
| sessionId |OAuth 承認セッションのセッション ID です。 各セッション ID は一意であり、1 回のみ使用できます。 セッション ID は、Data Factory エディターで自動生成されます。 |はい |

**[認証]** ボタンを使用して生成した認証コードは、いずれ有効期限が切れます。 さまざまな種類のユーザー アカウントの有効期限については、次の表を参照してください。 認証**トークンの有効期限が切れる**と、次のエラー メッセージが表示される場合があります: 資格情報の操作エラー: invalid_grant - AADSTS70002: 資格情報の検証中にエラーが発生しました。 AADSTS70008: 指定されたアクセス権の付与は期限が切れているか、失効しています。 トレース ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 相関 ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 タイムスタンプ: 2015-12-15 21:09:31Z"

| ユーザー タイプ | 有効期限 |
|:--- |:--- |
| Azure Active Directory で管理されていないユーザー アカウント (@hotmail.com、@live.com など)。 |12 時間 |
| Azure Active Directory (AAD) で管理されているユーザー アカウント |スライスの最後の実行から&14; 日後。 <br/><br/>OAuth ベースのリンクされたサービスに基づいて、少なくとも 14 日間に 1 回スライスが実行する場合、90 日です。 |

このエラーを回避または解決するには、**トークンの有効期限が切れた**ときに、**[承認する]** ボタンを使用して再承認し、リンクされたサービスを再デプロイします。 次のセクションのコードを使用して、**sessionId** と **authorization** プロパティの値をプログラムで生成することもできます。 

### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>プログラムを使用して sessionId と authorization の値を生成するには

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

コードで使用する Data Factory クラスの詳細については、「[AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)」、「[AzureDataLakeAnalyticsLinkedService クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)」、および「[AuthorizationSessionGetResponse クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)」をご覧ください。 WindowsFormsWebAuthenticationDialog クラスの Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll に参照を追加します。 

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL アクティビティ
次の JSON のスニペットでは、Data Lake Analytics U-SQL アクティビティを使用してパイプラインを定義します。 このアクティビティ定義には、先ほど作成した Azure Data Lake Analytics リンク サービスへの参照が含まれています。   

```JSON
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

次の表は、このアクティビティに固有のプロパティの名前と説明です。 

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| 型 |type プロパティは、 **DataLakeAnalyticsU-SQL**に設定する必要があります。 |はい |
| scriptPath |U-SQL スクリプトを含むフォルダーのパス。 ファイル名は大文字と小文字が区別されます。 |いいえ (スクリプトを使用する場合) |
| scriptLinkedService |Data Factory に対するスクリプトを含むストレージをリンクするリンク サービス |いいえ (スクリプトを使用する場合) |
| script (スクリプト) |scriptPath と scriptLinkedService を指定する代わりに、インライン スクリプトを指定します。 例: "script": "CREATE DATABASE test" |いいえ (scriptPath と scriptLinkedService を使用する場合) |
| degreeOfParallelism |ジョブを実行するために同時に使用される最大ノード数。 |なし |
| priority |キューされているすべてのジョブのうち、先に実行するジョブを決定します。 数値が小さいほど、優先度は高くなります。 |なし |
| parameters |U-SQL スクリプトのパラメーター |なし |

スクリプト定義については、 [SearchLogProcessing.txt のスクリプト定義](#script-definition) をご覧ください。 

## <a name="sample-input-and-output-datasets"></a>入力データセットと出力データセットの例
### <a name="input-dataset"></a>入力データセット
この例では、入力データは Azure Data Lake Store 内 (datalake/input フォルダーの SearchLog.tsv ファイル) にあります。 

```JSON
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>出力データセット
この例では、U-SQL スクリプトで生成された出力データは、Azure Data Lake Store (datalake/output フォルダー) に格納されます。 

```JSON
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Data Lake Store のリンクされたサービスのサンプル
入力/出力データセットで使用される Azure Data Lake Store にリンクされたサービスの定義例を次に示します。 

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>"
        }
    }
}
```

JSON プロパティについては、 [Azure Data Lake Store との間のデータの移動](data-factory-azure-datalake-connector.md) に関する記事を参照してください。 

## <a name="sample-u-sql-script"></a>U-SQL スクリプトのサンプル

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

U-SQL スクリプトの **@in** パラメーターと **@out** パラメーターの値は、"parameters" セクションを使用して ADF によって動的に渡されます。 パイプライン定義の "parameters" セクションを参照してください。

Azure Data Lake Analytics サービスで実行されるジョブのパイプライン定義で、他のプロパティ (degreeOfParallelism など) や優先度も指定できます。

## <a name="dynamic-parameters"></a>動的パラメーター
パイプライン定義のサンプルでは、in パラメーターと out パラメーターにハード コーディングされた値が割り当てられています。 

```JSON
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

代わりに、動的パラメーターを使用することもできます。 次に例を示します。 

```JSON
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

この場合、入力ファイルは引き続き /datalake/input フォルダーから取得され、出力ファイルは /datalake/output フォルダーに生成されます。 ファイル名はスライス開始時刻に基づいて動的に指定されます。  


