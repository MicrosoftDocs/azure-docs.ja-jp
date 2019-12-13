---
title: Dynamics でデータをコピーする (Common Data Service)
description: Data Factory パイプラインでコピー アクティビティを使用して、Microsoft Dynamics CRM または Microsoft Dynamics 365 (Common Data Service) からサポートされているシンク データ ストアに、またはサポートされているソース データ ストアから Dynamics CRM または Dynamics 365 にデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: d065439839ba5db479305ae81c61892cb5cf5e70
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929450"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Azure Data Factory を使用して Dynamics 365 (Common Data Service) または Dynamics CRM をコピー元またはコピー先としてデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、Microsoft Dynamics 365 または Microsoft Dynamics CRM をコピー元またはコピー先としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

このコネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Dynamics 365 (Common Data Service) または Dynamics CRM から、サポートされている任意のシンク データ ストアにデータをコピーできます。 サポートされている任意のソース データ ストアから Dynamics 365 (Common Data Service) または Dynamics CRM にデータをコピーすることもできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するページの表をご覧ください。

この Dynamics コネクタでは、オンラインとオンプレミスの両方で Dynamics バージョン 7.x から 9.x がサポートされます。 具体的には次のとおりです。

- バージョン 7.x は、Dynamics CRM 2015 にマップされます
- バージョン 8.x は、Dynamics CRM 2016 および初期バージョンの Dynamics 365 にマップされます
- バージョン 9.x は、以降のバージョンの Dynamics 365 にマップされます

Dynamics のそれぞれのバージョン/製品でサポートされている認証の種類と構成については、次の表を参照してください。 (IFD は Internet Facing Deployment の省略形です)。

