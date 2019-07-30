---
title: Data Factory を使用して Azure Blob Storage をコピー先またはコピー元としてデータをコピーする | Microsoft Docs
description: Data Factory を使用して、サポートされるソース データ ストアから Azure Blob Storage にデータをコピーしたり、Blob Storage からサポートされるシンク ストアにコピーしたりする方法を説明します。
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 3be075b78d8388b7146a9a3180ca825fc6476108
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206045"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Azure Data Factory を使用した Azure Blob Storage との間でのデータのコピー
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-azure-blob-connector.md)
> * [現在のバージョン](connector-azure-blob-storage.md)

この記事では、Azure Blob Storage との間でデータを双方向にコピーする方法について説明します。 Azure Data Factory については、[入門記事で](introduction.md)をご覧ください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>サポートされる機能

この Azure BLOB コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)

具体的には、この Blob Storage コネクタは、以下をサポートします。

- 汎用 Azure Storage アカウントとホット/クール Blob Storage との間での BLOB のコピー。 
- アカウント キー、サービスの Shared Access Signature、サービス プリンシパル、または Azure リソースのマネージド ID のいずれかの認証を使用した BLOB のコピー。
- ブロック BLOB、アペンド BLOB、またはページ BLOB からの BLOB のコピーと、ブロック BLOB だけへのデータのコピー。
- そのままの BLOB のコピー、または[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)を使用した BLOB の解析/生成。

>[!NOTE]
>Azure Storage のファイアウォール設定で _信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します_ オプションを有効にした場合、Azure Integration Runtime を使用した Azure Blob Storage への接続は、ADF が信頼された Microsoft サービスとして扱われないため、アクセス不可エラーで失敗します。 代わりに、セルフホステッド統合ランタイムを使用して接続してください。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Blob Storage に固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure BLOB コネクタは、次の認証の種類をサポートします。詳しくは、対応するセクションをご覧ください。

