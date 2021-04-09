---
title: Azure 自動スケーリングのトラブルシューティング
description: Service Fabric、Virtual Machines、Web Apps、クラウド サービスで使用される Azure 自動スケールに関する問題を追跡します。
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: a0fed6c2d06edcb2c9eb8d715feb0ef6c6ade46f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711401"
---
# <a name="troubleshooting-azure-autoscale"></a>Azure 自動スケーリングのトラブルシューティング
 
Azure Monitor 自動スケーリングを使用すると、適切な量のリソースを実行して、アプリケーションに対する負荷を処理する際に役立ちます。 リソースを追加して負荷の増加に対処したり、アイドル状態のリソースを削除して経費を節約したりできます。 スケジュール、特定の日時、または選択したリソース メトリックに基づいてスケールできます。 詳細については、[自動スケーリングの概要](autoscale-overview.md)に関するページを参照してください。

自動スケーリング サービスは、発生したスケール アクションと、それらのアクションを引き起こした条件の評価を理解するために、メトリックとログを提供します。 次のような質問に対する回答を見つけることができます。

- サービスがスケールアウトまたはスケールインした理由
- サービスがスケールされていない理由
- 自動スケーリングが失敗する理由
- 自動スケーリング アクションによってスケーリングするのに時間がかかる理由
  
## <a name="autoscale-metrics"></a>自動スケーリング メトリック

