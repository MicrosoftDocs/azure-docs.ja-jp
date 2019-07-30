---
title: Azure Resource Manager テンプレートで Azure Application Insights スマート検出ルール設定を構成する | Microsoft Docs
description: Azure Resource Manager テンプレートでの Azure Application Insights スマート検出ルールの管理と構成を自動化する
services: application-insights
documentationcenter: ''
author: harelbr
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 6bb89eec0b4905e101bed87d3d3fc617dec589e0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477862"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Application Insights スマート検出ルールを管理する

Application Insights のスマート検出ルールは、[Azure Resource Manager テンプレート](../../azure-resource-manager/resource-group-authoring-templates.md)を使用して管理および構成できます。
この手法は、Azure Resource Manager オートメーションで新しい Application Insights リソースをデプロイするとき、または既存のリソースの設定を変更するときに使用できます。

## <a name="smart-detection-rule-configuration"></a>スマート検出ルールの構成

スマート検出ルールに対して次の設定を構成できます。
- ルールが有効になっているかどうか (既定値は **true**)。
- 検出が見つかったときに、メールがサブスクリプションの [[閲覧者の監視]](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) ロールと [[共同作成者の監視]](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) ロールに関連付けられたユーザーに送信される必要がある場合 (既定値は **true**)。
- 検出が見つかったときに通知を受ける必要があるその他の電子メール受信者。
    -  メールの構成は、_プレビュー_とマークされたスマート検出ルールで使用できません。

Azure Resource Manager を使用してルールの設定を構成できるように、スマート検出ルールの構成は、Application Insights リソース内で **ProactiveDetectionConfigs** という名前の内部リソースとして使用できるようになりました。
柔軟性を最大化するために、各スマート検出ルールを一意の通知設定で構成できます。

## 

## <a name="examples"></a>例

Azure Resource Manager テンプレートを使用してスマート検出ルールの設定を構成する方法を示す例を次にいくつか示します。
すべてのサンプルは、 _"myApplication"_ という名前の Application Insights リソースと、 _"longdependencyduration"_ という内部名の "長い依存関係期間スマート検出ルール" を参照します。
Application Insights リソース名を置換し、関連するスマート検出ルールの内部名を指定してください。 各スマート検出ルールに対応する Azure Resource Manager 内部名の一覧については、次の表をご確認ください。

### <a name="disable-a-smart-detection-rule"></a>スマート検出ルールを無効にする

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
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
        "ApplicationId": "myApplication"
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
        "ApplicationId": "myApplication"
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
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

### <a name="failure-anomalies-v2-non-classic-alert-rule"></a>エラーの異常 v2 (非従来型) のアラート ルール

この Azure Resource Manager テンプレートでは、重大度 2 でのエラーの異常 v2 のアラート ルールの構成について示しています。 この新しいバージョンのエラーの異常のアラート ルールは、新しい Azure のアラート プラットフォームの一部であり、[従来のアラートの回収プロセス](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/)の一環として廃止される従来のバージョンに置き換わります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "properties": {
                  "description": "Detects a spike in the failure rate of requests or dependencies",
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
> この Azure Resource Manager テンプレートは、エラーの異常 v2 のアラート ルールに対して固有であり、この記事で説明されているその他の従来のスマート検出ルールとは異なります。   

## <a name="smart-detection-rule-names"></a>スマート検出ルール名

Azure Resource Manager テンプレートで使用する必要がある、ポータルに表示されるスマート検出ルール名とその内部名の表を次に示します。

> [!NOTE]
> _プレビュー_としてマークされているスマート検出ルールでは、メール通知がサポートされません。 そのため、これらのルールに対して_有効な_プロパティのみを設定できます。 

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

## <a name="next-steps"></a>次の手順

自動検出の詳細を確認します。

- [失敗の異常](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [メモリ リーク](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [パフォーマンスの異常](../../azure-monitor/app/proactive-performance-diagnostics.md)
