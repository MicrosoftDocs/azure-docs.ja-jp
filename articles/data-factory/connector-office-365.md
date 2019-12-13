---
title: Azure Data Factory を使用して Office 365 からデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Office 365 のデータをサポートされているシンク データ ストアにコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: d97b3caccc92f0fdfeb229d94e30ee6499c26181
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74912407"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Azure Data Factory を使用して Office 365 から Azure にデータをコピーする

Azure Data Factory は [Microsoft Graph データ接続](https://docs.microsoft.com/graph/data-connect-concept-overview)と統合されており、Office 365 テナント内にある高機能な組織データを Azure へとスケーラブルに取り込み、分析アプリケーションを構築して、それらの貴重なデータ資産に基づくインサイトを抽出することができます。 Privileged Access Management との統合により、Office 365 内の貴重な選別済みデータをセキュリティで保護することができます。  Microsoft Graph データ接続の概要については[こちらのリンク](https://docs.microsoft.com/graph/data-connect-concept-overview)を、ライセンス情報については[こちらのリンク](https://docs.microsoft.com/graph/data-connect-policies#licensing)を参照してください。

この記事では、Azure Data Factory のコピー アクティビティを使用して、Office 365 からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能
ADF Office 365 コネクタと Microsoft Graph データ接続を使用すると、アドレス帳の連絡先、予定表イベント、メール メッセージ、ユーザー情報、メールボックスの設定など、Exchange Email 対応のメールボックスからさまざまな種類のデータセットを大量に取り込むことができます。  使用できるデータセットの詳細な一覧については、[こちら](https://docs.microsoft.com/graph/data-connect-datasets)を参照してください。

現在、1 回のコピー操作の中で実行できるのは、**Office 365 から [Azure Blob Storage](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2 への、](connector-azure-data-lake-storage.md)JSON 形式** (setOfObjects 型) でのデータ コピーのみとなっています。 Office 365 を他の種類のデータ ストアに読み込んだり、他の形式で読み込む必要がある場合は、最初のコピー アクティビティを後続のコピー アクティビティに連結して、[サポートされている ADF 変換先ストアに関する記事に記載されている、任意のストアにデータを読み込むことができます ](copy-activity-overview.md#supported-data-stores-and-formats) (「サポートされるデータ ストアと形式」表の "シンクとしてサポート" 列をご覧ください)。

>[!IMPORTANT]
>- データ ファクトリとシンク データ ストアを含んだ Azure サブスクリプションは、Office 365 テナントと同じ Azure Active Directory (Azure AD) テナントの下に配置する必要があります。
>- コピー アクティビティに使用されるリージョンとコピー先が、Office 365 テナント ユーザーのメールボックスの場所と同じリージョン内であることを確認してください。 Azure IR の場所がどのように決定されるかについては、[こちら](concepts-integration-runtime.md#integration-runtime-location)をご覧ください。 サポートされている Office リージョンと、対応する Azure リージョンの一覧については、[こちらの表](https://docs.microsoft.com/graph/data-connect-datasets#regions)をご覧ください。
>- サービス プリンシパルの認証は、Azure Blob Storage、Azure Data Lake Storage Gen1、および Azure Data Lake Storage Gen2 で宛先ストアとしてサポートされている唯一の認証メカニズムです。

## <a name="prerequisites"></a>前提条件

Office 365 から Azure にデータをコピーするには、前提条件として次の手順を完了する必要があります。

- Office 365 テナントの管理者は、[こちら](https://docs.microsoft.com/graph/data-connect-get-started)の説明に従って、オンボーディング アクションを完了する必要があります。
- Azure Active Directory で Azure AD Web アプリケーションを作成し、構成します。  手順については、[Microsoft Azure での Ruby アプリケーションの作成](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)に関するページを参照してください。
- 次の値を記録しておきます。Office 365 用のリンク済みサービスを定義するときに使います。
    - テナント ID。 手順については、「[テナント ID を取得する](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)」をご覧ください。
    - アプリケーション ID とアプリケーション キー。  手順については、「[アプリケーション ID と認証キーを取得する](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)」をご覧ください。
- Azure AD Web アプリケーションの所有者としてデータ アクセス要求を行うユーザーの ID を追加します (Azure AD Web アプリケーションから、[設定] > [所有者] > [所有者の追加])。 
    - ユーザー ID は、データの取得先の Office 365 組織に属している必要があり、Guest ユーザーであってはなりません。

## <a name="approving-new-data-access-requests"></a>新しいデータ アクセス要求の承認

このコンテキスト (アクセスされようとしているデータ テーブル、データが読み込まれようとしている宛先アカウント、データ アクセス要求を行っているユーザー ID の組み合わせ) のデータを要求するのはこれが初めてである場合は、コピー アクティビティの状態が "In Progress (進行中)" として表示されます。[[アクション] の下にある [詳細]](copy-activity-overview.md#monitoring) リンクをクリックしたときにのみ、状態は "RequestingConsent" と表示されます。  データの抽出を進めるには、データ アクセス承認者グループのメンバーが、Privileged Access Management で要求を承認する必要があります。

承認者がデータ アクセス要求を承認する方法については[こちら](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal)を、Privileged Access Management との統合に関する概要 (データ アクセス承認者グループの設定を含む) については[こちら](https://docs.microsoft.com/graph/data-connect-pam)をご覧ください。

## <a name="policy-validation"></a>ポリシー検証

ADF がマネージド アプリの一部として作成され、管理リソース グループ内のリソースに対して Azure ポリシーの割り当てが行われた場合は、コピー アクティビティが実行されるたびに、ポリシー割り当てが適用されるかどうかが ADF によってチェックされます。 サポートされているポリシーの一覧については、[こちら](https://docs.microsoft.com/graph/data-connect-policies#policies)をご覧ください。

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
| connectVia | データ ストアに接続するために使用される統合ランタイム。  指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ |

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
| tableName | Office 365 から抽出するデータセットの名前です。 抽出に使用できる Office 365 データセットの一覧については、[こちら](https://docs.microsoft.com/graph/data-connect-datasets#datasets)をご覧ください。 | はい |

データセットに `dateFilterColumn`、`startTime`、`endTime`、`userScopeFilterUri` を設定していた場合は現状のまま引き続きサポートされますが、今後のアクティビティ ソースでは新しいモデルを使用することをお勧めします。

**例**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Office 365 ソースでサポートされるプロパティの一覧を示します。

### <a name="office-365-as-source"></a>ソースとしての Office 365

Office 365 からデータをコピーする場合、コピー アクティビティの **source** セクションで次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**Office365Source** | はい |
| allowedGroups | グループ選択の述語。  このプロパティを使用して、データの取得先のユーザー グループを最大 10 個まで選択します。  グループが指定されていない場合、組織全体のデータが返されます。 | いいえ |
| userScopeFilterUri | `allowedGroups` プロパティが指定されていない場合、テナント全体に適用される述語式を使用して、特定の行をフィルター処理して Office 365 から抽出することができます。 述語の形式は、Microsoft Graph API のクエリ形式 (例: `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`) と一致している必要があります。 | いいえ |
| dateFilterColumn | DateTime フィルター列の名前。 Office 365 データを抽出する時間範囲を制限するには、このプロパティを使用します。 | はい (データセットに DateTime 列が 1 つ以上ある場合)。 この DateTime フィルターが必要なデータセットの一覧については、[こちら](https://docs.microsoft.com/graph/data-connect-filtering#filtering)を参照してください。 |
| startTime | フィルター処理する開始 DateTime の値。 | はい (`dateFilterColumn` が指定されている場合) |
| endTime | フィルター処理する終了 DateTime の値。 | はい (`dateFilterColumn` が指定されている場合) |
| outputColumns | シンクにコピーする列の配列。 | いいえ |

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
                "type": "Office365Source",
                "dateFilterColumn": "CreatedDateTime",
                "startTime": "2019-04-28T16:00:00.000Z",
                "endTime": "2019-05-05T16:00:00.000Z",
                "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'",
                "outputColumns": [
                    {
                        "name": "Id"
                    },
                    {
                        "name": "CreatedDateTime"
                    },
                    {
                        "name": "LastModifiedDateTime"
                    },
                    {
                        "name": "ChangeKey"
                    },
                    {
                        "name": "Categories"
                    },
                    {
                        "name": "OriginalStartTimeZone"
                    },
                    {
                        "name": "OriginalEndTimeZone"
                    },
                    {
                        "name": "ResponseStatus"
                    },
                    {
                        "name": "iCalUId"
                    },
                    {
                        "name": "ReminderMinutesBeforeStart"
                    },
                    {
                        "name": "IsReminderOn"
                    },
                    {
                        "name": "HasAttachments"
                    },
                    {
                        "name": "Subject"
                    },
                    {
                        "name": "Body"
                    },
                    {
                        "name": "Importance"
                    },
                    {
                        "name": "Sensitivity"
                    },
                    {
                        "name": "Start"
                    },
                    {
                        "name": "End"
                    },
                    {
                        "name": "Location"
                    },
                    {
                        "name": "IsAllDay"
                    },
                    {
                        "name": "IsCancelled"
                    },
                    {
                        "name": "IsOrganizer"
                    },
                    {
                        "name": "Recurrence"
                    },
                    {
                        "name": "ResponseRequested"
                    },
                    {
                        "name": "ShowAs"
                    },
                    {
                        "name": "Type"
                    },
                    {
                        "name": "Attendees"
                    },
                    {
                        "name": "Organizer"
                    },
                    {
                        "name": "WebLink"
                    },
                    {
                        "name": "Attachments"
                    },
                    {
                        "name": "BodyPreview"
                    },
                    {
                        "name": "Locations"
                    },
                    {
                        "name": "OnlineMeetingUrl"
                    },
                    {
                        "name": "OriginalStart"
                    },
                    {
                        "name": "SeriesMasterId"
                    }
                ]
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
