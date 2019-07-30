---
title: Data Factory を使用して Azure Data Lake Storage Gen1 との間でデータをコピーする | Microsoft Docs
description: Data Factory を使用して、サポートされるソース データ ストアのデータを Azure Data Lake Store にコピーしたり、Data Lake Store のデータをサポートされるシンク ストアにコピーしたりできます。
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: df88c3e2e07165182c917eaf30a5f37451fbd073
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509586"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Data Lake Storage Gen1 との間でデータをコピーする
> [!div class="op_single_selector" title1="使用している Azure Data Factory のバージョンを選択してください:"]
> * [Version 1](v1/data-factory-azure-datalake-connector.md)
> * [現在のバージョン](connector-azure-data-lake-store.md)

この記事では、Azure Data Lake Storage Gen1 をコピー先またはコピー元としてデータをコピーする方法について説明します。 Azure Data Factory については、[入門記事で](introduction.md)をご覧ください。

## <a name="supported-capabilities"></a>サポートされる機能

この Azure Data Lake Storage Gen1 コネクタは、次のアクティビティでサポートされます。

- [サポートされるソースまたはシンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)

具体的には、このコネクタでは次のことができます。

- サービス プリンシパルと Azure リソースのマネージド ID のどちらかの認証方法を使用して、ファイルをコピーする。
- ファイルをそのままコピーするか、[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)でファイルを解析または生成する。

> [!IMPORTANT]
> セルフホステッド統合ランタイムを使ってデータをコピーする場合は、`<ADLS account name>.azuredatalakestore.net` および `login.microsoftonline.com/<tenant>/oauth2/token` に対するポート 443 のアウトバウンド トラフィックを許可するように、会社のファイアウォールを構成します。 後者は、Azure セキュリティ トークン サービスです。統合ランタイムがアクセス トークンを取得するためには、このサービスと通信を行う必要があります。

## <a name="get-started"></a>作業開始

> [!TIP]
> Azure Data Lake Store コネクタの使い方のチュートリアルについては、[Azure Data Lake Store へのデータの読み込み](load-azure-data-lake-store.md)に関する記事を参照してください。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、Azure Data Lake Store に固有の Data Factory エンティティを定義するために使用されるプロパティについて説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Data Lake Store のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | `type` プロパティは **AzureDataLakeStore** に設定する必要があります。 | はい |
| dataLakeStoreUri | Azure Data Lake Store アカウントに関する情報です。 この情報の形式は、`https://[accountname].azuredatalakestore.net/webhdfs/v1` または `adl://[accountname].azuredatalakestore.net/` です。 | はい |
| subscriptionId | Data Lake Store アカウントが属している Azure サブスクリプション ID です。 | シンクでは必須 |
| resourceGroupName | Data Lake Store アカウントが属している Azure リソース グループ名です。 | シンクでは必須 |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムを使うか、データ ストアがプライベート ネットワークにある場合はセルフホステッド統合ランタイムを使うことができます。 このプロパティを指定しないと、既定の Azure 統合ランタイムが使われます。 |いいえ |

### <a name="use-service-principal-authentication"></a>サービス プリンシパル認証を使用する

サービス プリンシパル認証を使用するには、Azure Active Directory にアプリケーション エンティティを登録し、Data Lake Store へのアクセス権を付与します。 詳細な手順については、「[サービス間認証](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)」を参照してください。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。

- アプリケーション ID
- アプリケーション キー
- テナント ID

