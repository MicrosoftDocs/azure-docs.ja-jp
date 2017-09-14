---
title: "Azure Log Analytics の警告について | Microsoft Docs"
description: "Log Analytics のアラートは、OMS リポジトリ内の重要な情報を識別し、問題について事前に通知したり、問題を修正するためのアクションを呼び出したりできます。  この記事では、さまざまな種類のアラート ルールとその定義方法について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 951e76d3fb18d9e433b148e82d4d6cee9417ce6d
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---
# <a name="understanding-alerts-in-log-analytics"></a>Log Analytics のアラートについて

Log Analytics のアラートは、Log Analytics リポジトリ内の重要な情報を特定します。  この記事では、Log Analytics のアラート ルールのしくみを詳しく紹介します。また、アラート ルールの種類ごとの違いについても説明します。

警告ルール作成のプロセスについては、以下の記事を参照してください。

- [Azure Portal](log-analytics-alerts-creating.md) を使ってアラート ルールを作成する
- [Resource Manager テンプレート](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)を使ってアラート ルールを作成する
- [REST API](log-analytics-api-alerts.md) を使ってアラート ルールを作成する


## <a name="alert-rules"></a>アラート ルール

アラートは、ログ検索を一定の間隔で自動的に実行するアラート ルールによって作成されます。  ログ検索の結果が特定の条件に一致すると、アラート レコードが作成されます。  さらに、アラートを事前に通知したり、別のプロセスを呼び出したりするために、1 つ以上のアクションを自動的に実行できます。  この分析では、アラート ルールの種類に応じてさまざまなロジックが使用されます。

![Log Analytics alerts](media/log-analytics-alerts/overview.png)

警告ルールは次の内容で定義されます。

- **ログ検索**:   警告ルールが実行されるたびに実行されるクエリ。  このクエリによって返されるレコードを使用して、警告を作成するかどうかが判断されます。
- **時間枠**:   クエリの時間範囲を指定します。  クエリでは、現在の時刻に先立つ指定の時間範囲の間に作成されたレコードだけを返します。  5 分から 24 時間までの値を指定できます。 たとえば、時間枠が 60 分に設定されていて、クエリが午後 1 時 15 分に実行された場合は、午後 12 時 15 分から午後 1 時 15 分までの間に作成されたレコードだけが返されます。
- **[頻度]**:   クエリの実行頻度を指定します。 5 分から 24 時間までの値を指定できます。 この値は、時間枠の値以下にする必要があります。  この値が時間枠の値よりも大きい場合、レコードを見落とすおそれがあります。<br>たとえば、時間枠が 30 分、頻度が 60 分であるとします。  クエリが午後 1 時に実行された場合、午後 12 時 30 分から午後 1 時までの間のレコードが返されます。  次回クエリが実行されるのは午後 2 時であり、このときには午後 1 時 30 分から午後 2 時までの間のレコードが返されます。  つまり、午後 1 時から午後 1 時 30 分までの間に作成されたレコードは評価されないことになります。
- **しきい値**:   ログ検索の結果を評価し、アラートの生成が必要であるかどうかを判定するための値です。  しきい値は、アラート ルールの種類によって異なります。

Log Analytics の警告ルールはいずれも、2 種類のどちらかに該当します。  どちらについても、後のセクションで詳しく説明します。

