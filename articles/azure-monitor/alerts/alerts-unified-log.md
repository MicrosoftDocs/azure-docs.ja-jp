---
title: Azure Monitor でのログ アラート
description: 指定したログ クエリの条件が満たされたら、電子メール、通知、Web サイトの URL (Webhook) の呼び出し、またはオートメーションをトリガーします
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 786e9b472d1f900e94e5d0cfa6a00e0f85547704
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037695"
---
# <a name="log-alerts-in-azure-monitor"></a>Azure Monitor でのログ アラート

## <a name="overview"></a>概要

ログ アラートは、[Azure アラート](./alerts-overview.md)でサポートされているアラートの種類の 1 つです。 ログ アラートにより、ユーザーは [Log Analytics](../logs/log-analytics-tutorial.md) クエリを使用して、設定された頻度でリソース ログを評価し、その結果に基づいてアラートを発行することができます。 [アクション グループ](./action-groups.md)を使用することで、ルールによって 1 つ以上のアクションをトリガーできます。

> [!NOTE]
> [Log Analytics ワークスペース](../logs/log-analytics-tutorial.md)からのログ データを、Azure Monitor メトリック ストアに送信することもできます。 各メトリック アラートの[動作](alerts-metric-overview.md)は異なります。これは、操作するデータによっては、より望ましい場合があります。 メトリックにルーティングできるログとその方法については、[ログのメトリック アラート](alerts-metric-logs.md)に関するページを参照してください。

> [!NOTE]
> 現時点では、API バージョン `2020-05-01-preview` とリソース中心のログ アラートに関して追加料金は発生しません。  プレビュー段階にある機能の価格は、後で発表され、課金が始まる前に通知されます。 通知期間後も新しい API バージョンとリソース中心ログ アラートを引き続き使用することを選択した場合は、該当する料金が適用されます。

## <a name="prerequisites"></a>前提条件

ログ アラートで、Log Analytics データに対するクエリを実行します。 最初に、[ログ データの収集](../essentials/resource-logs.md)を開始し、ログ データで問題を照会する必要があります。 検出できることを理解したり、[独自のクエリの記述を開始](../logs/log-analytics-tutorial.md)したりするには、Log Analytics での[アラートのクエリの例に関するトピック](../logs/example-queries.md)を使用してください。

[Azure 監視共同作成者](../roles-permissions-security.md)は、ログ アラートの作成、変更、更新に必要な一般的なロールです。 リソース ログに対するアクセスとクエリ実行の権限も必要です。 リソース ログに部分的にアクセスすると、クエリが失敗したり、部分的な結果が返されたりすることがあります。 [Azure でのログ アラートの構成に関する詳細を確認してください](./alerts-log.md)。

> [!NOTE]
> Log Analytics のログ アラートは、従来の [Log Analytics Alert API](./api-alerts.md) を使用して管理されていました。 [現在の ScheduledQueryRules API への切り替えの詳細について確認してください](../alerts/alerts-log-api-switch.md)。

## <a name="query-evaluation-definition"></a>クエリ評価の定義

ログ検索ルールの条件の定義は以下から始まります。

- どのようなクエリを実行するか?
- どのように結果を使用するか?

以下のセクションでは、上記のロジックを設定するために使用できるさまざまなパラメーターについて説明します。

### <a name="log-query"></a>Log query
ルールの評価に使用される [Log Analytics](../logs/log-analytics-tutorial.md) クエリ。 このクエリによって返される結果を使用して、アラートをトリガーするかどうかが決まります。 クエリのスコープは次のように設定できます。

- 仮想マシンなどの特定のリソース。
- サブスクリプションやリソース グループなどの大きなリソース。
- [クロスリソース クエリ](../logs/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights)を使用する複数のリソース。 
 
> [!IMPORTANT]
> アラート クエリには、最適なパフォーマンスと結果の関連性を保証するための制約があります。 [こちら](./alerts-log-query.md)を参照してください。

> [!IMPORTANT]
> リソース中心クエリと[クロスリソース クエリ](../logs/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights)は、現在の scheduledQueryRules API を使用してのみサポートされます。 従来の [Log Analytics Alert API](./api-alerts.md) を使用する場合は、切り替える必要があります。 [切り替えの詳細について確認してください](./alerts-log-api-switch.md)

