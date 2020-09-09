---
title: Azure Data Factory のコピー アクティビティを使用してメタデータと ACL を保持する
description: Azure Data Factory のコピー アクティビティを使用して、コピー中にメタデータと ACL を保持する方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: jingwang
ms.openlocfilehash: a1527195296237eb8c9c309f8ac4a5911136cf77
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891761"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Azure Data Factory のコピー アクティビティを使用してメタデータと ACL を保持する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

次のシナリオでは、Azure Data Factory のコピー アクティビティを使用してソースからシンクにデータをコピーするときに、メタデータと ACL を一緒に保持することもできます。

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a> Lake 移行用にメタデータを保持する

[Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) など、ある Data Lake から別の Data Lake にデータを移行するときに、データと共にファイルのメタデータを保持することもできます。

コピー アクティビティでは、データのコピー中に次の属性を保持できます。

- **お客様が指定したすべてのメタデータ** 
- 次の **5 つのデータ ストア組み込みシステム プロパティ**: `contentType`、`contentLanguage` (Amazon S3 を除く)、`contentEncoding`、`contentDisposition`、`cacheControl`

**メタデータの違いに対処する:** Amazon S3 と Azure Storage では、顧客が指定したメタデータのキーにさまざまな文字セットが許可されています。 コピー アクティビティを使用してメタデータを保持することを選択すると、ADF によって無効な文字が自動的に '_' に置き換えられます。

バイナリ形式を使用して Amazon S3/Azure Data Lake Storage Gen2/Azure Blob から Azure Data Lake Storage Gen2/Azure Blob にファイルをそのままコピーすると、アクティビティ作成のための **[コピー アクティビティ]**  >  **[設定]** タブまたは [データのコピー] ツールの **[設定]** ページに、 **[保持]** オプションが表示されます。

![コピー アクティビティでのメタデータの保持](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

コピー アクティビティの JSON 構成の例を次に示します (`preserve` を参照)。 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a> Data Lake Storage Gen1/Gen2 から Gen2 に ACL を保持する

Azure Data Lake Storage Gen1 から Gen2 にアップグレードするときに、または ADLS Gen2 間でデータをコピーするときに、データ ファイルと共に POSIX アクセス制御リスト (ACL) を保持することもできます。 アクセス制御の詳細については、「[Azure Data Lake Storage Gen1 のアクセス制御](../data-lake-store/data-lake-store-access-control.md)」および「[Azure Data Lake Storage Gen2 のアクセス制御](../storage/blobs/data-lake-storage-access-control.md)」をご覧ください。

コピー アクティビティでは、データのコピー中に次の種類の ACL を保持できます。 1 つ以上の種類を選択できます。

- **ACL**: ファイルおよびディレクトリの POSIX アクセス制御リストがコピーされ、保持されます。 既存の完全な ACL がソースからシンクにコピーされます。 
- **所有者**:ファイルおよびディレクトリの所有ユーザーがコピーされ、保持されます。 シンク Data Lake Storage Gen2 に対するスーパー ユーザーのアクセス権が必要です。
- **グループ**: ファイルおよびディレクトリの所有グループがコピーされ、保持されます。 シンク Data Lake Storage Gen2 に対するスーパー ユーザーのアクセス権、または所有ユーザー (所有ユーザーがターゲット グループのメンバーでもある場合) が必要です。

フォルダーからのコピーを指定すると、指定したフォルダーとその下にあるファイルおよびディレクトリの ACL が、Data Factory によってレプリケートされます (`recursive` が true に設定されている場合)。 1 つのファイルからのコピーを指定すると、そのファイルの ACL がコピーされます。

>[!NOTE]
>ADF を使用して Data Lake Storage Gen1/Gen2 から Gen2 に ACL を保持すると、シンク Gen2 に対応するフォルダーまたはファイルの既存の ACL が上書きされます。

>[!IMPORTANT]
>ACL を保持する場合は、Data Factory がシンク Data Lake Storage Gen2 アカウントに対して処理を実行できる十分なアクセス許可を付与してください。 たとえば、アカウント キー認証を使用するか、サービス プリンシパルまたはマネージド ID にストレージ BLOB データ所有者ロールを割り当てます。

バイナリ形式またはバイナリ コピー オプションを使用してソースを Data Lake Storage Gen1/Gen2 として構成し、バイナリ形式またはバイナリ コピー オプションを使用してシンクを Data Lake Storage Gen2 として構成すると、[データのコピー] ツールの **[設定]** ページまたはアクティビティ作成のための **[アクティビティのコピー]**  >  **[設定]** タブに、 **[保持]** オプションが表示されます。

![Data Lake Storage Gen1/Gen2 から Gen2 に ACL を保持](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

コピー アクティビティの JSON 構成の例を次に示します (`preserve` を参照)。 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>次のステップ

コピー アクティビティの他の記事を参照してください。

- [コピー アクティビティの概要](copy-activity-overview.md)
- [コピー アクティビティのパフォーマンス](copy-activity-performance.md)
