---
title: Azure Monitor Logs の構造 | Microsoft Docs
description: Azure Monitor からログ データを取得するにはログ クエリが必要です。  この記事では、Azure Monitor で新しいログ クエリを使用する方法と、ログ クエリを作成する前に理解しておく必要がある概念について説明します。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2020
ms.openlocfilehash: 4e36a21e9dbab6f9bf814cdeb86f175ded38ea8e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327295"
---
# <a name="structure-of-azure-monitor-logs"></a>Azure Monitor Logs の構造
[ログ クエリ](log-query-overview.md)を使用して迅速にデータの分析情報が得られるのは、Azure Monitor の強力な機能です。 効率的で便利なクエリを作成するには、目的のデータがどこにあり、どのように構造化されているかなど、いくつかの基本概念を理解する必要があります。 この記事では、作業を始めるために必要な基本概念を提供します。

## <a name="overview"></a>概要
Azure Monitor Logs のデータは、Log Analytics ワークスペースまたは Application Insights アプリケーションのどちらかに格納されます。 どちらの場合でも、[Azure Data Explorer](/azure/data-explorer/) の強力なデータ エンジンとクエリ言語が利用できます。

> [!IMPORTANT]
> [ワークスペース ベースの Application Insights リソース](../app/create-workspace-resource.md)を使用している場合、テレメトリは、その他のすべてのログ データと共に Log Analytics ワークスペースに格納されます。 テーブルは名前が変更されて再構成されていますが、テーブル内の情報は Application Insights アプリケーションのテーブルと同じです。

ワークスペースとアプリケーション両方のデータがテーブルに整理され、それぞれのテーブルはさまざまな種類のデータを格納し、独自かつ一意のプロパティ セットを持ちます。 ほとんどの[データ ソース](../platform/data-sources.md)は Log Analytics ワークスペース内の独自テーブルに書き込みますが、Application Insights は、Application Insights アプリケーション内のあらかじめ定義されたテーブル セットに書き込みます。 ログ クエリは非常に柔軟で、複数のテーブルからのデータを簡単に結合できますし、リソース間クエリを使用すれば、複数のワークスペース内のテーブルからのデータを結合したり、ワークスペースのデータとアプリケーションのデータを結合するクエリを記述したりできます。

次の図は、サンプル クエリで使用されているさまざまなテーブルに書き込むデータ ソースの例を示しています。