#### <a name="query-time-range"></a>クエリ時間の範囲

時間範囲は、ルール条件の定義で設定されています。 ワークスペースと Application Insights では、**期間** と呼ばれます。 他のすべてのリソースの種類では、**クエリの時間の範囲のオーバーライド** と呼ばれます。

Log Analytics の場合と同様に、時間範囲によりクエリ データは指定された範囲に制限されます。 クエリで **ago** コマンドが使用されていても、時間範囲が適用されます。

たとえば、時間範囲が 60 分のときは、テキストに **ago(1d)** が含まれていたとしても、クエリでスキャンされるのは 60 分です。 時間範囲とクエリ時間のフィルター処理は、一致している必要があります。 例のケースでは、**期間** / **クエリの時間の範囲のオーバーライド** を 1 日に変更すると、想定どおりに動作します。

### <a name="measure"></a>Measure

ログ アラートによって、ログは評価可能な数値に変換されます。 2 つの異なる項目を測定できます。

#### <a name="count-of-the-results-table-rows"></a>結果テーブルの行の数

結果の数が既定のメジャーになります。 Windows イベント ログ、Syslog、アプリケーション例外などのイベントで使用するのに最適です。 ログ レコードが発生したとき、または評価対象時間枠内で発生しなかったときに、トリガーされます。

ログ アラートは、ログ内のデータを検出しようとする場合に最適です。 ログ内のデータの欠如を検出しようとするときは、それほど適切ではありません。 たとえば、仮想マシンのハートビートに対するアラートなどです。

ワークスペースと Application Insights では、選択した **結果の数** での **基準** と呼ばれます。 他のすべての種類のリソースでは、選択した **テーブル行数** での **メジャー** と呼ばれます。

> [!NOTE]
> ログは半構造化データなので、本質的にメトリックより潜在的であり、ログ内のデータの欠如を検出しようとすると、誤って発生する可能性があります。[メトリック アラート](alerts-metric-overview.md)の使用を検討する必要があります。 [ログのメトリック アラート](alerts-metric-logs.md)を使用して、ログからメトリック ストアにデータを送信できます。

##### <a name="example-of-results-table-rows-count-use-case"></a>結果テーブル行数のユース ケースの例

アプリケーションがエラー コード 500 (内部サーバー エラー) で応答したときに、それを把握します。 以下のような詳細情報を使用してアラート ルールを作成します。

- **クエリ:** 

```Kusto
requests
| where resultCode == "500"
```

- **期間/集約粒度:** 約 15 分
- **アラートの頻度:** 約 15 分
- **しきい値:** 0 より大きい

このアラート ルールでは、500 エラー コードで終了したすべての要求が監視されます。 クエリは 15 分ごとに、過去 15 分間に対して実行されます。 レコードが 1 つでも見つかると、アラートが生成され、構成されたアクションがトリガーされます。

#### <a name="calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value"></a>数値列に基づくメジャーの計算 (CPU カウンター値など)

ワークスペースと Application Insights では、選択した **メトリック測定** での **基準** と呼ばれます。 他のすべての種類のリソースでは、選択した任意の数値列名での **メジャー** と呼ばれます。

### <a name="aggregation-type"></a>集計の種類

