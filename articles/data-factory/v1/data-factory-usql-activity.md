---
title: U-SQL スクリプトを使用したデータ変換 - Azure
description: Azure Data Lake Analytics コンピューティング サービスで、U-SQL スクリプトを実行してデータを処理または変換する方法について説明します。
services: data-factory
documentationcenter: ''
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: c6d3510dfdd02bf2eb07d656c706c44d895c582d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927902"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Azure Data Lake Analytics で U-SQL スクリプトを実行してデータを変換 
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください。"]
> * [Version 1](data-factory-usql-activity.md)
> * [バージョン 2 (最新バージョン)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 の U-SQL アクティビティ](../transform-data-using-data-lake-analytics.md)に関するページを参照してください。

Azure Data Factory のパイプラインは、リンクされたコンピューティング サービスを使用して、リンクされたストレージ サービス内のデータを処理します。 パイプラインは、一連のアクティビティで構成されます。各アクティビティは、特定の処理操作を実行します。 この記事では、**Azure Data Lake Analytics** コンピューティング リンク サービスで **U-SQL** スクリプトを実行する **Data Lake Analytics U-SQL アクティビティ**について説明します。 

Data Lake Analytics U-SQL アクティビティでパイプラインを作成する前に、Azure Data Lake Analytics アカウントを作成します。 Azure Data Lake Analytics の詳細については、 [Azure Data Lake Analytics の使用開始](../../data-lake-analytics/data-lake-analytics-get-started-portal.md)に関するページをご覧ください。

Data Factory、リンクされたサービス、データセット、およびパイプラインを作成する詳細な手順については、 [最初のパイプラインを作成するチュートリアル](data-factory-build-your-first-pipeline.md) に関するページを確認してください。 Data Factory エディター、Visual Studio、または Azure PowerShell と JSON のスニペットを使用して、Data Factory エンティティを作成できます。

## <a name="supported-authentication-types"></a>サポートされている認証の種類
U-SQL アクティビティでは、Data Lake Analytics に対して次の種類の認証をサポートしています。
* サービス プリンシパルの認証
* ユーザー資格情報 (OAuth) 認証 

