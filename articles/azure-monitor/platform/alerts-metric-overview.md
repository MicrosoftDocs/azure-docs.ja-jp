---
title: Azure Monitor でのメトリック アラートの機能
description: メトリック アラートの用途と、Azure Monitor での機能の概要を理解します。
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: alerts
ms.openlocfilehash: ce65d87142df64a9f0c27f3acdb4d6f25e86fb8a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071622"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Azure Monitor でのメトリック アラートの機能

Azure Monitor でメトリック アラートは、複数ディメンションのメトリック上で動作します。 これらのメトリックには、[プラットフォーム メトリック](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)、[カスタム メトリック](../../azure-monitor/platform/metrics-custom-overview.md)、[Azure Monitor からの一般的なログのメトリック](../../azure-monitor/platform/alerts-metric-logs.md)への変換、Application Insights のメトリックが含まれます。 メトリック アラートは 1 つまたは複数のメトリック タイム シリーズの条件が true かどうかを定期的に評価し、評価が満たされたときに通知します。 メトリック アラートはステートフルです。つまり、状態が変更されたときにのみ通知を送信します。

## <a name="how-do-metric-alerts-work"></a>メトリック アラートの機能

メトリックのアラート ルールを定義するには、監視対象のターゲット リソース、メトリック名、条件タイプ (静的または動的)、および条件 (演算子としきい値/秘密度) と、アラート ルールが発生したときにトリガーされるアクション グループを指定します。 条件タイプは、しきい値の決定方法に影響を与えます。 [動的しきい値の条件タイプと秘密度のオプションの詳細については、こちらをご覧ください](alerts-dynamic-thresholds.md)。

### <a name="alert-rule-with-static-condition-type"></a>静的条件タイプのアラート ルール

次のような単純な静的しきい値メトリック アラート ルールを作成したとします。

- ターゲット リソース (監視対象の Azure リソース): myVM
- メトリック: Percentage CPU
- 条件タイプ: 静的
- 累計時間 (生のメトリックの値に対して実行される統計。 サポートされている集計時間には Min、Max、Avg、Total、Count があります):平均
- 期間 (メトリック値がチェックされるルック バック ウィンドウ): 直近 5 分間
- 頻度 (メトリック アラートで条件が満たされているかどうかをチェックする頻度): 1 分
- 演算子: より大きい
- しきい値: 70

アラート ルールの作成時から、モニターは 1 分ごとに実行し、直近 5 分間のメトリック値を調べ、それらの値の平均値が 70 を超えるかどうかをチェックします。 条件が満たされている場合、つまり直近 5 分間の平均 CPU が 70 を超えている場合、アラート ルールによりアクティブ通知が生成されます。 アラート ルールに関連付けられているアクション グループで、電子メールまたは web フック アクションを構成した場合、両方でアクティブ通知を受け取ります。

1 つのルールに複数の条件を使用している場合、そのルールは条件を "and" で結合します。  つまり、アラートのすべての条件が true と評価されたときにアラートが発生し、条件のいずれかが true ではなくなったときに解決します。 この種類のアラートの and の例は、"CPU が 90% を超過" かつ "キューの長さが 300 項目を超過" である場合のアラートなどです。 

### <a name="alert-rule-with-dynamic-condition-type"></a>動的条件タイプのアラート ルール

次のような単純な動的しきい値メトリック アラート ルールを作成したとします。

- ターゲット リソース (監視対象の Azure リソース): myVM
- メトリック: Percentage CPU
- 条件タイプ: 動的
- 累計時間 (生のメトリックの値に対して実行される統計。 サポートされている集計時間には Min、Max、Avg、Total、Count があります):平均
- 期間 (メトリック値がチェックされるルック バック ウィンドウ): 直近 5 分間
- 頻度 (メトリック アラートで条件が満たされているかどうかをチェックする頻度): 1 分
- 演算子: より大きい
- 秘密度: Medium
- ルックバック期間: 4
- 違反数: 4

アラート ルールが作成されると、動的しきい値の機械学習アルゴリズムにより、しきい値の精度を高めるために、使用可能な履歴データが取得され、メトリック系列の動作パターンに最適なしきい値が計算されて、新しいデータに基づいて継続的な学習が行われます。

アラート ルールの作成時から、1 分ごとに監視が実行され、5 分間ずつグループ化された直近 20 分間のメトリック値が参照されて、4 つの各間隔の平均値が予想されたしきい値を超えているかどうかがチェックされます。 条件が満たされている場合、つまり直近 20 分間 (5 分間 × 4 回) の平均 CPU 使用率が、予想された動作から 4 回逸脱している場合、アラート ルールによりアクティブ通知が生成されます。 アラート ルールに関連付けられているアクション グループで、電子メールまたは web フック アクションを構成した場合、両方でアクティブ通知を受け取ります。

