---
title: ターゲット リソースが別の Azure リージョンに移動されるときにアラート ルールまたはアクション ルールを更新する方法
description: ターゲット リソースが別の Azure リージョンに移動されるときにアラート ルールまたはアクション ルールを更新する方法に関する背景と手順。
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/14/2021
ms.openlocfilehash: eb6dbb74fe0d345a157049e79f7a3642499d7cfa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037984"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>ターゲット リソースが別の Azure リージョンに移動されるときにアラート ルールまたはアクション ルールを更新する方法

この記事では、他の Azure リソースをリージョン間で移動するときに、既存の[アラート ルール](./alerts-overview.md)と[アクション ルール](./alerts-action-rules.md)が影響を受ける可能性がある理由と、それらの問題を特定して解決する方法について説明します。 主要な[リソースの移動に関するドキュメント](../../azure-resource-manager/management/move-region.md)を参照して、リージョン間のリソースの移動が有用な場合と、移動プロセスを設計する際のチェックリストに関する追加情報を確認してください。

## <a name="why-the-problem-exists"></a>問題が存在する理由

アラート ルールとアクション ルールでは、他の Azure リソースが参照されます。 例としては、[Azure VM](../../site-recovery/azure-to-azure-tutorial-migrate.md)、[Azure SQL](../../azure-sql/database/move-resources-across-regions.md)、および [Azure Storage](../../storage/common/storage-account-move.md) があります。 これらのルールで参照されているリソースを移動すると、ルールは、参照しているリソースを見つけることができないため、正常に動作しなくなる可能性があります。

ターゲット リソースの移動後にルールが動作を停止する主な理由は 2 つあります。

- ルールのスコープによって、古いリソースが明示的に参照されている。
- アラート ルールがメトリックに基づいている。

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>ルールのスコープによって、古いリソースが明示的に参照されている

リソースを移動すると、ほとんどの場合、そのリソース ID は変更されます。 バックグラウンドでは、リソースは古いリージョンから削除される前に、システムによって新しいリージョンにレプリケートされます。 このプロセスでは、2 つのリソース (つまり、2 つの異なるリソース ID) がわずかな期間、同時に存在する必要があります。 リソース ID は一意でなければならないため、このプロセス中に新しい ID を作成する必要があります。 

**リソースの移動により、既存のルールにはどのような影響があるか**

アラート ルールとアクション ルールには、適用されるリソースのスコープがあります。 スコープは、サブスクリプション全体、リソース グループ、または 1 つ以上の特定のリソースの場合があります。
たとえば、次に示すのは、2 つのリソース (2 つの仮想マシン) があるスコープのルールです。

![マルチ リソース アラート ルール](media/alerts-resource-move/multi-resource-alert-rule.png)

ルールのスコープでリソースが明示的に示されていて、そのリソースが移動され、そのリソース ID が変更された場合、そのルールは、間違った、または存在しないリソースを検索するため、失敗します。

**問題を解決する方法**

影響を受けたルールを、新しいリソースを指すように更新または再作成します。 スコープを更新するプロセスについては、この記事の後半で説明します。

この問題は、これらのルールの種類に適用されます。

