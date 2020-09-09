---
title: 新しい Azure Alerts API に切り替える
description: 従来の savedSearch ベースの Log Analytics Alert API の概要と、アラート ルールを新しい ScheduledQueryRules API に切り替えるプロセスについて、お客様の一般的な問題への対処の詳細を含めて説明します。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: 7e1073817013d45558a9679a4f70db0c002cfaa9
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324082"
---
# <a name="switch-api-preference-for-log-alerts"></a>ログ アラートの API の基本設定を切り替える

> [!NOTE]
> 記載されている内容は Azure パブリック クラウドのユーザーのみに適用され、Azure Government または Azure China クラウドには適用**されません**。  

> [!NOTE]
> ユーザーが、基本設定を新しい [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) に切り替える選択をした後は、古い[レガシ ログ Analytics アラート API](api-alerts.md) の使用に戻すことはできません。

最近まで、アラート ルールは Microsoft Operations Management Suite ポータルで管理していました。 新しいアラート エクスペリエンスは Log Analytics などの Microsoft Azure のさまざまなサービスと統合されており、Microsoft はお客様に[アラート ルールを OMS ポータルから Azure に拡張する](./alerts-unified-log.md)ことをお願いしました。 しかし、お客様の中断を最小限にするため、このプロセスではそれを使用するためのプログラム インターフェイスは変更されませんでした (SavedSearch に基づく [Log Analytics Alert API](api-alerts.md))。

しかし現在は、実際には Azure プログラムでは代わりに [Azure Monitor - ScheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) を使用するようになっており、[ログ アラートに対する Azure の課金](alerts-unified-log.md#pricing-and-billing-of-log-alerts)にもそれが反映されていることを、Log Analytics アラートのユーザーにお知らせします。 API を使用してログ アラートを管理する方法の詳細については、「[Azure リソース テンプレートを使用したログ アラートの管理](alerts-log.md#managing-log-alerts-using-azure-resource-template)」および「[PowerShell を使用したログ アラートの管理](alerts-log.md#managing-log-alerts-using-powershell)」をご覧ください。

## <a name="benefits-of-switching-to-new-azure-api"></a>新しい Azure API に切り替える利点

[従来の Log Analytics Alert API](api-alerts.md) ではなく [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) を使用してアラートの作成と管理を行うと、いくつかの利点があります。以下にその主要なものを示します。

- アラート ルールの[クロス ワークスペース ログ検索](../log-query/cross-workspace-query.md)を行い、Log Analytics ワークスペースや Application Insights アプリなどの外部リソースまで拡張する機能
- クエリでのグループ化に複数のフィールドを使用するとき、[scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) を使用すると、Azure portal での集計フィールドを指定できます
- [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) を使用して作成されたログ アラートには、最大 48 時間の期間を定義でき、以前より長い期間でデータをフェッチできます
- 単一リソースとして 1 回でアラート ルールを作成でき、[従来の Log Analytics Alert API](api-alerts.md) のように 3 レベルのリソースを作成する必要はありません
- Azure のクエリ ベースのログ アラートのすべてのバリエーションに対する 1 つのプログラム インターフェイス - 新しい [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) は、Log Analytics と Application Insights のルールを管理するために使用できます
- [PowerShell コマンドレット](alerts-log.md#managing-log-alerts-using-powershell)を使用してログ アラートを管理する
- ログ アラートのすべての新機能と将来の開発は、新しい [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) でのみ利用できます

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>従来のログ アラート API から切り替えるプロセス

ユーザーは、[従来の Log Analytics Alert API](api-alerts.md) または新しい [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) のどちらを使用してもかまいません。 アラート ルールの作成にどちらの API を使用しても、"*同じ API によってのみ管理できます*" (Azure portal の場合と同様)。 既定では、Azure Monitor では引き続き、Log Analytics の既存のワークスペースに対して Azure portal から新しいアラート ルールを作成するときに[従来の Log Analytics Alert API](api-alerts.md) が使用されます。 お知らせしているように、[2019 年 6 月 1 日以降に作成される新しい Log ワークスペース](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)では自動的に、Azure portal での作業時を含め、新しい [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) が既定で使用されます。

設定を scheduledQueryRules API に切り替えることによる影響は次のとおりです。

- プログラム インターフェイスを使用してログ アラートを管理するために行われるすべての操作は、代わりに [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) を使用して行う必要があります。 詳細については、[Azure リソース テンプレートによるサンプルの使用](alerts-log.md#managing-log-alerts-using-azure-resource-template)および [PowerShell によるサンプルの使用](alerts-log.md#managing-log-alerts-using-powershell)に関するページをご覧ください
- Azure portal で作成される新しいログ アラート ルールはすべて、[scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) のみを使用して作成され、ユーザーは Azure portal で[新しい API の追加機能](#benefits-of-switching-to-new-azure-api)を使用することもできます
- ログ アラート ルールの重大度が変わります ("*重大、警告、および情報*" から "*重大度値の 0、1、および 2*")。 重大度 3 および 4 でアラート ルールを作成または更新するオプションも同様です。

[従来の Log Analytics Alert API](api-alerts.md) からアラート ルールを移動するプロセスに、アラートの定義、クエリ、構成の変更は含まれません。 アラート ルールおよび監視は影響を受けず、切り替え中または切り替え後にアラートが停止またはストールすることはありません。 唯一の変更点は次のとおりです:

- API の基本設定での変更と、新しい API 経由のルールへのアクセス。
- 変更されたアラート ルール リソース URI には、この構造 `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` のアラート ルール名の代わりに、[レガシ ログ Analytics アラート API](api-alerts.md) で使用された ID が含まれています。 アラートルールの表示名は変更されません。

自主的に新しい [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) に切り替えて[従来の Log Analytics Alert API](api-alerts.md) からの使用をブロックしたいお客様は、次の API で PUT 呼び出しを実行して特定の Log Analytics ワークスペースに関連付けられているすべてのアラート ルールを切り替えることによって行うことができます。

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

要求の本文には次の JSON を含めます。

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

この API には、Azure Resource Manager API の呼び出しを簡略化するオープン ソースのコマンドライン ツールである [ARMClient](https://github.com/projectkudu/ARMClient) を使用して PowerShell コマンド ラインからアクセスすることもできます。 下に示すように、サンプル PUT 呼び出しで ARMclient ツールを使用して、特定の Log Analytics ワークスペースに関連付けられているすべてのアラート ルールを切り替えます。

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Log Analytics ワークスペース内のすべてのアラート ルールが新しい [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) を使用するように正常に切り替えられた場合、次の応答が表示されます。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

ユーザーは、Log Analytics ワークスペースの現在の状態をチェックしたり、[scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) だけを使用するように切り替えられているかどうかを確認することもできます。 確認するには、次の API で GET 呼び出しを実行します。

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

[ARMClient](https://github.com/projectkudu/ARMClient)ツールで、PowerShell コマンドラインを使用して上記を実行するには、下のサンプルを参照してください。

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

指定した Log Analytics ワークスペースが [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) のみを使用するように切り替えられている場合、応答の JSON は次のようになります。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
または、指定した Log Analytics ワークスペースが [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) のみを使用するように切り替えられていない場合は、応答の JSON は次のようになります。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>次のステップ

- [Azure Monitor でのログ アラート](alerts-unified-log.md)について学習します。
- [Azure Alerts でのログ アラート](alerts-log.md)の作成方法について学習します。
- [Azure Alerts のエクスペリエンス](./alerts-overview.md)の詳細について学習する。

