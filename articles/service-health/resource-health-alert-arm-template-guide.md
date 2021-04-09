---
title: Resource Health アラートを作成するためのテンプレート
description: Azure リソースが利用不可になったときに通知するアラートをプログラムで作成します。
ms.topic: conceptual
ms.date: 9/4/2018
ms.openlocfilehash: 4f1cbe1e2d2c185906feb4ccba380cb31df864f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588198"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Resource Manager テンプレートを使用して Resource Health アラートを構成する

この記事では、Azure Resource Manager テンプレートと Azure PowerShell を使用して、Resource Health アクティビティ ログ アラートをプログラムで作成する方法を示します。

Azure Resource Health では、Azure リソースの現在および過去の正常性状態に関する情報が持続的に通知されます。 Azure Resource Health アラートでは、これらのリソースの正常性状態が変化すると、ほぼリアルタイムで通知できます。 Resource Health アラートをプログラムで作成すると、ユーザーは通知を一括で作成およびカスタマイズできます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

このページの指示に従うには、事前にいくつかの項目を設定する必要があります。

1. [Azure PowerShell モジュール](/powershell/azure/install-az-ps)をインストールする必要があります
2. 通知を行うよう構成された[アクション グループを作成または再利用する](../azure-monitor/alerts/action-groups.md)必要があります。

## <a name="instructions"></a>Instructions
1. PowerShell を使用し、アカウントを使用して Azure にログインし、操作するサブスクリプションを選択します。

    ```azurepowershell
    Login-AzAccount
    Select-AzSubscription -Subscription <subscriptionId>
    ```

    > `Get-AzSubscription` を使用すると、アクセスできるサブスクリプションを一覧表示できます。

2. アクション グループの完全な Azure Resource Manager ID を検索して保存します。

    ```azurepowershell
    (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id
    ```