- **[結果の数](#number-of-results-alert-rules)**。 ログ検索によって返されるレコードの数が指定された数を超えた場合に、アラートが 1 回生成されます。
- **[メトリック測定](#metric-measurement-alert-rules)**。  ログ検索の結果の値が指定されたしきい値を超えた場合に、オブジェクトごとにアラートが生成されます。

この 2 種類のアラート ルールの違いは次のとおりです。

- **結果の数**の警告ルールでは常に、警告が 1 回だけ生成されます。これに対して、**メトリック測定**の警告ルールでは、しきい値を超えたオブジェクトそれぞれについて、警告が生成されます。
- **結果の数**の警告ルールでは、1 回しきい値を超えた時点で警告が生成されます。 これに対して、**メトリック測定**のアラート ルールでは、一定期間内にしきい値を一定回数超過した場合に、アラートが生成されます。

## <a name="number-of-results-alert-rules"></a>結果の数のアラート ルール
**結果の数**のアラート ルールでは、検索クエリによって返されるレコード数が指定されたしきい値を超えた場合に、1 回だけアラートが生成されます。

### <a name="threshold"></a>しきい値
**結果の数**のアラート ルールでは、単に特定の値との大小の比較によって、しきい値に対する抵触の有無を判定します。  ログ検索によって返されるレコード数がこの条件を満たしたときに、アラートが生成されます。

### <a name="scenarios"></a>シナリオ

#### <a name="events"></a>イベント
この種類の警告ルールは、Windows イベント ログ、Syslog、カスタム ログのようなイベントでの使用に最適です。  特定のエラー イベントが作成されたとき、または特定の時間枠内に複数のエラー イベントが作成されたときなどに、アラートを作成することができます。

1 つのイベントに対してアラートを作成するには、結果の数を 0 より大きな値に設定し、頻度と時間枠の両方を 5 分に設定します。  それにより、クエリが 5 分ごとに実行され、前回のクエリ実行後に作成された 1 つのイベントの発生を確認します。  頻度の値を大きくすると、イベントが収集されてアラートが作成される間隔が長くなります。

一部のアプリケーションでは、必ずしもアラートを発生させない偶発的なエラーが記録される場合もあります。  たとえば、エラー イベントを作成したプロセスをアプリケーションが再試行し、次回は成功する場合などがあります。  このような場合は、特定の時間枠内に複数のイベントが作成されない限り、アラートを作成しないようにできます。  

また、イベントが発生しないときにアラートを作成する場合もあります。  たとえば、正しく動作していることを示すために定期的なイベントを記録するプロセスがあります。  そのようなプロセスが特定の時間枠内にそれらのイベントを記録しなかった場合には、アラートを作成する必要があります。  この場合は、しきい値を **Less than 1** に設定します。

#### <a name="performance-alerts"></a>パフォーマンスのアラート
[パフォーマンス データ](log-analytics-data-sources-performance-counters.md) は、イベントと同様に OMS リポジトリ内のレコードとして格納されます。  パフォーマンス カウンターが特定のしきい値を超えたときにアラートを生成する場合は、クエリにそのしきい値を含める必要があります。

たとえば、プロセッサが 90% を超える割合で実行されたときにアラートを生成させるには、次のようにクエリにアラート ルールのしきい値 **greater than 0** を含めます。

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

プロセッサが一定の時間内に平均 90% を超える割合で実行されたときにアラートを生成させるには、次のようにクエリで [measure コマンド](log-analytics-search-reference.md#commands)を使用し、アラート ルールのしきい値 **greater than 0** を含めます。

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90

>[!NOTE]
> ワークスペースが[新しい Log Analytics クエリ言語](log-analytics-log-search-upgrade.md)にアップグレードされている場合は、上記のクエリによって次が変更されます: `Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" and CounterValue>90`
> `Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" | summarize avg(CounterValue) by Computer | where CounterValue>90`


## <a name="metric-measurement-alert-rules"></a>メトリック測定のアラート ルール

>[!NOTE]
> メトリック測定のアラート ルールは、現在パブリック プレビューの段階です。

**メトリック測定**のアラート ルールでは、クエリの対象となったオブジェクトが分析され、特定の値が指定されたしきい値を上回っているオブジェクトについて、それぞれ別個にアラートが生成されます。  **結果の数**のアラート ルールとは、以下の点が明確に異なります。

#### <a name="log-search"></a>ログ検索
**結果の数**のアラート ルールではどのようなクエリでも使用できるのに対し、メトリック測定のアラート ルールの場合にはクエリに一定の要件が存在します。  具体的には、特定のフィールドに関する結果をグループ化するために [measure コマンド](log-analytics-search-reference.md#commands)が 1 つ必要になります。 このコマンドに必要な要素は以下のとおりです。

- **集計関数**。  実行する計算と、集計の対象になる可能性がある数値フィールドを決める要素です。  たとえば、**count()** であれば、クエリで指定したレコードの件数が返されます。**avg(CounterValue)** であれば、一定期間内の CounterValue フィールドの平均値が返されます。
- **グループ フィールド**。  このフィールドのインスタンスごとに値を集計したレコードが作成されます。警告は、それぞれのインスタンスについて生成されます。  たとえば、コンピューターごとにアラートを生成する場合には、**by Computer** を使用します。   
- **[間隔]**:   データを集計する間隔を定義する要素です。  たとえば、**5minutes** を指定した場合には、アラートに対して指定した期間にわたり、グループ フィールドの各インスタンスについて、5 分間隔でレコードが作成されます。

#### <a name="threshold"></a>しきい値
メトリック測定のアラート ルールのしきい値は、集計値と "抵触" の発生回数の 2 つの要素によって決まります。  ログ検索でいずれかのデータ ポイントが一定の値を超えると、抵触が 1 回発生したと判定されます。  そして、結果に含まれるオブジェクトの抵触の発生回数が指定された値を超えたときに、そのオブジェクトについて警告が生成されます。

#### <a name="example"></a>例
いずれかのコンピューターでプロセッサの使用率が 90% を超える状態が 30 分間に 3 回発生した場合にアラートを生成するシナリオを考えてみましょう。  この場合、以下のようなアラート ルールを作成します。  

**[クエリ]:** Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer Interval 5minute<br>
**[時間枠]: 30 分**<br>
**[アラートの頻度]:** 5 分<br>
**[集計値]:** 90 よりも大きい<br>
**[アラートをトリガーする基準]:** 抵触の発生総数が 5 件より多い<br>

上のクエリは、5 分間隔で各コンピューターの平均値を計算するものです。  このクエリは過去 30 分間に収集されたデータを対象に、5 分ごとに実行されます。  コンピューターが 3 台の場合、サンプル データは以下のようになります。

![サンプル クエリの結果](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

この例では、指定された期間内に srv02 と srv03 の 2 台がしきい値 (90 %) に合計 3 回抵触しています。このため、この 2 台について個別にアラートが生成されます。  **[アラートをトリガーする基準]** を **[Consecutive (連続)]** に変更した場合には、3 回連続でしきい値に抵触した srv03 についてのみアラートが生成されます。

## <a name="alert-records"></a>アラート レコード
Log Analytics のアラート ルールで作成されるアラート レコードは、**[Type]** が **[Alert]**、**[SourceSystem]** が **[OMS]** に設定されています。  これらのレコードには、次の表に示したプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| 型 |*アラート:* |
| SourceSystem |*OMS* |
| *Object*  | [メトリック測定のアラート](#metric-measurement-alert-rules)には、グループ フィールドのプロパティがあります。  たとえば、ログ検索のグループ分けがコンピューターごとの場合には、アラートのレコードに Computer フィールドが存在します。また、そのフィールドの値はコンピューター名となります。
| AlertName |アラートの名前。 |
| AlertSeverity |アラートの重大度。 |
| LinkToSearchResults |アラートを作成したクエリに基づいてレコードを返す Log Analytics ログ検索へのリンク。 |
| クエリ |実行されたクエリのテキスト。 |
| QueryExecutionEndTime |クエリの時間範囲の終了時刻。 |
| QueryExecutionStartTime |クエリの時間範囲の開始時刻。 |
| ThresholdOperator | アラート ルールで使用された演算子。 |
| ThresholdValue | アラート ルールで使用された値。 |
| TimeGenerated |アラートが作成された日付と時刻。 |

[Alert Management ソリューション](log-analytics-solution-alert-management.md)および [Power BI エクスポート](log-analytics-powerbi.md)では、他の種類のアラート レコードも作成されます。  これらはすべて、**[Type]** が **[Alert]** ですが、それぞれ **[SourceSystem]** によって区別されます。


## <a name="next-steps"></a>次のステップ
* [Alert Management ソリューション](log-analytics-solution-alert-management.md) をインストールして、Log Analytics で作成された警告および System Center Operations Manager から収集された警告を分析します。
* アラートを生成する [ログ検索](log-analytics-log-searches.md) の詳細を確認します。
* アラート ルールに関する [Webhook を構成する](log-analytics-alerts-webhooks.md) チュートリアルを完了します。  
* アラートで識別された問題を修復するために [Azure Automation の Runbook](https://azure.microsoft.com/documentation/services/automation) を作成する方法について学習します。

