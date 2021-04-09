---
title: Azure Application Insights アプリのデータを表示する | Microsoft Docs
description: Application Insights Connector ソリューションを使用すると、Application Insights でアプリを監視しているときにパフォーマンスに関する問題を診断し、ユーザーがアプリで何を行っているかを理解することができます。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c21ed461280dfa617c852fe6c18c30e5d697b704
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031278"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Application Insights Connector 管理ソリューション (非推奨)

![Application Insights シンボル](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> [リソース間のクエリ](../logs/cross-workspace-query.md)のサポートにより、Application Insights Connector 管理ソリューションはもはや必要なくなりました。 その機能は非推奨になり、2019 年 1 月 15 日に Azure 商用クラウドで正式に非推奨となった OMS ポータルと共に、Azure Marketplace から削除されています。 Azure US Government クラウドでは 2019 年 3 月 30 日に廃止されます。
>
>既存の接続は 2019 年 6 月 30日まで引き続き機能します。  OMS ポータルの廃止により、ポータルから既存の接続を構成および削除する方法がなくなります。 PowerShell を使用して既存の接続を削除するスクリプトについては、後の「[PowerShell でコネクタを削除する](#removing-the-connector-with-powershell)」をご覧ください。
>
>複数のアプリケーションに対する Application Insights ログ データのクエリを実行する方法については、「[Azure Monitor で複数の Application Insights リソースを統合する](./unify-app-resource-data.md)」をご覧ください。 OMS ポータルの廃止について詳しくは、「[OMS ポータルの Azure への移行](./oms-portal-transition.md)」をご覧ください。
>
> 

Application Insights Connector ソリューションを使用すると、[Application Insights](../app/app-insights-overview.md) でアプリを監視しているときにパフォーマンスに関する問題を診断し、ユーザーがアプリで何を行っているかを理解することができます。 Log Analytics でも Application Insights で開発者に表示されるものと同じアプリケーション テレメトリのビューを使用できます。 しかし、Application Insights アプリを Log Analytics と統合すると、運用データとアプリケーション データを 1 か所にまとめることによってアプリケーションの可視性が向上します。 同じビューの表示は、アプリ開発者との共同作業を支援します。 共通のビューによって、アプリケーションの問題とプラットフォームの問題の両方を検出して解決するための時間を短縮できます。

このソリューションを使用して、次の操作を実行できます。

- 複数の Azure サブスクリプションに属している場合でも、すべての Application Insights アプリを 1 か所に表示する
- インフラストラクチャ データとアプリケーション データを相互に関連付ける
- ログ検索でパースペクティブを使用してアプリケーション データをグラフ化する
- Azure Portal で、Log Analytics データから Application Insights アプリにピボットする


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>接続先ソース

他のほとんどの Log Analytics ソリューションとは異なり、Application Insights Connector 用のデータはエージェントによって収集されません。 ソリューションで使用されるデータはすべて、Azure から直接収集されます。

| 接続先ソース | サポートされています | 説明 |
| --- | --- | --- |
| [Windows エージェント](./../agents/agent-windows.md) | いいえ | ソリューションでは、Windows エージェントの情報は収集しません。 |
| [Linux エージェント](../vm/quick-collect-linux-computer.md) | いいえ | ソリューションでは、Linux エージェントの情報は収集しません。 |
| [SCOM 管理グループ](../agents/om-agents.md) | いいえ | ソリューションでは、接続された SCOM 管理グループ内のエージェントの情報は収集しません。 |
| [Azure Storage アカウント](../essentials/resource-logs.md#send-to-log-analytics-workspace) | いいえ | ソリューションでは、Azure Storage の情報は収集しません。 |

## <a name="prerequisites"></a>前提条件

- Application Insights Connector 情報にアクセスするには、Azure サブスクリプションが必要です。
- 少なくとも 1 つの Application Insights リソースが構成済みである必要があります。
- Application Insights リソースの所有者または共同作成者である必要があります。

## <a name="configuration"></a>構成

1. Azure Web Apps Analytics ソリューションを有効にします。[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps) から有効にするか、[ソリューション ギャラリーからの Log Analytics ソリューションの追加](../insights/solutions.md)に関するページで説明されているプロセスを使用して有効にしてください。
2. [Azure ポータル](https://portal.azure.com)にアクセスします。 **[すべてのサービス]** を選択して、Application Insights を開きます。 次に、"Application Insights" を検索します。 
3. **[サブスクリプション]** で、Application Insights リソースを所有するサブスクリプションを選択し、 **[名前]** で、1 つまたは複数のアプリケーションを選択します。
4. **[保存]** をクリックします。

約 30 分でデータが使用可能となり、次の図のように、[Application Insights] タイルがデータで更新されます。

![[Application Insights] タイル](./media/app-insights-connector/app-insights-tile.png)

その他の留意点:

- Application Insights アプリは、1 つの Log Analytics ワークスペースにのみリンクできます。
- [Basic または Enterprise の Application Insights リソース](https://azure.microsoft.com/pricing/details/application-insights)は、Log Analytics にのみリンクできます。 ただし、Log Analytics の Free レベルを使用することができます。

## <a name="management-packs"></a>管理パック

このソリューションでは、接続されている管理グループに管理パックがインストールされることはありません。

## <a name="use-the-solution"></a>ソリューションの使用

次のセクションで、Application Insights ダッシュボードに表示されるブレードを使用して、アプリからのデータの表示と操作を行う方法について説明します。

### <a name="view-application-insights-connector-information"></a>Application Insights Connector 情報を表示する

**[Application Insights]** タイルをクリックします。**Application Insights** ダッシュボードが開いて、次のブレードが表示されます。

![アプリケーション、データ ボリューム、および可用性のブレードを表示する [Application Insights] ダッシュボードのスクリーンショット。](./media/app-insights-connector/app-insights-dash01.png)

![サーバー要求、障害、および例外のブレードを表示する [Application Insights] ダッシュボードのスクリーンショット。](./media/app-insights-connector/app-insights-dash02.png)

ダッシュボードには、次の表に示すブレードが含まれます。 それぞれのブレードには、特定のスコープと時間範囲について、そのブレードの基準に該当する項目が最大 10 個表示されます。 ブレードの一番下にある **[すべて表示]** をクリックするかブレード ヘッダーをクリックすると、すべてのレコードを返すログ検索を実行できます。


| **列** | **説明** |
| --- | --- |
| アプリケーション: アプリケーションの数 | アプリケーション リソース内のアプリケーションの数を示します。 アプリケーションの名前と各アプリケーションのレコードの数も示されます。 数値をクリックすると、ログ検索 (<code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code>) が実行されます。 <br><br>  アプリケーション名をクリックすると、アプリケーションのログ検索が実行され、ホストごとのアプリケーションのレコード数、テレメトリの種類別のレコード数、および種類別のすべてのデータが表示されます (各データは最終日に基づきます)。 |
| データ ボリューム: データを送信中のホスト | データを送信しているコンピューター ホストの数を示します。 コンピューター ホストと各ホストのレコード数も示されます。 数値をクリックすると、ログ検索 (<code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code>) が実行されます。 <br><br> コンピューター名をクリックすると、ホストのログ検索が実行され、ホストごとのアプリケーションのレコード数、テレメトリの種類別のレコード数、および種類別のすべてのデータが表示されます (各データは最終日に基づきます)。 |
| 可用性: Web テストの結果 | Web テストの結果 (合格または不合格) を示すドーナツ グラフを表示します。 グラフをクリックすると、ログ検索 (<code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code>) が実行されます。 <br><br> 結果は、すべてのテストの合格数と不合格数を示します。 直前の 1 分間にトラフィックが発生した Web Apps がすべて表示されます。 アプリケーション名をクリックすると、不合格だった Web テストの詳細を示すログ検索が表示されます。 |
| サーバー要求: 1 時間あたりの要求の数 | さまざまなアプリケーションの 1 時間あたりのサーバー要求の数を折れ線グラフで示します。 グラフ内の線上にポインターを置くと、ある時点での要求の受信数が多い上位 3 つのアプリケーションが表示されます。 要求を受信しているアプリケーションの一覧と、選択した期間中の要求の数も示されます。 <br><br>グラフをクリックすると、ログ検索 (<code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>) が実行され、さまざまなアプリケーションの 1 時間あたりのサーバー要求の数を示す詳細な折れ線グラフが表示されます。 <br><br> 一覧のアプリケーションをクリックすると、ログ検索 (<code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>) が実行され、要求の一覧、時間を追った要求数のグラフ、要求の実行時間別の要求数のグラフ、および要求応答コードの一覧が表示されます。   |
| 失敗: 1 時間あたりの失敗した要求数 | 1 時間あたりの失敗したアプリケーション要求の数を折れ線グラフで示します。 グラフ内の線上にポインターを置くと、ある時点での失敗した要求の数が多い上位 3 つのアプリケーションが表示されます。 アプリケーションと各アプリケーションの失敗した要求の数の一覧も表示されます。 グラフをクリックすると、ログ検索 (<code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>) が実行され、失敗したアプリケーション要求の数を示す詳細な折れ線グラフが表示されます。 <br><br>一覧の項目をクリックすると、ログ検索 (<code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>) が実行され、失敗した要求の数、時間を追った失敗要求数のグラフ、要求の実行時間別の失敗要求数のグラフ、および失敗した要求の応答コードの一覧が表示されます。 |
| 例外: 1 時間あたりの例外の数 | 1 時間あたりの例外の数を示す折れ線グラフを表示します。 グラフ内の線上にポインターを置くと、ある時点での例外の数が多い上位 3 つのアプリケーションが表示されます。 アプリケーションと各アプリケーションの例外の数の一覧も表示されます。 グラフをクリックすると、ログ検索 (<code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>) が実行され、例外の数を示す詳細な折れ線グラフが表示されます。 <br><br>一覧の項目をクリックすると、ログ検索 (<code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code>) が実行され、例外の一覧、時間を追った例外のグラフ、失敗した要求の数のグラフ、および例外の種類別の一覧が表示されます。  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>ログ検索で Application Insights のパースペクティブを表示する

ダッシュボードのいずれかの項目をクリックすると、検索に Application Insights のパースペクティブが表示されます。 パースペクティブは、選択されたテレメトリの種類に基づく拡張グラフを提供します。 そのため、グラフ化されるコンテンツは、テレメトリの種類に応じて変化します。

[アプリケーション] ブレード内の任意の場所をクリックすると、既定の **アプリケーション** パースペクティブが表示されます。

![Application Insights のアプリケーション パースペクティブ](./media/app-insights-connector/applications-blade-drill-search.png)

このパースペクティブは、選択されたアプリケーションの概要を示します。

**[可用性]** ブレードには複数のパースペクティブ ビューが表示されるため、Web テストの結果と、関連する失敗した要求を確認できます。

![Application Insights の可用性パースペクティブ](./media/app-insights-connector/availability-blade-drill-search.png)

**[サーバー要求]** ブレードまたは **[失敗]** ブレード内の任意の場所をクリックすると、パースペクティブ コンポーネントが変わり、要求に関連するグラフが表示されます。

![Application Insights の [失敗] ブレード](./media/app-insights-connector/server-requests-failures-drill-search.png)

**[例外]** ブレード内の任意の場所をクリックすると、例外に合わせたグラフが表示されます。

![Application Insights の [例外] ブレード](./media/app-insights-connector/exceptions-blade-drill-search.png)

**Application Insights Connector** ダッシュボードで何かをクリックしているかどうかに関係なく、 **[検索]** ページでは、Application Insights データを返すすべてのクエリで Application Insights パースペクティブが表示されます。 たとえば、Application Insights データを表示する場合、 **&#42;** クエリでも、次の図のように、パースペクティブ タブが表示されます。

![Application Insights](./media/app-insights-connector/app-insights-search.png)

パースペクティブ コンポーネントは、検索クエリに応じて更新されます。 つまり、結果を任意の検索フィールドを使用してフィルター処理することで、以下のデータを表示することができます。

- すべてのアプリケーション
- 1 つの選択したアプリケーション
- アプリケーションのグループ

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Azure ポータルでアプリにピボットする

[Application Insights Connector] ブレードは、"*Azure portal を使用しているときに*"、選択した Application Insights アプリにピボットできるように設計されています。 このソリューションを高度な監視プラットフォームとして使用して、アプリのトラブルシューティングを行うことができます。 接続されているアプリケーションのいずれかで潜在的な問題があることを確認したら、Log Analytics 検索で問題をドリルダウンするか、Application Insights アプリに直接ピボットすることができます。

ピボットするには、各行の最後に表示される省略記号 ( **…** ) をクリックし、 **[Application Insights で開く]** を選択します。

>[!NOTE]
>**[Application Insights で開く]** は Azure ポータルでは使用できません。

![Application Insights で開く](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>サンプリング補正データ

Application Insights では、" *[サンプリング補正](../app/sampling.md)* " によって、テレメトリのトラフィックを削減しています。 Application Insights アプリでサンプリングを有効にすると、Application Insights と Log Analytics の両方に格納されるエントリの数が減少します。 データの整合性は **Application Insights Connector** ページとパースペクティブで保持されますが、カスタム クエリでは、サンプリング データを手動で補正する必要があります。

ログ検索クエリでのサンプリング補正の例を次に示します。

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

**SampledCount** フィールドはすべてのエントリに存在し、エントリが表すデータ ポイントの数を示します。 Application Insights アプリのサンプリングを有効にした場合、**SampledCount** は 1 より大きくなります。 アプリケーションが生成するエントリの実際の数をカウントするには、**SampledCount** フィールドを合計します。

サンプリングは、アプリケーションが生成するエントリの総数のみに影響します。 **RequestDuration** や **AvailabilityDuration** などのメトリック フィールドは、表わされているエントリの平均値を示すため、これらのフィールドのサンプリングは補正する必要がありません。

## <a name="input-data"></a>入力データ

ソリューションは、接続されている Application Insights アプリから次のテレメトリの種類のデータを受信します。

- 可用性
- 例外
- Requests
- ページ ビュー: ワークスペースでページ ビューを受信するには、その情報を収集するようにアプリを構成する必要があります。 詳細については、「[ページ ビュー](../app/api-custom-events-metrics.md#page-views)」を参照してください。
- カスタム イベント: ワークスペースでページ ビューを受信するには、その情報を収集するようにアプリを構成する必要があります。 詳細については、「[TrackEvent](../app/api-custom-events-metrics.md#trackevent)」を参照してください。

データは、使用可能になると、Log Analytics によって Application Insights から受信されます。

## <a name="output-data"></a>出力データ

入力データの種類ごとに、"*種類*" が "*ApplicationInsights*" であるレコードが作成されます。 ApplicationInsights レコードには、次のセクションに示すプロパティがあります。

### <a name="generic-fields"></a>一般的なフィールド

| プロパティ | 説明 |
| --- | --- |
| Type | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | レコードの時刻 |
| ApplicationId | Application Insights アプリのインストルメンテーション キー |
| ApplicationName | Application Insights アプリの名前 |
| RoleInstance | サーバー ホストの ID |
| DeviceType | クライアント デバイス |
| ScreenResolution |   |
| Continent | 要求元の大陸 |
| Country | 要求元の国や地域 |
| Province | 要求元の都道府県またはロケール |
| City | 要求元の市町村 |
| isSynthetic | 要求がユーザーによって作成されたか、自動化された方法で作成されたかを示します。 true = 自動化された方法、false = ユーザーが生成 |
| SamplingRate | ポータルに送信される、SDK によって生成されたテレメトリの割合。 範囲 0.0 ～ 100.0。 |
| SampledCount | 100/(SamplingRate)。 例: 4 =&gt; 25% |
| IsAuthenticated | ｔrue または false |
| OperationID | 同じ操作 ID を持つ項目は、関連項目としてポータルに表示されます。 通常は要求 ID です |
| ParentOperationID | 親操作の ID |
| OperationName |   |
| SessionId | 要求が作成されたセッションを一意に識別する GUID |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>可用性に固有のフィールド

| プロパティ | 説明 |
| --- | --- |
| TelemetryType | 可用性 |
| AvailabilityTestName | Web テストの名前 |
| AvailabilityRunLocation | Http 要求の地理的ソース |
| AvailabilityResult | Web テストの合格結果を示します |
| AvailabilityMessage | Web テストに添付されたメッセージ |
| AvailabilityCount | 100/(サンプリング レート)。 例: 4 =&gt; 25% |
| DataSizeMetricValue | 1.0 または 0.0 |
| DataSizeMetricCount | 100/(サンプリング レート)。 例: 4 =&gt; 25% |
| AvailabilityDuration | Web テスト継続時間 (ミリ秒単位) |
| AvailabilityDurationCount | 100/(サンプリング レート)。 例: 4 =&gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | Web テストの一意の GUID |
| AvailabilityTimestamp | 可用性テストの正確なタイムスタンプ |
| AvailabilityDurationMin | サンプリング レコードでは、このフィールドは、表されるデータ ポイントの最小の Web テスト継続時間 (ミリ秒単位) を示します |
| AvailabilityDurationMax | サンプリング レコードでは、このフィールドは、表されるデータ ポイントの最大の Web テスト継続時間 (ミリ秒単位) を示します |
| AvailabilityDurationStdDev | サンプリング レコードでは、このフィールドは、表されるデータ ポイントのすべての Web テスト継続時間 (ミリ秒単位) の標準偏差を示します |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>例外に固有のフィールド

| Type | ApplicationInsights |
| --- | --- |
| TelemetryType | 例外 |
| ExceptionType | 例外の種類 |
| ExceptionMethod | 例外を作成するメソッド |
| ExceptionAssembly | アセンブリには、フレームワーク、バージョン、および公開キー トークンが含まれます |
| ExceptionGroup | 例外の種類 |
| ExceptionHandledAt | 例外を処理したレベルを示します |
| ExceptionCount | 100/(サンプリング レート)。 例: 4 =&gt; 25% |
| ExceptionMessage | 例外のメッセージ |
| ExceptionStack | 例外の完全なスタック |
| ExceptionHasStack | 例外にスタックがある場合は true |



### <a name="request-specific-fields"></a>要求に固有のフィールド

| プロパティ | 説明 |
| --- | --- |
| Type | ApplicationInsights |
| TelemetryType | Request |
| ResponseCode | クライアントに送信された HTTP 応答 |
| RequestSuccess | 成功または失敗を示します。 true または false |
| RequestID | 要求を一意に識別する ID |
| RequestName | GET/POST + URL ベース |
| RequestDuration | 要求の実行時間の時間 (秒単位) |
| URL | ホストを含まない要求の URL |
| Host | Web サーバー ホスト |
| URLBase | 要求の完全な URL |
| ApplicationProtocol | アプリケーションで使用されるプロトコルの種類 |
| RequestCount | 100/(サンプリング レート)。 例: 4 =&gt; 25% |
| RequestDurationCount | 100/(サンプリング レート)。 例: 4 =&gt; 25% |
| RequestDurationMin | サンプリング レコードでは、このフィールドは、最小の要求の実行時間 (ミリ秒単位) を示します |
| RequestDurationMax | サンプリング レコードでは、このフィールドは、最大の要求の実行時間 (ミリ秒単位) を示します |
| RequestDurationStdDev | サンプリング レコードでは、このフィールドは、表されるデータ ポイントのすべての要求の実行時間 (ミリ秒単位) の標準偏差を示します |

## <a name="sample-log-searches"></a>サンプル ログ検索

このソリューションには、ダッシュボードに表示されるサンプル ログ検索のセットはありません。 ただし、サンプル ログ検索クエリとその説明が「[Application Insights Connector 情報を表示する](#view-application-insights-connector-information)」セクションに示されています。

## <a name="removing-the-connector-with-powershell"></a>PowerShell でコネクタを削除する
OMS ポータルの廃止により、ポータルから既存の接続を構成および削除する方法がなくなります。 次の PowerShell スクリプトを使用して、既存の接続を削除することができます。 この操作を実行するには、ワークスペースの所有者または共同作成者であり、Application Insights リソースの閲覧者である必要があります。

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

REST API 呼び出しを行う次の PowerShell スクリプトを使用して、アプリケーションの一覧を取得することができます。 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
このスクリプトでは、Azure Active Directory に対する認証のためにベアラー認証トークンが必要です。 このトークンを取得する方法の 1 つでは、[REST API のドキュメント サイト](/rest/api/loganalytics/datasources/createorupdate)の記事を使用します。 **[試してみる]** をクリックして、お使いの Azure サブスクリプションにログインします。 次の図のように、 **[Request Preview]\(要求プレビュー\)** からベアラー トークンをコピーできます。


![ベアラー トークン](media/app-insights-connector/bearer-token.png)


また、ログ クエリを使用してアプリケーションの一覧を取得することもできます。

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>次のステップ

- [ログ検索](./log-query-overview.md)を使用して Application Insights アプリの詳細情報を表示します。