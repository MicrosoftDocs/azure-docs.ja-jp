---
title: 初めてのデータ ファクトリの作成 (REST) | Microsoft Docs
description: このチュートリアルでは、Data Factory REST API を使用して、サンプルの Azure Data Factory パイプラインを作成します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 7e0a2465-2d85-4143-a4bb-42e03c273097
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: ab36d475052aa71427427e1362c74abd031fa414
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839451"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-data-factory-rest-api"></a>チュートリアル:Data Factory REST API を使用した初めての Azure データ ファクトリの作成
> [!div class="op_single_selector"]
> * [概要と前提条件](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager テンプレート](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
>
>


> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[Azure Data Factory を使用してデータ ファクトリを作成する方法のクイック スタート](../quickstart-create-data-factory-rest-api.md)に関するページを参照してください。

この記事では、Data Factory REST API を使用して最初の Azure データ ファクトリを作成します。 その他のツールや SDK を使用してチュートリアルを行うには、ドロップダウン リストでいずれかのオプションを選択します。

このチュートリアルのパイプラインには、1 つのアクティビティ (**HDInsight Hive アクティビティ**) が含まれます。 このアクティビティは、入力データを変換して出力データを生成する Hive スクリプトを Azure HDInsight クラスターで実行します。 このパイプラインは、指定した開始時刻と終了時刻の間で、月 1 回実行されるようスケジュールされています。

> [!NOTE]
> この記事では、すべての REST API を取り上げているわけではありません。 REST API に関する包括的なドキュメントについては、[Data Factory REST API リファレンス](/rest/api/datafactory/)を参照してください。
> 
> 1 つのパイプラインには複数のアクティビティを含めることができます。 また、1 つのアクティビティの出力データセットを別のアクティビティの入力データセットとして指定することで、2 つのアクティビティを連鎖させる (アクティビティを連続的に実行する) ことができます。 詳細については、[Data Factory のスケジュール設定と実行](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)に関するページを参照してください。


## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* 「 [チュートリアルの概要](data-factory-build-your-first-pipeline.md) 」に目を通し、 **前提条件** の手順を完了する必要があります。
* コンピューターに [Curl](https://curl.haxx.se/dlwiz/) をインストールします。 データ ファクトリを作成するには、CURL ツールと REST コマンドを使用します。
* [この記事](../../active-directory/develop/howto-create-service-principal-portal.md) の手順に従って、次の操作を行います。
  1. Azure Active Directory に、 **ADFGetStartedApp** という名前の Web アプリケーションを作成します。
  2. **クライアント ID** と**秘密キー**を取得します。
  3. **テナント ID**を取得します。
  4. **ADFGetStartedApp** アプリケーションを **Data Factory 共同作成者**ロールに割り当てます。
* [Azure PowerShell](/powershell/azure/overview)をインストールします。
* **PowerShell** を起動し、次のコマンドを実行します。 Azure PowerShell は、このチュートリアルが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。
  1. **Connect-AzAccount** を実行し、Azure portal へのサインインに使用するユーザー名とパスワードを入力します。
  2. **Get-AzSubscription** を実行して、このアカウントのサブスクリプションをすべて表示します。
  3. **Get-AzSubscription -SubscriptionName NameOfAzureSubscription | Set-AzContext** を実行して、使用するサブスクリプションを選択します。 **NameOfAzureSubscription** を自分の Azure サブスクリプションの名前で置き換えます。
* PowerShell で次のコマンドを実行して、 **ADFTutorialResourceGroup** という名前の Azure リソース グループを作成します。

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

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
    "name": "FirstDataFactoryREST",
    "location": "WestUS"
}
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> **accountname** と **accountkey** を Azure ストレージ アカウントの名前とキーに置き換えます。 ストレージ アクセス キーを取得する方法については、「[ストレージ アカウントの管理](../../storage/common/storage-account-manage.md#access-keys)」のストレージ アクセス キーを表示、コピー、再生成する方法に関する情報を参照してください。
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

### <a name="hdinsightondemandlinkedservicejson"></a>hdinsightondemandlinkedservice.json

```JSON
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

次の表に、このスニペットで使用される JSON プロパティの説明を示します。

| プロパティ | 説明 |
|:--- |:--- |
| clusterSize |HDInsight クラスターのサイズ。 |
| timeToLive |削除されるまでの HDInsight クラスターのアイドル時間を指定します。 |
| linkedServiceName |HDInsight によって生成されるログを保存するために使用されるストレージ アカウントを指定します。 |

以下の点に注意してください。

* Data Factory は、上記の JSON で **Linux ベース** の HDInsight クラスターを自動的に作成します。 詳細については、 [オンデマンド HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) に関するセクションを参照してください。
* オンデマンド HDInsight クラスターの代わりに、 **独自の HDInsight クラスター** を使用できます。 詳細については、 [HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) に関するセクションを参照してください。
* HDInsight クラスターは、JSON (**linkedServiceName**) で指定した BLOB ストレージに**既定のコンテナー**を作成します。 クラスターを削除しても、HDInsight はこのコンテナーを削除しません。 この動作は仕様です。 オンデマンド HDInsight のリンクされたサービスでは、既存のライブ クラスター (**timeToLive**) がある場合を除き、スライスが処理されるたびに HDInsight クラスターが作成され、処理が終了すると削除されます。

    処理されるスライスが多いほど、Azure BLOB ストレージ内のコンテナーも増えます。 ジョブのトラブルシューティングのためにコンテナーが必要ない場合、コンテナーを削除してストレージ コストを削減できます。 これらのコンテナーの名前は、"adf**データ ファクトリ名**-**リンクされたサービス名**-日時スタンプ" というパターンに従います。 Azure BLOB ストレージ内のコンテナーを削除するには、 [Microsoft ストレージ エクスプローラー](https://storageexplorer.com/) などのツールを使用します。

詳細については、 [オンデマンド HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) に関するセクションを参照してください。

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

この JSON では、**AzureBlobInput** という名前のデータセットを定義します。これは、パイプラインのアクティビティの入力データを表します。 さらに、**adfgetstarted** という BLOB コンテナーと **inputdata** というフォルダーに入力データが配置されるように指定します。

次の表に、このスニペットで使用される JSON プロパティの説明を示します。

| プロパティ | 説明 |
|:--- |:--- |
| type |データは Azure Blob Storage に存在するため、type プロパティを AzureBlob に設定しています。 |
| linkedServiceName |前に作成した StorageLinkedService を参照します。 |
| fileName |このプロパティは省略可能です。 このプロパティを省略した場合は、folderPath のすべてのファイルが取得されます。 このチュートリアルでは、input.log のみが処理されます。 |
| type |ログ ファイルはテキスト形式です。そのため、TextFormat を使用します。 |
| columnDelimiter |ログ ファイル内の列はコンマ (,) で区切られています。 |
| frequency/interval |frequency を Month に設定し、interval を 1 に設定しています。そのため、入力スライスは 1 か月ごとになります。 |
| external |Data Factory サービスによって入力データが生成されない場合は、このプロパティを true に設定します。 |

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "adfgetstarted/partitioneddata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        }
    }
}
```

この JSON では、**AzureBlobOutput** という名前のデータセットを定義します。これは、パイプラインのアクティビティの出力データを表します。 さらに、**adfgetstarted** という BLOB コンテナーと **partitioneddata** というフォルダーに結果が保存されるように指定します。 **availability** セクションでは、出力データセットが 1 か月ごとに生成されることを指定します。

### <a name="pipelinejson"></a>pipeline.json
> [!IMPORTANT]
> **storageaccountname** を Azure ストレージ アカウントの名前に置き換えます。
>
>

```JSON
{
    "name": "MyFirstPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                "scriptLinkedService": "AzureStorageLinkedService",
                "defines": {
                    "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                    "partitionedtable": "wasb://adfgetstarted@<storageaccountname>t.blob.core.windows.net/partitioneddata"
                }
            },
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "policy": {
                "concurrency": 1,
                "retry": 3
            },
            "scheduler": {
                "frequency": "Month",
                "interval": 1
            },
            "name": "RunSampleHiveActivity",
            "linkedServiceName": "HDInsightOnDemandLinkedService"
        }],
        "start": "2017-07-10T00:00:00Z",
        "end": "2017-07-11T00:00:00Z",
        "isPaused": false
    }
}
```

この JSON スニペットでは、Hive を使用して HDInsight クラスターのデータを処理する 1 つのアクティビティで構成されるパイプラインを作成します。

Hive スクリプト ファイル **partitionweblogs.hql** は、(**StorageLinkedService** という scriptLinkedService によって指定された) Azure ストレージ アカウントと **adfgetstarted** コンテナーの **script** フォルダーに格納されます。

**defines** セクションでは、Hive 構成値 (例: ${hiveconf:inputtable}、${hiveconf:partitionedtable}) として Hive スクリプトに渡される実行時設定を指定します。

パイプラインの **start** および **end** プロパティでは、パイプラインのアクティブな期間を指定します。

アクティビティ JSON では、**linkedServiceName** に指定されたコンピューティング **HDInsightOnDemandLinkedService** で Hive スクリプトが実行されるように指定します。

> [!NOTE]
> 前の例で使用されている JSON プロパティの詳細については、「[Azure Data Factory のパイプラインとアクティビティ](data-factory-create-pipelines.md)」の「パイプライン JSON」を参照してください。
>
>

## <a name="set-global-variables"></a>グローバル変数の設定
Azure PowerShell で、値を独自の値に置き換えて、以下のコマンドを実行します。

> [!IMPORTANT]
> クライアント ID、クライアント シークレット、テナント ID、サブスクリプション ID を取得する手順については、「 [前提条件](#prerequisites) 」セクションを参照してください。
>
>

```powershell
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
$adf = "FirstDataFactoryREST"
```


## <a name="authenticate-with-aad"></a>AAD での認証

```powershell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken)
```


## <a name="create-data-factory"></a>データ ファクトリの作成
この手順では、 **FirstDataFactoryREST**という名前の Azure Dデータ ファクトリを作成します。 データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 パイプラインには、1 つまたは複数のアクティビティを含めることができます。 たとえば、コピー元からコピー先のデータ ストアにデータをコピーするコピー アクティビティや、Hive スクリプトを実行してデータを変換する HDInsight Hive アクティビティなどを含めることができます。 以下のコマンドを実行して、データ ファクトリを作成します。

1. コマンドを **cmd**という名前の変数に割り当てます。

    ここで指定するデータ ファクトリの名前 (ADFCopyTutorialDF) が、 **datafactory.json**で指定した名前と一致することを確認します。

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
    ```
2. **Invoke-Command**を使用して、コマンドを実行します。

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 結果を表示します。 データ ファクトリが正常に作成された場合は、**results** にデータ ファクトリの JSON が表示されます。そうでない場合は、エラー メッセージが表示されます。

    ```powershell
    Write-Host $results
    ```

以下の点に注意してください。

* Azure Data Factory の名前はグローバルに一意にする必要があります。 results に"**データ ファクトリ名 "FirstDataFactoryREST" は使用できません**" というエラーが表示される場合は、次の手順に従います。
  1. **datafactory.json** ファイルで名前を変更します (例: yournameFirstDataFactoryREST)。 Data Factory アーティファクトの名前付け規則については、「 [Azure Data Factory - 名前付け規則](data-factory-naming-rules.md) 」を参照してください。
  2. **$cmd** 変数に値が割り当てられる最初のコマンドで、FirstDataFactoryREST を新しい名前に置き換え、コマンドを実行します。
  3. REST API を呼び出す次の 2 つのコマンドを実行して、データ ファクトリを作成し、操作の結果を出力します。
* Data Factory インスタンスを作成するには、Azure サブスクリプションの共同作成者または管理者である必要があります。
* データ ファクトリの名前は今後、DNS 名として登録される可能性があるため、一般ユーザーに表示される場合があります。
* エラー "**This subscription is not registered to use namespace Microsoft.DataFactory (このサブスクリプションは、名前空間 Microsoft.DataFactory を使用するように登録されていません)** " が表示された場合は、以下のいずれかの操作を行ってから、もう一度発行してみます。

  * Azure PowerShell で次のコマンドを実行して、Data Factory プロバイダーを登録します。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

      Data Factory プロバイダーが登録されたことを確認するには、次のコマンドを実行します。
    ```powershell
    Get-AzResourceProvider
    ```
  * Azure サブスクリプションを使用して [Azure ポータル](https://portal.azure.com) にログインし、[Data Factory] ブレードに移動するか、Azure ポータルでデータ ファクトリを作成します。 この操作によって、プロバイダーが自動的に登録されます。

パイプラインを作成する前に、まず、Data Factory エンティティをいくつか作成する必要があります。 まずはデータ ストアやコンピューティングを自分のデータ ストアにリンクするリンクされたサービスを作成し、リンクされたデータ ストア内のデータを表す入力データセットと出力データセットを定義します。

## <a name="create-linked-services"></a>リンクされたサービスを作成します
この手順では、Azure ストレージ アカウントとオンデマンド Azure HDInsight クラスターをデータ ファクトリにリンクします。 Azure ストレージ アカウントには、このサンプルのパイプラインの入力データと出力データが保持されます。 HDInsight のリンクされたサービスは、このサンプルのパイプラインのアクティビティに指定された Hive スクリプトを実行するために使用されます。

### <a name="create-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する
この手順では、Azure ストレージ アカウントをデータ ファクトリにリンクします。 このチュートリアルでは、同じ Azure ストレージ アカウントを使用して、入力/出力データと HQL スクリプト ファイルを格納します。

1. コマンドを **cmd**という名前の変数に割り当てます。

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. **Invoke-Command**を使用して、コマンドを実行します。

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 結果を表示します。 リンクされたサービスが正常に作成された場合は、**results** に、リンクされたサービスの JSON が表示されます。そうでない場合は、エラー メッセージが表示されます。

    ```powershell
    Write-Host $results
    ```

### <a name="create-azure-hdinsight-linked-service"></a>Azure HDInsight のリンクされたサービスを作成する
この手順では、オンデマンド HDInsight クラスターをデータ ファクトリにリンクします。 HDInsight クラスターは、実行時に自動的に作成されます。また、処理が終わり、アイドル状態が一定時間続くと削除されます。 オンデマンド HDInsight クラスターの代わりに、独自の HDInsight クラスターを使用できます。 コピー アクティビティでサポートされているすべてのソースとシンクについては、 [Compute Linked Services](data-factory-compute-linked-services.md) に関するセクションを参照してください。

1. コマンドを **cmd**という名前の変数に割り当てます。

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
    ```
2. **Invoke-Command**を使用して、コマンドを実行します。

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 結果を表示します。 リンクされたサービスが正常に作成された場合は、**results** に、リンクされたサービスの JSON が表示されます。そうでない場合は、エラー メッセージが表示されます。

    ```powershell
    Write-Host $results
    ```

## <a name="create-datasets"></a>データセットを作成する
この手順では、Hive 処理の入力データと出力データを表すデータセットを作成します。 これらのデータセットは、このチュートリアルで前に作成した **StorageLinkedService** を参照します。 このリンクされたサービスは Azure ストレージ アカウントを指し、データセットは入力データと出力データを保持するストレージのコンテナー、フォルダー、ファイル名を指定します。

### <a name="create-input-dataset"></a>入力データセットの作成
この手順では、Azure BLOB ストレージに格納される入力データを表す入力データセットを作成します。

1. コマンドを **cmd**という名前の変数に割り当てます。

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. **Invoke-Command**を使用して、コマンドを実行します。

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 結果を表示します。 データセットが正常に作成された場合は、**results** にデータセットの JSON が表示されます。そうでない場合は、エラー メッセージが表示されます。

    ```powershell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>出力データセットの作成
この手順では、Azure BLOB ストレージに格納される出力データを表す出力データセットを作成します。

1. コマンドを **cmd**という名前の変数に割り当てます。

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
    ```
2. **Invoke-Command**を使用して、コマンドを実行します。

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 結果を表示します。 データセットが正常に作成された場合は、**results** にデータセットの JSON が表示されます。そうでない場合は、エラー メッセージが表示されます。

    ```powershell
    Write-Host $results
    ```

## <a name="create-pipeline"></a>パイプラインの作成
この手順では、 **HDInsightHive** アクティビティを含む最初のパイプラインを作成します。 入力スライスは 1 か月ごと (frequency:Month、interval:1) に使用可能であり、出力スライスは 1 か月ごとに生成されるため、アクティビティの scheduler プロパティも 1 か月ごとに設定します。 出力データセットとアクティビティの scheduler の設定は一致している必要があります。 現在、スケジュールは出力データセットによって開始されるため、アクティビティが出力を生成しない場合でも、出力データセットを作成する必要があります。 アクティビティが入力を受け取らない場合は、入力データセットの作成を省略できます。

Azure Blob Storage の **adfgetstarted/inputdata** フォルダーに **input.log** ファイルがあることを確認し、次のコマンドを実行してパイプラインをデプロイします。 **start** と **end** が過去の日時に設定され、**isPaused** が false に設定されているため、パイプライン (パイプラインのアクティビティ) はデプロイするとすぐに実行されます。

1. コマンドを **cmd**という名前の変数に割り当てます。

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. **Invoke-Command**を使用して、コマンドを実行します。

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 結果を表示します。 データセットが正常に作成された場合は、**results** にデータセットの JSON が表示されます。そうでない場合は、エラー メッセージが表示されます。

    ```powershell
    Write-Host $results
    ```
4. これで、Azure PowerShell を使用して最初のパイプラインを作成できました。

## <a name="monitor-pipeline"></a>パイプラインを監視する
この手順では、Data Factory REST API を使用して、パイプラインによって生成されるスライスを監視します。

```powershell
$ds ="AzureBlobOutput"

$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

$results2 = Invoke-Command -scriptblock $cmd;

IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

> [!IMPORTANT]
> オンデマンド HDInsight クラスターの作成には通常しばらく時間がかかります (約 20 分)。 そのため、パイプラインによるスライスの処理に **約 30 分** かかると想定してください。
>
>

**準備完了**状態または**失敗**状態のスライスが見つかるまで、順番に Invoke-Command を実行します。 スライスが準備完了状態になったら、Blob Storage の **adfgetstarted** コンテナーの **partitioneddata** フォルダーで出力データを調べます。  オンデマンド HDInsight クラスターの作成には、通常はしばらく時間がかかります。

![output data](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [!IMPORTANT]
> 入力ファイルは、スライスが正常に処理された時点で削除されます。 そのためスライスを取得したり、このチュートリアルをもう一度行ったりする場合は、adfgetstarted コンテナーの inputdata フォルダーに入力ファイル (input.log) をアップロードしてください。
>
>

Azure ポータルを使用して、スライスを監視し、問題のトラブルシューティングを行うこともできます。 詳細については、 [Azure ポータルを使用したパイプラインの監視](data-factory-monitor-manage-pipelines.md) に関する記事を参照してください。

## <a name="summary"></a>まとめ
このチュートリアルでは、HDInsight Hadoop クラスター上で Hive スクリプトを実行してデータを処理するために、Azure データ ファクトリを作成しました。 以下の手順を実行するために、Azure ポータルで Data Factory エディターを使用しました。

1. Azure **データ ファクトリ**を作成しました。
2. 次の 2 つの **リンクされたサービス**を作成しました。
   1. **Azure Storage** のリンクされたサービス。
   2. **Azure HDInsight** オンデマンドのリンクされたサービス。 Azure Data Factory は、入力データを処理し、出力データを生成するために、HDInsight Hadoop クラスターをジャストインタイムで作成します。
3. パイプラインの HDInsight Hive アクティビティ向けの入出力データを記述する 2 つの **データセット**を作成しました。
4. **HDInsight Hive** アクティビティを持つ**パイプライン**を作成しました。

## <a name="next-steps"></a>次の手順
この記事では、オンデマンド Azure HDInsight クラスターで Hive スクリプトを実行する変換アクティビティ (HDInsight アクティビティ) を含むパイプラインを作成しました。 コピー アクティビティを使用して Azure BLOB から Azure SQL にデータをコピーする方法については、[Azure BLOB から Azure SQL にデータをコピーする方法のチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。

## <a name="see-also"></a>関連項目
| トピック | 説明 |
|:--- |:--- |
| [Data Factory REST API リファレンス](/rest/api/datafactory/) |Data Factory コマンドレットに関する包括的なドキュメントです。 |
| [パイプライン](data-factory-create-pipelines.md) |この記事では、Azure Data Factory のパイプラインとアクティビティの概要、およびそれらを利用して実際のシナリオやビジネスのためにエンド ツー エンドのデータ主導ワークフローを作成する方法を説明します。 |
| [データセット](data-factory-create-datasets.md) |この記事では、Azure Data Factory のデータセットについて説明します。 |
| [スケジュールと実行](data-factory-scheduling-and-execution.md) |この記事では、Azure Data Factory アプリケーション モデルのスケジュール設定と実行の側面について説明します。 |
| [監視アプリを使用したパイプラインの監視と管理に関する記事](data-factory-monitor-manage-app.md) |この記事では、監視と管理アプリを使用してパイプラインを監視、管理、デバッグする方法について説明します。 |
