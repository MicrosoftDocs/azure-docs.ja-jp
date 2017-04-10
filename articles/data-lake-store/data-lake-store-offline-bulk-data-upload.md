---
title: "オフライン メソッドを使用して大量のデータを Data Lake Store へアップロードする | Microsoft Docs"
description: "AdlCopy ツールを使用して Azure Storage BLOB のデータを Data Lake Store にコピーします"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: dae5491962b22453c517da35539ce09463d8802d
ms.lasthandoff: 03/27/2017


---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-store"></a>Azure Import/Export サービスを使用した Data Lake Store へのデータのオフライン コピー
この記事では、[Azure Import/Export サービス](../storage/storage-import-export-service.md)などのオフライン コピー メソッドを使用して、大きなデータセット (200 GB 超) を Azure Data Lake Store にコピーする方法について説明します。 具体的には、この記事の例では、ディスク上で 339,420,860,416 バイト、つまり約 319 GB のファイルを使用します。 このファイルに 319GB.tsv と名前を付けます。

Azure Import/Export サービスを使用すると、ハード ディスク ドライブを Azure データ センターに送付することで、大量のデータを Azure Blob Storage により安全に転送できます。

## <a name="prerequisites"></a>前提条件
開始する前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure ストレージ アカウント**。
* **Azure Data Lake Store アカウント**。 このアカウントを作成する手順については、「 [Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)

## <a name="preparing-the-data"></a>データを準備する
Import/Export サービスを使用する前に、転送するデータ ファイルを **200 GB 未満のコピーに**分割します。 これは、インポート ツールでは 200 GB を超えるファイルを扱えないためです。 このチュートリアルでは、それぞれ 100 GB のチャンクにファイルを分割します。 これは [Cygwin](https://cygwin.com/install.html) を使用して実行できます。 Cygwin では、Linux のコマンドがサポートされています。 この場合は、次のコマンドを使用します。

    split -b 100m 319GB.tsv

この split 操作により下記の名前のファイルが作成されます。

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>ディスクにデータを準備する
[Azure Import/Export サービスの使用](../storage/storage-import-export-service.md)に関する記事 (「**ドライブの準備**」セクションの下) にある手順に従って、ハード ドライブを準備します。 全体の手順を次に示します。

1. Azure Import/Export サービスでの使用要件を満たしているハード ディスクを調達します。
2. Azure ストレージ アカウントを指定します。データは、Azure データセンターへの発送後そのアカウントにコピーされます。
3. コマンドライン ユーティリティの一種である [Azure Import/Export ツール](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409)を使用します。 ツールの使用方法を示すサンプル スニペットを次に示します。

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    さらに複雑なスニペットについては、[Azure Import/Export サービスの使用](../storage/storage-import-export-service.md)に関するページを参照してください。
4. 上記のコマンドを実行すると、指定した場所にジャーナル ファイルが作成されます。 このジャーナル ファイルを使用して、[Azure クラシック ポータル](https://manage.windowsazure.com)でインポート ジョブを作成します。

## <a name="create-an-import-job"></a>インポート ジョブの作成
これで、[Azure Import/Export サービスの使用](../storage/storage-import-export-service.md)に関する記事 (「**インポート ジョブの作成**」セクションの下) にある手順を使って、インポート ジョブを作成できるようになりました。 このインポート ジョブでは、他の詳細とともに、ディスク ドライブの準備中に作成されたジャーナル ファイルを提供します。

## <a name="physically-ship-the-disks"></a>ディスクを物理的に出荷する
これで、Azure データセンターにディスクを物理的に発送できるようになりました。 データセンターでは、インポート ジョブ作成時に指定した Azure Storage BLOB にデータがコピーされます。 さらに、ジョブ作成時に、後から追跡情報を提供することを選択した場合、インポート ジョブに戻って追跡番号を更新することができます。

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Azure Storage BLOB から Azure Data Lake Store へのデータのコピー
インポート ジョブの状態が完了になった後、指定した Azure Storage BLOB でデータが使用できるかどうかを確認できます。 その後、さまざまな方法で、そのデータを BLOB から Azure Data Lake Store へ移動することができます。 データのアップロードについて利用可能なすべてのオプションについては、「[Data Lake Store へのデータの取り込み](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store)」を参照してください。

このセクションでは、データをコピーするための Azure Data Factory パイプラインの作成に使用できる JSON 定義を示します。 JSON 定義は、[Azure Portal](../data-factory/data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](../data-factory/data-factory-copy-activity-tutorial-using-powershell.md) で使用できます。

### <a name="source-linked-service-azure-storage-blob"></a>ソース リンク サービス (Azure Storage BLOB)
````
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
````

### <a name="target-linked-service-azure-data-lake-store"></a>ターゲット リンク サービス (Azure Data Lake Store)
````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>入力データ セット
````
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
````
### <a name="output-data-set"></a>出力データ セット
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>パイプライン (コピー アクティビティ)
````
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
````
詳細については、[Azure Data Factory を使用した Azure Storage BLOB から Azure Data Lake Store へのデータの移動](../data-factory/data-factory-azure-datalake-connector.md#example-copy-data-from-azure-blob-to-azure-data-lake-store)に関するページを参照してください。

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>Azure Data Lake Store 内でデータ ファイルを再構築する
初めに、319 GB のファイルを用意して、Azure Import/Export サービスを使用して転送できるように小さなサイズのファイルに分割しました。 データが Azure Data Lake Store 内に置かれたので、ファイルを元のサイズに再構築することができます。 これは、次の Azure PowerShell コマンドレットを使用して実行します。

````
# Login to our account
Login-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext –SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv”
````

## <a name="next-steps"></a>次のステップ
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
* [Data Lake Store で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)

