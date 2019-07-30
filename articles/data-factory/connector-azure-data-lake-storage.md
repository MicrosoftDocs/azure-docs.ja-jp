---
title: Data Factory を使用して Azure Data Lake Storage Gen2 との間でデータをコピーする | Microsoft Docs
description: Azure Data Factory を使用して、Azure Data Lake Storage Gen2 との間でデータをコピーする方法について説明します。
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 9f60c6258da77c0aaa99d16e178f4b3531ce90d9
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509255"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Data Lake Storage Gen2 との間でデータをコピーする

Azure Data Lake Storage Gen2 は、[Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) に組み込まれているビッグ データ分析専用の一連の機能です。 ファイル システムとオブジェクト ストレージの両方のパラダイムを使用して、データと連携させることができます。

この記事では、Azure Data Lake Storage Gen2 をコピー先またはコピー元としてデータをコピーする方法について説明します。 Azure Data Factory については、[入門記事で](introduction.md)をご覧ください。

## <a name="supported-capabilities"></a>サポートされる機能

この Azure Data Lake Storage Gen2 コネクタは、次のアクティビティでサポートされます。

- [サポートされるソースまたはシンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)

具体的には、このコネクタを使用して次のことができます。

- アカウント キー認証、サービス プリンシパル認証、または Azure リソースのマネージド ID 認証を使用してデータをコピーする。
- ファイルをそのままコピーするか、[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)を使用してファイルを解析または生成する。

>[!TIP]
>階層型名前空間を有効にした場合、現時点では、BLOB API と Data Lake Storage Gen2 API の間に操作の相互運用性はありません。 "指定されたファイルシステムは存在しません" というメッセージを含む "ErrorCode=FilesystemNotFound" エラーが発生した場合、指定されたシンク ファイル システムが、他の場所で Data Lake Storage Gen2 API ではなく BLOB API によって作成されたものであることが原因です。 この問題を解決するには、BLOB コンテナーの名前として存在しない名前で新しいファイル システムを指定します。 そのファイル システムは、データ コピー中に Data Factory によって自動的に作成されます。

>[!NOTE]
>Azure Storage のファイアウォール設定で **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** オプションを有効にした場合、Azure Integration Runtime が Data Lake Storage Gen2 に接続せず、アクセス不可エラーが表示されます。 このエラー メッセージが表示されるのは、Data Factory が信頼された Microsoft サービスとして扱われていないためです。 代わりに、セルフホステッド統合ランタイムを使用して接続してください。

## <a name="get-started"></a>作業開始

>[!TIP]
>Data Lake Storage Gen2 コネクタの使用方法のチュートリアルについては、[Azure Data Lake Storage Gen2 へのデータの読み込み](load-azure-data-lake-storage-gen2.md)に関する記事をご覧ください。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、Data Lake Storage Gen2 に固有の Data Factory エンティティの定義に使用されるプロパティについて説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Data Lake Storage Gen2 コネクタでは、次の認証の種類がサポートされています。 詳細については、対応するセクションをご覧ください。

