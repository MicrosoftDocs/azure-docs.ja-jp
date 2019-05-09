---
title: Azure Monitor でのログ アラート
description: Azure アラートに関して指定した分析クエリ条件が満たされたときに、電子メール、通知、Web サイト URL (webhook)の呼び出し、またはオートメーションをトリガーします。
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/20/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 194fba3296359f5f7d29a37425a938fe08f1332b
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56452884"
---
# <a name="log-alerts-in-azure-monitor"></a>Azure Monitor でのログ アラート

この記事では、ログ アラートの詳細について説明します。ログ アラートは、[Azure アラート](../../azure-monitor/platform/alerts-overview.md)でサポートされるアラートの一種です。これを使用すると、Azure の分析プラットフォームをアラート発信の基盤として使用できます。

ログ アラートは、[Azure Monitor ログ](../../azure-monitor/learn/tutorial-viewdata.md)または [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events) 用に作成されたログ検索ルールで構成されます。 その使用の詳細については、[Azure でのログ アラートの作成](../../azure-monitor/platform/alerts-log.md)に関するページをご覧ください

> [!NOTE]
> [Azure Monitor ログ](../../azure-monitor/learn/tutorial-viewdata.md)のメトリック プラットフォームでは、Azure Monitor の一般的なログ データも利用できるようになっています。 詳細ビューについては、[ログのメトリック アラート](../../azure-monitor/platform/alerts-metric-logs.md)に関するページをご覧ください


## <a name="log-search-alert-rule---definition-and-types"></a>ログ検索アラート ルールの定義と種類

ログ検索ルールは、指定されたログ クエリを一定の間隔で自動的に実行するために、Azure アラートによって作成されます。  ログ クエリの結果が特定の条件に一致すると、アラート レコードが作成されます。 ルールではその後、[アクション グループ](../../azure-monitor/platform/action-groups.md)を使用して、1 つ以上のアクションを自動的に実行できます。 ログ アラートを作成、変更、および更新するための [Azure Monitor 共同作成者](../../azure-monitor/platform/roles-permissions-security.md)ロール、およびアラート ルールまたはアラート クエリの分析対象に対するアクセスおよびクエリ実行権限が必要な場合があります。 作成しているユーザーが、アラート ルールまたはアラート クエリの分析対象の一部に対してアクセス権限がない場合、ルール作成が失敗したり、部分的な結果を使用してログ アラート ルールが実行されたりする場合があります。

ログ検索ルールは次の内容で定義されます。

- **ログ クエリ**:  警告ルールが実行されるたびに実行されるクエリ。  このクエリによって返されるレコードを使用して、アラートをトリガーするかどうかが決まります。 特定の Log Analytics ワークスペースまたは Application Insights アプリに対して分析クエリを実行できます。ユーザーがすべてのリソースに対するアクセス権とクエリ権を所有していることを条件として、[複数の Log Analytics と Application Insights のリソース](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights)を対象にすることもできます。 
    > [!IMPORTANT]
    > ログ アラートでは、セキュリティ上の理由により、[関数](../log-query/functions.md)の使用はサポート**されません**。 また、[リソース間のクエリ](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights)は、Application Insights 用のログ アラートと、[scheduledQueryRules API を使用するように構成された Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) 用のログ アラートでのみサポートされます。

    ログ アラートで使用できない分析コマンドと組み合わせがあります。詳細については、「[Azure Monitor でのログ アラートのクエリ](../../azure-monitor/platform/alerts-log-query.md)」を参照してください。

- **期間**。  クエリの時間範囲を指定します。 クエリでは、現在の時刻に先立つ指定の時間範囲の間に作成されたレコードだけを返します。 期間では、ログ クエリ用にフェッチされるデータを制限して不正使用を防いだり、ログ クエリで使用されている時間コマンド (ago など) を回避することができます。 <br>*たとえば、期間が 60 分に設定されていて、クエリが午後 1 時 15 分に実行された場合は、午後 12 時 15 分から午後 1 時 15 分までの間に作成されたレコードだけが、ログ クエリの実行用に返されます。ログ クエリで「ago (7d)」などの時間コマンドが使用されている場合、そのログ クエリは午後 12 時 15 分から午後 1 時 15 分までの間のデータのみを対象に実行されます。つまり、過去 60 分間についてのみデータが存在し、ログ クエリで指定された 7 日間についてはデータが存在しないかのように動作します。*