![テーブル](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics ワークスペース
Application Insights を除いた Azure Monitor Logs によって収集されたすべてのデータは、[Log Analytics ワークスペース](../platform/manage-access.md)に格納されます。 特定の要件に応じて、1 つまたは複数のワークスペースを作成できます。 Azure リソースのアクティビティ ログやリソース ログ、仮想マシン上のエージェント、インサイトや監視ソリューションからのデータなどの[データ ソース](../platform/data-sources.md)は、そのオンボーディング過程で構成された 1 つ以上のワークスペースにデータを書き込みます。 [Azure Security Center](../../security-center/index.yml) や [Azure Sentinel](../../sentinel/index.yml) など、その他のサービスも Log Analytics ワークスペースを使用してデータを格納し、他のソースからの監視データと共にログ クエリを使って分析できるようにします。

さまざまな種類のデータがワークスペース内のさまざまなテーブルに格納され、各テーブルには一意のプロパティ セットがあります。 ワークスペースが作成されると、標準的なテーブル セットがワークスペースに追加され、各種データ ソース、ソリューション、およびサービスのオンボーディング過程でそれら用の新しいテーブルが追加されます。 [Data Collector API](../platform/data-collector-api.md) を使用してカスタム テーブルを作成することもできます。

ワークスペース内のテーブルとそれらのスキーマは、ワークスペースの Log Analytics の **[スキーマ]** タブで参照できます。

![ワークスペース スキーマ](media/scope/workspace-schema.png)

次のクエリを使用して、ワークスペース内のテーブルと、前日に各テーブルに収集されたレコード数を一覧表示します。 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
データ ソースが作成するテーブルの詳細については、各データ ソースのドキュメントを参照してください。 例としては、[エージェント データ ソース](../platform/agent-data-sources.md)、[リソース ログ](../platform/resource-logs-schema.md)、および[監視ソリューション](../monitor-reference.md)に関する記事があります。

### <a name="workspace-permissions"></a>ワークスペースのアクセス許可
ワークスペース内のデータへのアクセスを提供するためのアクセス制御戦略と推奨事項については、「[Azure Monitor ログのデプロイの設計](../platform/design-logs-deployment.md)」を参照してください。 ワークスペース自体へのアクセスを許可することに加えて、[テーブル レベルの RBAC](../platform/manage-access.md#table-level-rbac) を使用して個々のテーブルへのアクセスを制限できます。

## <a name="application-insights-application"></a>Application Insights アプリケーション

> [!IMPORTANT]
> [ワークスペース ベースの Application Insights リソース](../app/create-workspace-resource.md)を使用している場合、テレメトリは、その他のすべてのログ データと共に Log Analytics ワークスペースに格納されます。 テーブルは名前が変更されて再構成されていますが、テーブル内の情報は既存の Application Insights リソースのテーブルと同じです。

Application Insights でアプリケーションを作成すると、対応するアプリケーションが自動的に Azure Monitor Logs に作成されます。 データを収集するための構成は不要であり、アプリケーションは、ページ ビュー、要求、例外などの監視データを自動的に書き込みます。

Log Analytics ワークスペースと異なり、Application Insights アプリケーションには固定のテーブル セットがあります。 アプリケーションに書き込むように他のデータ ソースを構成することはできないため、追加のテーブルは作成できません。 

| テーブル | 説明 | 
|:---|:---|
| availabilityResults | 可用性テストの要約データ。 |
| browserTimings      | 受信データの処理にかかった時間などのクライアントのパフォーマンスに関するデータ。 |
| customEvents        | アプリケーションで作成されたカスタム イベント。 |
| customMetrics       | アプリケーションで作成されたカスタム メトリック。 |
| dependencies        | TrackDependency () を使用して記録された他のコンポーネント (外部コンポーネントを含む) へのアプリケーションからの呼び出し (REST API、データベース、またはファイル システムへの呼び出しなど)。 |
| exceptions          | アプリケーションのランタイムからスローされた例外。サーバー側とクライアント側 (ブラウザー) の両方の例外がキャプチャされます。|
| pageViews           | 各 Web サイトのビューに関するデータとブラウザー情報。 |
| performanceCounters | Windows のパフォーマンス カウンターなど、アプリケーションをサポートするコンピューティング リソースのパフォーマンス測定結果。 |
| requests            | お使いのアプリケーションで受信された要求。 たとえば、お使いの Web アプリが受信する HTTP 要求ごとに、個別の要求レコードがログ記録されます。  |
| traces              | TrackTrace () を使用して記録された、アプリケーション コード/ログ記録フレームワークによって出力された詳細なログ (トレース)。 |

各テーブルのスキーマは、アプリケーションの Log Analytics の **[スキーマ]** タブで確認できます。

![アプリケーション スキーマ](media/scope/application-schema.png)

## <a name="standard-properties"></a>標準のプロパティ
Azure Monitor Logs の各テーブルには独自のスキーマがありますが、全テーブルに共通の標準プロパティがあります。 それぞれの詳細については、[Azure Monitor Logs の標準プロパティ](../platform/log-standard-properties.md)に関する記事を参照してください。

| Log Analytics ワークスペース | Application Insights アプリケーション | 説明 |
|:---|:---|:---|
| TimeGenerated | timestamp  | レコードが作成された日付と時刻。 |
| Type          | itemType   | レコードの取得元テーブルの名前。 |
| _ResourceId   |            | レコードが関連付けられているリソースの一意識別子。 |
| _IsBillable   |            | 取り込まれたデータが課金対象かどうかを指定します。 |
| _BilledSize   |            | 課金対象となるデータのサイズをバイト単位で指定します。 |

## <a name="next-steps"></a>次のステップ
- [ログ検索を作成および編集するための Log Analytics](./log-query-overview.md) の使用について確認します。
- 新しいクエリ言語を使用した[クエリ記述のチュートリアル](./get-started-queries.md)を確認します。

