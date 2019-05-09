---
title: Azure Data Factory を使用して Office 365 からデータをコピーする (プレビュー) | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Office 365 のデータをサポートされているシンク データ ストアにコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: jingwang
ms.openlocfilehash: b86aef7de048690d689a87d4fb844f77ea986445
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55297489"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory-preview"></a>Azure Data Factory を使用して Office 365 から Azure にデータをコピーする (プレビュー) 

Azure Data Factory では、Office 365 テナント内にある高機能な組織データを Azure へとスケーラブルに取り込み、分析アプリケーションを構築して、それらの貴重なデータ資産に基づくインサイトを抽出することができます。 Privileged Access Management との統合により、Office 365 内の貴重な選別済みデータをセキュリティで保護することができます。  Microsoft Graph データ接続について詳しくは、[こちらのリンク](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki)をご覧ください。

この記事では、Azure Data Factory のコピー アクティビティを使用して、Office 365 からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

現在、1 回のコピー アクティビティで実行できるのは、**Office 365 から [Azure Blob Storage](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2 (プレビュー) への、](connector-azure-data-lake-storage.md)JSON 形式** (setOfObjects 型) でのデータ コピーのみとなっています。 Office 365 を他の種類のデータ ストアに読み込んだり、他の形式で読み込む必要がある場合は、最初のコピー アクティビティを後続のコピー アクティビティに連結して、[サポートされている ADF 変換先ストアに関する記事に記載されている、任意のストアにデータを読み込むことができます ](copy-activity-overview.md#supported-data-stores-and-formats) (「サポートされるデータ ストアと形式」表の "シンクとしてサポート" 列をご覧ください)。

>[!IMPORTANT]
>- データ ファクトリとシンク データ ストアを含んだ Azure サブスクリプションは、Office 365 テナントと同じ Azure Active Directory (Azure AD) テナントの下に配置する必要があります。
>- コピー アクティビティに使用されるリージョンとコピー先が、Office 365 テナント ユーザーのメールボックスの場所と同じリージョン内であることを確認してください。 Azure IR の場所がどのように決定されるかについては、[こちら](concepts-integration-runtime.md#integration-runtime-location)をご覧ください。 サポートされている Office リージョンと、対応する Azure リージョンの一覧については、[こちらの表](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#data-regions)をご覧ください。
>-  Office 365 のデータを **Azure Blob Storage** 内に読み込む場合は、Azure Blob Storage へのリンク済みサービスを定義する際に、**[サービス プリンシパル認証](connector-azure-blob-storage.md#service-principal-authentication)** を使用するようにしてください。[アカウント キー](connector-azure-blob-storage.md#account-key-authentication)、[共有アクセス署名](connector-azure-blob-storage.md#shared-access-signature-authentication)、[Azure リソースのマネージド ID](connector-azure-blob-storage.md#managed-identity) による認証は使わないようにしてください。
>-  Office 365 のデータを **Azure Data Lake Storage Gen1** 内に読み込む場合は、Azure Data Lake Storage Gen1 へのリンク済みサービスを定義する際に、[**サービス プリンシパル認証**](connector-azure-data-lake-store.md#use-service-principal-authentication)を使用するようにしてください。[Azure リソースのマネージド ID](connector-azure-data-lake-store.md#managed-identity) による認証は使わないようにしてください。

## <a name="prerequisites"></a>前提条件

Office 365 から Azure にデータをコピーするには、前提条件として次の手順を完了する必要があります。

- Office 365 テナントの管理者は、[こちら](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/On-boarding)の説明に従って、オンボーディング アクションを完了する必要があります。
- Azure Active Directory で Azure AD Web アプリケーションを作成し、構成します。  手順については、[Microsoft Azure での Ruby アプリケーションの作成](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)に関するページを参照してください。
- 次の値を記録しておきます。Office 365 用のリンク済みサービスを定義するときに使います。
    - テナント ID。 手順については、「[テナント ID を取得する](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id)」をご覧ください。
    - アプリケーション ID とアプリケーション キー。  手順については、「[アプリケーション ID と認証キーを取得する](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key)」をご覧ください。
- Azure AD Web アプリケーションの所有者としてデータ アクセス要求を行うユーザーの ID を追加します (Azure AD Web アプリケーションから、[設定] > [所有者] > [所有者の追加])。 
    - ユーザー ID は、データの取得先の Office 365 組織に属している必要があり、Guest ユーザーであってはなりません。

## <a name="approving-new-data-access-requests"></a>新しいデータ アクセス要求の承認

このコンテキスト (アクセスされようとしているデータ テーブル、データが読み込まれようとしている宛先アカウント、データ アクセス要求を行っているユーザー ID の組み合わせ) のデータを要求するのはこれが初めてである場合は、コピー アクティビティの状態が "In Progress (進行中)" として表示されます。[[アクション] の下にある [詳細]](copy-activity-overview.md#monitoring) リンクをクリックしたときにのみ、状態は "RequestingConsent" と表示されます。  データの抽出を進めるには、データ アクセス承認者グループのメンバーが、Privileged Access Management で要求を承認する必要があります。

承認者がデータ アクセス要求を承認する方法については[こちら](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Approving-data-access-requests)を、Privileged Access Management との統合に関する概要 (データ アクセス承認者グループの設定を含む) については[こちら](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#integration-with-privileged-access-management)をご覧ください。

## <a name="policy-validation"></a>ポリシー検証

ADF がマネージド アプリの一部として作成され、管理リソース グループ内のリソースに対して Azure ポリシーの割り当てが行われた場合は、コピー アクティビティが実行されるたびに、ポリシー割り当てが適用されるかどうかが ADF によってチェックされます。 サポートされているポリシーの一覧については、[こちら](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#policies)をご覧ください。

## <a name="getting-started"></a>使用の開始

>[!TIP]
>Office 365 コネクタの使用に関するチュートリアルについては、[Office 365 からのデータ読み込み](load-office-365-data.md)関する記事をご覧ください。

コピー アクティビティを含んだパイプラインは、次のいずれかのツールまたは SDK を使用して作成できます。 リンクを選択すると、コピー アクティビティを含んだパイプラインの作成手順を詳しく説明したチュートリアルにアクセスできます。 

- [Azure Portal](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager テンプレート](quickstart-create-data-factory-resource-manager-template.md) 

次のセクションでは、Office 365 コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Office 365 のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります: **Office 365** | はい |
| office365TenantId | Office 365 アカウントが属している Azure テナント ID です。 | はい |
| servicePrincipalTenantId | Azure AD Web アプリケーションが存在するテナントの情報を指定します。 | はい |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | はい |
| servicePrincipalKey | アプリケーションのキーを取得します。 Data Factory に安全に格納するには、このフィールドを SecureString として指定します。 | はい |
| connectVia | データ ストアに接続するために使用される統合ランタイム。  指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ  |

>[!NOTE]
> **office365TenantId** と **servicePrincipalTenantId** の違いと、対応する指定値:
>- 自分の組織専用の Office 365 データに対してアプリケーションを開発している場合は、両方のプロパティに同じ ID を指定します (組織の AAD テナント ID)。
>- 顧客用のアプリケーションを開発している ISV デベロッパーの場合は、office365TenantId が顧客の (アプリケーション インストーラー) AAD テナント ID になり、servicePrincipalTenantId が自社の AAD テナント ID になります。

**例:**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Office 365 データセットでサポートされるプロパティの一覧を示します。

Office 365 からのデータ コピーについては、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは **Office365Table** に設定する必要があります。 | はい |
| tableName | Office 365 から抽出するデータセットの名前です。 抽出に使用できる Office 365 データセットの一覧については、[こちら](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#datasets)をご覧ください。 | はい |
| predicate | Office 365 から抽出する行をフィルター処理するために使用できる述語式です。  各テーブルの述語フィルターに使用できる列とフィルター式の形式については、[こちら](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#filters)をご覧ください。 | いいえ <br>(述語が指定されていない場合は、既定の動作として、過去 30 日間のデータが抽出されます) |

**例**

```json
{
    "name": "Office365Table1",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "ReceivedDateTime",
                "type": "datetime"
            },
            {
                "name": "SentDateTime",
                "type": "datetime"
            },
            {
                "name": "HasAttachments",
                "type": "boolean"
            },
            {
                "name": "InternetMessageId",
                "type": "string"
            },
            {
                "name": "Subject",
                "type": "string"
            },
            {
                "name": "Importance",
                "type": "string"
            },
            {
                "name": "ParentFolderId",
                "type": "string"
            },
            {
                "name": "Sender",
                "type": "string"
            },
            {
                "name": "From",
                "type": "string"
            },
            {
                "name": "ToRecipients",
                "type": "string"
            },
            {
                "name": "CcRecipients",
                "type": "string"
            },
            {
                "name": "BccRecipients",
                "type": "string"
            },
            {
                "name": "ReplyTo",
                "type": "string"
            },
            {
                "name": "ConversationId",
                "type": "string"
            },
            {
                "name": "UniqueBody",
                "type": "string"
            },
            {
                "name": "IsDeliveryReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsReadReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsRead",
                "type": "boolean"
            },
            {
                "name": "IsDraft",
                "type": "boolean"
            },
            {
                "name": "WebLink",
                "type": "string"
            },
            {
                "name": "CreatedDateTime",
                "type": "datetime"
            },
            {
                "name": "LastModifiedDateTime",
                "type": "datetime"
            },
            {
                "name": "ChangeKey",
                "type": "string"
            },
            {
                "name": "Categories",
                "type": "string"
            },
            {
                "name": "Id",
                "type": "string"
            },
            {
                "name": "Attachments",
                "type": "string"
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Contact_v0",
            "predicate": "CreatedDateTime >= 2018-07-11T16:00:00.000Z AND CreatedDateTime <= 2018-07-18T16:00:00.000Z"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Office 365 ソースでサポートされるプロパティの一覧を示します。

### <a name="office-365-as-source"></a>ソースとしての Office 365

Office 365 からデータをコピーするは、コピー アクティビティのソースの種類を **Office365Source** に設定します。 コピー アクティビティの **source** セクションでは、追加のプロパティはサポートされません。

**例:**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
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
                "type": "Office365Source"
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