>[!IMPORTANT]
> サービス プリンシパルに Data Lake Store での適切なアクセス許可を付与します。
>- **ソースとして**: フォルダーとサブフォルダーにあるファイルを一覧表示してコピーするためには、 **[データ エクスプローラー]**  >  **[アクセス]** で、少なくとも**読み取り + 実行**アクセス許可を付与します。 1 つのファイルをコピーするのであれば、**読み取り**アクセス許可の付与でかまいません。 **[このフォルダーとすべての子]** を選択して再帰的に追加したり、 **[アクセス許可エントリと既定のアクセス許可エントリ]** として追加したりすることができます。 アカウント レベルのアクセスの制御 (IAM) に関する要件はありません。
>- **シンクとして**: フォルダー内に子項目を作成するためには、 **[データ エクスプローラー]**  >  **[アクセス]** で、少なくとも**書き込み + 実行**アクセス許可を付与します。 **[このフォルダーとすべての子]** を選択して再帰的に追加したり、 **[アクセス許可エントリと既定のアクセス許可エントリ]** として追加したりすることができます。 Azure 統合ランタイムを使用してコピーする (ソースとシンクの両方がクラウドに存在する) 場合は、Data Factory で Data Lake Store のリージョンを検出させるために、IAM で少なくとも**閲覧者**ロールを付与します。 この IAM ロールを避けたい場合は、Data Lake Store の場所を使用して明示的に [Azure 統合ランタイムを作成](create-azure-integration-runtime.md#create-azure-ir)してください。 たとえば、Data Lake Store が西ヨーロッパにある場合、場所を "西ヨーロッパ" に設定した Azure 統合ランタイムを作成します。 次の例で示すように、Data Lake Store のリンクされたサービスでそれらを関連付けます。

>[!NOTE]
>ルートを起点としてフォルダーを一覧表示するには、サービス プリンシパルのアクセス許可を**ルート レベルで "実行" 権限**が付与されるように設定する必要があります。 これは、以下を使用する場合に該当します。
>- **データ コピー ツール** (コピー パイプラインを作成する)。
>- **Data Factory UI** (接続およびフォルダーのナビゲーションを作成中にテストする)。
>ルート レベルでのアクセス許可の付与に関して問題がある場合は、作成中に、接続のテストをスキップし、アクセス許可が付与された親パスを入力した後、その指定したパスから参照することを選択します。 コピー アクティビティは、コピーされるファイルで、サービス プリンシパルに適切なアクセス許可が与えられている限り機能します。

次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | はい |
| servicePrincipalKey | アプリケーションのキーを取得します。 このフィールドを `SecureString` としてマークして Data Factory に安全に格納するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名やテナント ID など) を指定します。 Azure Portal の右上隅をマウスでポイントすることにより取得できます。 | はい |

**例:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Azure リソースの認証にマネージド ID を使用する

データ ファクトリは、特定のデータ ファクトリを表す、[Azure リソースのマネージド ID](data-factory-service-identity.md) に関連付けることができます。 独自のサービス プリンシパルを使用するのと同様に、Data Lake Store 認証にこのマネージド ID を直接使用できます。 これにより、この指定されたファクトリは、Data Lake Store にアクセスしてデータをコピーできます。

Azure リソースのマネージド ID 認証を使用するには、次のようにします。