自動スケーリングでは、操作を理解するための [4 つのメトリック](../essentials/metrics-supported.md#microsoftinsightsautoscalesettings)が提供されます。 

- **実際のメトリック値** - スケール アクションを実行するために選択したメトリックの値。自動スケーリング エンジンによって表示または計算されます。 1 つの自動スケーリング設定に複数のルールを設定できるため、"メトリック ソース" をディメンションとして使用して、複数のメトリック ソースをフィルター処理できます。
- **メトリックのしきい値** - スケール アクションを実行するために設定するしきい値。 1 つの自動スケーリング設定に複数のルールを設定できるため、"メトリック ルール" をディメンションとして使用して、複数のメトリック ソースをフィルター処理できます。
- **実際の容量** - 自動スケーリング エンジンから見たターゲット リソースのアクティブなインスタンス数。
- **スケール アクション開始** - 自動スケーリング エンジンによって開始されるスケールアウトおよびスケールイン アクションの数。 スケールアウトとスケールイン アクションでフィルター処理できます。

[メトリックス エクスプローラー](../essentials/metrics-getting-started.md) を使用すると、上記のメトリックをすべて 1 か所でグラフ化できます。 グラフには次が表示されます。

  - 実際のメトリック
  - 自動スケーリング エンジンから見た/計算されたメトリック
  - スケール アクションのしきい値
  - 容量の変更 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>例 1 - 単純な自動スケーリング ルールの分析 

次のような仮想マシン スケール セットの自動スケーリング設定があるとします。

- セットの平均 CPU 使用率が 10 分間 70% を超えたときにスケールアウトする 
- セットの CPU 使用率が 10 分以上 5 % 未満のときにスケールインする 

自動スケーリング サービスのメトリックを確認してみましょう。
 
![仮想マシン スケール セットの CPU 使用率の例を示すスクリーンショット。](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![仮想マシン スケール セットの CPU 使用率の例](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***図 1a - 仮想マシン スケール セットの CPU メトリックの割合と、自動スケーリング設定の実際のメトリック値のメトリック***

![メトリックのしきい値と実際の容量](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***図 1b - メトリックのしきい値と実際の容量***

図 1b では、スケールアウト ルールの **メトリックのしきい値** (水色の線) は 70 です。  **実際の容量** (濃い青の線) は、アクティブなインスタンスの数 (現在は 3) を示します。 

> [!NOTE]
> スケールアウトのしきい値を確認するには、メトリック トリガー ルール ディメンションのスケールアウト (増加) ルールと、スケールイン ルール (減少) で **メトリックのしきい値** をフィルター処理する必要があります。 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>例 2 - 仮想マシン スケール セットの高度な自動スケーリング

仮想マシン スケール セットのリソースが、**送信フロー** 独自のメトリックに基づいてスケールアウトできるようにする自動スケーリング設定が用意されています。 メトリックのしきい値として **[divide metric by instance count]\(メトリックをインスタンス数で割る\)** オプションがオンになっていることに注意してください。 

スケール アクション ルールは次のとおりです。 

**[Outbound Flow per instance]\(インスタンスごとの送信フロー\)** が10 を超える場合、自動スケーリング サービスは 1 つのインスタンスでスケールアウトする必要があります。 

この場合、自動スケーリング エンジンの実際のメトリック値は、インスタンスの数で割った実際のメトリック値として計算されます。 実際のメトリック値がしきい値未満の場合、スケールアウト アクションは開始されません。 
 
![Average Outbound Flows ページで、仮想マシン スケール セットの自動スケーリング メトリック グラフの例を示すスクリーンショット。](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![仮想マシン スケール セットの自動スケーリング メトリック グラフの例](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***図 2 - 仮想マシン スケール セットの自動スケーリング メトリック グラフの例***

図 2 では、2 つのメトリック グラフを確認できます。 

上部のグラフは、**送信フロー** メトリックの実際の値を示します。 実際の値は 6 です。 

下部のグラフには、いくつかの値が表示されます。 
 - **実際のメトリック値** (水色) は 3 です。これは、アクティブなインスタンスが 2 つあり、6 を 2 で割った数が 3 であるためです。 
 - **実際の容量** (紫色) は、自動スケーリング エンジンによって検出されたインスタンス数を示します。 
 - **メトリックのしきい値** (明るい緑) は 10 に設定されています。 

複数のスケール アクション ルールがある場合は、[分割] を使用するか、メトリックス エクスプローラー グラフの **[フィルターの追加]** オプションを選択して、特定のソースまたはルールでメトリックを確認できます。 メトリック グラフの分割の詳細については、[メトリック グラフの高度な機能 - 分割](../essentials/metrics-charts.md#apply-splitting)に関する記事を参照してください。

## <a name="example-3---understanding-autoscale-events"></a>例 3 - 自動スケーリング イベントについて

自動スケーリングの設定画面で、 **[実行履歴]** タブにアクセスして、最新のスケール アクションを確認します。 また、このタブには **実際の容量** が時系列で示されます。 自動スケーリング設定の更新や削除などの操作を含む、すべての自動スケーリング アクションの詳細を確認するには、アクティビティ ログを表示し、自動スケーリング アクションでフィルター処理します。

![自動スケーリング設定の実行履歴](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>自動スケーリングのリソース ログ

他の Azure リソースと同じように、自動スケーリング サービスは[リソース ログ](../essentials/platform-logs-overview.md)を提供します。 イベント ログには、次の 2 つがあります。

- **自動スケーリング評価** - 自動スケーリング エンジンでは、チェックを行うたびに、単一の条件評価ごとにログ エントリが記録されます。  エントリには、メトリックの実際の値、評価されたルール、評価の結果がスケール アクションであったかどうかに関する詳細が含まれます。

- **自動スケーリング アクション** - エンジンは、自動スケーリング サービスによって開始されるスケール アクション イベントと、スケール アクションの結果 (成功、失敗、および自動スケーリング サービスから見たスケーリングの数) を記録します。

サポートされている Azure Monitor サービスと同様に、[[診断設定]](../essentials/diagnostic-settings.md) を使用して、ログを次へルーティングできます。

- Log Analytics ワークスペースへ (詳細な分析のため)
- Event Hubs の後に Azure 以外のツールへ
- Azure ストレージ アカウントへ (アーカイブのため)  

![自動スケーリングの診断設定](media/autoscale-troubleshoot/diagnostic-settings.png)

前の図は、Azure portal 自動スケーリングの診断設定を示しています。 ここで、[診断ログ] タブまたは [リソース ログ] タブを選択し、ログの収集とルーティングを有効にすることができます。 また、REST API、CLI、PowerShell、Resource Manager テンプレートを使用して診断設定を行うこともできます。これを行うには、リソースの種類として *[Microsoft Insights/AutoscaleSettings]* を選択します。 

## <a name="troubleshooting-using-autoscale-logs"></a>自動スケーリング ログを使用したトラブルシューティング 

最適なトラブルシューティングを行うには、自動スケーリング設定を作成するときに、ワークスペースを使用して Azure Monitor ログ (Log Analytics) にログをルーティングすることをお勧めします。 このプロセスについては、前のセクションの図を参照してください。 Log Analytics を使用すると、評価とスケーリング アクションをより適切に検証できます。

Log Analytics ワークスペースに送信されるように自動スケーリング ログを構成したら、次のクエリを実行してログを確認できます。 

開始するには、次のクエリを実行して、最新の自動スケーリング評価ログを表示します。

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

または、次のクエリを実行して、最新のスケール アクション ログを表示します。

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

これらの質問に関しては、次のセクションを使用してください。 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>予期していなかったスケール アクションが発生した

まず、スケール アクションのクエリを実行して、目的のスケール アクションを見つけます。 最新のスケール アクションの場合は、次のクエリを使用します。

```Kusto
AutoscaleScaleActionsLog
| take 1
```

[scale actions]\(スケール アクション\) ログから [CorrelationId] フィールドを選択します。 正しい評価ログを検索するには、CorrelationId を使用します。 次のクエリを実行すると、そのスケール アクションの前に評価されたすべてのルールと条件が表示されます。

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>スケール アクションの原因となったプロファイルは何か

スケーリングされたアクションが発生しましたが、ルールとプロファイルが重複しているため、このアクションの原因となった問題を追跡する必要があります。 

スケール アクションの correlationId (例 1 で説明) を見つけて、評価ログに対してクエリを実行してそのプロファイルの詳細について確認します。

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

次のクエリを使用して、プロファイル全体の評価をより適切に理解することもできます

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>スケール アクションが実行されませんでした

スケール アクションを想定していましたが、発生しませんでした。 スケール アクション イベントまたはログがない可能性があります。

メトリック ベースのスケール ルールを使用している場合は、自動スケーリング メトリックを確認します。 **実際のメトリック値** または **実際の容量** が想定したものではないため、スケール ルールが起動されなかった可能性があります。 評価は引き続き表示されますが、スケールアウト ルールは表示されません。 また、クールダウン時間によりスケール アクションが実行されなかった可能性もあります。 

 スケール アクションが発生すべき期間の自動スケーリングの評価ログを確認します。 実行したすべての評価と、スケール アクションがトリガーされなかった理由を確認します。


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>スケール アクションに失敗しました

自動スケーリング サービスではスケール アクションが実行されましたが、システムによりスケーリングが中止されたか、スケーリング アクションの完了に失敗した可能性があります。 このクエリを使用して、失敗したスケール アクションを検索します。

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

アラート ルールを作成して、自動スケーリングのアクションまたはエラーが通知されるようにします。 アラート ルールを作成して、自動スケーリング イベントについて通知を受け取ることもできます。

## <a name="schema-of-autoscale-resource-logs"></a>自動スケーリングのリソース ログのスキーマ

詳細については、[自動スケーリングのリソース ログ](autoscale-resource-log-schema.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
[自動スケーリングのベストプラクティス](autoscale-best-practices.md)に関する情報を参照してください。