- [アカウント キー認証](#account-key-authentication)
- [サービス プリンシパルの認証](#service-principal-authentication)
- [Azure リソースのマネージド ID 認証](#managed-identity)

>[!NOTE]
>PolyBase を使用して SQL Data Warehouse にデータを読み込むときに、ソース Data Lake Storage Gen2 が仮想ネットワーク エンドポイントで構成されている場合、PolyBase で要求されるマネージド ID 認証を使用する必要があります。 構成の前提条件の詳細については、[マネージド ID 認証](#managed-identity)に関するセクションをご覧ください。

### <a name="account-key-authentication"></a>アカウント キー認証

ストレージ アカウント キー認証の使用には、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **AzureBlobFS** に設定する必要があります。 |はい |
| url | Data Lake Storage Gen2 のエンドポイントのパターンは `https://<accountname>.dfs.core.windows.net` です。 | はい |
| accountKey | Data Lake Storage Gen2 のアカウント キー。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 このプロパティが指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>サービス プリンシパルの認証

サービス プリンシパル認証を使用するには、次の手順に従います。

1. 「[アプリケーションを Azure AD テナントに登録する](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)」の手順に従って、Azure Active Directory (Azure AD) にアプリケーション エンティティを登録します。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。

    - アプリケーション ID
    - アプリケーション キー
    - テナント ID

2. サービス プリンシパルに適切なアクセス許可を付与します。 Data Lake Storage Gen2 におけるアクセス許可のしくみの詳細については、「[ファイルとディレクトリのアクセス制御リスト](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)」をご覧ください。

    - **ソースとして**: Storage Explorer で、ファイルをコピーするための**読み取り**アクセス許可と共に、ソース ファイル システムからの**実行**アクセス許可を少なくとも付与します。 または、[アクセス制御 (IAM)] で、少なくとも**ストレージ BLOB データ閲覧者**ロールを付与します。
    - **シンクとして**: Storage Explorer で、シンク フォルダーに対する**書き込み**アクセス許可と共に、シンク ファイル システムからの**実行**アクセス許可を少なくとも付与します。 または、[アクセス制御 (IAM)] で、少なくとも**ストレージ BLOB データ共同作成者**ロールを付与します。

>[!NOTE]
>アカウント レベルを起点としてフォルダーを一覧表示したり、接続をテストしたりするには、付与されるサービス プリンシパルのアクセス許可を、**IAM で "ストレージ BLOB データ閲覧者" アクセス許可が付与されたストレージ アカウント**に設定する必要があります。 これは、以下を使用する場合に該当します。
>- **データ コピー ツール** (コピー パイプラインを作成する)。
>- **Data Factory UI** (接続およびフォルダーのナビゲーションを作成中にテストする)。 
>アカウント レベルでのアクセス許可の付与について懸念がある場合は、作成中に、接続のテストをスキップし、アクセス許可が付与された親パスを入力した後、その指定したパスから参照することを選択します。 コピー対象のファイルでサービス プリンシパルに適切なアクセス許可が付与されていれば、コピー アクティビティは機能します。

リンクされたサービスでは、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **AzureBlobFS** に設定する必要があります。 |はい |
| url | Data Lake Storage Gen2 のエンドポイントのパターンは `https://<accountname>.dfs.core.windows.net` です。 | はい |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | はい |
| servicePrincipalKey | アプリケーションのキーを取得します。 Data Factory に安全に格納するには、このフィールドを `SecureString` としてマークします。 また、[Azure Key Vault に格納されているシークレットを参照する](store-credentials-in-key-vault.md)こともできます。 | はい |
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 これは、Azure portal の右上隅をマウスでポイントすることで取得できます。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Azure リソースのマネージド ID 認証

データ ファクトリは、特定のデータ ファクトリを表す、[Azure リソースのマネージド ID](data-factory-service-identity.md) に関連付けることができます。 独自のサービス プリンシパルを使用する場合と同様に、Data Lake Storage Gen2 の認証にこのマネージド ID を直接使用できます。 これにより、この指定されたファクトリは、Data Lake Storage Gen2 にアクセスしてデータをコピーできます。

Azure リソースのマネージド ID 認証を使用するには、次の手順に従います。

1. ファクトリと共に生成された**サービス ID アプリケーション ID** の値をコピーして、[Data Factory のマネージド ID 情報を取得](data-factory-service-identity.md#retrieve-managed-identity)します。

2. マネージド ID に適切なアクセス許可を付与します。 Data Lake Storage Gen2 におけるアクセス許可のしくみの詳細については、「[ファイルとディレクトリのアクセス制御リスト](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)」をご覧ください。

    - **ソースとして**: Storage Explorer で、ファイルをコピーするための**読み取り**アクセス許可と共に、ソース ファイル システムからの**実行**アクセス許可を少なくとも付与します。 または、[アクセス制御 (IAM)] で、少なくとも**ストレージ BLOB データ閲覧者**ロールを付与します。
    - **シンクとして**: Storage Explorer で、シンク フォルダーに対する**書き込み**アクセス許可と共に、シンク ファイル システムからの**実行**アクセス許可を少なくとも付与します。 または、[アクセス制御 (IAM)] で、少なくとも**ストレージ BLOB データ共同作成者**ロールを付与します。

>[!NOTE]
>アカウント レベルを起点としてフォルダーを一覧表示したり、接続をテストしたりするには、付与されるマネージド ID のアクセス許可を、**IAM で "ストレージ BLOB データ閲覧者" アクセス許可が付与されたストレージ アカウント**に設定する必要があります。 これは、以下を使用する場合に該当します。
>- **データ コピー ツール** (コピー パイプラインを作成する)。
>- **Data Factory UI** (接続およびフォルダーのナビゲーションを作成中にテストする)。 
>アカウント レベルでのアクセス許可の付与について懸念がある場合は、作成中に、接続のテストをスキップし、アクセス許可が付与された親パスを入力した後、その指定したパスから参照することを選択します。 コピー対象のファイルでサービス プリンシパルに適切なアクセス許可が付与されていれば、コピー アクティビティは機能します。

>[!IMPORTANT]
>PolyBase を使用して Data Lake Storage Gen2 から SQL Data Warehouse にデータを読み込む場合、Data Lake Storage Gen2 にマネージド ID 認証を使用しているときは、[こちらのガイダンス](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)の手順 1. と 2. に従って、1) SQL Database サーバーを Azure Active Directory (Azure AD) に登録し、2) SQL Database サーバーにストレージ BLOB データ共同作成者ロールを割り当てます。残りの部分は Data Factory によって処理されます。 Data Lake Storage Gen2 が Azure Virtual Network エンドポイントで構成されている場合、PolyBase を使用してそこからデータを読み込むには、PolyBase で要求されるマネージド ID 認証を使用する必要があります。

リンクされたサービスでは、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **AzureBlobFS** に設定する必要があります。 |はい |
| url | Data Lake Storage Gen2 のエンドポイントのパターンは `https://<accountname>.dfs.core.windows.net` です。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットの定義に使用できるセクションとプロパティの一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。

- Parquet 形式と区切りテキスト形式については、「[Parquet 形式と区切りテキスト形式のデータセット](#parquet-and-delimited-text-format-dataset)」をご覧ください。
- ORC、Avro、JSON、バイナリ形式などの他の形式については、「[他の形式のデータセット](#other-format-dataset)」をご覧ください。

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet 形式と区切りテキスト形式のデータセット

Data Lake Storage Gen2 との間で、Parquet 形式または区切りテキスト形式のデータをコピーするには、[Parquet 形式](format-parquet.md)および[区切りテキスト形式](format-delimited-text.md)に関する記事で、形式ベースのデータセットとサポートされる設定について確認してください。 Data Lake Storage Gen2 では、形式ベースのデータセットの `location` 設定で次のプロパティがサポートされています。

| プロパティ   | 説明                                                  | 必須 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | データセットの `location` の type プロパティは、**AzureBlobFSLocation** に設定する必要があります。 | はい      |
| fileSystem | Data Lake Storage Gen2 ファイル システム名。                              | いいえ       |
| folderPath | 指定されたファイル システムの下のフォルダーのパス。 ワイルドカードを使用してフォルダーをフィルター処理する場合は、この設定をスキップし、アクティビティのソース設定で指定します。 | いいえ       |
| fileName   | 指定された fileSystem + folderPath の下のファイル名。 ワイルドカードを使用してファイルをフィルター処理する場合は、この設定をスキップし、アクティビティのソース設定で指定します。 | いいえ       |

> [!NOTE]
> 次のセクションで説明する Parquet 形式またはテキスト形式の **AzureBlobFSFile** 型のデータセットは、下位互換性を確保するために、コピー、ルックアップ、GetMetadata アクティビティで引き続きそのままサポートされます。 ただし、マッピング データ フロー機能では動作しません。 今後はこの新しいモデルを使用することをお勧めします。 Data Factory オーサリング UI では、これらの新しい型が生成されます。

**例:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>他の形式のデータセット

Data Lake Storage Gen2 との間で、ORC、Avro、JSON、またはバイナリ形式のデータをコピーする場合は、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**AzureBlobFSFile** に設定する必要があります。 |はい |
| folderPath | Data Lake Storage Gen2 のフォルダーのパス。 指定しないと、ルートが参照されます。 <br/><br/>ワイルドカード フィルターがサポートされています。 使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br/><br/>例: filesystem/folder/。 「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 |いいえ |
| fileName | 指定された "folderPath" の下にあるファイルの名前またはワイルドカード フィルター。 このプロパティの値を指定しない場合、データセットはフォルダー内のすべてのファイルをポイントします。 <br/><br/>フィルターに使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。<br/>- 例 1: `"fileName": "*.csv"`<br/>- 例 2: `"fileName": "???20180427.txt"`<br/>実際のファイル名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。<br/><br/>出力データセットに fileName の指定がなく、アクティビティ シンクに **preserveHierarchy** の指定がない場合、コピー アクティビティは、"*Data.[activity run ID GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]* " というパターンでファイル名が自動的に生成されます (例: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz")。 クエリの代わりにテーブル名を使用して表形式のソースからコピーする場合、名前のパターンは " *[table name].[format].[compression if configured]* " になります (例: "MyTable.csv")。 |いいえ |
| modifiedDatetimeStart | 最終変更日時属性に基づくファイル フィルター。 最終変更日時が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の範囲内にあるファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> 大量のファイルでファイル フィルターを実行する場合、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けます。 <br/><br/> 各プロパティには NULL を指定できます。これは、ファイル属性フィルターをデータセットに適用しないことを意味します。 `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が NULL の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。 `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が NULL の場合は、最終変更日時属性が datetime 値以下であるファイルが選択されます。| いいえ |
| modifiedDatetimeEnd | 最終変更日時属性に基づくファイル フィルター。 最終変更日時が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の範囲内にあるファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> 大量のファイルでファイル フィルターを実行する場合、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けます。 <br/><br/> 各プロパティには NULL を指定できます。これは、ファイル属性フィルターをデータセットに適用しないことを意味します。 `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が NULL の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。 `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が NULL の場合は、最終変更日時属性が datetime 値以下であるファイルが選択されます。| いいえ |
| format | ファイルベースのストア間でファイルをそのままコピー (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。<br/><br/>特定の形式のファイルを解析または生成する場合、サポートされるファイル形式の種類は、**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 **format** の **type** プロパティをいずれかの値に設定します。 詳細については、「[テキスト形式](supported-file-formats-and-compression-codecs.md#text-format)」、「[JSON 形式](supported-file-formats-and-compression-codecs.md#json-format)」、「[AVRO 形式](supported-file-formats-and-compression-codecs.md#avro-format)」、「[ORC 形式](supported-file-formats-and-compression-codecs.md#orc-format)」、[Parquet 形式](supported-file-formats-and-compression-codecs.md#parquet-format)」の各セクションをご覧ください。 |いいえ (バイナリ コピー シナリオのみ) |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md#compression-support)に関する記事を参照してください。<br/>サポートされる種類は、**GZip**、**Deflate**、**BZip2**、および **ZipDeflate** です。<br/>サポートされるレベルは、**Optimal** と **Fastest** です。 |いいえ |

>[!TIP]
>フォルダーの下のすべてのファイルをコピーするには、**folderPath** のみを指定します。<br>特定の名前の単一のファイルをコピーするには、フォルダー部分で **folderPath**、ファイル名で **fileName** を指定します。<br>フォルダーの下のファイルのサブセットをコピーするには、フォルダー部分で **folderPath**、ワイルドカード フィルターで **fileName** を指定します。 

**例:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
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

アクティビティの定義に使用できるセクションとプロパティの一覧については、[コピー アクティビティの構成](copy-activity-overview.md#configuration)および[パイプラインとアクティビティ](concepts-pipelines-activities.md)に関する記事をご覧ください。 このセクションでは、Data Lake Storage Gen2 のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>ソースの種類としての Azure Data Lake Storage Gen2

- Parquet 形式または区切りテキスト形式からコピーする場合は、「[Parquet 形式と区切りテキスト形式のソース](#parquet-and-delimited-text-format-source)」をご覧ください。
- ORC、Avro、JSON、バイナリ形式などの他の形式からコピーする場合は、「[他の形式のソース](#other-format-source)」をご覧ください。

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet 形式と区切りテキスト形式のソース

Data Lake Storage Gen2 から、Parquet 形式または区切りテキスト形式のデータをコピーするには、[Parquet 形式](format-parquet.md)および[区切りテキスト形式](format-delimited-text.md)に関する記事で、形式ベースのコピー アクティビティ ソースとサポートされる設定について確認してください。 Data Lake Storage Gen2 では、形式ベースのコピー ソースの `storeSettings` 設定で次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` の type プロパティは **AzureBlobFSReadSetting** に設定する必要があります。 | はい                                           |
| recursive                | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 recursive が true に設定されていて、シンクがファイル ベースのストアである場合、空のフォルダーまたはサブフォルダーはシンクでコピーも作成もされません。 使用可能な値: **true** (既定値) および **false**。 | いいえ                                            |
| wildcardFolderPath       | ソース フォルダーをフィルター処理するためにデータセットで構成されている、特定のファイル システムの下のワイルドカード文字を含むフォルダーのパス。 <br>使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br>「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | いいえ                                            |
| wildcardFileName         | ソース ファイルをフィルター処理するための、特定のファイル システム + folderPath/wildcardFolderPath の下のワイルドカード文字を含むファイル名。 <br>使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | はい (データセットで `fileName` が指定されていない場合) |
| modifiedDatetimeStart    | 最終変更日時属性に基づくファイル フィルター。 最終変更日時が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の範囲内にあるファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br> 各プロパティには NULL を指定できます。これは、ファイル属性フィルターをデータセットに適用しないことを意味します。 `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が NULL の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。 `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が NULL の場合は、最終変更日時属性が datetime 値以下であるファイルが選択されます。 | いいえ                                            |
| modifiedDatetimeEnd      | 上記と同じです。                                               | いいえ                                            |
| maxConcurrentConnections | ストレージ ストアに同時に接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ                                            |

> [!NOTE]
> Parquet 形式または区切りテキスト形式の場合、次のセクションで説明する **AzureBlobFSSource** 型のコピー アクティビティ ソースは、下位互換性を確保するために引き続きそのままサポートされます。 今後はこの新しいモデルを使用することをお勧めします。 Data Factory オーサリング UI では、これらの新しい型が生成されます。

**例:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>他の形式のソース

Data Lake Storage Gen2 から、ORC、Avro、JSON、またはバイナリ形式のデータをコピーする場合、コピー アクティビティの **source** セクションで次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **AzureBlobFSSource** に設定する必要があります。 |はい |
| recursive | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 recursive が true に設定されていて、シンクがファイル ベースのストアである場合、空のフォルダーまたはサブフォルダーはシンクでコピーも作成もされません。<br/>使用可能な値: **true** (既定値) および **false**。 | いいえ |
| maxConcurrentConnections | データ ストアに同時に接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>シンクの種類としての Azure Data Lake Storage Gen2

- Parquet 形式または区切りテキスト形式にコピーする場合は、「[Parquet 形式と区切りテキスト形式のシンク](#parquet-and-delimited-text-format-sink)」をご覧ください。
- ORC、Avro、JSON、バイナリ形式などの他の形式にコピーする場合は、「[他の形式のシンク](#other-format-sink)」をご覧ください。

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet 形式と区切りテキスト形式のシンク

Data Lake Storage Gen2 に、Parquet 形式または区切りテキスト形式のデータをコピーするには、[Parquet 形式](format-parquet.md)および[区切りテキスト形式](format-delimited-text.md)に関する記事で、形式ベースのコピー アクティビティ シンクとサポートされる設定について確認してください。 Data Lake Storage Gen2 では、形式ベースのコピー シンクの `storeSettings` 設定で次のプロパティがサポートされます。

| プロパティ                 | 説明                                                  | 必須 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` の type プロパティは **AzureBlobFSWriteSetting** に設定する必要があります。 | はい      |
| copyBehavior             | ソースがファイル ベースのデータ ストアのファイルの場合は、コピー動作を定義します。<br/><br/>使用できる値は、以下のとおりです。<br/><b>- PreserveHierarchy (既定値)</b>:ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーへのソース ファイルの相対パスはターゲット フォルダーへのターゲット ファイルの相対パスと同じになります。<br/><b>- FlattenHierarchy</b>:ソース フォルダーのすべてのファイルをターゲット フォルダーの第一レベルに配置します。 ターゲット ファイルは、自動生成された名前になります。 <br/><b>- MergeFiles</b>:ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル名を指定した場合、マージされたファイル名は指定した名前になります。 それ以外は自動生成されたファイル名になります。 | いいえ       |
| maxConcurrentConnections | データ ストアに同時に接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ       |

> [!NOTE]
> Parquet 形式または区切りテキスト形式の場合、次のセクションで説明する **AzureBlobFSSink** 型のコピー アクティビティ シンクは、下位互換性を確保するために引き続きそのままサポートされます。 今後はこの新しいモデルを使用することをお勧めします。 Data Factory オーサリング UI では、これらの新しい型が生成されます。

**例:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobFSWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>他の形式のシンク

Data Lake Storage Gen2 に、ORC、Avro、JSON、またはバイナリ形式のデータをコピーする場合は、**sink** セクションで次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは **AzureBlobFSSink** に設定する必要があります。 |はい |
| copyBehavior | ソースがファイル ベースのデータ ストアのファイルの場合は、コピー動作を定義します。<br/><br/>使用できる値は、以下のとおりです。<br/><b>- PreserveHierarchy (既定値)</b>:ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーへのソース ファイルの相対パスはターゲット フォルダーへのターゲット ファイルの相対パスと同じになります。<br/><b>- FlattenHierarchy</b>:ソース フォルダーのすべてのファイルをターゲット フォルダーの第一レベルに配置します。 ターゲット ファイルは、自動生成された名前になります。 <br/><b>- MergeFiles</b>:ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル名を指定した場合、マージされたファイル名は指定した名前になります。 それ以外は自動生成されたファイル名になります。 | いいえ |
| maxConcurrentConnections | データ ストアに同時に接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>フォルダーとファイル フィルターの例

このセクションでは、ワイルドカード フィルターを使用した結果のフォルダーのパスとファイル名の動作について説明します。

| folderPath | fileName | recursive | ソースのフォルダー構造とフィルターの結果 (**太字**のファイルが取得されます)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (空、既定値を使用) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (空、既定値を使用) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>recursive と copyBehavior の例

このセクションでは、recursive 値と copyBehavior 値のさまざまな組み合わせでのコピー操作の動作について説明します。

| recursive | copyBehavior | ソースのフォルダー構造 | ターゲットの結果 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット Folder1 は、ソースと同じ構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 の自動生成された名前 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2、File3、File4、File5 の内容は 1 つのファイルにマージされて、自動生成されたファイル名が付けられます。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>File3、File4、File5 を含む Subfolder1 は取得されません。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/><br/>File3、File4、File5 を含む Subfolder1 は取得されません。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2 の内容は 1 つのファイルにマージされ、自動生成されたファイル名が付けられます。 File1 の自動生成された名前<br/><br/>File3、File4、File5 を含む Subfolder1 は取得されません。 |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Data Lake Storage Gen1 の ACL を保持する

>[!TIP]
>Azure Data Lake Storage Gen1 から Gen2 への一般的なデータ コピーを実行する場合、チュートリアルとベスト プラクティスについては、「[Azure Data Factory を使用して Azure Data Lake Storage Gen1 から Gen2 にデータをコピーする](load-azure-data-lake-storage-gen2-from-gen1.md)」をご覧ください。

Azure Data Lake Storage Gen1 から Gen2 にファイルをコピーするときに、データと共に POSIX アクセス制御リスト (ACL) を保持することもできます。 アクセス制御の詳細については、「[Azure Data Lake Storage Gen1 のアクセス制御](../data-lake-store/data-lake-store-access-control.md)」および「[Azure Data Lake Storage Gen2 のアクセス制御](../storage/blobs/data-lake-storage-access-control.md)」をご覧ください。

Azure Data Factory のコピー アクティビティを使用して、次の種類の ACL を保持できます。 1 つ以上の種類を選択できます。

- **ACL**: ファイルおよびディレクトリの POSIX アクセス制御リストがコピーされ、保持されます。 既存の完全な ACL がソースからシンクにコピーされます。 
- **所有者**:ファイルおよびディレクトリの所有ユーザーがコピーされ、保持されます。 シンク Data Lake Storage Gen2 に対するスーパー ユーザーのアクセス権が必要です。
- **グループ**: ファイルおよびディレクトリの所有グループがコピーされ、保持されます。 シンク Data Lake Storage Gen2 に対するスーパー ユーザーのアクセス権、または所有ユーザー (所有ユーザーがターゲット グループのメンバーでもある場合) が必要です。

フォルダーからのコピーを指定すると、指定したフォルダーとその下にあるファイルおよびディレクトリの ACL が、Data Factory によってレプリケートされます (`recursive` が true に設定されている場合)。 1 つのファイルからのコピーを指定すると、そのファイルの ACL がコピーされます。

>[!IMPORTANT]
>ACL を保持する場合は、Data Factory がシンク Data Lake Storage Gen2 アカウントに対して処理を実行できる十分なアクセス許可を付与してください。 たとえば、アカウント キー認証を使用するか、サービス プリンシパルまたはマネージド ID にストレージ BLOB データ所有者ロールを割り当てます。

バイナリ コピー オプションまたはバイナリ形式を使用してソースを Data Lake Storage Gen1 として構成し、バイナリ コピー オプションまたはバイナリ形式を使用してシンクを Data Lake Storage Gen2 として構成すると、 **[Copy Data Tool Settings]\(データ ツール設定のコピー\)** ページまたはアクティビティ作成のための **[アクティビティのコピー]**  >  **[設定]** タブに、 **[保持]** オプションが表示されます。

![Data Lake Storage Gen1 から Gen2 に ACL を保持](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

JSON 構成の例を次に示します (`preserve` を参照)。 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>マッピング データ フローのプロパティ

マッピング データ フロー機能の[ソース変換](data-flow-source.md)と[シンク変換](data-flow-sink.md)に関する記事で詳細を確認してください。

## <a name="next-steps"></a>次の手順

Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
