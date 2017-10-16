---
title: "Azure Data Factory を使用して Amazon Simple Storage からデータをコピーする | Microsoft Docs"
description: "Azure Data Factory を使用して、Amazon Simple Storage Service (S3) のデータをサポートされているシンク データ ストアにコピーする方法について説明します。"
services: data-factory
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 1263f2cdf6a6467d973f838bb380bd00ce52ba1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Azure Data Factory を使用して Amazon Simple Storage Service からデータをコピーする
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [バージョン 2 - プレビュー](connector-amazon-simple-storage-service.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure Blob Storage をコピー先またはコピー元としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 のAmazon S3 コネクタ](v1/data-factory-amazon-simple-storage-service-connector.md)に関する記事を参照してください。

## <a name="supported-scenarios"></a>サポートされるシナリオ

サポートされている任意のソース データ ストアのデータを Azure Data Lake Store にコピーしたり、Azure Data Lake Store のデータをサポートされている任意のシンク データ ストアにコピーしたりできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Amazon S3 コネクタでは、ファイルをそのままコピーするか、[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)を使用してファイルを解析することをサポートしています。

## <a name="required-permissions"></a>必要なアクセス許可

Amazon S3 からデータをコピーするには、次のアクセス許可が付与されている必要があります。

- Amazon S3 オブジェクトの操作には `s3:GetObject` と `s3:GetObjectVersion`。
- Amazon S3 バケットの操作には `s3:ListBucket`。 Data Factory コピー ウィザードを使用している場合は、`s3:ListAllMyBuckets` も必要です。

Amazon S3 のアクセス許可の完全な一覧については、「[ポリシーでのアクセス許可の指定](http://docs.aws.amazon.com/amazons3/latest/dev/using-with-s3-actions.html)」を参照してください。

## <a name="getting-started"></a>使用の開始
コピー アクティビティを含むパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)をご覧ください。 

以下のセクションで、Amazon S3 に固有の Data Factory エンティティを定義するために使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Amazon S3 のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **AmazonS3** に設定する必要があります。 | あり |
| accessKeyID | シークレット アクセス キーの ID。 |あり |
| secretAccessKey | シークレット アクセス キー自体。 このフィールドを SecureString とマークします。 |あり |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイム (データ ストアがプライベート ネットワークにある場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

>[!NOTE]
>IAM アカウントが Amazon S3 からデータをコピーするには、このコネクタにアクセス キーが必要です。 [一時的なセキュリティ資格情報](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)はサポートされていません。
>

たとえば次のようになります。

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                    "type": "SecureString",
                    "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、Amazon S3 データセットでサポートされるプロパティの一覧を示します。

Amazon S3 からデータをコピーするには、データセットの type プロパティを **AmazonS3Object** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**AmazonS3Object** を設定する必要があります。 |あり |
| bucketName | S3 バケットの名前。 |あり |
| key | S3 オブジェクト キー。 prefix が指定されていないときにのみ適用されます。 |いいえ |
| prefix | S3 オブジェクト キーのプレフィックス。 キーがこのプレフィックスで始まるオブジェクトが選択されます。 key が指定されていない場合にのみ適用されます。 |いいえ |
| version | S3 のバージョン管理が有効になっている場合の S3 オブジェクトのバージョン。 |いいえ |
| format | ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。<br/><br/>ファイルを特定の形式で解析するか生成する場合、次のファイル形式がサポートされます。**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](supported-file-formats-and-compression-codecs.md#text-format)、[Json Format](supported-file-formats-and-compression-codecs.md#json-format)、[Avro Format](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc Format](supported-file-formats-and-compression-codecs.md#orc-format)、[Parquet Format](supported-file-formats-and-compression-codecs.md#parquet-format) の各セクションを参照してください。 |いいえ (バイナリ コピー シナリオのみ) |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md#compression-support)に関する記事を参照してください。<br/>サポートされる種類は、**GZip**、**Deflate**、**BZip2**、および **ZipDeflate** です。<br/>サポートされるレベルは、**Optimal** と **Fastest** です。 |いいえ |

> [!NOTE]
> **bucketName とキーの組み合わせ**によって S3 オブジェクトの場所が指定されます。バケットは S3 オブジェクトのルート コンテナーであり、キーは、S3 オブジェクトへの完全パスです。

**例: prefix の使用**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**例: key と version の使用 (省略可能)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Azure Data Lake のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="amazon-s3-as-source"></a>ソースとしての Amazon S3

Amazon S3 からデータをコピーするには、コピー アクティビティのソースの種類を **FileSystemSource** (Amazon S3 が含まれます) に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **FileSystemSource** に設定する必要があります。 |あり |
| recursive | データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。<br/>使用可能な値: **true** (既定値)、**false** | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。