1. ファクトリと共に生成された "サービス ID アプリケーション ID" の値をコピーして、[データ ファクトリのマネージド ID 情報を取得](data-factory-service-identity.md#retrieve-managed-identity)します。
2. サービス プリンシパルの場合と同じように、以下の注意に従って Data Lake Store へのアクセス権をマネージド ID に付与します。

>[!IMPORTANT]
> Data Lake Store でデータ ファクトリ マネージド ID に適切なアクセス許可を付与してください。
>- **ソースとして**: フォルダーとサブフォルダーにあるファイルを一覧表示してコピーするためには、 **[データ エクスプローラー]**  >  **[アクセス]** で、少なくとも**読み取り + 実行**アクセス許可を付与します。 1 つのファイルをコピーするのであれば、**読み取り**アクセス許可の付与でかまいません。 **[このフォルダーとすべての子]** を選択して再帰的に追加したり、 **[アクセス許可エントリと既定のアクセス許可エントリ]** として追加したりすることができます。 アカウント レベルのアクセスの制御 (IAM) に関する要件はありません。
>- **シンクとして**: フォルダー内に子項目を作成するためには、 **[データ エクスプローラー]**  >  **[アクセス]** で、少なくとも**書き込み + 実行**アクセス許可を付与します。 **[このフォルダーとすべての子]** を選択して再帰的に追加したり、 **[アクセス許可エントリと既定のアクセス許可エントリ]** として追加したりすることができます。 Azure 統合ランタイムを使用してコピーする (ソースとシンクの両方がクラウドに存在する) 場合は、Data Factory で Data Lake Store のリージョンを検出させるために、IAM で少なくとも**閲覧者**ロールを付与します。 この IAM ロールを避けたい場合は、Data Lake Store の場所を使用して明示的に [Azure 統合ランタイムを作成](create-azure-integration-runtime.md#create-azure-ir)してください。 次の例で示すように、Data Lake Store のリンクされたサービスでそれらを関連付けます。

>[!NOTE]
>ルートを起点としてフォルダーを一覧表示するには、マネージド ID のアクセス許可を**ルート レベルで "実行" 権限**が付与されるように設定する必要があります。 これは、以下を使用する場合に該当します。
>- **データ コピー ツール** (コピー パイプラインを作成する)。
>- **Data Factory UI** (接続およびフォルダーのナビゲーションを作成中にテストする)。
>ルート レベルでのアクセス許可の付与に関して問題がある場合は、作成中に、接続のテストをスキップし、アクセス許可が付与された親パスを入力した後、その指定したパスから参照することを選択します。 コピー アクティビティは、コピーされるファイルで、サービス プリンシパルに適切なアクセス許可が与えられている限り機能します。

Azure Data Factory では、リンクされたサービスの Data Lake Store の一般的な情報以外にプロパティを指定する必要はありません。

**例:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 

- Parquet 形式と区切りテキスト形式については、「[Parquet 形式と区切りテキスト形式のデータセット](#parquet-and-delimited-text-format-dataset)」セクションをご覧ください。
- ORC、Avro、JSON、またはバイナリ形式などの他の形式については、「[他の形式のデータセット](#other-format-dataset)」セクションをご覧ください。

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet 形式と区切りテキスト形式のデータセット

Azure Data Lake Store Gen1 をコピー先またはコピー元とし、Parquet 形式または区切りテキスト形式のデータをコピーするには、[Parquet 形式](format-parquet.md)および[区切りテキスト形式](format-delimited-text.md)に関する記事で、形式ベースのデータセットおよびサポートされる設定について参照してください。 Azure Data Lake Store Gen1 では、形式ベースのデータセットの `location` 設定において、次のプロパティがサポートされています。

| プロパティ   | 説明                                                  | 必須 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | データセットの `location` の type プロパティは、**AzureDataLakeStoreLocation** に設定する必要があります。 | はい      |
| folderPath | フォルダーのパス。 フォルダーをフィルター処理するためにワイルドカードを使用する場合は、この設定をスキップし、アクティビティのソースの設定で指定します。 | いいえ       |
| fileName   | 特定の folderPath の下のファイル名。 ファイルをフィルター処理するためにワイルドカードを使用する場合は、この設定をスキップし、アクティビティのソースの設定で指定します。 | いいえ       |

> [!NOTE]
>
> 次のセクションで説明する Parquet 形式またはテキスト形式の **AzureDataLakeStoreFile** 型データセットは、下位互換性のために引き続きコピー、ルックアップ、GetMetadata アクティビティでそのままサポートされます。 ただし、マッピング データ フロー機能では動作しません。 今後は、この新しいモデルを使うことをお勧めします。 Data Factory 作成 UI では、これらの新しい型が生成されます。

**例:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
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

Azure Data Lake Store Gen1 をコピー先またはコピー元として、ORC、Avro、JSON、バイナリ形式のデータをコピーする場合は、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティには、**AzureDataLakeStoreFile** を設定する必要があります。 |はい |
| folderPath | Data Lake Store のフォルダーへのパス。 指定しないと、ルートが参照されます。 <br/><br/>ワイルドカード フィルターがサポートされています。 使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br/><br/>例: rootfolder/subfolder/。 「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 |いいえ |
| fileName | 指定された "folderPath" の下にあるファイルの名前またはワイルドカード フィルター。 このプロパティの値を指定しない場合、データセットはフォルダー内のすべてのファイルをポイントします。 <br/><br/>フィルターに使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。<br/>- 例 1: `"fileName": "*.csv"`<br/>- 例 2: `"fileName": "???20180427.txt"`<br/>実際のファイル名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。<br/><br/>出力データセットに fileName の指定がなく、アクティビティ シンクに **preserveHierarchy** の指定がない場合、コピー アクティビティは、"*Data.[activity run ID GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]* " というパターンでファイル名が自動的に生成されます。たとえば、"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz" などです。 クエリの代わりにテーブル名を使用して表形式のソースからコピーする場合、名前のパターンは " *[table name].[format].[compression if configured]* " となります。たとえば、"MyTable.csv" などです。 |いいえ |
| modifiedDatetimeStart | ファイルはフィルター処理され、元になる属性は最終更新時刻です。 最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> 多数のファイルでファイル フィルターを実行する場合は、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けます。 <br/><br/> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する NULL にすることができます。 `modifiedDatetimeStart` に datetime 値を設定し、`modifiedDatetimeEnd` を NULL にした場合は、最終更新時刻属性が datetime 値以上であるファイルが選択されることを意味します。 `modifiedDatetimeEnd` に datetime 値を設定し、`modifiedDatetimeStart` を NULL にした場合は、最終更新時刻属性が datetime 値以下であるファイルが選択されることを意味します。| いいえ |
| modifiedDatetimeEnd | ファイルはフィルター処理され、元になる属性は最終更新時刻です。 最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> 多数のファイルでファイル フィルターを実行する場合は、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けます。 <br/><br/> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する NULL にすることができます。 `modifiedDatetimeStart` に datetime 値を設定し、`modifiedDatetimeEnd` を NULL にした場合は、最終更新時刻属性が datetime 値以上であるファイルが選択されることを意味します。 `modifiedDatetimeEnd` に datetime 値を設定し、`modifiedDatetimeStart` を NULL にした場合は、最終更新時刻属性が datetime 値以下であるファイルが選択されることを意味します。| いいえ |
| format | ファイルベースのストア間でファイルをそのままコピー (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。<br/><br/>特定の形式のファイルを解析または生成する場合、サポートされるファイル形式の種類は、**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 **format** の **type** プロパティをいずれかの値に設定します。 詳細については、[Text 形式](supported-file-formats-and-compression-codecs.md#text-format)、[Json 形式](supported-file-formats-and-compression-codecs.md#json-format)、[Avro 形式](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc 形式](supported-file-formats-and-compression-codecs.md#orc-format)、[Parquet 形式](supported-file-formats-and-compression-codecs.md#parquet-format) の各セクションを参照してください。 |いいえ (バイナリ コピー シナリオのみ) |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md#compression-support)に関する記事を参照してください。<br/>サポートされる種類は、**GZip**、**Deflate**、**BZip2**、および **ZipDeflate** です。<br/>サポートされるレベルは、**Optimal** と **Fastest** です。 |いいえ |


>[!TIP]
>フォルダーの下のすべてのファイルをコピーするには、**folderPath** のみを指定します。<br>特定の名前が付いた単一のファイルをコピーするには、フォルダー部分で **folderPath**、ファイル名で **fileName** を指定します。<br>フォルダーの下のファイルのサブセットをコピーするには、フォルダー部分で **folderPath**、ワイルドカード フィルターで **fileName** を指定します。 

**例:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
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

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関するページを参照してください。 このセクションでは、Azure Data Lake Store のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-data-lake-store-as-source"></a>ソースとしての Azure Data Lake Store

- Parquet 形式または区切りテキスト形式からコピーする場合は、「[Parquet 形式と区切りテキスト形式のソース](#parquet-and-delimited-text-format-source)」セクションを参照してください。
- ORC、Avro、JSON、バイナリ形式などの他の形式からコピーする場合は、「[他の形式のソース](#other-format-source)」セクションを参照してください。

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet 形式と区切りテキスト形式のソース

Azure Data Lake Store Gen1 から、Parquet 形式または区切りテキスト形式のデータをコピーするには、[Parquet 形式](format-parquet.md)および[区切りテキスト形式](format-delimited-text.md)に関する記事で、形式ベースのコピー アクティビティ ソースおよびサポートされる設定について参照してください。 Azure Data Lake Store Gen1 では、形式ベースのコピー ソースの `storeSettings` 設定において、次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` の type プロパティは **AzureDataLakeStoreReadSetting** に設定する必要があります。 | はい                                           |
| recursive                | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 recursive が true に設定されていて、シンクがファイル ベースのストアである場合、シンクでは空のフォルダーまたはサブフォルダーがコピーも作成もされません。 使用可能な値: **true** (既定値) および **false**。 | いいえ                                            |
| wildcardFolderPath       | ソース フォルダーをフィルター処理するための、ワイルドカード文字を含むフォルダー パス。 <br>使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br>「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | いいえ                                            |
| wildcardFileName         | ソース ファイルをフィルター処理するための、特定の folderPath/wildcardFolderPath の下のワイルドカード文字を含むファイル名。 <br>使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | はい (データセットで `fileName` が指定されていない場合) |
| modifiedDatetimeStart    | ファイルはフィルター処理され、元になる属性は最終更新時刻です。 最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する NULL にすることができます。 `modifiedDatetimeStart` に datetime 値を設定し、`modifiedDatetimeEnd` を NULL にした場合は、最終更新時刻属性が datetime 値以上であるファイルが選択されることを意味します。 `modifiedDatetimeEnd` に datetime 値を設定し、`modifiedDatetimeStart` を NULL にした場合は、最終更新時刻属性が datetime 値以下であるファイルが選択されることを意味します。 | いいえ                                            |
| modifiedDatetimeEnd      | 上記と同じです。                                               | いいえ                                            |
| maxConcurrentConnections | 同時にストレージ ストアに接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ                                            |

> [!NOTE]
> Parquet 形式または区切りテキスト形式の場合、次のセクションで説明する **AzureDataLakeStoreSource** 型のコピー アクティビティ ソースは、下位互換性のために引き続きそのままサポートされます。 今後は、この新しいモデルを使うことをお勧めします。 Data Factory 作成 UI では、これらの新しい型が生成されます。

**例:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
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
                    "type": "AzureDataLakeStoreReadSetting",
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

ORC、Avro、JSON、またはバイナリ形式のデータを Azure Data Lake Store Gen1 からコピーする場合、コピー アクティビティの **source** セクションで次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの `type` プロパティは **AzureDataLakeStoreSource** に設定する必要があります。 |はい |
| recursive | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 `recursive` が true に設定されていて、シンクがファイル ベースのストアである場合、シンクでは空のフォルダーまたはサブフォルダーがコピーも作成もされません。 使用可能な値: **true** (既定値) および **false**。 | いいえ |
| maxConcurrentConnections | 同時にデータ ストアに接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>シンクとしての Azure Data Lake Store

- Parquet 形式および区切りテキスト形式にコピーする場合は、「[Parquet 形式と区切りテキスト形式のシンク](#parquet-and-delimited-text-format-sink)」セクションを参照してください。
- ORC、Avro、JSON、バイナリ形式などの他の形式にコピーする場合は、「[他の形式のシンク](#other-format-sink)」セクションを参照してください。

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet 形式と区切りテキスト形式のシンク

Azure Data Lake Store Gen1 に、Parquet 形式または区切りテキスト形式のデータをコピーするには、[Parquet 形式](format-parquet.md)および[区切りテキスト形式](format-delimited-text.md)に関する記事で、形式ベースのコピー アクティビティ シンクおよびサポートされる設定について参照してください。 Azure Data Lake Store Gen1 では、形式ベースのコピー シンクの `storeSettings` 設定において、次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` の type プロパティは **AzureDataLakeStoreWriteSetting** に設定する必要があります。 | はい      |
| copyBehavior             | ソースがファイル ベースのデータ ストアのファイルの場合は、コピー動作を定義します。<br/><br/>使用できる値は、以下のとおりです。<br/><b>- PreserveHierarchy (既定値)</b>:ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーへのソース ファイルの相対パスはターゲット フォルダーへのターゲット ファイルの相対パスと同じになります。<br/><b>- FlattenHierarchy</b>:ソース フォルダーのすべてのファイルをターゲット フォルダーの第一レベルに配置します。 ターゲット ファイルは、自動生成された名前になります。 <br/><b>- MergeFiles</b>:ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル名を指定した場合、マージされたファイル名は指定した名前になります。 それ以外は自動生成されたファイル名になります。 | いいえ       |
| maxConcurrentConnections | 同時にデータ ストアに接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ       |

> [!NOTE]
> Parquet 形式または区切りテキスト形式の場合、次のセクションで説明する **AzureDataLakeStoreSink** 型のコピー アクティビティ シンクは、下位互換性のために引き続きそのままサポートされます。 今後は、この新しいモデルを使うことをお勧めします。 Data Factory 作成 UI では、これらの新しい型が生成されます。

**例:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
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
                    "type": "AzureDataLakeStoreWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>他の形式のシンク

Azure Data Lake Store Gen1 に、ORC、Avro、JSON、またはバイナリ形式のデータをコピーする場合は、**sink** セクションで次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの `type` プロパティは、**AzureDataLakeStoreSink** に設定する必要があります。 |はい |
| copyBehavior | ソースがファイル ベースのデータ ストアのファイルの場合は、コピー動作を定義します。<br/><br/>使用できる値は、以下のとおりです。<br/><b>- PreserveHierarchy (既定値)</b>:ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーへのソース ファイルの相対パスはターゲット フォルダーへのターゲット ファイルの相対パスと同じになります。<br/><b>- FlattenHierarchy</b>:ソース フォルダーのすべてのファイルをターゲット フォルダーの第一レベルに配置します。 ターゲット ファイルは、自動生成された名前になります。 <br/><b>- MergeFiles</b>:ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル名を指定した場合、マージされたファイル名は指定した名前になります。 指定しないと、ファイル名は自動生成されます。 | いいえ |
| maxConcurrentConnections | 同時にデータ ストアに接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
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

### <a name="examples-of-behavior-of-the-copy-operation"></a>コピー操作の動作の例

このセクションでは、コピー操作の動作から得られる結果を、`recursive` 値と `copyBehavior` 値の組み合わせごとに説明します。

| recursive | copyBehavior | ソースのフォルダー構造 | ターゲットの結果 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット Folder1 は、ソースと同じ構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 の自動生成された名前 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2、File3、File4、File5 の内容は 1 つのファイルにマージされて、自動生成されたファイル名が付けられます。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2 の内容は 1 つのファイルにマージされ、自動生成されたファイル名が付けられます。 File1 の自動生成された名前<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Data Lake Storage Gen2 に ACL を保持する

Data Lake Storage Gen1 から Data Lake Storage Gen2 にアップグレードするときに、アクセス制御リスト (ACL) をデータ ファイルと共にレプリケートする必要がある場合は、「[Data Lake Storage Gen1 の ACL を保持する](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)」をご覧ください。

## <a name="mapping-data-flow-properties"></a>マッピング データ フローのプロパティ

マッピング データ フロー機能の[ソース変換](data-flow-source.md)と[シンク変換](data-flow-sink.md)に関する記事で詳細を確認してください。

## <a name="next-steps"></a>次の手順

Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
