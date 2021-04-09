---
title: スマート検出のルール設定 - Azure Application Insights
description: Azure Resource Manager テンプレートでの Azure Application Insights スマート検出ルールの管理と構成を自動化する
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 02/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: e3a7b71cd8975957754ba014ecc700484c27a6d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726123"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Application Insights スマート検出ルールを管理する

Application Insights のスマート検出ルールは、[Azure Resource Manager テンプレート](../../azure-resource-manager/templates/template-syntax.md)を使用して管理および構成できます。
この手法は、Azure Resource Manager オートメーションで新しい Application Insights リソースをデプロイするとき、または既存のリソースの設定を変更するときに使用できます。

## <a name="smart-detection-rule-configuration"></a>スマート検出ルールの構成

スマート検出ルールに対して次の設定を構成できます。
- ルールが有効になっているかどうか (既定値は **true**)。
- 検出が見つかったときに、メールがサブスクリプションの [[閲覧者の監視]](../../role-based-access-control/built-in-roles.md#monitoring-reader) ロールと [[共同作成者の監視]](../../role-based-access-control/built-in-roles.md#monitoring-contributor) ロールに関連付けられたユーザーに送信される必要がある場合 (既定値は **true**)。
- 検出が見つかったときに通知を受ける必要があるその他の電子メール受信者。
    -  メールの構成は、_プレビュー_ とマークされたスマート検出ルールで使用できません。

Azure Resource Manager を使用してルールの設定を構成できるように、スマート検出ルールの構成は、Application Insights リソース内で **ProactiveDetectionConfigs** という名前の内部リソースとして使用できるようになりました。
柔軟性を最大化するために、各スマート検出ルールを一意の通知設定で構成できます。

## <a name="examples"></a>例

Azure Resource Manager テンプレートを使用してスマート検出ルールの設定を構成する方法を示す例を次にいくつか示します。
すべてのサンプルは、_"myApplication"_ という名前の Application Insights リソースと、_"longdependencyduration"_ という内部名の "長い依存関係期間スマート検出ルール" を参照します。
Application Insights リソース名を置換し、関連するスマート検出ルールの内部名を指定してください。 各スマート検出ルールに対応する Azure Resource Manager 内部名の一覧については、次の表をご確認ください。

### <a name="disable-a-smart-detection-rule"></a>スマート検出ルールを無効にする

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>スマート検出ルールの電子メール通知の送信を無効にする

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>スマート検出ルールに対して電子メール受信者を追加する

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ["alice@contoso.com", "bob@contoso.com"],
            "enabled": true
          }
        }
      ]
    }

```


## <a name="smart-detection-rule-names"></a>スマート検出ルール名

Azure Resource Manager テンプレートで使用する必要がある、ポータルに表示されるスマート検出ルール名とその内部名の表を次に示します。

> [!NOTE]
> _プレビュー_ としてマークされているスマート検出ルールでは、メール通知がサポートされません。 そのため、これらのルールに対して _有効な_ プロパティのみを設定できます。 

| Azure portal ルール名 | 内部名
|:---|:---|
| ページの読み込み速度が遅い | slowpageloadtime |
| サーバーの応答速度が遅い | slowserverresponsetime |
| 依存関係の期間が長い | longdependencyduration |
| サーバー応答速度の低下 | degradationinserverresponsetime |
| 依存関係の期間の減少 | degradationindependencyduration |
| トレースの重大度の比率の低下 (プレビュー) | extension_traceseveritydetector |
| 例外数の異常な上昇 (プレビュー) | extension_exceptionchangeextension |
| Potential memory leak detected (潜在的なメモリ リークの検出) (プレビュー) | extension_memoryleakextension |
| Potential security issue detected (潜在的なセキュリティの問題の検出) (プレビュー) | extension_securityextensionspackage |
| 日次データ ボリュームの異常な上昇 (プレビュー) | extension_billingdatavolumedailyspikeextension |

### <a name="failure-anomalies-alert-rule"></a>失敗の異常の警告ルール

この Azure Resource Manager テンプレートでは、重大度が 2 の失敗の異常警告ルールの構成について示しています。

> [!NOTE]
> 失敗の異常はグローバル サービスであるため、ルールはグローバルな場所に作成されます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> この Azure Resource Manager テンプレートは、失敗の異常の警告ルールに固有のものであり、この記事で説明されている他の従来のスマート検出ルールとは異なります。 失敗の異常を手動で管理したい場合は、Azure Monitor アラートで行います。他のすべてのスマート検出ルールは、UI の [スマート検出] ウィンドウで管理されます。

## <a name="next-steps"></a>次の手順

自動検出の詳細を確認します。

- [失敗の異常](./proactive-failure-diagnostics.md)
- [メモリ リーク](./proactive-potential-memory-leak.md)
- [パフォーマンスの異常](./proactive-performance-diagnostics.md)