- [アカウント キー認証](#account-key-authentication)
- [Shared Access Signature 認証](#shared-access-signature-authentication)
- [サービス プリンシパルの認証](#service-principal-authentication)
- [Azure リソースのマネージド ID 認証](#managed-identity)

>[!NOTE]
>ソースまたはステージング BLOB ストレージが Virtual Network エンドポイントで構成されており、PolyBase を使用して SQL Data Warehouse にデータを読み込む場合、PolyBase で要求されるマネージド ID 認証とバージョン 3.18 以降のセルフホステッド統合ランタイムを使用する必要があります。 構成の前提条件の詳細については、[マネージド ID 認証](#managed-identity)セクションを参照してください。

>[!NOTE]
>HDInsights および Azure Machine Learning のアクティビティは、Azure BLOB ストレージ アカウント キー認証のみをサポートします。

### <a name="account-key-authentication"></a>アカウント キー認証

ストレージ アカウント キー認証の使用には、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、**AzureBlobStorage** (推奨) または **AzureStorage** (後の注を参照) に設定する必要があります。 |はい |
| connectionString | connectionString プロパティのために Storage に接続するために必要な情報を指定します。 <br/>Data Factory に安全に格納するには、このフィールドを SecureString として指定します。 アカウント キーを Azure Key Vault に格納して、接続文字列から `accountKey` 構成をプルすることもできます。 詳細については、下記の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事を参照してください。 |はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用できます (データ ストアがプライベート ネットワークにある場合)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

>[!NOTE]
>"AzureStorage" タイプのリンクされたサービスを使用していた場合は、まだそのままサポートされていますが、今後は新しい "AzureBlobStorage" のリンクされたサービス タイプを使用することをお勧めします。

**例:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例: アカウント キーを Azure Key Vault に格納する**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>Shared Access Signature 認証

Shared Access Signature を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 Shared Access Signature を使用して、ストレージ アカウントのオブジェクトへの制限付きアクセス許可を、期間を指定してクライアントに付与できます。 アカウントのアクセス キーを共有する必要はありません。 Shared Access Signature とは、ストレージ リソースへの認証アクセスに必要なすべての情報をクエリ パラメーター内に含む URI です。 クライアントは、Shared Access Signature 内で適切なコンストラクターまたはメソッドに渡すだけで、Shared Access Signature でストレージ リソースにアクセスできます。 Shared Access Signature について詳しくは、[Shared Access Signature のモデルの概要](../storage/common/storage-dotnet-shared-access-signature-part-1.md)に関するページをご覧ください。

> [!NOTE]
>- Data Factory で、**サービスの Shared Access Signature** と**アカウントの Shared Access Signature** がサポートされるようになりました。 この 2 種類の SAS とその作成方法について詳しくは、「[共有アクセス署名の種類](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures)」を参照してください。
>- 後のデータセットの構成では、フォルダーのパスはコンテナー レベルから始まる絶対パスです。 SAS URI のパスに揃えて構成する必要があります。

> [!TIP]
> ストレージ アカウントに使用するサービスの Shared Access Signature を生成するには、次の PowerShell コマンドを実行します。 プレースホルダーを置き換えたうえで、必要なアクセス許可を付与してください。
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Shared Access Signature 認証の使用には、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、**AzureBlobStorage** (推奨) または **AzureStorage** (後の注を参照) に設定する必要があります。 |はい |
| sasUri | BLOB、コンテナーなどの Storage リソースへの Shared Access Signature URI を指定します。 <br/>Data Factory に安全に格納するには、このフィールドを SecureString として指定します。 自動ローテーションを活用してトークン部分を削除するために、SAS トークンを Azure Key Vault に配置することもできます。 詳細については、下記の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事を参照してください。 |はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用できます (データ ストアがプライベート ネットワークにある場合)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

>[!NOTE]
>"AzureStorage" タイプのリンクされたサービスを使用していた場合は、まだそのままサポートされていますが、今後は新しい "AzureBlobStorage" のリンクされたサービス タイプを使用することをお勧めします。

**例:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<container>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例: アカウント キーを Azure Key Vault に格納する**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<container>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Shared Access Signature の URI を作成する際は、次の点に注意してください。

- リンクされたサービス (読み取り、書き込み、読み取り/書き込み) がデータ ファクトリ内でどのように使用されているかに応じて、オブジェクトに対する適切な読み取り/書き込みアクセス許可を設定します。
- **有効期限**を適切に設定します。 Storage オブジェクトへのアクセスがパイプラインのアクティブな期間内に期限切れにならないことを確認します。
- URI は、必要に応じて、適切なコンテナーや BLOB で作成する必要があります。 Data Factory から特定の BLOB にアクセスするには、その BLOB の Shared Access Signature URI を使用します。 Data Factory で Blob Storage コンテナー内の BLOB を反復処理するには、そのコンテナーの Shared Access Signature URI を使用します。 アクセスするオブジェクトの数を後で変更する場合、または Shared Access Signature URI を更新する場合は必ず、リンクされたサービスを新しい URI で更新してください。

### <a name="service-principal-authentication"></a>サービス プリンシパルの認証

一般的な Azure Storage サービス プリンシパル認証については、「[Azure Active Directory を使用して Azure Storage へのアクセスを認証する](../storage/common/storage-auth-aad.md)」をご覧ください。

サービス プリンシパル認証を使用するには、次の手順のようにします。

1. 「[アプリケーションを Azure AD テナントに登録する](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)」に従って、Azure Active Directory (Azure AD) にアプリケーション エンティティを登録します。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。

    - アプリケーション ID
    - アプリケーション キー
    - テナント ID

2. Azure BLOB ストレージでサービス プリンシパルに適切なアクセス許可を付与します。 ロールについて詳しくは、「[RBAC を使用して Azure Storage データへのアクセス権を管理する](../storage/common/storage-auth-aad-rbac.md)」をご覧ください。

    - **ソースとして**、[アクセス制御 (IAM)] で、少なくとも**ストレージ BLOB データ閲覧者**ロールを付与します。
    - **シンクとして**、[アクセス制御 (IAM)] で、少なくとも**ストレージ BLOB データ共同作成者**ロールを付与します。

Azure BLOB ストレージのリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、**AzureBlobStorage** に設定する必要があります。 |はい |
| serviceEndpoint | `https://<accountName>.blob.core.windows.net/` のパターンで、Azure BLOB ストレージ サービス エンドポイントを指定します。 |はい |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | はい |
| servicePrincipalKey | アプリケーションのキーを取得します。 このフィールドを **SecureString** としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure portal の右上隅にマウスを置くことで取得します。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用できます (データ ストアがプライベート ネットワークにある場合)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

>[!NOTE]
>サービス プリンシパル認証は、"AzureBlobStorage" タイプのリンクされたサービスによってのみサポートされており、以前の "AzureStorage" タイプのリンクされたサービスではサポートされていません。

**例:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
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

データ ファクトリは、特定のデータ ファクトリを表す、[Azure リソースのマネージド ID](data-factory-service-identity.md) に関連付けることができます。 独自のサービス プリンシパルを使用するのと同様に、BLOB ストレージ認証にこのマネージド ID を直接使用できます。 これにより、この指定されたファクトリは、BLOB ストレージにアクセスしてデータをコピーできます。

一般的な Azure Storage 認証については、「[Azure Active Directory を使用して Azure Storage へのアクセスを認証する](../storage/common/storage-auth-aad.md)」をご覧ください。 Azure リソースのマネージド ID 認証を使用するには、次のようにします。

1. ファクトリと共に生成された "サービス ID アプリケーション ID" の値をコピーして、[データ ファクトリのマネージド ID 情報を取得](data-factory-service-identity.md#retrieve-managed-identity)します。

2. Azure BLOB ストレージでマネージド ID に適切なアクセス許可を付与します。 ロールについて詳しくは、「[RBAC を使用して Azure Storage データへのアクセス権を管理する](../storage/common/storage-auth-aad-rbac.md)」をご覧ください。

    - **ソースとして**、[アクセス制御 (IAM)] で、少なくとも**ストレージ BLOB データ閲覧者**ロールを付与します。
    - **シンクとして**、[アクセス制御 (IAM)] で、少なくとも**ストレージ BLOB データ共同作成者**ロールを付与します。

>[!IMPORTANT]
>PolyBase を使用して BLOB (ソースまたはステージングとして) から SQL Data Warehouse にデータを読み込む場合、BLOB にマネージド ID 認証を使用しているときは、[こちらのガイダンス](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)の手順 1 と 2 にも従って、1) SQL Database サーバーを Azure Active Directory (Azure AD) に登録し、2) SQL Database サーバーにストレージ BLOB データ共同作成者のロールを割り当ててください。残りの部分は Data Factory によって処理されます。 お使いの BLOB ストレージが Azure Virtual Network エンドポイントで構成されている場合、PolyBase を使用してそこからデータを読み込むには、PolyBase で要求されるマネージド ID 認証を使用する必要があります。

Azure BLOB ストレージのリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、**AzureBlobStorage** に設定する必要があります。 |はい |
| serviceEndpoint | `https://<accountName>.blob.core.windows.net/` のパターンで、Azure BLOB ストレージ サービス エンドポイントを指定します。 |はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用できます (データ ストアがプライベート ネットワークにある場合)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

> [!NOTE]
> Azure リソースのマネージド ID 認証は、"AzureBlobStorage" タイプのリンクされたサービスによってのみサポートされており、以前の "AzureStorage" タイプのリンクされたサービスではサポートされていません。 

**例:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
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

- **Parquet 形式と区切りテキスト形式**については、「[Parquet 形式と区切りテキスト形式のデータセット](#parquet-and-delimited-text-format-dataset)」セクションを参照してください。
- **ORC/Avro/JSON/バイナリ形式**などのその他の形式については、「[他の形式のデータセット](#other-format-dataset)」セクションを参照してください。

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet 形式と区切りテキスト形式のデータセット

Blob Storage をコピー先またはコピー元として Parquet 形式または区切りテキスト形式のデータをコピーする場合は、[Parquet 形式](format-parquet.md)および[区切りテキスト形式](format-delimited-text.md)に関する記事で、形式ベースのデータセットおよびサポートされる設定について参照してください。 Azure BLOB では、形式ベースのデータセットの `location` 設定において、次のプロパティがサポートされています。

| プロパティ   | 説明                                                  | 必須 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | データセット内の location の type プロパティは、**AzureBlobStorageLocation** に設定する必要があります。 | はい      |
| container  | BLOB コンテナー。                                          | はい      |
| folderPath | 特定のコンテナーの下のフォルダーへのパス。 フォルダーをフィルター処理するためにワイルドカードを使用する場合は、この設定をスキップし、アクティビティのソースの設定で指定します。 | いいえ       |
| fileName   | 特定のコンテナー + folderPath の下のファイル名。 ファイルをフィルター処理するためにワイルドカードを使用する場合は、この設定をスキップし、アクティビティのソースの設定で指定します。 | いいえ       |

> [!NOTE]
>
> 次のセクションで説明する Parquet/テキスト形式の **AzureBlob** 型のデータセットは、下位互換性のために引き続きコピー/Lookup/GetMetadata アクティビティでそのままサポートされますが、Mapping Data Flow では機能しません。 今後は、この新しいモデルを使用することをお勧めします。ADF オーサリング UI はこれらの新しい型を生成するように切り替えられています。

**例:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
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

Blob Storage をコピー先またはコピー元として ORC/Avro/JSON/Binary 形式でデータをコピーするには、データセットの type プロパティを **AzureBlob** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**AzureBlob** に設定する必要があります。 |はい |
| folderPath | BLOB ストレージのコンテナーとフォルダーのパス。 <br/><br/>ワイルドカード フィルターは、コンテナー名を除くパスに対してサポートされます。 使用できるワイルドカーは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br/><br/>例: myblobcontainer/myblobfolder/。「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の例を参照してください。 |はい (Copy/Lookup アクティビティの場合)、いいえ (GetMetadata アクティビティの場合) |
| fileName | 指定された "folderPath" の下にある BLOB の**名前またはワイルドカード フィルター**。 このプロパティの値を指定しない場合、データセットはフォルダー内のすべての BLOB をポイントします。 <br/><br/>フィルターに使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。<br/>- 例 1: `"fileName": "*.csv"`<br/>- 例 2: `"fileName": "???20180427.txt"`<br/>実際のファイル名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。<br/><br/>出力データセットに fileName の指定がなく、アクティビティ シンクに **preserveHierarchy** の指定がない場合、コピー アクティビティによって自動的に生成される BLOB 名のパターンは、"*Data.[activity run ID GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]* " になります。たとえば、"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz" です。クエリの代わりにテーブル名を使用して表形式のソースからコピーする場合は、名前のパターンは " *[table name].[format].[compression if configured]* " になります。たとえば、"MyTable.csv" です。 |いいえ |
| modifiedDatetimeStart | ファイルはフィルター処理され、元になる属性は最終更新時刻です。 最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> 多数のファイルにファイル フィルターを実行する場合は、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けることに注意してください。 <br/><br/> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する NULL にすることができます。  `modifiedDatetimeStart` に datetime 値を設定し、`modifiedDatetimeEnd` を NULL にした場合は、最終更新時刻属性が datetime 値以上であるファイルが選択されることを意味します。  `modifiedDatetimeEnd` に datetime 値を設定し、`modifiedDatetimeStart` を NULL にした場合は、最終更新時刻属性が datetime 値以下であるファイルが選択されることを意味します。| いいえ |
| modifiedDatetimeEnd | ファイルはフィルター処理され、元になる属性は最終更新時刻です。 最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> 多数のファイルにファイル フィルターを実行する場合は、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けることに注意してください。 <br/><br/> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する NULL にすることができます。  `modifiedDatetimeStart` に datetime 値を設定し、`modifiedDatetimeEnd` を NULL にした場合は、最終更新時刻属性が datetime 値以上であるファイルが選択されることを意味します。  `modifiedDatetimeEnd` に datetime 値を設定し、`modifiedDatetimeStart` を NULL にした場合は、最終更新時刻属性が datetime 値以下であるファイルが選択されることを意味します。| いいえ |
| format | ファイルベースのストア間でファイルをそのままコピー (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。<br/><br/>特定の形式のファイルを解析または生成する場合、サポートされるファイル形式の種類は、**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 **format** の **type** プロパティをいずれかの値に設定します。 詳細については、[Text 形式](supported-file-formats-and-compression-codecs.md#text-format)、[Json 形式](supported-file-formats-and-compression-codecs.md#json-format)、[Avro 形式](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc 形式](supported-file-formats-and-compression-codecs.md#orc-format)、[Parquet 形式](supported-file-formats-and-compression-codecs.md#parquet-format) の各セクションを参照してください。 |いいえ (バイナリ コピー シナリオのみ) |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md#compression-support)に関する記事を参照してください。<br/>サポートされる種類は、**GZip**、**Deflate**、**BZip2**、および **ZipDeflate** です。<br/>サポートされるレベルは、**Optimal** と **Fastest** です。 |いいえ |

>[!TIP]
>フォルダーの下のすべての BLOB をコピーするには、**folderPath** のみを指定します。<br>特定の名前の単一の BLOB をコピーするには、フォルダー部分で **folderPath**、ファイル名で **fileName** を指定します。<br>フォルダーの下の BLOB のサブセットをコピーするには、フォルダー部分で **folderPath**、ワイルドカード フィルターで **fileName** を指定します。 

**例:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Blob Storage のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="blob-storage-as-a-source-type"></a>ソースの種類として Blob Storage を設定する

- コピー元から **Parquet 形式および区切りテキスト形式**でコピーする場合は、「[Parquet 形式と区切りテキスト形式のソース](#parquet-and-delimited-text-format-source)」セクションを参照してください。
- コピー元から **ORC/Avro/JSON/バイナリ形式**などの他の形式でコピーする場合は、「[他の形式のソース](#other-format-source)」セクションを参照してください。

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet 形式と区切りテキスト形式のソース

Parquet 形式または区切りテキスト形式のデータを Blob Storage からコピーする場合は、[Parquet 形式](format-parquet.md)および[区切りテキスト形式](format-delimited-text.md)に関する記事で、形式ベースのコピー アクティビティのソースと、サポートされる設定について参照してください。 Azure BLOB では、形式ベースのコピー ソースの `storeSettings` 設定において、次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` の type プロパティは **AzureBlobStorageReadSetting** に設定する必要があります。 | はい                                           |
| recursive                | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 recursive が true に設定され、シンクがファイル ベースのストアである場合、空のフォルダーおよびサブフォルダーはシンクでコピーも作成もされないことに注意してください。 使用可能な値: **true** (既定値) および **false**。 | いいえ                                            |
| wildcardFolderPath       | ソース フォルダーをフィルター処理するためにデータセットで構成されている、特定のコンテナーの下のワイルドカード文字を含むフォルダーのパス。 <br>使用できるワイルドカーは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br>「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | いいえ                                            |
| wildcardFileName         | ソース ファイルをフィルター処理するための、特定のコンテナー + folderPath/wildcardFolderPath の下のワイルドカード文字を含むファイル名。 <br>使用できるワイルドカーは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。  「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | はい (データセットで `fileName` が指定されていない場合) |
| modifiedDatetimeStart    | ファイルはフィルター処理され、元になる属性は最終更新時刻です。 最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する NULL にすることができます。  `modifiedDatetimeStart` に datetime 値を設定し、`modifiedDatetimeEnd` を NULL にした場合は、最終更新時刻属性が datetime 値以上であるファイルが選択されることを意味します。  `modifiedDatetimeEnd` に datetime 値を設定し、`modifiedDatetimeStart` を NULL にした場合は、最終更新時刻属性が datetime 値以下であるファイルが選択されることを意味します。 | いいえ                                            |
| modifiedDatetimeEnd      | 上記と同じです。                                               | いいえ                                            |
| maxConcurrentConnections | 同時にストレージ ストアに接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ                                            |

> [!NOTE]
> Parquet や区切りテキスト形式の場合、次のセクションで説明する **BlobSource** 型のコピー アクティビティ ソースは、下位互換性のために引き続きそのままサポートされます。 今後は、この新しいモデルを使用することをお勧めします。ADF オーサリング UI はこれらの新しい型を生成するように切り替えられています。

**例:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageReadSetting",
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

Blob Storage から ORC/Avro/JSON/Binary 形式でデータをコピーするには、コピー アクティビティのソースの種類を **BlobSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **BlobSource** に設定する必要があります |はい |
| recursive | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 recursive が true に設定され、シンクがファイル ベースのストアである場合、空のフォルダーおよびサブフォルダーはシンクでコピーも作成もされないことに注意してください。<br/>使用可能な値: **true** (既定値) および **false**。 | いいえ |
| maxConcurrentConnections | 同時にストレージ ストアに接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>シンクの種類として Blob Storage を設定する

- コピー先に **Parquet 形式および区切りテキスト形式**でコピーする場合は、「[Parquet 形式と区切りテキスト形式のシンク](#parquet-and-delimited-text-format-sink)」セクションを参照してください。
- コピー先に **ORC/Avro/JSON/バイナリ形式**などの他の形式でコピーする場合は、「[他の形式のシンク](#other-format-sink)」セクションを参照してください。

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet 形式と区切りテキスト形式のシンク

Parquet 形式または区切りテキスト形式のデータを Blob Storage にコピーする場合は、[Parquet 形式](format-parquet.md)および[区切りテキスト形式](format-delimited-text.md)に関する記事で、形式ベースのコピー アクティビティのシンクと、サポートされる設定について参照してください。 Azure BLOB では、形式ベースのコピー シンクの `storeSettings` 設定において、次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` の type プロパティは **AzureBlobStorageWriteSetting** に設定する必要があります。 | はい      |
| copyBehavior             | ソースがファイル ベースのデータ ストアのファイルの場合は、コピー動作を定義します。<br/><br/>使用できる値は、以下のとおりです。<br/><b>- PreserveHierarchy (既定値)</b>:ファイル階層をターゲット フォルダー内で保持します。 ソース フォルダーに対するソース ファイルの相対パスと、ターゲット フォルダーに対するターゲット ファイルの相対パスが一致します。<br/><b>- FlattenHierarchy</b>:ソース フォルダーのすべてのファイルをターゲット フォルダーの第一レベルに配置します。 ターゲット ファイルは、自動生成された名前になります。 <br/><b>- MergeFiles</b>:ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイルまたは BLOB の名前を指定した場合、マージされたファイル名は指定した名前になります。 それ以外は自動生成されたファイル名になります。 | いいえ       |
| maxConcurrentConnections | 同時にストレージ ストアに接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ       |

> [!NOTE]
> Parquet や区切りテキスト形式の場合、次のセクションで説明する **BlobSink** 型のコピー アクティビティ シンクは、下位互換性のために引き続きそのままサポートされます。 今後は、この新しいモデルを使用することをお勧めします。ADF オーサリング UI はこれらの新しい型を生成するように切り替えられています。

**例:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>他の形式のシンク

データを Blob Storage にコピーするには、コピー アクティビティのシンクの種類を **BlobSink** に設定します。 **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは **BlobSink** に設定する必要があります。 |はい |
| copyBehavior | ソースがファイル ベースのデータ ストアのファイルの場合は、コピー動作を定義します。<br/><br/>使用できる値は、以下のとおりです。<br/><b>- PreserveHierarchy (既定値)</b>:ファイル階層をターゲット フォルダー内で保持します。 ソース フォルダーに対するソース ファイルの相対パスと、ターゲット フォルダーに対するターゲット ファイルの相対パスが一致します。<br/><b>- FlattenHierarchy</b>:ソース フォルダーのすべてのファイルをターゲット フォルダーの第一レベルに配置します。 ターゲット ファイルは、自動生成された名前になります。 <br/><b>- MergeFiles</b>:ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイルまたは BLOB の名前を指定した場合、マージされたファイル名は指定した名前になります。 それ以外は自動生成されたファイル名になります。 | いいえ |
| maxConcurrentConnections | 同時にストレージ ストアに接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
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
| `container/Folder*` | (空、既定値を使用) | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (空、既定値を使用) | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>recursive と copyBehavior の例

このセクションでは、recursive 値と copyBhavior 値の組み合わせごとに、Copy 操作で行われる動作について説明します。

| recursive | copyBehavior | ソースのフォルダー構造 | ターゲットの結果 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、ソースと同じ構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 の自動生成された名前 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2、File3、File4、File5 の内容は 1 つのファイルにマージされて、自動生成されたファイル名が付けられます。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2 の内容は 1 つのファイルにマージされ、自動生成されたファイル名が付けられます。 File1 の自動生成された名前<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

Mapping Data Flow の[ソース変換](data-flow-source.md)と[シンク変換](data-flow-sink.md)に関する記事で詳細を確認してください。

## <a name="next-steps"></a>次の手順

Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