3. Resource Health アラートの Resource Manager テンプレートを `resourcehealthalert.json` として作成して保存します ([次の詳細を参照](#resource-manager-template-options-for-resource-health-alerts))。

4. このテンプレートを使用して、新しい Azure Resource Manager デプロイを作成します。

    ```azurepowershell
    New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>
    ```

5. アラート名と、先ほどコピーしたアクション グループのリソース ID の入力を求めるメッセージが表示されます。

    ```azurepowershell
    Supply values for the following parameters:
    (Type !? for Help.)
    activityLogAlertName: <Alert Name>
    actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>
    ```

6. すべてが正常に動作すると、PowerShell に確認メッセージが表示されます。

    ```output
    DeploymentName          : ExampleDeployment
    ResourceGroupName       : <resourceGroup>
    ProvisioningState       : Succeeded
    Timestamp               : 11/8/2017 2:32:00 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                            Name                     Type       Value
                            ===============          =========  ==========
                            activityLogAlertName     String     <Alert Name>
                            activityLogAlertEnabled  Bool       True
                            actionGroupResourceId    String     /...

    Outputs                 :
    DeploymentDebugLogLevel :
    ```

このプロセスを完全に自動化する場合は、手順 5 で値を求めるメッセージが表示されないよう、Resource Manager テンプレートを編集します。

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Resource Health アラートの Resource Manager テンプレート オプション

Resource Health アラートを作成するための開始点として、この基本テンプレートを使用できます。 このテンプレートは記述どおりに動作し、ユーザーはサインアップして、サブスクリプション内のすべてのリソースにわたり、新しくアクティブになったすべてのリソース正常性イベントに関するアラートを受信できます。

> また、この記事の最後に、このテンプレートと比較して Resource Health アラートの信号雑音比率が大きくなる、より複雑な警告テンプレートも示されています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

ただし、このような広範なアラートは一般的に推奨されません。 以下に、重要なイベントに焦点を合わせるためにこのアラートの範囲を設定する方法について説明します。

### <a name="adjusting-the-alert-scope"></a>アラート スコープの調整

Resource Health アラートは、次の 3 つの異なるスコープでイベントを監視するよう構成できます。

 * サブスクリプション レベル
 * リソース グループ レベル
 * リソース レベル

アラート テンプレートはサブスクリプション レベルで構成されますが、特定のリソース、または特定のリソース グループ内のリソースに関してのみ通知を行うようアラートを構成する場合は、上記のテンプレートの `scopes` セクションを変更するだけです。

リソース グループ レベルのスコープの場合、scopes セクションは次のようになります。
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

一方、リソース レベルのスコープの場合、scopes セクションは次のようになります。

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

例: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Azure portal に移動し、Azure リソースを表示しているときに URL を確認して、この文字列を取得できます。

### <a name="adjusting-the-resource-types-which-alert-you"></a>アラートを生成するリソースの種類の調整

サブスクリプションまたはリソース グループ レベルのアラートには、異なる種類のリソースが含まれる場合があります。 リソースの種類の特定のサブセットからのみ生成されるようアラートを制限する場合は、次のように、テンプレートの `condition` セクションで定義できます。

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.COMPUTE/VIRTUALMACHINES",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.STORAGE/STORAGEACCOUNTS",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

ここでは、`anyOf` ラッパーを使用して、指定したいずれかの条件と一致する Resource Health アラートを定義し、アラートが特定のリソースの種類をターゲットにできるようにします。

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>アラートを生成する Resource Health イベントの調整
リソースで正常性イベントが発生すると、正常性イベントの状態を表す一連の段階: `Active`、`In Progress`、`Updated`、および `Resolved` を経由する可能性があります。

リソースが異常になった場合にのみ通知を受け取るには、`status` が `Active` の際にのみ通知するようアラートを構成する必要があります。 一方、その他の段階でも通知を受け取るには、次のような詳細情報を追加します。

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "In Progress"
                },
                {
                    "field": "status",
                    "equals": "Resolved"
                },
                {
                    "field": "status",
                    "equals": "Updated"
                }
            ]
        }
    ]
}
```

正常性イベントの 4 つすべての段階で通知を受け取るには、この条件をすべて削除して、`status` プロパティとは関係なくアラートが生成されるようにします。

> [!NOTE]
> 各 "anyOf" セクションには、フィールド型の値を 1 つだけ含める必要があります。

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>"Unknown" イベントを回避するための Resource Health アラートの調整

Azure Resource Health では、テスト ランナーを使用してリソースを持続的に監視することで、リソースの最新の正常性をレポートできます。 レポートされる関連する正常性状態は"Available"、"Unavailable"、および "Degraded" です。 ただし、ランナーと Azure リソースが通信できない場合、リソースに関して "Unknown" 状態がレポートされ、"Active" 正常性イベントと見なされます。

しかし、リソースで "Unknown" がレポートされた場合、その正常性状態は前回の正確なレポートから変化していない可能性があります。 "Unknown" イベントに関するアラートを生成しない場合は、テンプレートで次のロジックを指定します。

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

この例では、現在および以前の正常性状態が "Unknown" でないイベントのみを通知します。 この変更は、アラートが携帯電話または電子メールに直接送信される場合に役立つ可能性があります。 

イベントによっては、currentHealthStatus および previousHealthStatus プロパティが null 値になっていることがあることにご注意ください。 たとえば Updated イベントが発生する場合、リソースの正常性の状態は最後のレポートから変化しておらず、追加のイベント情報が利用できるようになった (cause など) だけの可能性があります。 そのため、上記の句を使用すると、properties.currentHealthStatus および properties.previousHealthStatus の値は null に設定されるため、アラートによってはトリガーされないことがあります。

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>ユーザーが開始したイベントを回避するためのアラートの調整

Resource Health イベントは、プラットフォームまたはユーザーによって開始されたイベントによってトリガーできます。 正常性イベントが Azure プラットフォームに由来する場合にのみ通知を送信することは、理に適っています。

このような種類のイベントのみをフィルター処理するようアラートを構成することは簡単です。

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```
イベントによっては、cause フィールドが null 値になっていることがあることにご注意ください。 つまり、正常性の遷移 (available から unavailable など) が起こり、通知の遅延を避けるためにイベントがすぐにログに記録されます。 そのため、上記の句を使用すると、properties.clause プロパティ値が null に設定されるため、アラートはトリガーされないことがあります。

## <a name="complete-resource-health-alert-template"></a>完全な Resource Health アラート テンプレート

前のセクションで説明したさまざまな調整を使用した、信号雑音比率が最大になるよう構成されたサンプル テンプレートを次に示します。 イベントによっては、currentHealthStatus、previousHealthStatus、cause プロパティ値が null の場合があるという前述の注意を念頭に置いてください。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "In Progress",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Updated",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

しかし、自分にとって最も効果的な構成を把握するためには、ドキュメントで学習したツールを使用して独自のカスタマイズを行ってください。

## <a name="next-steps"></a>次のステップ

Resource Health に関する詳細情報を参照してください。
-  [Azure Resource Health の概要](Resource-health-overview.md)
-  [Azure Resource Health で利用できるリソースの種類と正常性チェック](resource-health-checks-resource-types.md)


Service Health アラートを作成します。
-  [Service Health のアラートの構成](./alerts-activity-log-service-notifications-portal.md) 
-  [Azure アクティビティ ログのイベント スキーマ](../azure-monitor/essentials/activity-log-schema.md)