### <a name="view-and-resolution-of-fired-alerts"></a>生成されたアラートの表示と解決

生成されるアラート ルールの上記の例は、Azure portal の **[すべてのアラート]** ブレードでも確認できます。

後続のチェックで "myVM" の使用率がしきい値を超えている場合、アラート ルールは、条件が解決されるまで再実行されません。

しばらくして、"myVM" の使用率が標準 (しきい値未満) に戻った場合、動作は次のようになります。 アラート ルールでは、その条件をさらに 2 回監視してから、解決済みの通知を送信します。 アラート ルールは、条件が変動している場合のノイズを減らすため、3 つの連続する期間にアラート条件が検出されない場合に、解決済み/非アクティブ化済みのメッセージを送信します。

解決済みの通知が Web フックまたは電子メールで送信されると、Azure portal でのアラート インスタンスの状態 (監視状態と呼ばれる) も解決済みに設定されます。

### <a name="using-dimensions"></a>ディメンションの使用

Azure Monitor のメトリック アラートでは、1 つのルールによる複数のディメンション値の組み合わせの監視もサポートされます。 複数のディメンションの組み合わせを使用する利点を、例を使って説明します。

たとえば、Web サイトに App Service プランがあるとします。 Web サイト/アプリを実行している複数のインスタンスの CPU 使用率を監視できると便利です。 メトリック アラート ルールを以下のように使用すればそれが可能になります。

- ターゲット リソース: myAppServicePlan
- メトリック: Percentage CPU
- 条件タイプ: 静的
- Dimensions
  - Instance = InstanceName1, InstanceName2
- 累計時間: 平均
- 期間: 直近 5 分間
- 頻度: 1 分
- 演算子: GreaterThan
- しきい値: 70

前に示したように、このルールは、直近 5 分間の平均 CPU 使用率が 70% を超えているかどうかを監視します。 ただし、同じルールによって web サイトを実行している 2 つのインスタンスを監視できます。 各インスタンスは個別に監視され、個別に通知が受領されます。

たとえば、ある Web アプリに大量の要求があり、インスタンスを追加する必要があるとします。 その場合でも上記のルールは 2 つのインスタンスだけを監視します。 ただし、次のようにルールを作成できます。

- ターゲット リソース: myAppServicePlan
- メトリック: Percentage CPU
- 条件タイプ: 静的
- Dimensions
  - インスタンス = *
- 累計時間: 平均
- 期間: 直近 5 分間
- 頻度: 1 分
- 演算子: GreaterThan
- しきい値: 70

このルールは、このインスタンスのすべての値を自動的に監視します。つまり、 インスタンスが生成されるたびにメトリックのアラート ルールを変更しなくても、それを監視できます。

複数のディメンションを監視する場合は、動的しきい値のアラート ルールにより、一度に数百のメトリック系列に対して調整済みのしきい値を作成できます。 動的しきい値では、管理するアラート ルールが少なくなり、アラート ルールの管理と作成において膨大な時間を節約できます。

多数のインスタンスを含む Web アプリがあり、最も適切なしきい値が不明であるとします。 上記のルールでは、常に 70% のしきい値が使用されます。 ただし、次のようにルールを作成できます。

- ターゲット リソース: myAppServicePlan
- メトリック: Percentage CPU
- 条件タイプ: 動的
- Dimensions
  - インスタンス = *
- 累計時間: 平均
- 期間: 直近 5 分間
- 頻度: 1 分
- 演算子: GreaterThan
- 秘密度: Medium
- ルックバック期間: 1
- 違反数: 1

このルールでは、直近 5 分間の平均 CPU 使用率が、各インスタンスの予想された動作を超えているかどうかが監視されます。 同じルールにより、インスタンスが生成されるたびにメトリックのアラート ルールを再度変更しなくても、それらのインスタンスを監視できます。 各インスタンスでは、しきい値の精度を高めるために、メトリック系列の動作パターンに適合するしきい値を取得し、新しいデータに基づいて継続的に変更を行います。 前述したように、各インスタンスは個別に監視され、通知も個別に受け取ります。

ルックバック期間と違反の数が大きくなると、大きな偏差の定義に対するアラートのみに、アラートをフィルター処理することもできます。 [動的しきい値の詳細設定オプションについて詳しくはこちらをご覧ください](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean)。

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Azure Monitor のメトリック アラートによるスケールの監視