| Dynamics のバージョン | 認証の種類 | リンクされたサービスの例 |
|:--- |:--- |:--- |
| Common Data Service <br> Dynamics 365 Online <br> Dynamics CRM Online | AAD サービス プリンシパル <br> Office365 | [Dynamics online + AAD サービス プリンシパルまたは Office365 認証](#dynamics-365-and-dynamics-crm-online) |
| IFD 対応オンプレミス Dynamics 365 <br> IFD 対応オンプレミス Dynamics CRM 2016 <br> IFD 対応オンプレミス Dynamics CRM 2015 | IFD | [IFD + IFD 認証対応オンプレミス Dynamics](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

具体的には、Dynamics 365 では、次のアプリケーションの種類がサポートされます。

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

その他のアプリケーションの種類 (金融、運用、タレントなど) は、このコネクタではサポートされていません。

この Dynamics コネクタは [Dynamics XRM ツール](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools)に基づいて構築されています。

>[!TIP]
>**Dynamics 365 Finance and Operations** からデータをコピーするには、[Dynamics AX コネクタ](connector-dynamics-ax.md)を使用できます。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Dynamics に固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Dynamics のリンクされたサービスでは、次のプロパティがサポートされます。

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 および Dynamics CRM Online

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、**Dynamics**、**DynamicsCrm**、**CommonDataServiceForApps** のいずれかに設定する必要があります。 | はい |
| deploymentType | Dynamics インスタンスの展開の種類。 Dynamics Online を **"Online"** にする必要があります。 | はい |
| serviceUri | Dynamics インスタンスのサービス URL (例: `https://adfdynamics.crm.dynamics.com`)。 | はい |
| authenticationType | Dynamics サーバーに接続する認証の種類。 使用できる値は、以下のとおりです。**AADServicePrincipal**、または **"Office365"** 。 | はい |
| servicePrincipalId | Azure Active Directory アプリケーションのクライアント ID を指定します。 | はい (`AADServicePrincipal` 認証を使用する場合) |
| servicePrincipalCredentialType | サービス プリンシパル認証に使用する資格情報の種類を指定します。 使用できる値は、以下のとおりです。**ServicePrincipalKey** または **ServicePrincipalCert**。 | はい (`AADServicePrincipal` 認証を使用する場合) |
| servicePrincipalCredential | サービス プリンシパル認証を指定します。 <br>資格情報の種類として `ServicePrincipalKey` を使用する場合、`servicePrincipalCredential` は文字列 (ADF はリンクされたサービスのデプロイ時に ADF が暗号化します)、または AKV のシークレットへの参照になります。 <br>資格情報として `ServicePrincipalCert` を使用する場合、`servicePrincipalCredential` は AKV の証明書への参照である必要があります。 | はい (`AADServicePrincipal` 認証を使用する場合) | 
| username | Dynamics に接続するためのユーザー名を指定します。 | はい (`Office365` 認証を使用する場合) |
| password | username に指定したユーザー アカウントのパスワードを指定します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい (`Office365` 認証を使用する場合) |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | ソースの場合は「いいえ」、シンクの場合は「はい」 (ソースにリンクされたサービスに統合ランタイムがない場合) |

>[!NOTE]
>Dynamics コネクタは、省略可能な "organizationName" プロパティを使用して Dynamics CRM/365 Online インスタンスを識別するために使用されていました。 それは引き続き機能しますが、代わりに新しい "serviceUri" プロパティを指定して、インスタンス検出のパフォーマンスを向上させることをお勧めします。

**例:AAD サービス プリンシパル + キー認証を使用した Dynamics Online**

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://adfdynamics.crm.dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
**例:AAD サービスプリンシパル + 証明書認証を使用した Dynamics Online**

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://adfdynamics.crm.dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

**例:Office 365 の認証を使用する Dynamics Online**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>IFD 対応 Dynamics 365 および Dynamics CRM (オンプレミス)

"*Dyanmics Online と比べた場合に追加されたプロパティは、"hostName" と "port" です。* "

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、**Dynamics**、**DynamicsCrm**、**CommonDataServiceForApps** のいずれかに設定する必要があります。 | はい |
| deploymentType | Dynamics インスタンスの展開の種類。 IFD 対応オンプレミス Dynamics を **"OnPremisesWithIfd"** にする必要があります。| はい |
| hostName | オンプレミス Dynamics サーバーのホスト名。 | はい |
| port | オンプレミス Dynamics サーバーのポート。 | いいえ (既定値は 443) |
| organizationName | Dynamics インスタンスの組織の名前。 | はい |
| authenticationType | Dynamics サーバーに接続する認証の種類。 IFD 対応オンプレミス Dynamics を **"Ifd"** に指定します。 | はい |
| username | Dynamics に接続するためのユーザー名を指定します。 | はい |
| password | username に指定したユーザー アカウントのパスワードを指定します。 このフィールドを SecureString としてマークして ADF に安全に格納するか、Azure Key Vault にパスワードを格納し、データ コピーの実行時にコピー アクティビティでそこからプルするかを選択できます。詳しくは、[Key Vault への資格情報の格納](store-credentials-in-key-vault.md)に関するページをご覧ください。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | ソースの場合はいいえ、シンクの場合ははい |

**例:IFD 認証を使用する IFD 対応オンプレミス Dynamics**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Dynamics データセット でサポートされるプロパティの一覧を示します。

Dynamics との間でのデータ コピーについては、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**DynamicsEntity**、**DynamicsCrmEntity**、**CommonDataServiceForAppsEntity** のいずれかに設定する必要があります。 |はい |
| entityName | 取得するエンティティの論理名。 | ソースの場合はいいえ (アクティビティ ソースの "query" が指定されている場合)、シンクの場合ははい |

**例:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Dynamics ソースとシンクの種類でサポートされるプロパティの一覧を示します。

### <a name="dynamics-as-a-source-type"></a>ソースの種類としての Dynamics

Dynamics からデータをコピーするために、コピー アクティビティの **source** セクションでは次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、**DynamicsSource**、**DynamicsCrmSource**、**CommonDataServiceForAppsSource** のいずれかに設定する必要があります。 | はい |
| query | FetchXML は、Dynamics (オンラインおよびオンプレミス) で使用される独自のクエリ言語です。 次の例を参照してください。 詳細については、「[FetchXML を使用したクエリの構築](https://msdn.microsoft.com/library/gg328332.aspx)」を参照してください。 | いいえ (データセットの "entityName" が指定されている場合) |

>[!NOTE]
>PK 列は、FetchXML クエリで構成する列のプロジェクションに含まれていない場合でも常にコピーされます。

> [!IMPORTANT]
>- Dynamics からデータをコピーするとき、Dynamics からシンクへの明示的な列のマッピングを省略できますが、決定論的なコピー結果を確保することを強くお勧めします。
>- UI 作成でスキーマをインポートする際､ADF は Dynamics のクエリ結果の上位の行をサンプリングすることで (上位の行における値のない列は除外)､スキーマを推論し､ソース列のリストを初期化します。 明示的なマッピングがない場合でも、コピーの実行には同じ動作が適用されます。 マッピングには、自分で確認したうえで列を追加することができます。コピーの実行時には、それが反映されます。

**例:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>FetchXML クエリの例

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>シンクの種類としての Dynamics

Dynamics にデータをコピーするために、コピー アクティビティの **sink** セクションでは次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは **DynamicsSink**、**DynamicsCrmSink**、**CommonDataServiceForAppsSink** のいずれかに設定する必要があります。 | はい |
| writeBehavior | 操作の書き込み動作。<br/>使用可能な値: **"Upsert"** 。 | はい |
| alternateKeyName | "Upsert" を実行するには、エンティティに定義されている代替キー名を指定します。 | いいえ |
| writeBatchSize | 各バッチで Dynamics に書き込まれたデータの行数。 | いいえ (既定値は 10) |
| ignoreNullValues | 書き込み操作時に入力データ (キー フィールドを除く) からの null 値を無視するかどうかを示します。<br/>使用可能な値: **true** および **false**。<br>- **True**:upsert または更新操作を行うときに、対象オブジェクト内のデータが変更されないようにします。 挿入操作を実行するときに、定義済みの既定値を挿入します。<br/>- **False**:upsert または更新操作を行うときに、対象オブジェクト内のデータを NULL に更新します。 挿入操作を実行するときに、NULL 値を挿入します。 | いいえ (既定値は false) |

>[!NOTE]
>Dynamics シンクでのシンク "**writeBatchSize**" とコピー アクティビティ " **[parallelCopies](copy-activity-performance.md#parallel-copy)** " の既定値は、どちらも 10 です。 そのため、100 個のレコードが Dynamics に同時に送信されます。

Dynamics 365 オンラインでは、[1 組織あたりの同時バッチ呼び出し数が 2](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations) という制限があります。 この制限を超えた場合、最初の要求が実行される前に "サーバー ビジー" 状態が発生します。 "writeBatchSize" を 10 以下に保つと、このような同時呼び出しの制限が回避されます。

"**writeBatchSize**" と "**parallelCopies**" の最適な組み合わせは、エンティティのスキーマによって変わります。たとえば、その呼び出しのためにフックされる列数、行サイズ、プラグイン/ワークフロー/ワークフロー アクティビティの数などです。10 writeBatchSize * 10 parallelCopies という既定の設定は、最高のパフォーマンスではないかもしれませんが、ほとんどの Dynamics エンティティに利用できる Dynamics サービスの推奨値です。 コピー アクティビティ設定の組み合わせを調整することで、パフォーマンスを調整できます。

**例:**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Dynamics のデータ型のマッピング

Dynamics からデータをコピーするとき、次の Dynamics のデータ型から Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

次のマッピング テーブルを使用して、ソースの Dynamics データ型に基づき、データセット構造に対応する Data Factory データ型を構成します。

| Dynamics データ型 | Data Factory の中間データ型 | ソースとしてサポート | シンクとしてサポート |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolean | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | string | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (関連付けられている 1 つのターゲットを含む) |
| AttributeType.ManagedProperty | Boolean | ✓ | |
| AttributeType.Memo | string | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | string | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Dynamics データ型の AttributeType.CalendarRules、AttributeType.MultiSelectPicklist および AttributeType.PartyList はサポートされていません。

## <a name="lookup-activity-properties"></a>ルックアップ アクティビティのプロパティ

プロパティの詳細については、[ルックアップ アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順
Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
