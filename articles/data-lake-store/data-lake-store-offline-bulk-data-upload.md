---
title: Azure Data Lake Storage Gen1 に大きなデータ セットをアップロードする - オフライン メソッド
description: Import/Export サービスを使用して Azure Blob ストレージから Azure Data Lake Storage Gen1 にデータをコピーする
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 940b7ac90f85e0254d59459b70ccc15312cd69f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98700841"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-storage-gen1"></a>Azure Import/Export サービスを使用した Data Lake Storage Gen1 へのデータのオフライン コピー

この記事では、[Azure Import/Export サービス](../import-export/storage-import-export-service.md)などのオフライン コピー メソッドを使用して、大きなデータセット (200 GB 超) を Data Lake Storage Gen1 にコピーする方法について説明します。 具体的には、この記事の例では、ディスク上で 339,420,860,416 バイト、つまり約 319 GB のファイルを使用します。 このファイルに 319GB.tsv と名前を付けます。

Azure Import/Export サービスを使用すると、ハード ディスク ドライブを Azure データ センターに送付することで、大量のデータを Azure Blob Storage により安全に転送できます。

## <a name="prerequisites"></a>前提条件

開始する前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure ストレージ アカウント**。
* **Azure Data Lake Storage Gen1 アカウント**。 これを作成する手順については、[Azure Data Lake Storage Gen1 の使用開始](data-lake-store-get-started-portal.md)に関する記事をご覧ください。

## <a name="prepare-the-data"></a>データを準備する

Import/Export サービスを使用する前に、転送するデータ ファイルを **200 GB 未満のコピーに** 分割します。 これは、インポート ツールでは 200 GB を超えるファイルを扱えないためです。 この記事では、それぞれ 100 GB のチャンクにファイルを分割します。 これは [Cygwin](https://cygwin.com/install.html) を使用して実行できます。 Cygwin では、Linux のコマンドがサポートされています。 この場合は、次のコマンドを使用します。

```console
split -b 100m 319GB.tsv
```

この split 操作により下記の名前のファイルが作成されます。

* *319GB.tsv-part-aa*
* *319GB.tsv-part-ab*
* *319GB.tsv-part-ac*
* *319GB.tsv-part-ad*

## <a name="get-disks-ready-with-data"></a>ディスクにデータを準備する

[Azure Import/Export サービスの使用](../import-export/storage-import-export-service.md)に関する記事 (「**ドライブの準備**」セクションの下) にある手順に従って、ハード ドライブを準備します。 全体の手順を次に示します。

1. Azure Import/Export サービスでの使用要件を満たしているハード ディスクを調達します。
2. Azure ストレージ アカウントを指定します。データは、Azure データセンターへの発送後そのアカウントにコピーされます。
3. コマンドライン ユーティリティの一種である [Azure Import/Export ツール](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409)を使用します。 ツールの使用方法を示すサンプル スニペットを次に示します。

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    さらに複雑なスニペットについては、[Azure Import/Export サービスの使用](../import-export/storage-import-export-service.md)に関するページを参照してください。
4. 上記のコマンドを実行すると、指定した場所にジャーナル ファイルが作成されます。 このジャーナル ファイルを使用して、[Azure Portal](https://portal.azure.com) でインポート ジョブを作成します。

## <a name="create-an-import-job"></a>インポート ジョブの作成

これで、[Azure Import/Export サービスの使用](../import-export/storage-import-export-service.md)に関する記事 (「**インポート ジョブの作成**」セクションの下) にある手順を使って、インポート ジョブを作成できるようになりました。 このインポート ジョブでは、他の詳細とともに、ディスク ドライブの準備中に作成されたジャーナル ファイルを提供します。

## <a name="physically-ship-the-disks"></a>ディスクを物理的に出荷する

これで、Azure データセンターにディスクを物理的に発送できるようになりました。 データセンターでは、インポート ジョブ作成時に指定した Azure Storage BLOB にデータがコピーされます。 さらに、ジョブ作成時に、後から追跡情報を提供することを選択した場合、インポート ジョブに戻って追跡番号を更新することができます。

## <a name="copy-data-from-blobs-to-data-lake-storage-gen1"></a>BLOB から Azure Data Lake Storage Gen1 にデータをコピーする

インポート ジョブの状態が完了になった後、指定した Azure Storage BLOB でデータが使用できるかどうかを確認できます。 その後、さまざまな方法で、そのデータを BLOB から Azure Data Lake Storage Gen1 へ移動することができます。 データのアップロードについて利用可能なすべてのオプションについては、[Data Lake Storage Gen1 へのデータの取り込み](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1)に関するページを参照してください。

このセクションでは、データをコピーするための Azure Data Factory パイプラインの作成に使用できる JSON 定義を示します。 JSON 定義は、[Azure portal](../data-factory/tutorial-copy-data-portal.md) または [Visual Studio](../data-factory/tutorial-copy-data-dot-net.md) で使用できます。

### <a name="source-linked-service-azure-storage-blob"></a>ソース リンク サービス (Azure Storage BLOB)

```JSON
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
```

### <a name="target-linked-service-data-lake-storage-gen1"></a>ターゲット リンク サービス (Data Lake Storage Gen1)

```JSON
{
    "name": "AzureDataLakeStorageGen1LinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Storage Gen1 account with your access rights>",
            "dataLakeStoreUri": "https://<adlsg1_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
```

### <a name="input-data-set"></a>入力データ セット

```JSON
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
```

### <a name="output-data-set"></a>出力データ セット

```JSON
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStorageGen1LinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
```

### <a name="pipeline-copy-activity"></a>パイプライン (コピー アクティビティ)

```JSON
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
```

詳細については、[Azure Data Factory を使用した Azure Storage BLOB から Azure Data Lake Storage Gen1 へのデータの移動](../data-factory/connector-azure-data-lake-store.md)に関するページを参照してください。

## <a name="reconstruct-the-data-files-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1 内でデータ ファイルを再構築する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

初めに、319 GB のファイルを用意して、Azure Import/Export サービスを使用して転送できるように小さなサイズのファイルに分割しました。 データが Azure Data Lake Storage Gen1 内に置かれたので、ファイルを元のサイズに再構築することができます。 これは、次の Azure PowerShell コマンドレットを使用して実行します。

```PowerShell
# Login to our account
Connect-AzAccount

# List your subscriptions
Get-AzSubscription

# Switch to the subscription you want to work with
Set-AzContext -SubscriptionId
Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
```

## <a name="next-steps"></a>次のステップ

* [Data Lake Storage Gen1 でのデータのセキュリティ保護](data-lake-store-secure-data.md)
* [Data Lake Storage Gen1 で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Storage Gen1 で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)