ここまでは、単一の Azure リソースに関連する 1 つ以上のメトリックの時系列を監視するために、単一のメトリック アラートを使用する方法について説明しました。 1 つのアラート ルールを多数のリソースに適用する場合はよくあります。 また、Azure Monitor はプレビューで 1 つのメトリック アラート ルールによる複数リソースの監視をサポートするようになりました。 現在、この機能は仮想マシンでのみサポートされています。 また、1 つのメトリック アラートが 1 つの Azure リージョン内のリソースを監視することもできます。

1 つのメトリック アラートでの監視範囲は、次の 3 つのいずれかの方法で指定することができます。

- サブスクリプション内の 1 つの Azure リージョン内の仮想マシンのリストとして
- サブスクリプション内の 1 つまたは複数のリソース グループ内の (1 つの Azure リージョン内の) すべての仮想マシン
- 1 つのサブスクリプション内の (1 つの Azure リージョン内の) すべての仮想マシン

複数のリソースを監視するメトリックのアラート ルールを作成する方法は、単一のリソースを監視する[他のメトリック アラートを作成する](alerts-metric.md)場合と同じです。 唯一の違いは、監視対象にするリソースをすべて選択する点です。 このようなルールは [Azure Resource Manager テンプレート](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-metric-alert-that-monitors-multiple-resources)を使って作成することもできます。 仮想マシンごとに個別の通知が届きます。

## <a name="typical-latency"></a>一般的な待機時間

メトリック アラートでは通常、アラート ルールの頻度を 1 分に設定している場合は 5 分以内に通知を受領します。通知システムが高負荷の場合は、待機時間が長くなることがあります。

## <a name="supported-resource-types-for-metric-alerts"></a>メトリック アラートでサポートされているリソースの種類

サポートされているリソースの種類の完全な一覧については、こちらの[記事](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)をご覧ください。

現在クラシック メトリック アラートを使用していて、使用しているすべてのリソースの種類がメトリック アラートでサポートされるかどうかを確認する場合のために、次の表に、クラシック メトリック アラートでサポートされているリソースの種類と、これらが現在のメトリック アラートでサポートされているかどうかを示します。

|クラシック メトリック アラートでサポートされているリソースの種類 | メトリック アラートでサポートされている |
|-------------------------------------------------|----------------------------|
| Microsoft.ApiManagement/service | はい |
| Microsoft.Batch/batchAccounts| はい|
|Microsoft.Cache/redis| はい |
|Microsoft.ClassicCompute/virtualMachines | いいえ |
|Microsoft.ClassicCompute/domainNames/slots/roles | いいえ|
|Microsoft.CognitiveServices/accounts | いいえ |
|Microsoft.Compute/virtualMachines | はい|
|Microsoft.Compute/virtualMachineScaleSets| はい|
|Microsoft.ClassicStorage/storageAccounts| いいえ |
|Microsoft.DataFactory/datafactories | はい|
|Microsoft.DBforMySQL/servers| はい|
|Microsoft.DBforPostgreSQL/servers| はい|
|Microsoft.Devices/IotHubs | いいえ|
|Microsoft.DocumentDB/databaseAccounts| はい|
|Microsoft.EventHub/namespaces | はい|
|Microsoft.Logic/workflows | はい|
|Microsoft.Network/loadBalancers |はい|
|Microsoft.Network/publicIPAddresses| はい|
|Microsoft.Network/applicationGateways| はい|
|Microsoft.Network/expressRouteCircuits| はい|
|Microsoft.Network/trafficManagerProfiles | はい|
|Microsoft.Search/searchServices | はい|
|Microsoft.ServiceBus/namespaces| はい |
|Microsoft.Storage/storageAccounts | はい|
|Microsoft.StreamAnalytics/streamingjobs| はい|
|Microsoft.TimeSeriesInsights/environments | はい|
|Microsoft. Web/serverfarms | はい |
|Microsoft. Web/sites (関数を除く) | はい|
|Microsoft. Web/hostingEnvironments/multiRolePools | いいえ|
|Microsoft. Web/hostingEnvironments/workerPools| いいえ |
|Microsoft.SQL/Servers | いいえ |

## <a name="next-steps"></a>次の手順

- [Azure でメトリック アラートを作成、表示、管理する方法を学習する](alerts-metric.md)
- [Azure Resource Manager のテンプレートを使ってメトリック アラートを配置する方法を学習する](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [アクション グループの詳細について学習する](action-groups.md)
- [動的しきい値の条件タイプの詳細について学習する](alerts-dynamic-thresholds.md)
