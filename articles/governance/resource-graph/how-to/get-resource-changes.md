---
title: リソースの変更の取得
description: リソースがいつ変更されたかを見つけ、変更されたプロパティの一覧を取得し、それらの差分を評価する方法について説明します。
ms.date: 10/09/2019
ms.topic: how-to
ms.openlocfilehash: 9504ac77fc4a3b03434912cc65284e2001df6e03
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873031"
---
# <a name="get-resource-changes"></a>リソースの変更の取得

リソースは、日々の使用や再構成を通じて、また再デプロイによっても変更されます。
変更は、個別のプロセスで行われることもあれば、自動プロセスによって行われることもあります。 多くの変更は目的を持って行われますが、そうでないものもあります。 Azure Resource Graph を使用すると、変更履歴の最後の 14 日を使用して次のことを実行できます。

- Azure Resource Manager プロパティでいつ変更が検出されたかを見つける
- 各リソースの変更について、プロパティ変更の詳細を参照する
- 変更の検出前と後でのリソースの完全な比較を確認する

次のサンプル シナリオにとって、変更の検出と詳細は貴重です。

- インシデント管理中に、_潜在的に_関連している変更を把握する。 特定の時間枠の間の変更イベントのクエリを実行し、変更の詳細を評価する。
- CMDB (Configuration Management Database) を最新状態に保つ。 スケジュールされた頻度ですべてのリソースとそれらの全プロパティ セットを更新する代わりに、変更されたものだけを取得します。
- リソースがコンプライアンス状態を変更したときに他のどのプロパティが変更された可能性があるかを把握する。 これらの追加プロパティの評価により、Azure Policy 定義による管理が必要な可能性がある他のプロパティの分析情報を得ることができます。

この記事では、Resource Graph の SDK を介してこの情報を収集する方法について説明します。 Azure portal でこの情報を表示するには、Azure Policy の[変更履歴](../../policy/how-to/determine-non-compliance.md#change-history-preview)またはAzure アクティビティ ログの[変更履歴](../../../azure-monitor/platform/activity-log-view.md#azure-portal)を参照してください。
インフラストラクチャ レイヤーからアプリケーションのデプロイまで、アプリケーションに対する変更の詳細については、[Azure Monitor でのアプリケーション変更分析の使用 (プレビュー)](../../../azure-monitor/app/change-analysis.md)に関するページをご覧ください。

> [!NOTE]
> Resource Graph の変更の詳細は、Resource Manager プロパティが対象です。 仮想マシン内で変更を追跡するには、Azure Automation の[変更の追跡](../../../automation/automation-change-tracking.md)または Azure Policy の [VM のゲスト構成](../../policy/concepts/guest-configuration.md)を参照してください。

> [!IMPORTANT]
> Azure Resource Graph の変更履歴はパブリック プレビュー段階です。

## <a name="find-detected-change-events-and-view-change-details"></a>検出された変更イベントの検索と変更の詳細の表示

リソースで何が変更されたかを知るための最初の手順は、時間枠内でのそのリソースに関連した変更イベントを見つけることです。 各変更イベントには、リソースの変更内容の詳細も含まれています。 この手順は、**resourceChanges** REST エンドポイントを通じて行われます。

**resourceChanges** エンドポイントでは、要求本文で次の 2 つのパラメーターが受け入れられます。

- **resourceId** \[required\]:変更を検索する対象の Azure リソース。
- **interval** \[required\]:**Zulu タイム ゾーン (Z)** を使用して変更イベントを確認する期間の _start_ 日付と _end_ 日付を持つプロパティ。
- **fetchPropertyChanges** (省略可能):応答オブジェクトにプロパティの変更が含まれている場合に設定するブール型プロパティ。

要求本文の例

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

上記の要求本文を使用した場合、**resourceChanges** の REST API URI は次のようになります。

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

応答は次の例のようになります。

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

**resourceId** の検出された各変更イベントには、次のプロパティがあります。

- **changeId** - この値は、そのリソースに対して一意です。 **changeId** 文字列には他のプロパティが含まれていることがありますが、固有であることだけが保証されています。
- **beforeSnapshot** - 変更が検出される前に取得されたリソース スナップショットの **snapshotId** と **timestamp** が含まれています。
- **afterSnapshot** - 変更が検出された後に取得されたリソース スナップショットの **snapshotId** と **timestamp** が含まれています。
- **changeType** - **beforeSnapshot** と **afterSnapshot** の間の変更レコード全体に対して検出された変更の種類が示されます。 値は次のとおりです。_Create_、_Update_、_Delete_。 **propertyChanges** プロパティ配列は、**changeType** が _Update_ の場合にのみ含まれます。
- **propertyChanges** - このプロパティの配列では、**beforeSnapshot** と **afterSnapshot** の間で更新されたすべてのリソース プロパティの詳細が示されます。
  - **propertyName** - 変更されたリソース プロパティの名前。
  - **changeCategory** - 変更内容が示されます。 値は次のとおりです。_System_ と _User_。
  - **changeType** - 個々のリソース プロパティに対して検出された変更の種類が示されます。
    値は次のとおりです。_Insert_、_Update_、_Remove_。
  - **beforeValue** - **beforeSnapshot** のリソース プロパティの値。 **changeType** が _Insert_ の場合は表示されません。
  - **afterValue** - **afterSnapshot** のリソース プロパティの値。 **changeType** が _Remove_ の場合は表示されません。

## <a name="compare-resource-changes"></a>リソースの変更の比較

**resourceChanges** エンドポイントから **changeId** を入手したので、次に **resourceChangeDetails** REST エンドポイントを使用して、リソースの変更される前と変更された後のスナップショットを取得します。

**resourceChangesDetails** エンドポイントには、要求本文に次の 2 つのパラメーターが必要です。

- **resourceId**:変更を比較する Azure リソース。
- **changeId**:**resourceChanges** から収集された **resourceId** の固有の変更イベント。

要求本文の例

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

上記の要求本文を使用した場合、**resourceChangesDetails** の REST API URI は次のようになります。

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

応答は次の例のようになります。

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** と **afterSnapshot** はそれぞれ、スナップショットが取られた時刻と、その時点のプロパティを提供ます。 変更は、これらのスナップショット間のある時点で発生しました。 上記の例を見ると、変更されたプロパティは **supportsHttpsTrafficOnly** であったことが分かります。

結果を比較するには、**resourceChanges** の **changes** プロパティを使用するか、**resourceChangeDetails** で各スナップショットの**コンテンツ**部分を評価して相違点を確認します。 スナップショットを比較すると、**timestamp**は、予期されているにもかかわらず、常に違いとして示されています。

## <a name="next-steps"></a>次の手順

- [初歩的なクエリ](../samples/starter.md)で使用されている言語を確認します。
- [高度なクエリ](../samples/advanced.md)で高度な使用方法を確認します。
- [リソースを探索する](../concepts/explore-resources.md)方法について詳しく確認します。