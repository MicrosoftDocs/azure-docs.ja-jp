---
title: Azure Security Center でロジック アプリを監視するためのログを設定する
description: 診断ログを設定して、Azure Security Center 内の Logic Apps リソースの正常性を監視します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 42b33a5b96de7334f8310b040052c633342f5e05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712387"
---
# <a name="set-up-logging-to-monitor-logic-apps-in-azure-security-center"></a>Azure Security Center でロジック アプリを監視するためのログを設定する

[Microsoft Azure Security Center](../security-center/security-center-introduction.md) で Logic Apps リソースを監視するときに、[ロジック アプリが既定のポリシーに従っているかどうかを確認する](#view-logic-apps-health-status)ことができます。 Azure には、ログを有効にし、ログの出力先を正しく設定した後に、Logic Apps リソースの正常性状態が表示されます。 この記事では、診断ログを構成し、すべてのロジック アプリが正常なリソースであることを確認する方法について説明します。

> [!TIP]
> Logic Apps サービスの現在の状態を確認するには、利用可能な各リージョンのさまざまな製品とサービスの状態が表示されている [Azure の状態のページ](https://status.azure.com/)を確認します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合は、始める前に[無料の Azure アカウントを作成](https://azure.microsoft.com/free/)してください。
* [診断ログが有効になっている](#enable-diagnostic-logging)既存のロジック アプリ。
* Log Analytics ワークスペース。ロジック アプリのログを有効にするために必要です。 ワークスペースがない場合は、まず[ワークスペースを作成](../azure-monitor/logs/quick-create-workspace.md)します。

## <a name="enable-diagnostic-logging"></a>診断ログの有効化

ロジック アプリのリソース正常性状態を表示するには、まず[診断ログを設定する](monitor-logic-apps-log-analytics.md)必要があります。 既に Log Analytics ワークスペースがある場合は、ロジック アプリを作成するとき、または既存のロジック アプリでログを有効にすることができます。

> [!TIP]
> 既定では、Logic Apps の診断ログを有効にすることをお勧めします。 ただし、この設定はロジック アプリの場合は自分で制御します。 ロジック アプリの診断ログを有効にすると、セキュリティ インシデントの分析に役立つ情報を使用できます。

### <a name="check-diagnostic-logging-setting"></a>診断ログ設定を確認する

ロジック アプリで診断ログが有効になっているかどうか不明な場合は、以下のように Security Center を確認してください。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 検索バーに「**Security Center**」と入力して選択します。
1. Security Center ダッシュボード メニューの **[全般]** で **[推奨事項]** を選択します。
1. セキュリティに関する推薦事項の表で、セキュリティ制御の表から **[監査とログを有効にする]** &gt; **[Logic Apps で診断ログを有効にする必要がある]** を探して選択します。
1. [推奨事項] ページで、 **[修復手順]** セクションを展開し、オプションを確認します。 Logic Apps 診断を有効にするには、 **[クイック修正]** ボタンを選択するか、手動による修復手順に従ってください。

## <a name="view-logic-apps-health-status"></a>ロジック アプリの正常性状態を表示する

[診断ログを有効](#enable-diagnostic-logging)にした後、Security Center でロジック アプリの正常性状態を確認できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 検索バーに「**Security Center**」と入力して選択します。
1. Security Center ダッシュボード メニューの **[全般]** で **[インベントリ]** を選択します。
1. [インベントリ] ページで、アセット リストをフィルター処理して Logic Apps リソースのみを表示します。 [ページ] メニューで、 **[リソースの種類]** &gt; **[ロジック アプリ]** を選択します。

   **[異常なリソース]** カウンターには、Security Center によって異常と見なされたロジック アプリの数が示されます。
1.  ロジック アプリ リソースの一覧の **[推奨事項]** 列を確認します。 特定のロジック アプリの正常性の詳細を確認するには、リソース名を選択するか、省略記号ボタン ( **...** ) &gt; **[リソースを表示]** の順に選択します。
1.  潜在的なリソース正常性の問題を修復するには、ロジック アプリ用に一覧表示されている手順に従います。

診断ログが既に有効になっている場合は、ログの出力先に問題がある可能性があります。 [さまざまな診断ログの出力先に関する問題を修正する方法](#fix-diagnostic-logging-for-logic-apps)について確認してください。

## <a name="fix-diagnostic-logging-for-logic-apps"></a>ロジック アプリの診断ログを修正する

[ロジック アプリが Security Center で異常と表示されている](#view-logic-apps-health-status)場合は、Azure portal または Azure CLI を使用して、コード ビューでロジック アプリを開きます。 次に、診断ログの出力先の構成を確認します:[Azure Log Analytics](#log-analytics-and-event-hubs-destinations)、[Azure Event Hubs](#log-analytics-and-event-hubs-destinations)、または [Azure Storage アカウント](#storage-account-destination)。

### <a name="log-analytics-and-event-hubs-destinations"></a>Log Analytics と Event Hubs の出力先

Logic Apps 診断ログの出力先として Log Analytics または Event Hubs を使用する場合は、次の設定を確認してください。 

1. 診断ログが有効になっていることを確認するには、診断設定の `logs.enabled` フィールドが `true` に設定されていることを確認します。 
1. 出力先としてストレージ アカウントを設定していないことを確認するには、`storageAccountId` フィールドが `false` に設定されていることを確認します。

例:

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
                "notEquals": "true"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/storageAccountId",
                "exists": false
            }
        ]
    }
] 
```

### <a name="storage-account-destination"></a>ストレージ アカウントの出力先

Logic Apps 診断ログの出力先としてストレージ アカウントを使用する場合は、次の設定を確認してください。

1. 診断ログが有効になっていることを確認するには、診断設定の `logs.enabled` フィールドが `true` に設定されていることを確認します。
1. 診断ログの保持ポリシーが有効になっていることを確認するには、`retentionPolicy.enabled` フィールドが `true` に設定されていることを確認します。
1. 保持期間が 0 から 365 日までの間に設定されていることを確認するには、`retentionPolicy.days` フィールドが 0 から 365 までの数値に設定されていることを確認します。

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "0"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "[parameters('requiredRetentionDays')]"
            }
          ]
    },
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    }
]
```