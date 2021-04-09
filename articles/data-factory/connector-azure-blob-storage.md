---
title: Azure Blob Storage のデータをコピーし変換する
description: BLOB ストレージとの間でデータをコピーする方法、および Data Factory を使用して BLOB ストレージのデータを変換する方法について説明します。
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/08/2020
ms.openlocfilehash: 809dc6d0958b754911362f933e9fe964bce9c679
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727925"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure BLOB ストレージのデータをコピーおよび変換する

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択します。"]
> - [Version 1](v1/data-factory-azure-blob-connector.md)
> - [現在のバージョン](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure BLOB ストレージをコピー元またはコピー先としてデータをコピーする方法について説明します。 Data Flow アクティビティを使用して Azure BLOB ストレージ内のデータを変換する方法についても説明します。 Azure Data Factory については、[入門記事で](introduction.md)をご覧ください。

>[!TIP]
>データ レイクまたはデータ ウェアハウスの移行シナリオについては、「[Azure Data Factory を使用してデータ レイクまたはデータ ウェアハウスから Azure にデータを移行する](data-migration-guidance-overview.md)」を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

この Azure BLOB ストレージ コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [アクティビティを削除する](delete-activity.md)

コピー アクティビティの場合、この BLOB ストレージ コネクタは、以下をサポートします。

- 汎用 Azure Storage アカウントとホット/クール Blob Storage との間での BLOB のコピー。
- アカウント キー、サービスの Shared Access Signature (SAS)、サービス プリンシパル、Azure リソースのマネージド ID のいずれかの認証を使用した BLOB のコピー。
- ブロック BLOB、アペンド BLOB、またはページ BLOB からの BLOB のコピーと、ブロック BLOB だけへのデータのコピー。
- そのままの BLOB のコピー、または[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)を使用した BLOB の解析/生成。
- [コピー中のファイル メタデータの保持](#preserving-metadata-during-copy)。

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Blob Storage に固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

この BLOB ストレージ コネクタでは、次の認証の種類がサポートされています。 詳細については、対応するセクションをご覧ください。

- [アカウント キー認証](#account-key-authentication)
- [Shared Access Signature 認証](#shared-access-signature-authentication)
- [サービス プリンシパルの認証](#service-principal-authentication)
- [Azure リソース認証用のマネージド ID](#managed-identity)

>[!NOTE]
>- Azure Storage ファイアウォールで有効にした **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** オプションを利用することで、パブリック Azure 統合ランタイムを使用して BLOB ストレージに接続する場合は、[マネージド ID 認証](#managed-identity)を使用する必要があります。
>- PolyBase または COPY ステートメントを使用して Azure Synapse Analytics にデータを読み込むときに、ソースまたはステージング BLOB ストレージが Azure Virtual Network エンドポイントで構成されている場合は、Synapse の要求に従ってマネージド ID 認証を使用する必要があります。 構成の前提条件の詳細については、「[マネージド ID の認証](#managed-identity)」を参照してください。

>[!NOTE]
>Azure HDInsight および Azure Machine Learning のアクティビティは、Azure BLOB ストレージ アカウント キーを使用する認証のみをサポートします。

### <a name="account-key-authentication"></a>アカウント キー認証

Data Factory では、ストレージ アカウント キー認証用の次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | `type` プロパティは、`AzureBlobStorage` (推奨) または `AzureStorage` (下記の注を参照) に設定する必要があります。 | はい |
| connectionString | `connectionString` プロパティには、Storage に接続するために必要な情報を指定します。 <br/> アカウント キーを Azure Key Vault に格納して、接続文字列から `accountKey` 構成をプルすることもできます。 詳細については、下記の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事を参照してください。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 このプロパティが指定されていない場合は、サービスでは、既定の Azure Integration Runtime が使用されます。 | いいえ |

>[!NOTE]
>アカウント キー認証を使用する場合、セカンダリ BLOB サービス エンドポイントはサポートされません。 ほかの認証の種類を使用できます。

>[!NOTE]
>`AzureStorage` タイプのリンクされたサービスを使用している場合、そのままでも引き続きサポートされます。 ただし今後は、新しい `AzureBlobStorage` タイプのリンクされたサービスを使用することをお勧めします。

**例:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
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
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName>"
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

Shared Access Signature を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 Shared Access Signature を使用して、ストレージ アカウントのオブジェクトへの制限付きアクセス許可を、期間を指定してクライアントに付与できます。 

アカウントのアクセス キーを共有する必要はありません。 Shared Access Signature とは、ストレージ リソースへの認証アクセスに必要なすべての情報をクエリ パラメーター内に含む URI です。 クライアントは、Shared Access Signature 内で適切なコンストラクターまたはメソッドに渡すだけで、Shared Access Signature でストレージ リソースにアクセスできます。

Shared Access Signature について詳しくは、[Shared Access Signature のモデルの概要](../storage/common/storage-sas-overview.md)に関するページをご覧ください。

> [!NOTE]
>- Data Factory で、*サービスの Shared Access Signature* と *アカウントの Shared Access Signature* がサポートされるようになりました。 Shared Access Signature の詳細については、「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../storage/common/storage-sas-overview.md)」を参照してください。
>- 後のデータセットの構成では、フォルダーのパスはコンテナー レベルから始まる絶対パスです。 SAS URI のパスに揃えて構成する必要があります。

Data Factory では、Shared Access Signature 認証を使用するための次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | `type` プロパティは、`AzureBlobStorage` (推奨) または `AzureStorage` (下記の注を参照) に設定する必要があります。 | はい |
| sasUri | BLOB、コンテナーなどの Storage リソースへの Shared Access Signature URI を指定します。 <br/>Data Factory に安全に格納するには、このフィールドを `SecureString` として指定します。 自動ローテーションを使用してトークン部分を削除するために、SAS トークンを Azure Key Vault に配置することもできます。 詳細については、下記の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 このプロパティが指定されていない場合は、サービスでは、既定の Azure Integration Runtime が使用されます。 | いいえ |

>[!NOTE]
>`AzureStorage` タイプのリンクされたサービスを使用している場合、そのままでも引き続きサポートされます。 ただし今後は、新しい `AzureBlobStorage` タイプのリンクされたサービスを使用することをお勧めします。

**例:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<accountname>.blob.core.windows.net/?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
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
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.blob.core.windows.net/>"
            },
            "sasToken": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName with value of SAS token e.g. ?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
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
- **有効期限** を適切に設定します。 Storage オブジェクトへのアクセスがパイプラインのアクティブな期間内に期限切れにならないことを確認します。
- URI は、必要に応じて、適切なコンテナーまたは BLOB で作成する必要があります。 Data Factory から特定の BLOB にアクセスするには、その BLOB の Shared Access Signature URI を使用します。 Data Factory で Blob Storage コンテナー内の BLOB を反復処理するには、そのコンテナーの Shared Access Signature URI を使用します。 アクセスするオブジェクトの数を後で変更する場合、または Shared Access Signature URI を更新する場合は必ず、リンクされたサービスを新しい URI で更新してください。

### <a name="service-principal-authentication"></a>サービス プリンシパルの認証

Azure Storage サービス プリンシパル認証の全般的な情報については、「[Azure Active Directory を使用して Azure Storage へのアクセスを認証する](../storage/common/storage-auth-aad.md)」をご覧ください。

サービス プリンシパル認証を使用するには、次の手順のようにします。

1. 「[アプリケーションを Azure AD テナントに登録する](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)」に従って、Azure Active Directory (Azure AD) にアプリケーション エンティティを登録します。 これらの値を記録しておきます。リンクされたサービスを定義するときに使います。

    - アプリケーション ID
    - アプリケーション キー
    - テナント ID

2. Azure BLOB ストレージでサービス プリンシパルに適切なアクセス許可を付与します。 ロールの詳細については、「[Azure portal を使用して BLOB とキュー データへのアクセスのための Azure ロールを割り当てる](../storage/common/storage-auth-aad-rbac-portal.md)」を参照してください。

    - **ソースとして**、**アクセス制御 (IAM)** で、少なくとも **ストレージ BLOB データ閲覧者** のロールを付与します。
    - **シンクとして**、**アクセス制御 (IAM)** で、少なくとも **ストレージ BLOB データ共同作成者** のロールを付与します。

Azure BLOB ストレージのリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは、**AzureBlobStorage** に設定する必要があります。 | はい |
| serviceEndpoint | `https://<accountName>.blob.core.windows.net/` のパターンで、Azure BLOB ストレージ サービス エンドポイントを指定します。 | はい |
| accountKind | ストレージ アカウントの種類を指定します。 使用できる値は、以下のとおりです。**Storage** (汎用 v1)、**StorageV2** (汎用 v2)、**BlobStorage**、または **BlockBlobStorage**。 <br/> データ フローで Azure Blob のリンクされたサービスを使用する場合、アカウントの種類が空または "Storage" の場合、マネージド ID またはサービス プリンシパルの認証はサポートされません。 適切なアカウントの種類を指定するか、別の認証を選択するか、ストレージ アカウントを汎用 v2 にアップグレードします。 | いいえ |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | はい |
| servicePrincipalKey | アプリケーションのキーを取得します。 このフィールドを **SecureString** としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 これは、Azure portal の右上隅にマウス ポインターを合わせると取得できます。 | はい |
| azureCloudType | サービス プリンシパル認証の場合は、Azure Active Directory アプリケーションの登録先である Azure クラウド環境の種類を指定します。 <br/> 指定できる値は、**AzurePublic**、**AzureChina**、**AzureUsGovernment**、および **AzureGermany** です。 既定では、データ ファクトリのクラウド環境が使用されます。 | いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 このプロパティが指定されていない場合は、サービスでは、既定の Azure Integration Runtime が使用されます。 | いいえ |

>[!NOTE]
>BLOB アカウントで[論理的な削除](../storage/blobs/soft-delete-blob-overview.md)が有効な場合、サービス プリンシパル認証は Data Flow でサポートされません。

>[!NOTE]
>サービス プリンシパル認証は、"AzureBlobStorage" タイプのリンクされたサービスのみでサポートされており、以前の "AzureStorage" タイプのリンクされたサービスではサポートされていません。

**例:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2",
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

### <a name="managed-identities-for-azure-resource-authentication"></a><a name="managed-identity"></a> Azure リソース認証用のマネージド ID

データ ファクトリは、特定のデータ ファクトリを表す、[Azure リソースのマネージド ID](data-factory-service-identity.md) に関連付けることができます。 独自のサービス プリンシパルを使用するのと同様に、BLOB ストレージ認証にこのマネージド ID を直接使用できます。 これにより、この指定されたファクトリは、BLOB ストレージにアクセスし、BLOB ストレージとの間でデータをコピーできます。

Azure Storage 認証の全般的な情報については、「[Azure Active Directory を使用して Azure Storage へのアクセスを認証する](../storage/common/storage-auth-aad.md)」をご覧ください。 Azure リソースのマネージド ID 認証を使用するには、次の手順に従います。

1. ファクトリと共に生成されたマネージド ID オブジェクト ID の値をコピーして、[Data Factory のマネージド ID 情報を取得します](data-factory-service-identity.md#retrieve-managed-identity)。

2. Azure BLOB ストレージでマネージド ID にアクセス許可を付与します。 ロールの詳細については、「[Azure portal を使用して BLOB とキュー データへのアクセスのための Azure ロールを割り当てる](../storage/common/storage-auth-aad-rbac-portal.md)」を参照してください。

    - **ソースとして**、**アクセス制御 (IAM)** で、少なくとも **ストレージ BLOB データ閲覧者** のロールを付与します。
    - **シンクとして**、**アクセス制御 (IAM)** で、少なくとも **ストレージ BLOB データ共同作成者** のロールを付与します。

>[!IMPORTANT]
>PolyBase または COPY ステートメントを使用して (ソースまたはステージングとしての) BLOB ストレージから Azure Synapse Analytics にデータを読み込む場合に、BLOB ストレージに対してマネージド ID 認証を使用するときは、必ず[こちらのガイダンス](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)の手順 1 から 3 にも従ってください。 これらの手順により、サーバーが Azure AD に登録され、ストレージ BLOB データ共同作成者のロールがサーバーに割り当てられます。 残りの処理は Data Factory によって行われます。 Azure Virtual Network エンドポイントを使用して Blob Storage を構成する場合は、さらに Synapse の要求に従って、Azure Storage アカウントで、 **[ファイアウォールと仮想ネットワーク]** 設定メニューの **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** をオンにする必要があります。

Azure BLOB ストレージのリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは、**AzureBlobStorage** に設定する必要があります。 | はい |
| serviceEndpoint | `https://<accountName>.blob.core.windows.net/` のパターンで、Azure BLOB ストレージ サービス エンドポイントを指定します。 | はい |
| accountKind | ストレージ アカウントの種類を指定します。 使用できる値は、以下のとおりです。**Storage** (汎用 v1)、**StorageV2** (汎用 v2)、**BlobStorage**、または **BlockBlobStorage**。 <br/> データ フローで Azure Blob のリンクされたサービスを使用する場合、アカウントの種類が空または "Storage" の場合、マネージド ID またはサービス プリンシパルの認証はサポートされません。 適切なアカウントの種類を指定するか、別の認証を選択するか、ストレージ アカウントを汎用 v2 にアップグレードします。 | いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 このプロパティが指定されていない場合は、サービスでは、既定の Azure Integration Runtime が使用されます。 | いいえ |

> [!NOTE]
> BLOB アカウントで[論理的な削除](../storage/blobs/soft-delete-blob-overview.md)が有効な場合、マネージド ID 認証は Data Flow でサポートされません。

> [!NOTE]
> Azure リソースのマネージド ID 認証は、"AzureBlobStorage" タイプのリンクされたサービスでのみサポートされており、以前の "AzureStorage" タイプのリンクされたサービスではサポートされていません。

**例:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2" 
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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Azure BLOB ストレージでは、形式ベースのデータセットの `location` 設定において、次のプロパティがサポートされています。

| プロパティ   | 説明                                                  | 必須 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | データセット内の location の **type** プロパティは、**AzureBlobStorageLocation** に設定する必要があります。 | はい      |
| container  | BLOB コンテナー。                                          | はい      |
| folderPath | 特定のコンテナーの下のフォルダーへのパス。 ワイルドカードを使用してフォルダーをフィルター処理する場合は、この設定をスキップし、アクティビティのソース設定でこれを指定します。 | いいえ       |
| fileName   | 特定のコンテナーおよびフォルダー パスの下のファイル名。 ファイルをフィルター処理するためにワイルドカードを使用する場合は、この設定をスキップし、アクティビティのソースの設定でこれを指定します。 | いいえ       |

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

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、BLOB ストレージのソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="blob-storage-as-a-source-type"></a>ソースの種類として Blob Storage を設定する

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Azure BLOB ストレージでは、形式ベースのコピー ソースの `storeSettings` 設定において、次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` の **type** プロパティは **AzureBlobStorageReadSettings** に設定する必要があります。 | はい                                           |
| ***コピーするファイルを特定する:*** |  |  |
| オプション 1: 静的パス<br> | データセットに指定されている所定のコンテナーまたはフォルダー/ファイル パスからコピーします。 コンテナーまたはフォルダーからすべての BLOB をコピーする場合は、さらに `wildcardFileName` を `*` として指定します。 |  |
| オプション 2: BLOB プレフィックス<br>- prefix | ソース オブジェクトをフィルター処理するためにデータセット内に構成されている、特定のコンテナー下の BLOB 名のプレフィックス。 名前が `container_in_dataset/this_prefix` で始まる BLOB が選択されます。 ワイルドカード フィルターより優れたパフォーマンスを提供する、BLOB ストレージ用のサービス側フィルターを利用します。<br><br>プレフィックスを使用して階層を保持した状態でファイルベースのシンクにコピーする場合は、プレフィックスの最後の "/" の後のサブパスが保持されます。 たとえば、ソース `container/folder/subfolder/file.txt` があり、プレフィックスを `folder/sub` として構成した場合、保持されるファイル パスは `subfolder/file.txt` です。 | いいえ                                                          |
| オプション 3: ワイルドカード<br>- wildcardFolderPath | ソース フォルダーをフィルター処理するためにデータセットで構成されている、特定のコンテナーの下のワイルドカード文字を含むフォルダーのパス。 <br>使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 フォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br>「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | いいえ                                            |
| オプション 3: ワイルドカード<br>- wildcardFileName | ソース ファイルをフィルター処理するための、特定のコンテナーおよびフォルダー パス (またはワイルドカード フォルダー パス) の下のワイルドカード文字を含むファイル名。 <br>使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 ファイル名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | はい |
| オプション 4: ファイルの一覧<br>- fileListPath | 指定されたファイル セットをコピーすることを示します。 コピーするファイルの一覧を含むテキスト ファイルをポイントします。データセットで構成されているパスへの相対パスであるファイルを 1 行につき 1 つずつ指定します。<br/>このオプションを使用している場合は、データ セットにファイル名を指定しないでください。 その他の例については、[ファイル リストの例](#file-list-examples)を参照してください。 | いいえ |
| ***追加の設定:*** |  | |
| recursive | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 **recursive** が **true** に設定され、シンクがファイル ベースのストアである場合、空のフォルダーおよびサブフォルダーはシンクでコピーも作成もされないことに注意してください。 <br>使用可能な値: **true** (既定値) および **false**。<br>`fileListPath` を構成する場合、このプロパティは適用されません。 | いいえ |
| deleteFilesAfterCompletion | 宛先ストアに正常に移動した後、バイナリ ファイルをソース ストアから削除するかどうかを示します。 ファイルの削除はファイルごとに行われるので、コピー操作が失敗した場合、一部のファイルが既に宛先にコピーされソースからは削除されているが、他のファイルはまだソース ストアに残っていることがわかります。 <br/>このプロパティは、バイナリ ファイルのコピー シナリオでのみ有効です。 既定値: false。 | いいえ |
| modifiedDatetimeStart    | ファイルは、属性 (最終変更日時) に基づいてフィルター処理されます。 <br>最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する **NULL** にすることができます。  `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が **NULL** の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。  `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が **NULL** の場合は、最終変更日時属性が datetime 値未満であるファイルが選択されます。<br/>`fileListPath` を構成する場合、このプロパティは適用されません。 | いいえ                                            |
| modifiedDatetimeEnd      | 上記と同じです。                                               | いいえ                                            |
| enablePartitionDiscovery | パーティション分割されているファイルの場合は、ファイル パスのパーティションを解析し、それを追加のソース列として追加するかどうかを指定します。<br/>指定できる値は **false** (既定値) と **true** です。 | いいえ                                            |
| partitionRootPath | パーティション検出が有効になっている場合は、パーティション分割されたフォルダーをデータ列として読み取るための絶対ルート パスを指定します。<br/><br/>これが指定されていない場合は、既定で次のようになります。<br/>- ソース上のデータセットまたはファイルの一覧内のファイル パスを使用する場合、パーティションのルート パスはそのデータセットで構成されているパスです。<br/>- ワイルドカード フォルダー フィルターを使用する場合、パーティションのルート パスは最初のワイルドカードの前のサブパスです。<br/>- プレフィックスを使用する場合、パーティションのルート パスは最後の "/" の前のサブパスです。 <br/><br/>たとえば、データセット内のパスを "root/folder/year=2020/month=08/day=27" として構成するとします。<br/>- パーティションのルート パスを "root/folder/year=2020" として指定した場合は、コピー アクティビティによって、ファイル内の列とは別に、それぞれ "08" と "27" の値を持つ `month` と `day` という 2 つの追加の列が生成されます。<br/>- パーティションのルート パスが指定されない場合、追加の列は生成されません。 | いいえ                                            |
| maxConcurrentConnections | ストレージへのコンカレント接続数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ                                            |

> [!NOTE]
> Parquet や区切りテキスト形式の場合、次のセクションで説明するコピー アクティビティ ソース用の **BlobSource** 型は、下位互換性のために引き続きそのままサポートされます。 Data Factory 作成 UI がこれらの新しい型の生成に切り替わるまでは、新しいモデルを使用することをお勧めします。

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
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

> [!NOTE]
> `$logs` コンテナーは、あるストレージ アカウントに対して Storage Analytics が有効になったときに自動的に作成されますが、Data Factory UI でコンテナーの一覧表示操作を実行するときには表示されません。 `$logs` コンテナーからファイルを使用するため、Data Factory にはファイル パスを直接指定する必要があります。

### <a name="blob-storage-as-a-sink-type"></a>シンクの種類として Blob Storage を設定する

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)] 

Azure BLOB ストレージでは、形式ベースのコピー シンクの `storeSettings` 設定において、次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` の `type` プロパティは `AzureBlobStorageWriteSettings` に設定する必要があります。 | はい      |
| copyBehavior             | ソースがファイル ベースのデータ ストアのファイルの場合は、コピー動作を定義します。<br/><br/>使用できる値は、以下のとおりです。<br/><b>- PreserveHierarchy (既定値)</b>:ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーへのソース ファイルの相対パスはターゲット フォルダーへのターゲット ファイルの相対パスと同じになります。<br/><b>- FlattenHierarchy</b>:ソース フォルダーのすべてのファイルをターゲット フォルダーの第一レベルに配置します。 ターゲット ファイルは、自動生成された名前になります。 <br/><b>- MergeFiles</b>:ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイルまたは BLOB の名前を指定した場合、マージされたファイル名は指定した名前になります。 それ以外は自動生成されたファイル名になります。 | いいえ       |
| blockSizeInMB | ブロック BLOB にデータを書き込むために使用されるブロック サイズを MB 単位で指定します。 詳細は、[ブロック BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) に関するページを参照してください。 <br/>指定できる値は、*4 MB から 100 MB* です。 <br/>既定では、ソース ストアの種類とデータに基づいて、Data Factory によってブロック サイズが自動的に決定されます。 BLOB ストレージへの非バイナリ コピーの場合、既定のブロック サイズは 100 MB なので、(最大) 4.95 TB のデータに収めることができます。 これは、データが大きくない場合、特に、ネットワーク接続が貧弱な状態でセルフホステッド統合ランタイムを使用し、操作のタイムアウトやパフォーマンスの問題が発生する場合は、最適ではない可能性があります。 `blockSizeInMB*50000` がデータを格納するのに十分な大きさであることを保証しつつ、ブロック サイズを明示的に指定できます。 そのようにしないと、コピー アクティビティの実行に失敗します。 | いいえ |
| maxConcurrentConnections | ストレージへのコンカレント接続数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ       |

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
                    "type": "AzureBlobStorageWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>フォルダーとファイル フィルターの例

このセクションでは、ワイルドカード フィルターを使用した結果のフォルダーのパスとファイル名の動作について説明します。

| folderPath | fileName | recursive | ソースのフォルダー構造とフィルターの結果 (**太字** のファイルが取得されます)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (空、既定値を使用) | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (空、既定値を使用) | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>ファイル リストの例

このセクションでは、コピー アクティビティのソースでファイル リスト パスを使用した結果の動作について説明します。

次のソース フォルダー構造があり、太字のファイルをコピーするとします。

| サンプルのソース構造                                      | FileListToCopy.txt のコンテンツ                             | Data Factory の構成                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadata<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **データセット内:**<br>- コンテナー: `container`<br>- フォルダー パス: `FolderA`<br><br>**コピー アクティビティ ソース内:**<br>- ファイル リストのパス: `container/Metadata/FileListToCopy.txt` <br><br>ファイル リストのパスは、コピーするファイルの一覧を含む同じデータ ストア内のテキスト ファイルをポイントします。データセットで構成されているパスへの相対パスで 1 行につき 1 つのファイルを指定します。 |

### <a name="some-recursive-and-copybehavior-examples"></a>recursive と copyBehavior の例

このセクションでは、**recursive** 値と **copyBehavior** 値のさまざまな組み合わせでのコピー操作の動作について説明します。

| recursive | copyBehavior | ソースのフォルダー構造 | ターゲットの結果 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、ソースと同じ構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 の自動生成された名前 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2、File3、File4、File5 の内容は 1 つのファイルにマージされて、自動生成されたファイル名が付けられます。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2 の内容は 1 つのファイルにマージされ、自動生成されたファイル名が付けられます。 File1 の自動生成された名前<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |

## <a name="preserving-metadata-during-copy"></a>コピー中のメタデータの保持

Amazon S3、Azure BLOB ストレージ、または Azure Data Lake Storage Gen2 から Azure Data Lake Storage Gen2 または Azure BLOB ストレージにファイルをコピーする場合、ファイルのメタデータをデータと共に保持することもできます。 詳細については、[メタデータの保存](copy-activity-preserve-metadata.md#preserve-metadata)に関する記事を参照してください。

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

マッピング データ フローでデータを変換するときに、次の形式で Azure BLOB ストレージのファイルの読み取りと書き込みができます。

- [Avro](format-avro.md#mapping-data-flow-properties)
- [区切りテキスト](format-delimited-text.md#mapping-data-flow-properties)
- [Delta](format-delta.md#mapping-data-flow-properties)
- [Excel](format-excel.md#mapping-data-flow-properties)
- [JSON](format-json.md#mapping-data-flow-properties)
- [Parquet](format-parquet.md#mapping-data-flow-properties)

形式固有の設定は、各形式のドキュメントに記載されています。 詳細については、「[マッピング データ フローのソース変換](data-flow-source.md)」および「[マッピング データ フローでのシンク変換](data-flow-sink.md)」を参照してください。

### <a name="source-transformation"></a>ソース変換

ソース変換では、Azure BLOB ストレージ内のコンテナー、フォルダー、または個々のファイルから読み取ることができます。 ファイルの読み取り方法を管理するには、 **[ソース オプション]** タブを使用します。 

![ソース オプション](media/data-flow/sourceOptions1.png "ソース オプション")

**[ワイルドカード パス]:** ワイルドカード パターンを使用すると、Data Factory は、単一のソース変換で一致する各フォルダーとファイルをループ処理するよう指示されます。 これは、単一のフロー内の複数のファイルを処理するのに効果的な方法です。 既存のワイルドカード パターンにマウス ポインターを合わせると表示されるプラス記号を使って、複数のワイルドカード一致パターンを追加します。

ソース コンテナーから、パターンに一致する一連のファイルを選択します。 データセット内で指定できるのはコンテナーのみです。 そのため、ワイルドカード パスには、ルート フォルダーからのフォルダー パスも含める必要があります。

ワイルドカードの例:

- `*` - 任意の文字セットを表します。
- `**` - ディレクトリの再帰的な入れ子を表します。
- `?` - 1 文字を置き換えます。
- `[]` - 角カッコ内の 1 文字以上に一致します。

- `/data/sales/**/*.csv` - /data/sales の下のすべての .csv ファイルを取得します。
- `/data/sales/20??/**/` - 20 世紀のすべてのファイルを取得します。
- `/data/sales/*/*/*.csv` - /data/sales の 2 レベル下の .csv ファイルを取得します。
- `/data/sales/2004/*/12/[XY]1?.csv` - 前に 2 桁の数字が付いた X または Y で始まる、2004 年 12 月のすべての .csv ファイルを取得します。

**[Partition root path]\(パーティションのルート パス\):** `key=value` 形式 (例: `year=2019`) のファイル ソース内のフォルダーをパーティション分割した場合、そのパーティション フォルダー ツリーの最上位をデータ フローのデータ ストリーム内の列名に割り当てることができます。

最初に、ワイルドカードを設定して、パーティション分割されたフォルダーと読み取るリーフ ファイルのすべてのパスを含めます。

![パーティション ソース ファイルの設定](media/data-flow/partfile2.png "パーティション ファイルの設定")

**[Partition root path]\(パーティションのルート パス\)** 設定を使用して、フォルダー構造の最上位レベルを定義します。 データ プレビューを使用してデータの内容を表示すると、Data Factory によって、各フォルダー レベルで見つかった解決済みのパーティションが追加されることがわかります。

![パーティションのルート パス](media/data-flow/partfile1.png "パーティション ルート パスのプレビュー")

**[List of files]:** これはファイル セットです。 処理する相対パス ファイルの一覧を含むテキスト ファイルを作成します。 このテキスト ファイルをポイントします。

**[Column to store file name]\(ファイル名を格納する列\):** ソース ファイルの名前をデータの列に格納します。 ファイル名文字列を格納するための新しい列名をここに入力します。

**[After completion]\(完了後\):** データ フローの実行後にソース ファイルに何もしないか、ソース ファイルを削除するか、またはソース ファイルを移動することを選択します。 移動のパスは相対パスです。

後処理でソース ファイルを別の場所に移動するには、まず、ファイル操作の "移動" を選択します。 次に、"移動元" ディレクトリを設定します。 パスにワイルドカードを使用していない場合、"移動元" 設定はソース フォルダーと同じフォルダーになります。

ワイルドカードを含むソース パスがある場合、構文は次のようになります。

`/data/sales/20??/**/*.csv`

"移動元" は次のように指定できます。

`/data/sales`

"移動先" は次のように指定できます。

`/backup/priorSales`

この場合、ソースとして指定された `/data/sales` の下のすべてのファイルは `/backup/priorSales` に移動されます。

> [!NOTE]
> ファイル操作は、パイプライン内のデータ フローの実行アクティビティを使用するパイプライン実行 (パイプラインのデバッグまたは実行) からデータ フローを開始する場合にのみ実行されます。 データ フロー デバッグ モードでは、ファイル操作は実行 *されません*。

**[Filter by last modified]\(最終更新日時でフィルター処理\):** 最終更新日時の範囲を指定することで、処理するファイルをフィルター処理できます。 日時はすべて UTC 形式です。 

### <a name="sink-properties"></a>シンクのプロパティ

シンク変換では、Azure BLOB ストレージ内のコンテナーまたはフォルダーに書き込むことができます。 ファイルへの書き込み方法を管理するには、 **[設定]** タブを使用します。

![シンクのオプション](media/data-flow/file-sink-settings.png "シンク オプション")

**Clear the folder**\(フォルダーのクリア\):データが書き込まれる前に、書き込み先のフォルダーをクリアするかどうかを決定します。

**File name option**\(ファイル名のオプション\):書き込み先のフォルダー内の書き込み先ファイルの命名方法を決定します。 ファイル名のオプションを次に示します。
   - **既定**:Spark は PART の既定値に基づいて、ファイルに名前を付けることができます。
   - **パターン**:パーティションごとに出力ファイルを列挙するパターンを入力します。 たとえば、`loans[n].csv` と指定すると、`loans1.csv`、`loans2.csv`、といった要領でファイルが作成されます。
   - **Per partition**(パーティションあたり):パーティションごとに 1 つのファイル名を入力します。
   - **As data in column**(列内のデータとして):出力ファイルを列の値に設定します。 パスは、書き込み先フォルダーではなく、データセット コンテナーからの相対パスです。 データセット内にフォルダー パスがある場合は、上書きされます。
   - **Output to a single file**(1 つのファイルに出力する):パーティション分割された出力ファイルを単一の名前付きファイルに結合します。 パスは、データセット フォルダーからの相対パスです。 マージ操作は、ノード サイズによっては失敗する可能性があることに注意してください。 このオプションは、大きなデータセットには推奨されません。

**Quote all**\(すべてを引用符で囲む\):すべての値を引用符で囲むかどうかを決定します

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="getmetadata-activity-properties"></a>GetMetadata アクティビティのプロパティ

プロパティの詳細については、[GetMetadata アクティビティ](control-flow-get-metadata-activity.md)に関するページを参照してください。 

## <a name="delete-activity-properties"></a>Delete アクティビティのプロパティ

プロパティの詳細については、[Delete アクティビティ](delete-activity.md)に関するページを参照してください。

## <a name="legacy-models"></a>レガシ モデル

>[!NOTE]
>次のモデルは、下位互換性のために引き続きそのままサポートされます。 前述の新しいモデルを使用することをお勧めします。 Data Factory 作成 UI は、新しいモデルの生成に切り替えられました。

### <a name="legacy-dataset-model"></a>レガシ データセット モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの `type` プロパティは、`AzureBlob` に設定する必要があります。 | はい |
| folderPath | BLOB ストレージのコンテナーとフォルダーのパス。 <br/><br/>ワイルドカード フィルターは、コンテナー名を除くパスに対してサポートされます。 使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 フォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br/><br/>(例: `myblobcontainer/myblobfolder/`)。 「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | はい (Copy または Lookup アクティビティの場合)、いいえ (GetMetadata アクティビティの場合) |
| fileName | 指定された `folderPath` 値の下にある BLOB の名前またはワイルドカード フィルター。 このプロパティの値を指定しない場合、データセットはフォルダー内のすべての BLOB をポイントします。 <br/><br/>フィルターに使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。<br/>- 例 1: `"fileName": "*.csv"`<br/>- 例 2: `"fileName": "???20180427.txt"`<br/>ファイル名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。<br/><br/>出力データセットに `fileName` の指定がなく、アクティビティ シンクに `preserveHierarchy` の指定がない場合、コピー アクティビティによって自動的に生成される BLOB 名のパターンは次のようになります: "*Data.[activity run ID GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]* ". 次に例を示します。"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz" <br/><br/>クエリの代わりにテーブル名を使用して表形式のソースからコピーする場合、名前のパターンは `[table name].[format].[compression if configured]` となります。 次に例を示します。"MyTable.csv" です。 | いいえ |
| modifiedDatetimeStart | ファイルは、属性 (最終変更日時) に基づいてフィルター処理されます。 最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> この設定を有効にすると、大量のファイルのフィルター処理を実行する場合にデータ移動の全体的なパフォーマンスに影響することに注意してください。 <br/><br/> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する `NULL` にすることができます。  `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が `NULL` の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。  `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が `NULL` の場合は、最終変更日時属性が datetime 値未満であるファイルが選択されます。| いいえ |
| modifiedDatetimeEnd | ファイルは、属性 (最終変更日時) に基づいてフィルター処理されます。 最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> この設定を有効にすると、大量のファイルのフィルター処理を実行する場合にデータ移動の全体的なパフォーマンスに影響することに注意してください。 <br/><br/> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する `NULL` にすることができます。  `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が `NULL` の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。  `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が `NULL` の場合は、最終変更日時属性が datetime 値未満であるファイルが選択されます。| いいえ |
| format | ファイルベースのストア間でファイルをそのままコピー (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。<br/><br/>特定の形式のファイルを解析または生成する場合、サポートされるファイル形式の種類は、**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 **format** の **type** プロパティをいずれかの値に設定します。 詳細については、[Text 形式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[Json 形式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 形式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 形式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)、[Parquet 形式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) の各セクションを参照してください。 | いいえ (バイナリ コピー シナリオのみ) |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs-legacy.md#compression-support)に関する記事を参照してください。<br/>サポートされる種類は、**GZip**、**Deflate**、**BZip2**、および **ZipDeflate** です。<br/>サポートされるレベルは、**Optimal** と **Fastest** です。 | いいえ |

>[!TIP]
>フォルダーの下のすべての BLOB をコピーするには、**folderPath** のみを指定します。<br>特定の名前の単一の BLOB をコピーするには、フォルダー部分に **folderPath**、ファイル名に **fileName** を指定します。<br>フォルダーの下の BLOB のサブセットをコピーするには、フォルダー部分に **folderPath**、ワイルドカード フィルターで **fileName** を指定します。 

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

### <a name="legacy-source-model-for-the-copy-activity"></a>コピー アクティビティのレガシ ソース モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの `type` プロパティは、`BlobSource` に設定する必要があります。 | はい |
| recursive | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 `recursive` が `true` に設定され、シンクがファイルベースのストアである場合、空のフォルダーおよびサブフォルダーはシンクでコピーも作成もされないことに注意してください。<br/>使用できる値は、`true` (既定値) と `false` です。 | いいえ |
| maxConcurrentConnections | ストレージへのコンカレント接続数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ |

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

### <a name="legacy-sink-model-for-the-copy-activity"></a>コピー アクティビティのレガシ シンク モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの `type` プロパティは、`BlobSink` に設定する必要があります。 | はい |
| copyBehavior | ソースがファイル ベースのデータ ストアのファイルの場合は、コピー動作を定義します。<br/><br/>使用できる値は、以下のとおりです。<br/><b>- PreserveHierarchy (既定値)</b>:ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーに対するソース ファイルの相対パスと、ターゲット フォルダーに対するターゲット ファイルの相対パスが一致します。<br/><b>- FlattenHierarchy</b>:ソース フォルダーのすべてのファイルをターゲット フォルダーの第一レベルに配置します。 ターゲット ファイルは、自動生成された名前になります。 <br/><b>- MergeFiles</b>:ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイルまたは BLOB の名前を指定した場合、マージされたファイル名は指定した名前になります。 それ以外は自動生成されたファイル名になります。 | いいえ |
| maxConcurrentConnections | ストレージへのコンカレント接続数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ |

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

## <a name="next-steps"></a>次のステップ

Data Factory のコピー アクティビティでソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するページを参照してください。
