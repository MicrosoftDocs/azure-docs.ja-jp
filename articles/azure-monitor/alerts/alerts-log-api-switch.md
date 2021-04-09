---
title: 現在の Azure Monitor ログ アラート API にアップグレードする
description: ログ アラート ScheduledQueryRules API に切り替える方法について説明します。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b91560f09b30cc5d3089e5beca4d670f94f33402
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031312"
---
# <a name="upgrade-to-the-current-log-alerts-api-from-legacy-log-analytics-alert-api"></a>現在のログ アラート API にレガシ Log Analytics アラート API からアップグレードする

> [!NOTE]
> この記事は、Azure パブリック (Azure Government または Azure China クラウドには関係 **しません**) にのみ関係します。

> [!NOTE]
> ユーザーが、基本設定を現在の [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) に切り替える選択をした後は、古い[従来の Log Analytics アラート API](./api-alerts.md) の使用に戻すことはできません。

以前は、ユーザーは[従来の Log Analytics アラート API](./api-alerts.md) を使用して、ログ アラート ルールを管理していました。 現在のワークスペースでは、[ScheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) を使用します。 この記事では、従来の API から現在の API に切り替えるメリットと手順について説明します。

## <a name="benefits"></a>メリット

- アラート ルールを作成するための単一のテンプレート (以前は 3 つの個別のテンプレートが必要)。
- Log Analytics ワークスペースまたは Application Insights リソースの両方に対する単一の API。
- [PowerShell コマンドレット サポート](./alerts-log.md#managing-log-alerts-using-powershell)。
- 他のすべてのアラートの種類に対する重大度の調整。
- Log Analytics ワークスペースや Application Insights リソースなどの複数の外部リソースにまたがる[クロス ワークスペース ログ アラート](../logs/cross-workspace-query.md)の作成機能。
- ユーザーは、[集計] パラメーターを使用して、アラートを分割するディメンションを指定できます。
- ログ アラートの期間は、最大 2 日間のデータに拡張されました (以前は 1 日に制限)。

## <a name="impact"></a>影響

- すべての新しいルールは、現在の API で作成または編集する必要があります。 [Azure リソース テンプレートによるサンプルの使用](alerts-log-create-templates.md)および [PowerShell によるサンプルの使用](./alerts-log.md#managing-log-alerts-using-powershell)に関するページを参照してください。
- ルールが現在の API 内の Azure Resource Manager 追跡対象リソースになり、一意である必要があるため、ルールのリソース ID は、`<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` の構造に変更されます。 アラート ルールの表示名は変更されません。

## <a name="process"></a>Process

切り替え手順は対話型ではなく、ほとんどの場合、手動の手順は必要ありません。 アラート ルールは、切り替え中または切り替え後に停止またはストールすることはありません。
この呼び出しを実行して、特定の Log Analytics ワークスペースに関連付けられているすべてのアラート ルールを切り替えます。

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

次の JSON を含む要求本文がある場合:

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

次に、上記の API 呼び出しの呼び出しを簡略化するオープンソースのコマンドライン ツールである [ARMClient](https://github.com/projectkudu/ARMClient) を使用する例を示します。

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

切り替えが成功した場合、応答は次のようになります。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

## <a name="check-switching-status-of-workspace"></a>ワークスペースの切り替え状態を確認する

この API 呼び出しを使用して、切り替えの状態を確認することもできます。

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

[ARMClient](https://github.com/projectkudu/ARMClient) ツールも使用できます。

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Log Analytics ワークスペースが [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) に切り替えられると、応答は次のようになります。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Log Analytics ワークスペースが切り替えられなかった場合、応答は次のようになります。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>次のステップ

- [Azure Monitor でのログ アラート](./alerts-unified-log.md)について学習します。
- [API を使用してログ アラートを管理](alerts-log-create-templates.md)する方法について説明します。
- [PowerShell を使用してログ アラートを管理](./alerts-log.md#managing-log-alerts-using-powershell)する方法について説明します。
- [Azure Alerts のエクスペリエンス](./alerts-overview.md)の詳細について学習する。