特に、スケジュールされた U-SQL の実行の場合は、サービス プリンシパル認証を使うことをお勧めします。 ユーザー資格情報認証では、トークンの有効期限の動作が発生する可能性があります。 構成の詳細については、「[リンクされたサービスのプロパティ](#azure-data-lake-analytics-linked-service)」セクションを参照してください。

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics リンク サービス
**Azure Data Lake Analytics** リンク サービスを作成して、Azure Data Lake Analytics コンピューティング サービスを Azure Data Factory にリンクします。 パイプラインの Data Lake Analytics U-SQL アクティビティは、このリンク サービスを参照します。 

次の表では、JSON 定義で使用される一般的なプロパティを説明しています。 サービス プリンシパル認証とユーザー資格情報認証のいずれかをさらに選ぶことができます。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| **type** |type プロパティは次の値に設定されます。**AzureDataLakeAnalytics**。 |はい |
| **accountName** |Azure Data Lake Analytics アカウント名。 |はい |
| **dataLakeAnalyticsUri** |Azure Data Lake Analytics URI。 |いいえ |
| **subscriptionId** |Azure サブスクリプション ID |いいえ (指定されていない場合、Data Factory のサブスクリプションが使用されます)。 |
| **resourceGroupName** |Azure リソース グループ名 |いいえ (指定されていない場合は Data Factory のリソース グループが使用されます)。 |

### <a name="service-principal-authentication-recommended"></a>サービス プリンシパル認証 (推奨)
サービス プリンシパル認証を使うには、Azure Active Directory (Azure AD) でアプリケーション エンティティを登録して、Data Lake Store へのアクセス権を付与します。 詳細な手順については、「[サービス間認証](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md)」を参照してください。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。
* アプリケーション ID
* アプリケーション キー 
* テナント ID

次のプロパティを指定して、サービス プリンシパル認証を使います。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| **servicePrincipalId** | アプリケーションのクライアント ID を取得します。 | はい |
| **servicePrincipalKey** | アプリケーションのキーを取得します。 | はい |
| **tenant** | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure Portal の右上隅をマウスでポイントすることにより取得できます。 | はい |

**例:サービス プリンシパルの認証**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>ユーザー資格情報認証
また、次のプロパティを指定することで、Data Lake Analytics のユーザー資格情報認証を使用することもできます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| **authorization** | Data Factory エディターで **[承認する]** をクリックし、資格情報を入力すると、自動生成された承認 URL がこのプロパティに割り当てられます。 | はい |
| **sessionId** | OAuth 承認セッションからの OAuth セッション ID です。 各セッション ID は一意であり、1 回のみ使うことができます。 Data Factory エディターを使うと、この設定が自動的に生成されます。 | はい |

**例:ユーザー資格情報認証**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>トークンの有効期限
**[認証]** ボタンを使用して生成した認証コードは、いずれ有効期限が切れます。 さまざまな種類のユーザー アカウントの有効期限については、次の表を参照してください。 認証**トークンの有効期限が切れる**と、次のエラー メッセージが表示される場合があります。資格情報の操作エラー: invalid_grant - AADSTS70002:Error validating credentials. (資格情報の検証中にエラーが発生しました。) AADSTS70008:指定されたアクセス権の付与は期限が切れているか、失効しています。 トレース ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 相関 ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 タイムスタンプ:2015-12-15 21:09:31Z

| ユーザー タイプ | 有効期限 |
|:--- |:--- |
| Azure Active Directory で管理されていないユーザー アカウント (@hotmail.com、@live.com など)。 |12 時間 |
| Azure Active Directory (AAD) で管理されているユーザー アカウント |スライスの最後の実行から 14 日後。 <br/><br/>OAuth ベースのリンクされたサービスに基づいて、少なくとも 14 日間に 1 回スライスが実行する場合、90 日です。 |

このエラーを回避または解決するには、**トークンの有効期限が切れた**ときに、 **[承認する]** ボタンを使用して再承認し、リンクされたサービスを再デプロイします。 次のようにコードを使用して、**sessionId** と **authorization** のプロパティ値をプログラムで生成することもできます。

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

コードで使用する Data Factory クラスの詳細については、「[AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)」、「[AzureDataLakeAnalyticsLinkedService クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)」、および「[AuthorizationSessionGetResponse クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)」をご覧ください。 参照を、WindowsFormsWebAuthenticationDialog クラスの Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll に追加します。 

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL アクティビティ
次の JSON のスニペットでは、Data Lake Analytics U-SQL アクティビティを使用してパイプラインを定義します。 このアクティビティ定義には、先ほど作成した Azure Data Lake Analytics リンク サービスへの参照が含まれています。   

```json
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

| プロパティ            | 説明                              | 必須                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| type                | type プロパティは、 **DataLakeAnalyticsU-SQL**に設定する必要があります。 | はい                                      |
| linkedServiceName   | Data Factory のリンクされたサービスとして登録されている Azure Data Lake Analytics への参照 | はい                                      |
| scriptPath          | U-SQL スクリプトを含むフォルダーのパス。 ファイル名は大文字と小文字が区別されます。 | いいえ (スクリプトを使用する場合)                   |
| scriptLinkedService | Data Factory に対するスクリプトを含むストレージをリンクするリンク サービス | いいえ (スクリプトを使用する場合)                   |
| script              | scriptPath と scriptLinkedService を指定する代わりに、インライン スクリプトを指定します。 たとえば、「 `"script": "CREATE DATABASE test"`」のように入力します。 | いいえ (scriptPath と scriptLinkedService を使用する場合) |
| degreeOfParallelism | ジョブを実行するために同時に使用される最大ノード数。 | いいえ                                       |
| priority            | キューされているすべてのジョブのうち、先に実行するジョブを決定します。 数値が小さいほど、優先度は高くなります。 | いいえ                                       |
| parameters          | U-SQL スクリプトのパラメーター          | いいえ                                       |
| runtimeVersion      | 使用する U-SQL エンジンのランタイム バージョン。 | いいえ                                       |
| compilationMode     | <p>U-SQL のコンパイル モード。 次のいずれかの値を指定する必要があります。</p> <ul><li>**Semantic:** セマンティック チェックと必要なサニティ チェックのみを実行します。</li><li>**Full:** 構文チェック、最適化、コード生成などを含めた完全コンパイルを実行します。</li><li>**SingleBox:** TargetType を SingleBox に設定して完全コンパイルを実行します。</li></ul><p>このプロパティの値を指定しない場合、サーバーが最適なコンパイル モードを決定します。 </p> | いいえ                                       |

スクリプト定義については、 [SearchLogProcessing.txt のスクリプト定義](#sample-u-sql-script) をご覧ください。 

## <a name="sample-input-and-output-datasets"></a>入力データセットと出力データセットの例
### <a name="input-dataset"></a>入力データセット
この例では、入力データは Azure Data Lake Store 内 (datalake/input フォルダーの SearchLog.tsv ファイル) にあります。 

```json
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

```json
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

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
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

U-SQL スクリプトの **\@in** パラメーターと **\@out** パラメーターの値は、"parameters" セクションを使用して ADF によって動的に渡されます。 パイプライン定義の "parameters" セクションを参照してください。

Azure Data Lake Analytics サービスで実行されるジョブのパイプライン定義で、他のプロパティ (degreeOfParallelism など) や優先度も指定できます。

## <a name="dynamic-parameters"></a>動的パラメーター
パイプライン定義のサンプルでは、in パラメーターと out パラメーターにハード コーディングされた値が割り当てられています。 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

代わりに、動的パラメーターを使用することもできます。 例: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

この場合、入力ファイルは引き続き /datalake/input フォルダーから取得され、出力ファイルは /datalake/output フォルダーに生成されます。 ファイル名はスライス開始時刻に基づいて動的に指定されます。  