複数のレコードを 1 つの数値に集計するために実行される計算。 次に例を示します。
- **Count** によって、クエリ内のレコード数が返されます
- **Average** によって、定義されているメジャー列の [**集計の細分性**](#aggregation-granularity)の平均が返されます。

ワークスペースと Application Insights では、**メトリック測定** メジャーの種類でのみサポートされます。 クエリ結果には、ユーザー定義の集計の後で数値を提供する AggregatedValue という名前の列が含まれている必要があります。 他のすべての種類のリソースでは、**集計の種類** はその名前のフィールドから選択されます。

### <a name="aggregation-granularity"></a>集計の細分性

複数のレコードを 1 つの数値に集計するために使用される間隔を決定します。 たとえば、**5 分** を指定した場合、レコードは指定した **集計の種類** を使用して 5 分間隔でグループ化されます。

ワークスペースと Application Insights では、**メトリック測定** メジャーの種類でのみサポートされます。 クエリ結果には、クエリ結果の間隔を設定する [bin()](/azure/kusto/query/binfunction) が含まれている必要があります。 他のすべてのリソースの種類では、この設定を制御するフィールドは **集計の細分性** と呼ばれます。

> [!NOTE]
> [bin()](/azure/kusto/query/binfunction) では不均一な期間が返される可能性があるため、実行時、アラート サービスでは適切な時間を使用して [bin()](/azure/kusto/query/binfunction) 関数が [bin_at()](/azure/kusto/query/binatfunction) に変換され、確実に固定小数点を持つ結果が得られるようにします。

### <a name="split-by-alert-dimensions"></a>アラート ディメンションで分割する

一意の組み合わせにグループ化することで、アラートを数値または文字列の列で個別のアラートに分割します。 大規模なリソース中心のアラート (サブスクリプションまたはリソース グループのスコープ) を作成するときは、Azure リソース ID 列で分割できます。 Azure リソース ID で分割すると、アラートのターゲットが指定したリソースに変わります。

複数の Azure リソースで同じ条件を監視する場合は、Azure リソース ID 列での分割を推奨します。 たとえば、CPU 使用率が 80% を超えるすべての仮想マシンの監視です。 また、リソース グループのスコープの少なくとも 5 台のマシンが CPU 使用率が 80% を超えていることを監視するなど、スコープ内の複数のリソースに対して 1 つの条件を設定する場合は、分割しないようにすることもできます。

ワークスペースと Application Insights では、**メトリック測定** メジャーの種類でのみサポートされます。 フィールドは、**集約** と呼ばれます。 それは、3 つの列に制限されています。 クエリ内の列で 3 つより多くのグループを作成すると、予期しない結果になる可能性があります。 他のすべてのリソースの種類では、条件の **ディメンションによる分割** セクションで構成されます (6 分割に限定)。

#### <a name="example-of-splitting-by-alert-dimensions"></a>アラート ディメンションによる分割の例

たとえば、特定のリソース グループで Web サイトやアプリを実行している複数の仮想マシンのエラーを監視するとします。 ログ アラート ルールを以下のように使用すればそれが可能になります。

- **クエリ:** 

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    ```

    **メトリック測定** アラート ロジックでワークスペースと Application Insights を使用するときは、この行をクエリ テキストに追加する必要があります。

    ```Kusto
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **リソース ID 列:** _ResourceId (アラート ルールでのリソース ID 列による分割は、現在、サブスクリプションとリソース グループでのみ使用可能)
- **ディメンションと集計対象:**
  - Computer = VM1、VM2 (アラート ルール定義のフィルター値は、現在、ワークスペースと Application Insights では使用できません。 クエリ テキストをフィルター処理します。)
- **期間/集約粒度:** 約 15 分
- **アラートの頻度:** 約 15 分
- **しきい値:** 0 より大きい

このルールでは、過去 15 分間にエラー イベントが発生した仮想マシンがあるかどうかが監視されます。 各仮想マシンは個別に監視され、アクションが個別にトリガーされます。

> [!NOTE]
> アラート ディメンションによる分割は、現在の scheduledQueryRules API でのみ使用できます。 従来の [Log Analytics Alert API](./api-alerts.md) を使用する場合は、切り替える必要があります。 [切り替えの詳細について参照してください](./alerts-log-api-switch.md)。 大規模なリソース中心のアラートは、`2020-05-01-preview` 以上の API バージョンでのみサポートされています。

## <a name="alert-logic-definition"></a>アラート ロジックの定義

実行するクエリと結果の評価を定義したら、アラートのロジックとアクションを実行するタイミングを定義する必要があります。 以下のセクションでは、使用できるさまざまなパラメーターについて説明します。

### <a name="threshold-and-operator"></a>しきい値と演算子

クエリの結果は、しきい値および演算子と比較される数値に変換されます。

### <a name="frequency"></a>頻度

クエリが実行される間隔。 5 分から 1 日の範囲で設定できます。 ログ レコードが漏れないように、[クエリ時間範囲](#query-time-range)以下にする必要があります。

たとえば、期間を 30 分に設定し、頻度を 1 時間に設定したとします。  クエリが 00:00 に実行された場合、23:30 から 00:00 の範囲のレコードが返されます。 次のクエリが実行されるのは 01:00 であり、このときは 00:30 から 01:00 までのレコードが返されます。 00:00 から 00:30 までの間に作成されたレコードは評価されません。

### <a name="number-of-violations-to-trigger-alert"></a>アラートをトリガーする違反の数

アラートの評価期間と、アラートのトリガーに必要なエラーの数を指定できます。 アラートをトリガーする影響時間をより適切に定義できます。 

たとえば、ルールの [**集計の細分性**](#aggregation-granularity)が "5 分" と定義されている場合、過去 1 時間に 3 件のエラー (15 分) が発生した場合にのみアラートをトリガーできます。 この設定は、アプリケーションのビジネス ポリシーによって定義されます。

## <a name="state-and-resolving-alerts"></a>アラートの状態と解決

ログ アラートはステートレスです。 アラートは、前に発生した場合でも、条件が満たされるたびに発生します。 発生したアラートは解決されません。 [アラートを終了としてマークする](../alerts/alerts-managing-alert-states.md)ことができます。 また、アクションをミュートして、アラート ルールが発生した後の期間にトリガーされないようにすることもできます。

ワークスペースと Application Insights では、**アラートの抑制** と呼ばれます。 他のすべてのリソースの種類では、**アクションのミュート** と呼ばれます。 

このアラートの評価例を参照してください。

| Time    | ログ条件の評価 | 結果 
| ------- | ----------| ----------| ------- 
| 00:05 | FALSE | アラートは発生しません。 アクションは呼び出されません。
| 00:10 | TRUE  | アラートが発生し、アクション グループが呼び出されます。 新しいアラートの状態はアクティブです。
| 00:15 | TRUE  | アラートが発生し、アクション グループが呼び出されます。 新しいアラートの状態はアクティブです。
| 00:20 | FALSE | アラートは発生しません。 アクションは呼び出されません。 前のアラートの状態はアクティブのままです。

## <a name="pricing-and-billing-of-log-alerts"></a>ログ アラートの価格と課金

価格情報については、[Azure Monitor の価格に関するページ](https://azure.microsoft.com/pricing/details/monitor/)を参照してください。 ログ アラートは、リソース プロバイダー `microsoft.insights/scheduledqueryrules` の下に一覧表示されます。

- Application Insights のログ アラートが正しいリソース名でリソース グループとアラート プロパティと共に表示されます。
- [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules) を使用して作成されている場合、Log Analytics のログ アラートは、正しいリソース名でリソース グループとアラート プロパティと共に表示されます。
- [従来の Log Analytics API](./api-alerts.md) から作成されたログ アラートは、[Azure リソース](../../azure-resource-manager/management/overview.md)で追跡されず、一意のリソース名は適用されません。 これらのアラートは、非表示リソースとして `microsoft.insights/scheduledqueryrules` でまだ作成され、`<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` というリソース名前付け構造が使用されます。 従来の API のログ アラートは、上記の非表示リソース名で、リソース グループとアラート プロパティと共に表示されます。

> [!NOTE]
> `<, >, %, &, \, ?, /` などのサポートされていないリソース文字は、非表示リソース名では `_` に置き換えられ、これは課金情報にも反映されます。

> [!NOTE]
> Log Analytics のログ アラートは、従来の [Log Analytics Alert API](./api-alerts.md) と [Log Analytics の保存された検索とアラート](../insights/solutions.md)の従来のテンプレートを使用して、管理されていました。 [現在の ScheduledQueryRules API への切り替えの詳細について確認してください](../alerts/alerts-log-api-switch.md)。 すべてのアラート ルール管理は、切り替えを選択して非表示のリソースを使用できないようにするまで、[従来の Log Analytics API](./api-alerts.md) を使用して行う必要があります。

## <a name="next-steps"></a>次のステップ

* [Azure でのログ アラートの作成](./alerts-log.md)について学習します。
* [Azure のログ アラートの Webhook](../alerts/alerts-log-webhook.md) について理解する。
* [Azure アラート](./alerts-overview.md)について学習します。
* [Log Analytics](../logs/log-query-overview.md) についてさらに学習します。