- アクティビティ ログ アラート ルール
- アクション ルール
- メトリック アラート – 詳細については、次のセクション「[メトリックに基づくアラート ルール](#alert-rules-based-on-metrics)」を参照してください。

> [!NOTE]
> ログ検索のアラート ルールと Smart Detector のアラートルールは、スコープがワークスペースまたは Application Insights のいずれかであるため、影響を受けません。 これらのいずれのスコープでも、現在、リージョンの移動はサポートされていません。

## <a name="alert-rules-based-on-metrics"></a>メトリックに基づくアラート ルール

Azure リソースによって出力されるメトリックはリージョン別です。 リソースは、新しいリージョンに移動するたびに、その新しいリージョンでメトリックの出力を開始します。 その結果、メトリックに基づくアラート ルールはすべて、正しいリージョンの現在のメトリック ストリームを指すように更新または再作成する必要があります。

この説明は、[メトリック アラート ルール](alerts-metric-overview.md)と[可用性テスト アラート ルール](../app/monitor-web-app-availability.md)の両方に適用されます。

スコープ内の **すべての** リソースが移動された場合は、ルールを再作成する必要はありません。 単に、アラート ルールの説明などのアラート ルールのフィールドを更新して保存できます。
スコープ内のリソースの **一部だけ** が移動された場合は、移動されたリソースを既存のルールから削除し、移動されたリソースだけを対象とする新しいルールを作成する必要があります。

## <a name="procedures-to-fix-problems"></a>問題を解決する手順

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>移動されたリソースに関連付けられているルールを Azure portal から特定する

- **アラート ルールの場合** - [アラート] > [アラート ルールの管理] に移動し、含まれているサブスクリプションと移動されたリソースでフィルターに掛けます。
> [!NOTE]
> アクティビティ ログ アラート ルールでは、このプロセスはサポートされていません。 アクティビティ ログ アラート ルールのスコープを更新して、別のサブスクリプションのリソースを参照させることはできません。 代わりに、古いものを置き換える新しいルールを作成できます。

- **アクション ルールの場合** - [アラート] > [アクションの管理] > [アクション ルール (プレビュー)] に移動し、含まれているサブスクリプションと移動されたリソースでフィルターに掛けます。

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>ルールのスコープを Azure portal から変更する

1. 前の手順で特定したルールをクリックして開きます。
2. **[リソース]** の下で **[編集]** をクリックし、必要に応じてスコープを調整します。
3. 必要に応じて、ルールのその他のプロパティを調整します。
4. **[保存]** をクリックします。

![アラート ルールのスコープを変更する](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用してルールのスコープを変更する

1. ルールの Azure Resource Manager テンプレートを取得します。  Azure portal からルールのテンプレートをエクスポートするには、次のようにします。
   1. ポータルの [リソース グループ] セクションに移動し、ルールが含まれているリソース グループを開きます。
   2. [概要] セクションで、 **[Show hidden type]\(非表示の種類を表示する\)** チェックボックスをオンにして、ルールの関連する種類でフィルター処理します。
   3. 関連するルールを選択して、その詳細を表示します。
   4. **[設定]** で、 **[テンプレートのエクスポート]** を選択します。
2. テンプレートを変更します。 必要に応じて、2 つのルールに分割します (前述のようなメトリック アラートの一部のケースに関連します)。
3. テンプレートを再デプロイします。

### <a name="change-scope-of-a-rule-using-rest-api"></a>REST API を使用してルールのスコープを変更する

1. 既存のルールを取得します ([メトリック アラート](/rest/api/monitor/metricalerts/get)、[アクティビティ ログ アラート](/rest/api/monitor/activitylogalerts/get))
2. スコープを変更します ([アクティビティ ログ アラート](/rest/api/monitor/activitylogalerts/update))
3. ルールを再デプロイします ([メトリック アラート](/rest/api/monitor/metricalerts/createorupdate)、[アクティビティ ログ アラート](/rest/api/monitor/activitylogalerts/createorupdate))

### <a name="change-scope-of-a-rule-using-powershell"></a>PowerShell を使用してルールのスコープを変更する

1. 既存のルールを取得します ([メトリック アラート](/powershell/module/az.monitor/get-azmetricalertrulev2)、[アクティビティ ログ アラート](/powershell/module/az.monitor/get-azactivitylogalert)、[アクション ルール](/powershell/module/az.alertsmanagement/get-azactionrule))。
2. スコープを変更します。 必要に応じて、2 つのルールに分割します (前述のようなメトリック アラートの一部のケースに関連します)。
3. ルールを再デプロイします ([メトリック アラート](/powershell/module/az.monitor/add-azmetricalertrulev2)、[アクティビティ ログ アラート](/powershell/module/az.monitor/enable-azactivitylogalert)、[アクション ルール](/powershell/module/az.alertsmanagement/set-azactionrule))。

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>Azure CLI を使用してルールのスコープを変更する

1.  既存のルールを取得します ([メトリック アラート](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-show)、[アクティビティ ログ アラート](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list))。
2.  ルールのスコープを直接更新します ([メトリック アラート](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-update)、[アクティビティ ログ アラート](/cli/azure/monitor/activity-log/alert/scope))
3.  必要に応じて、2 つのルールに分割します (前述のようなメトリック アラートの一部のケースに関連します)。

## <a name="next-steps"></a>次のステップ

[アラート通知](alerts-troubleshoot.md)、[メトリック アラート](alerts-troubleshoot-metric.md)、[ログ アラート](alerts-troubleshoot-log.md)に関するその他の問題の解決方法について説明します。