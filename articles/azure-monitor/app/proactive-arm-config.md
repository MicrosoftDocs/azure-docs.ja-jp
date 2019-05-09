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
ms.date: 02/07/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 3ab50c92543615488d9ced599df433bf7e1e4061
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962232"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Application Insights スマート検出ルールを管理する

Application Insights のスマート検出ルールは、[Azure Resource Manager テンプレート](../../azure-resource-manager/resource-group-authoring-templates.md)を使用して管理および構成できます。
この手法は、Azure Resource Manager オートメーションで新しい Application Insights リソースをデプロイするとき、または既存のリソースの設定を変更するときに使用できます。

## <a name="smart-detection-rule-configuration"></a>スマート検出ルールの構成

スマート検出ルールに対して次の設定を構成できます。
- ルールが有効になっているかどうか (既定値は **true**)。
- 検出が見つかったときに、サブスクリプションの所有者、共同作成者、閲覧者に電子メールを送信する必要があるかどうか (既定値は**true**)。
- 検出が見つかったときに通知を受ける必要があるその他の電子メール受信者。
- * 電子メールの構成は、_プレビュー_ とマークされたスマート検出ルールで使用できません。

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

## <a name="smart-detection-rule-names"></a>スマート検出ルール名

Azure Resource Manager テンプレートで使用する必要がある、ポータルに表示されるスマート検出ルール名とその内部名の表を次に示します。

> [!NOTE]
> プレビューとしてマークされているスマート検出ルールでは、電子メール通知はサポートされません。 そのため、これらのルールに対して有効なプロパティのみを設定できます。 

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
| Resource utilization issue detected (リソース使用率の問題の検出) (プレビュー) | extension_resourceutilizationextensionspackage |

## <a name="who-receives-the-classic-alert-notifications"></a>(クラシック) アラート通知は誰が受け取りますか。

このセクションは、スマート検出クラシック アラートにのみ適用され、目的の受信者だけが通知を受け取るように、アラート通知を最適化するために役立ちます。 [クラシック アラート](../platform/alerts-classic.overview.md)と新しいアラート エクスペリエンスの違いの詳細については、[アラートの概要の記事](../platform/alerts-overview.md)を参照してください。 現在スマート検出アラートは、クラシック アラート エクスペリエンスのみをサポートしています。 この 1 つの例外は、[Azure クラウド サービスのスマート検出アラート](./proactive-cloud-services.md)です。 Azure クラウド サービスのスマート検出アラートのアラート通知を制御するには、[アクション グループ](../platform/action-groups.md)を使用します。

* スマート検出/クラシック アラート通知には、特定の受信者の使用をお勧めします。

* スマート検出アラートの場合、**一括/グループ** チェックボックス オプションが有効にされていれば、サブスクリプション内の所有者、共同作成者、または閲覧者ロールを持つユーザーに送信されます。 実際には、サブスクリプションの Application Insights リソースにアクセスできる _すべて_ のユーザーが範囲内になり、通知を受け取ります。 

> [!NOTE]
> 現在、**一括/グループ** チェックボックス オプションを使用しており、それを無効にすると、変更を元に戻すことはできません。

## <a name="next-steps"></a>次の手順

自動検出の詳細を確認します。

- [失敗の異常](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [メモリ リーク](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [パフォーマンスの異常](../../azure-monitor/app/proactive-performance-diagnostics.md)