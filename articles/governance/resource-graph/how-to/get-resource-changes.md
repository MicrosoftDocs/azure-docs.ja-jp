---
title: リソースの変更の取得
description: いつリソースが変更されたかを知り、変更されたプロパティの一覧を取得する方法を理解します。
services: resource-graph
author: dcthegeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: b6ef57a3f39c82be30d92aef72c1bbe03b653768
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66236503"
---
# <a name="get-resource-changes"></a>リソースの変更の取得

リソースは、日々の使用や再構成を通じて、また再デプロイによっても変更されます。
変更は、個別のプロセスで行われることもあれば、自動プロセスによって行われることもあります。 多くの変更は目的を持って行われますが、そうでないものもあります。 Azure Resource Graph を使用すると、変更履歴の最後の 14 日を使用して次のことを実行できます。

- Azure Resource Manager プロパティでいつ変更が検出されたかを見つける。
- その変更イベントの一部としてどのプロパティが変更されたかを知る。

次のサンプル シナリオにとって、変更の検出と詳細は貴重です。

- インシデント管理中に、_潜在的に_関連している変更を把握する。 特定の時間枠の間の変更イベントのクエリを実行し、変更の詳細を評価する。
- CMDB (Configuration Management Database) を最新状態に保つ。 スケジュールされた頻度ですべてのリソースとそれらの全プロパティ セットを更新する代わりに、変更されたものだけを取得します。
- リソースがコンプライアンス状態を変更したときに他のどのプロパティが変更された可能性があるかを把握する。 これらの追加プロパティの評価により、Azure Policy 定義による管理が必要な可能性がある他のプロパティの分析情報を得ることができます。

この記事では、Resource Graph の SDK を介してこの情報を収集する方法について説明します。 Azure portal でこの情報を表示するには、Azure Policy の[変更履歴](../../policy/how-to/determine-non-compliance.md#change-history-preview)またはAzure アクティビティ ログの[変更履歴](../../../azure-monitor/platform/activity-log-view.md#azure-portal)を参照してください。

> [!NOTE]
> Resource Graph の変更の詳細は、Resource Manager プロパティが対象です。 仮想マシン内で変更を追跡するには、Azure Automation の[変更の追跡](../../../automation/automation-change-tracking.md)または Azure Policy の [VM のゲスト構成](../../policy/concepts/guest-configuration.md)を参照してください。

> [!IMPORTANT]
> Azure Resource Graph の変更履歴はパブリック プレビュー段階です。

## <a name="find-when-changes-were-detected"></a>いつ変更が検出されたかを見つける

リソースで何が変更されたかを知るための最初の手順は、時間枠内でのそのリソースに関連した変更イベントを見つけることです。 この手順は、**resourceChanges** REST エンドポイントを通じて行われます。

**resourceChanges** エンドポイントには、要求本文に次の 2 つのパラメーターが必要です。

- **resourceId**:変更を検索する対象の Azure リソース。
- **interval**:**Zulu タイム ゾーン (Z)** を使用して変更イベントを確認する期間の _start_ 日付と _end_ 日付を持つプロパティ。

要求本文の例

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

上記の要求本文を使用した場合、**resourceChanges** の REST API URI は次のようになります。

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

応答は次の例のようになります。

```json
{
    "changes": [{
            "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
            "beforeSnapshot": {
                "timestamp": "2019-03-29T01:32:05.993Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-29T01:54:24.42Z"
            }
        },
        {
            "changeId": "9dc352cb-b7c1-4198-9eda-e5e3ed66aec8",
            "beforeSnapshot": {
                "timestamp": "2019-03-28T10:30:19.68Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-28T21:12:31.337Z"
            }
        }
    ]
}
```

**resourceId** の検出された各変更イベントには、そのリソースに固有の **changeId** があります。 **changeId** 文字列には他のプロパティが含まれていることがありますが、固有であることだけが保証されています。 変更レコードには、スナップショットが取られる前と後の時刻が含まれています。
変更イベントは、この時間枠内のある時点で発生しました。

## <a name="see-what-properties-changed"></a>どのプロパティが変更されたかを確認する

**resourceChanges** エンドポイントから **changeId** を入手したので、次に **resourceChangeDetails** REST エンドポイントを使用して変更イベントの詳細を取得します。

**resourceChangesDetails** エンドポイントには、要求本文に次の 2 つのパラメーターが必要です。

- **resourceId**:変更を検索する対象の Azure リソース。
- **changeId**:**resourceChanges** から収集された **resourceId** の固有の変更イベント。

要求本文の例

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
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

結果をプログラムで比較するには、各スナップショットの **content** 部分を比較して違いを判別します。 スナップショット全体を比較すると、**timestamp**は、予期されているにもかかわらず、常に違いとして示されています。

## <a name="next-steps"></a>次の手順

- [初歩的なクエリ](../samples/starter.md)で使用されている言語を確認します。
- [高度なクエリ](../samples/advanced.md)で高度な使用方法を確認します。
- [リソースの探索](../concepts/explore-resources.md)について学習します。