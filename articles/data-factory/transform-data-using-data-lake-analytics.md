---
title: "U-SQL スクリプトを使用したデータ変換 - Azure | Microsoft Docs"
description: "Azure Data Lake Analytics コンピューティング サービスで、U-SQL スクリプトを実行してデータを処理または変換する方法について説明します。"
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: shengc
ms.openlocfilehash: 5e54464ceabfe1fea2af80d63e538bea6a0a50a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Azure Data Lake Analytics で U-SQL スクリプトを実行してデータを変換 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-usql-activity.md)
> * [バージョン 2 - プレビュー](transform-data-using-data-lake-analytics.md)

Azure Data Factory のパイプラインは、リンクされたコンピューティング サービスを使用して、リンクされたストレージ サービス内のデータを処理します。 パイプラインは、一連のアクティビティで構成されます。各アクティビティは、特定の処理操作を実行します。 この記事では、**Azure Data Lake Analytics** コンピューティング リンク サービスで **U-SQL** スクリプトを実行する **Data Lake Analytics U-SQL アクティビティ**について説明します。 

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 の USQL アクティビティ](v1/data-factory-usql-activity.md)に関する記事をご覧ください。

Data Lake Analytics U-SQL アクティビティでパイプラインを作成する前に、Azure Data Lake Analytics アカウントを作成します。 Azure Data Lake Analytics の詳細については、 [Azure Data Lake Analytics の使用開始](../data-lake-analytics/data-lake-analytics-get-started-portal.md)に関するページをご覧ください。


## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics リンク サービス
**Azure Data Lake Analytics** リンク サービスを作成して、Azure Data Lake Analytics コンピューティング サービスを Azure Data Factory にリンクします。 パイプラインの Data Lake Analytics U-SQL アクティビティは、このリンク サービスを参照します。 

次の表では、JSON 定義で使用される一般的なプロパティを説明しています。 

| プロパティ                 | 説明                              | 必須                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | type プロパティは **AzureDataLakeAnalytics**に設定する必要があります。 | あり                                      |
| **accountName**          | Azure Data Lake Analytics アカウント名。  | あり                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics URI。           | いいえ                                       |
| **subscriptionId**       | Azure サブスクリプション ID                    | いいえ (指定されていない場合、Data Factory のサブスクリプションが使用されます)。 |
| **resourceGroupName**    | Azure リソース グループ名                | いいえ (指定されていない場合は Data Factory のリソース グループが使用されます)。 |

### <a name="service-principal-authentication"></a>サービス プリンシパルの認証
Azure Data Lake Analytics のリンクされたサービスには、Azure Data Lake Analytics サービスに接続するためのサービス プリンシパル認証が必要です。 サービス プリンシパル認証を使うには、Azure Active Directory (Azure AD) でアプリケーション エンティティを登録し、Data Lake Analytics およびそれを使用する Data Lake Store の両方へのアクセス権を付与します。 詳細な手順については、「[サービス間認証](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)」を参照してください。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。
* アプリケーション ID
* アプリケーション キー 
* テナント ID

次のプロパティを指定して、サービス プリンシパル認証を使います。

| プロパティ                | 説明                              | 必須 |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | アプリケーションのクライアント ID を取得します。     | あり      |
| **servicePrincipalKey** | アプリケーションのキーを取得します。           | あり      |
| **tenant**              | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure Portal の右上隅をマウスでポイントすることにより取得できます。 | あり      |

**例: サービス プリンシパル認証**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL アクティビティ
次の JSON のスニペットでは、Data Lake Analytics U-SQL アクティビティを使用してパイプラインを定義します。 このアクティビティ定義には、先ほど作成した Azure Data Lake Analytics リンク サービスへの参照が含まれています。 Data Lake Analytics U-SQL スクリプトを実行するために、指定したスクリプトがData Factory によって Data Lake Analytics に送信され、フェッチおよび出力する必須の入力と出力が Data Lake Analytics のスクリプトで定義されます。 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "AzureDataLakeAnalyticsLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "LinkedServiceofAzureBlobStorageforscriptPath",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

次の表は、このアクティビティに固有のプロパティの名前と説明です。 

| プロパティ            | 説明                              | 必須 |
| :------------------ | :--------------------------------------- | :------- |
| name                | パイプラインのアクティビティの名前     | あり      |
| 説明         | アクティビティの動作を説明するテキスト。  | いいえ       |
| type                | Data Lake Analytics U-SQL アクティビティの場合、アクティビティの種類は **DataLakeAnalyticsU-SQL** です。 | あり      |
| 既定のコンテナー   | Azure Data Lake Analytics にリンクされたサービス。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。  |あり       |
| scriptPath          | U-SQL スクリプトを含むフォルダーのパス。 ファイル名は大文字と小文字が区別されます。 | あり      |
| scriptLinkedService | Data Factory に対するスクリプトを含むストレージをリンクするリンク サービス | あり      |
| degreeOfParallelism | ジョブを実行するために同時に使用される最大ノード数。 | なし       |
| priority            | キューされているすべてのジョブのうち、先に実行するジョブを決定します。 数値が小さいほど、優先度は高くなります。 | なし       |
| parameters          | U-SQL スクリプトのパラメーター          | いいえ       |
| runtimeVersion      | 使用する U-SQL エンジンのランタイム バージョン。 | いいえ       |
| compilationMode     | <p>U-SQL のコンパイル モード。 次のいずれかの値を指定する必要があります: **Semantic:** セマンティクスの検査と必要なサニティ チェックのみを実行します。**Full:** 構文チェック、最適化、コードの生成などを含む完全コンパイルを実行します。**SingleBox:** TargetType を SingleBox に設定して完全コンパイルを実行します。 このプロパティの値を指定しない場合、サーバーが最適なコンパイル モードを決定します。 | いいえ |

Data Factory によって送信されるスクリプト定義については、[SearchLogProcessing.txt のスクリプト定義](#sample-u-sql-script)をご覧ください。 

## <a name="sample-u-sql-script"></a>U-SQL スクリプトのサンプル

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
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

前述のスクリプト例では、スクリプトの入力と出力は **@in** パラメーターと **@out** パラメーターで定義されます。 U-SQL スクリプトの **@in** パラメーターと **@out** パラメーターの値は、"parameters" セクションを使用して Data Factory によって動的に渡されます。 

Azure Data Lake Analytics サービスで実行されるジョブのパイプライン定義で、他のプロパティ (degreeOfParallelism など) や優先度も指定できます。

## <a name="dynamic-parameters"></a>動的パラメーター
パイプライン定義のサンプルでは、in パラメーターと out パラメーターにハード コーディングされた値が割り当てられています。 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

代わりに、動的パラメーターを使用することもできます。 次に例を示します。 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

この場合、入力ファイルは引き続き /datalake/input フォルダーから取得され、出力ファイルは /datalake/output フォルダーに生成されます。 ファイル名はスライス開始時刻に基づいて動的に指定されます。  

## <a name="next-steps"></a>次のステップ
別の手段でデータを変換する方法を説明している次の記事を参照してください。 

* [Hive アクティビティ](transform-data-using-hadoop-hive.md)
* [Pig アクティビティ](transform-data-using-hadoop-pig.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming アクティビティ](transform-data-using-hadoop-streaming.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [.NET カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning バッチ実行アクティビティ](transform-data-using-machine-learning.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)