- **[頻度]**:   クエリの実行頻度を指定します。 5 分から 24 時間までの値を指定できます。 この値は、期間の値以下にする必要があります。  この値が期間の値よりも大きい場合、レコードを見落とすおそれがあります。<br>*たとえば、期間が 30 分、頻度が 60 分であるとします。クエリが午後 1 時に実行された場合、午後 12 時 30 分から午後 1 時までの間のレコードが返されます。次回クエリが実行されるのは午後 2 時であり、このときには午後 1 時 30 分から午後 2 時までの間のレコードが返されます。つまり、午後 1 時から午後 1 時 30 分までの間に作成されたレコードは評価されないことになります。*

- **しきい値**:   ログ検索の結果を評価し、アラートの生成が必要であるかどうかを判定するための値です。  しきい値は、ログ検索アラート ルールの種類によって異なります。

ログ検索ルールには、[Azure Monitor ログ](../../azure-monitor/learn/tutorial-viewdata.md)用と [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events) 用のいずれについても、2 つの種類があります。 どちらについても、後のセクションで詳しく説明します。

- **[結果の数](#number-of-results-alert-rules)**。 ログ検索によって返されるレコードの数が指定された数を超えた場合に、アラートが 1 回生成されます。
- **[メトリック測定](#metric-measurement-alert-rules)**。  ログ検索の結果の値が指定されたしきい値を超えた場合に、オブジェクトごとにアラートが生成されます。

この 2 種類のアラート ルールの違いは次のとおりです。

- *結果の数*アラート ルールが常に 1 つのアラートを作成するのに対して、*メトリック測定*アラート ルールは、しきい値を超えたオブジェクトごとにアラートを作成します。
- *結果の数*の警告ルールでは、1 回しきい値を超えた時点で警告が生成されます。 これに対して、*メトリック測定*のアラート ルールでは、一定期間内にしきい値を一定回数超過した場合に、アラートが生成されます。

### <a name="number-of-results-alert-rules"></a>結果の数のアラート ルール

**結果の数**のアラート ルールでは、検索クエリによって返されるレコード数が指定されたしきい値を超えた場合に、1 回だけアラートが生成されます。 この種類のアラート ルールは、Windows イベント ログ、Syslog、WebApp 応答、カスタム ログなどのイベントを操作するために最適です。  特定のエラー イベントが作成されたとき、または特定の期間内に複数のエラー イベントが作成されたときなどに、アラートを作成することができます。

**しきい値**: 結果の数アラート ルールのしきい値は、特定の値より大きいか小さいかです。  ログ検索によって返されるレコード数がこの条件を満たしたときに、アラートが生成されます。

1 つのイベントに関するアラートを生成するには、結果の数を 0 より大きい値に設定し、クエリが最後に実行されてから作成された 1 つのイベントの発生をチェックします。 一部のアプリケーションでは、必ずしもアラートを発生させない偶発的なエラーが記録される場合もあります。  たとえば、エラー イベントを作成したプロセスをアプリケーションが再試行し、次回は成功する場合などがあります。  このような場合は、特定の期間内に複数のイベントが作成されない限り、アラートを作成しないようにできます。  

また、イベントが発生しないときにアラートを作成する場合もあります。  たとえば、正しく動作していることを示すために定期的なイベントを記録するプロセスがあります。  そのようなプロセスが特定の期間内にそれらのイベントを記録しなかった場合には、アラートを作成する必要があります。  この場合、しきい値を **1 より小さく**設定します。

#### <a name="example-of-number-of-records-type-log-alert"></a>レコード タイプの数によるログ アラートの例

Web ベースのアプリがコード 500 (つまり) 内部サーバー エラーの応答をいつユーザーに提供するかを確認するシナリオを考えてみます。 以下のような詳細情報を使用してアラート ルールを作成します。  

- **クエリ:** requests | where resultCode == "500"<br>
- **期間:** 30 分<br>
- **アラート頻度:** 5 分<br>
- **しきい値:** 0 より大きい<br>

その後、アラートはこのクエリを 30 分のデータで 5 分ごとに実行して、結果コードが 500 であったすべてのレコードを探します。 このようなレコードが 1 つでも見つかると、アラートが生成され、構成されたアクションがトリガーされます。

### <a name="metric-measurement-alert-rules"></a>メトリック測定のアラート ルール

**メトリック測定**のアラート ルールでは、クエリの対象となったオブジェクトが分析され、特定の値が指定されたしきい値を上回っているオブジェクトについて、それぞれ別個にアラートが生成されます。  **結果の数**のアラート ルールとは、以下の点が明確に異なります。

- **集計関数**: 実行する計算と、集計の対象になる可能性がある数値フィールドを決める要素です。  たとえば、**count()** であれば、クエリで指定したレコードの件数が返されます。**avg(CounterValue)** であれば、一定期間内の CounterValue フィールドの平均値が返されます。 クエリの集計関数は、AggregatedValue という名前で指定して呼び出し、数値を指定する必要があります。 

- **グループ フィールド**: このフィールドのインスタンスごとに値を集計したレコードが作成されます。警告は、それぞれのインスタンスについて生成されます。  たとえば、コンピューターごとにアラートを生成する場合には、**by Computer** を使用します。 アラート クエリ内に複数のグループ フィールドが指定されている場合は、**[Aggregate On]\(集計\)** (metricColumn) パラメーターを使用して結果を並べ替えるために使用するフィールドを指定できます

    > [!NOTE]
    > *[Aggregate On]\(集計\)* (metricColumn) オプションは、Application Insights 用のメトリック測定タイプのログ アラートと、[scheduledQueryRules API を使用するように構成された Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) 用のログ アラートでのみ使用できます。

- **間隔**: データを集計する間隔を定義する要素です。  たとえば、**5 分**を指定した場合には、アラートに対して指定した期間にわたり、グループ フィールドの各インスタンスについて、5 分間隔でレコードが作成されます。

    > [!NOTE]
    > クエリでは Bin 関数を使用して間隔を指定する必要があります。 bin() では不均一な時間間隔が返される場合があるため、実行時、アラートは適切な時間を使用して bin を自動的に bin_at コマンドに変換し、固定小数点を持つ結果が得られるようにします。 メトリック測定タイプのログ アラートは、bin() コマンドの最大 3 個のインスタンスを含むクエリで機能するように設計されています
    
- **しきい値**: メトリック測定のアラート ルールのしきい値は、集計値と "抵触" の発生回数の 2 つの要素によって決まります。  ログ検索でいずれかのデータ ポイントが一定の値を超えると、抵触が 1 回発生したと判定されます。  そして、結果に含まれるオブジェクトの抵触の発生回数が指定された値を超えたときに、そのオブジェクトについて警告が生成されます。

*[Aggregate On]\(集計\)* または *metricColumn* オプションの構成を間違えると、アラート ルールが間違って適用される可能性があります。 詳細については、[メトリック測定アラート ルールが正しくない場合のトラブルシューティング](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect)に関する記事を参照してください。

#### <a name="example-of-metric-measurement-type-log-alert"></a>メトリック測定タイプのログ アラートの例

いずれかのコンピューターでプロセッサの使用率が 90% を超える状態が 30 分間に 3 回発生した場合にアラートを生成するシナリオを考えてみましょう。  以下のような詳細情報を使用してアラート ルールを作成します。  

- **クエリ:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer<br>
- **期間:** 30 分<br>
- **アラート頻度:** 5 分<br>
- **アラート ロジック - 条件としきい値:** 90 より大きい<br>
- **グループ フィールド (集計):** Computer
- **アラートをトリガーする基準:** 総違反数が 2 より多い<br>

このクエリは、各コンピューターの平均値を 5 分間隔で算出します。  このクエリは過去 30 分間に収集されたデータを対象に、5 分ごとに実行されます。 選択されたグループ フィールド (集計) は列指向の "Computer" であるため、AggregatedValue は "Computer" のさまざまな値に対応して分割され、各コンピューターの平均的なプロセッサ使用率は 5 分の時間ビンに対して特定されます。  たとえば、3 台のコンピューターのサンプル クエリの結果は次のようになります。


|TimeGenerated [UTC] |Computer  |AggregatedValue  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

クエリ結果をグラフで表すと、次のようになります。

![サンプル クエリの結果](media/alerts-unified-log/metrics-measurement-sample-graph.png)

この例では、3 台の各コンピューターが 5 分のビンで示されています。つまり、平均的なプロセッサ使用率が 5 分単位で計算されています。 srv01 は、1:25 のビンでしきい値 90 に 1 回だけ抵触しています。 比較すると、srv02 はしきい値 90 に 1:10、1:15、および 1:25 のビンで抵触しており、srv03 はしきい値 90 に 1:10、1:15、1:20、および 1:30 のビンで抵触しています。
合計違反回数 (3 回以上) に基づいてトリガーするようにアラートが構成されているので、条件を満たしているのは srv02 と srv03 のみです。 複数の時間ビンにわたって srv02 と srv03 の 2 台がしきい値 (90 %) に 2 回抵触しています。このため、この 2 台について個別にアラートが生成されます。*[アラートをトリガーする基準]* パラメーターが *[Continuous breaches]\(継続的な違反\)* オプション用に構成されている場合は、srv03 に対して**のみ**アラートが発生します。これは、1:10 から 1:20 までの 3 つの連続する時間ビンで srv03 がしきい値に抵触しているためです。 srv02 は 1:10 から 1:15 までの 2 つの連続する時間ビンでしきい値に抵触しているため、**該当しません**。

## <a name="log-search-alert-rule---firing-and-state"></a>ログ検索アラート ルール - 起動と状態

ログ検索アラート ルールは、使用されている構成およびカスタム分析クエリに従って、ユーザーが指定したロジックで動作します。 アラート ルールがトリガーされる正確な条件や理由のロジックは、分析クエリにカプセル化されているため、ログ アラート ルールごとに異なる場合があります。 ログ検索アラート ルールのしきい値の条件に達したり超えたりした場合でも、Azure アラートには、ログ結果内部の特定の具体的な根本原因に関する情報が多くありません。 このため、ログ アラートはステートレスと呼ばれ、ログ検索結果が、"*結果の数*" タイプまたは "*メトリック測定*" タイプの条件のとしてログ アラートで指定されたしきい値を超えるたびに発生します。 また、指定されたカスタム分析クエリの結果がアラート条件を満たす限り、ログ アラート ルールはトリガされ続けます。その際、アラートが毎回解決されるとは限りません。 監視失敗の正確な根本原因のロジックは、ユーザーが提供する分析クエリ内でマスクされます。このため、しきい値に達していないログ検索結果が問題の解決を示しているかどうかを最終的に推論する手段は Azure アラートにはありません。

[結果の数タイプのログ アラートの例](#example-of-number-of-records-type-log-alert)で指定された構成に従って、*Contoso-Log-Alert* というログ アラート ルールがあるとします。 
- 午後 1 時 05分に、Contoso-Log-Alert が Azure アラートによって実行されたとき、ログ検索結果により 0 個のレコードが生成されました。これはしきい値を下回っているため、アラートは発生しません。 
- 次のイテレーションである午後 1 時 10 分に Contoso-Log-Alert が Azure アラートによって実行されたとき、ログ検索結果により 5 個のレコードが提供されました。しきい値を超えたため、アラートが発生し、関連する[アクション グループ](../../azure-monitor/platform/action-groups.md)が直後にトリガーされました。 
- 午後 1 時 15 分に Contoso-Log-Alert が Azure アラートによって実行されたとき、ログ検索結果により 2 個のレコードが提供されました。しきい値を超えたため、アラートが発生し、関連する[アクション グループ](../../azure-monitor/platform/action-groups.md)が直後にトリガーされました。
- 次のイテレーションである午後 1 時 20 分に Contoso-Log-Alert が Azure アラートによって実行されたとき、ログ検索結果により再度 0 個のレコードが提供されました。これはしきい値を下回っているため、アラートは発生しません。

ただし、上にあげたケースでは、午後 1 時 15 分の時点で、Azure アラートでは、1 時 10 分に見られた問題の根本原因が継続しているかどうか、また新たな障害が発生しているかどうかが判断できません。ユーザーが提供したクエリで前のレコードが考慮されている可能性もあるためですが、Azure アラートでは認識できません。 このため慎重を期して、Contoso-Log-Alert が午後 1 時 15 分に実行されるときに、構成済みの[アクション グループ](../../azure-monitor/platform/action-groups.md)が再びトリガーされます。 午後 1 時 20 分にはレコードがありませんが、Azure アラートでは、レコードの原因が解決されたかどうかを確信できません。このため、Azure アラート ダッシュボードでは Contoso-Log-Alert が "解決済み" に変更されませんし、アラートの解決を示す通知も送信されません。


## <a name="pricing-and-billing-of-log-alerts"></a>ログ アラートの価格と課金

ログ アラートに適用される価格については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」ページを参照してください。 Azure 請求書には、ログ アラートが型 `microsoft.insights/scheduledqueryrules` として表示され、さらに次が表示されます。

- Application Insights のログ アラートが正しいアラート名でリソース グループとアラート プロパティと共に表示されます
- [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) を使用して作成されている場合、Log Analytics のログ アラートは、正しいアラート名でリソース グループとアラート プロパティと共に表示されます

[従来の Log Analytics API](../../azure-monitor/platform/api-alerts.md) には、Log Analytics の保存した検索条件の一部としてアラート アクションとスケジュールがあり、適切な [Azure リソース](../../azure-resource-manager/resource-group-overview.md)ではありません。 そのため、[新しい API への切り替え](../../azure-monitor/platform/alerts-log-api-switch.md)を**行わないで** Azure portal を使用して、または[従来の Log Analytics API](../../azure-monitor/platform/api-alerts.md) を使用して、Log Analytics に対して作成されたこのような従来のログ アラートに対する課金を有効にするため、非表示の擬似アラート ルールが Azure での課金用に `microsoft.insights/scheduledqueryrules` に作成されます。 課金用に `microsoft.insights/scheduledqueryrules` に作成された非表示の擬似アラート ルールは、リソース グループおよびアラートのプロパティと共に `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` と表示されます。

> [!NOTE]
> `<, >, %, &, \, ?, /` のような無効な文字が存在する場合、非表示の擬似アラート ルールの名前および Azure の課金では `_` に置き換えられます。

[従来の Log Analytics API](api-alerts.md) 使用してアラート ルールの課金用に作成された非表示の scheduleQueryRules リソースを削除するには、次のいずれかの方法で行うことができます。

- ユーザーは、[Log Analytics ワークスペースでのアラート ルールの API 基本設定を切り替え](../../azure-monitor/platform/alerts-log-api-switch.md)て、アラート ルールまたは監視を失うことなく、Azure Resource Manager に準拠している [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) に移動できます。 それにより、課金のために非表示の擬似アラート ルールを作成する必要はなくなります。
- ユーザーが API の基本設定を切り替えたくない場合は、[従来の Log Analytics API](api-alerts.md) を使用して元のスケジュールとアラートのアクションを**削除**するか、または [Azure portal で元のログ アラート ルール](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)を削除する必要があります

## <a name="next-steps"></a>次の手順

* [Azure でのログ アラートの作成](../../azure-monitor/platform/alerts-log.md)について学習します。
* [Azure のログ アラートの Webhook](alerts-log-webhook.md) について理解する。
* [Azure アラート](../../azure-monitor/platform/alerts-overview.md)について学習します。
* [Application Insights](../../azure-monitor/app/analytics.md) についてさらに学習します。
* [Log Analytics](../../azure-monitor/log-query/log-query-overview.md) についてさらに学習します。