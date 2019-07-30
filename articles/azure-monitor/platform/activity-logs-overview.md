---
title: Azure アクティビティ ログの概要
description: Azure アクティビティ ログの概要と、Azure アクティビティ ログを使用して Azure サブスクリプション内で発生するイベントを理解する方法について説明します。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 6fc00bf0dfb83f349da91989a579f31be2027ff0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071678"
---
# <a name="overview-of-azure-activity-log"></a>Azure アクティビティ ログの概要

**Azure アクティビティ ログ**により、Azure で発生したサブスクリプションレベルのイベントの分析が得られます。 たとえば、Azure Resource Manager の運用データから、サービスの正常性イベントまでの範囲のデータが含まれています。 管理者のカテゴリではサブスクリプションのコントロール プレーン イベントが報告されるため、アクティビティ ログは以前は "_監査ログ_" または "_操作ログ_" と呼ばれていました。 

アクティビティ ログを使用して、サブスクリプションのリソースに対して行われるすべての書き込み操作 (PUT、POST、DELETE) について、"_何を_"、"_誰が_"、"_いつ_" 行ったのかを確認できます。 さらに、操作の状態など、重要性の大きなプロパティを確認することもできます。 

アクティビティ ログには、読み取り (GET) 操作や、クラシック/RDFE モデルを使用しているリソースに対する操作は含まれません。

## <a name="comparison-to-diagnostic-logs"></a>診断ログと比較する
各 Azure サブスクリプションに 1 つのアクティビティ ログがあります。 これは、外部から行われるリソースの操作に関するデータを提供します ("コントロール プレーン")。 [診断ログ](diagnostic-logs-overview.md)は、リソースによって出力され、そのリソースの操作に関する情報を提供します ("データ プレーン")。 各リソースの診断設定を有効にする必要があります。

![アクティビティ ログと診断ログの比較](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Azure アクティビティ ログは、主に Azure Resource Manager で発生したアクティビティを記録します。 クラシック/RDFE モデルを使用しているリソースは追跡されません。 一部のクラシック リソース タイプでは、Azure Resource Manager にプロキシ リソース プロバイダー (例: Microsoft.ClassicCompute) が存在します。 これらのプロキシ リソース プロバイダーを使用して Azure Resource Manager 経由でクラシック リソース タイプを操作すると、その操作がアクティビティ ログに表示されます。 Azure Resource Manager プロキシの外部でクラシック リソース タイプを操作すると、そのアクションは操作ログにのみ記録されます。 操作ログは、ポータルの別のセクションで参照できます。

## <a name="activity-log-retention"></a>アクティビティ ログのリテンション期間
アクティビティ ログのエントリは一度作成されると、システムによって変更も削除も行われません。 また、インターフェイス内またはプログラムでそれらを変更することもできません。 アクティビティ ログ イベントは 90 日間保存されます。 このデータを長期間保存するには、[Azure Monitor](activity-log-collect.md) で収集するか、[ストレージまたは Event Hubs](activity-log-export.md) にエクスポートします。

## <a name="view-the-activity-log"></a>アクティビティ ログを表示する
すべてのリソースのアクティビティ ログは、Azure portal の **[モニター]** メニューから表示します。 特定のリソースのアクティビティ ログは、そのリソースのメニューの **[アクティビティ ログ]** オプションから表示します。 PowerShell、CLI、または REST API を使用してアクティビティ ログ レコードを取得することもできます。  「[View and retrieve Azure Activity log events (Azure アクティビティ ログ イベントを表示して取得する)](activity-log-view.md)」を参照してください。

![アクティビティ ログを表示する](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Azure Monitor でアクティビティ ログを収集する
他の監視データと共に分析し、90 日間よりも長くデータを保存するには、Azure Monitor で Log Analytics ワークスペースにアクティビティ ログを収集します。 「[Collect and analyze Azure activity logs in Log Analytics workspace in Azure Monitor (Azure Monitor の Log Analytics ワークスペースで Azure アクティビティ ログを収集して分析する)](activity-log-collect.md)」を参照してください。

![アクティビティ ログのクエリを実行する](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>アクティビティ ログをエクスポートする
アーカイブする場合は、アクティビティ ログを Azure Storage にエクスポートし、サードパーティーのサービスやカスタム分析ソリューションに取り込む場合は、イベント ハブにストリーム配信します。 「[Export the Azure Activity Log (Azure アクティビティ ログをエクスポートする)](activity-log-export.md)」を参照してください。 [**Power BI コンテンツ パック**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)を使用して、Power BI でアクティビティ ログ イベントを分析することもできます。

## <a name="alert-on-activity-log"></a>アクティビティ ログに関するアラート
特定のイベントがアクティビティ ログに作成されたときに[アクティビティ ログ アラート](activity-log-alerts.md)を使用してアラートを作成できます。 アクティビティ ログが Log Analytics ワークスペースに接続されているときに[ログ クエリ](alerts-log-query.md)を使用してアラートを作成することもできますが、クエリ アラートをログに記録するためのコストがかかります。 アクティビティ ログ アラートにコストはかかりません。

## <a name="categories-in-the-activity-log"></a>アクティビティ ログのカテゴリ
アクティビティ ログの各イベントには、次の表に示す特定のカテゴリがあります。 これらのカテゴリのスキーマの詳細については、「[Azure アクティビティ ログのイベント スキーマ](activity-log-schema.md)」を参照してください。 

| Category | 説明 |
|:---|:---|
| 管理 | Resource Manager で実行されるすべての作成、更新、削除、アクション操作のレコードが含まれます。 管理イベントの例としては、_仮想マシンの作成_、_ネットワーク セキュリティ グループの削除_があります。<br><br>Resource Manager を使用してユーザーまたはアプリケーションが実行するすべてのアクションは、特定のリソースの種類に対する操作としてモデル化されています。 操作の種類が_書き込み_、_削除_、または_アクション_の場合、その操作の開始のレコードと成功または失敗のレコードは、いずれも管理カテゴリに記録されます。 管理イベントには、サブスクリプション内のロールベースのアクセス制御に対する任意の変更も含まれています。 |
| サービス正常性 | Azure で発生した任意のサービス正常性インシデントのレコードが含まれます。 サービス正常性イベントの例としては、"_SQL Azure in East US is experiencing downtime_" (米国東部の SQL Azure でダウンタイムが発生しています) があります。 <br><br>サービス正常性イベントは 5 種類に分かれます。"_要対応_"、"_支援復旧_"、"_インシデント_"、"_メンテナンス_"、"_情報_"、または "_セキュリティ_"。 これらのイベントが作成されるのは、サブスクリプション内にイベントの影響を受けるリソースがある場合のみです。
| リソース正常性 | Azure リソースで発生したすべてのリソース正常性イベントのレコードが含まれます。 リソース正常性イベントの例としては、"_Virtual Machine health status changed to unavailable_" (仮想マシンの正常性状態が使用不可に変更されました) があります。<br><br>リソース正常性イベントは、次の 4 つの正常性状態のいずれかを示す可能性があります。"_使用可能_"、"_使用不可_"、"_デグレード_"、および "_不明_"。 さらに、リソース正常性イベントは、"_Platform Initiated_" (プラットフォーム開始) または "_User Initiated_" (ユーザー開始) のいずれかのカテゴリに分けることができます。 |
| アラート: | Azure アラートのアクティブ化のレコードが含まれます。 アラート イベントの例としては、"_CPU % on myVM has been over 80 for the past 5 minutes_" (myVM の CPU % が過去 5 分間で 80 を超えています) があります。|
| 自動スケール | サブスクリプションで定義したすべての自動スケーリング設定に基づいて、自動スケーリング エンジンの操作に関連するすべてのイベントのレコードが含まれます。 自動スケーリングの例としては、"_Autoscale scale up action failed_" (自動スケーリングのスケールアップ アクションが失敗しました) があります。 |
| 推奨 | Azure Advisor からの推奨イベントが含まれます。 |
| セキュリティ | Azure Security Center によって生成されたアラートのレコードが含まれます。 セキュリティ イベントの例としては、"_Suspicious double extension file executed_" (疑わしい二重拡張子ファイルが実行されました) があります。 |
| ポリシー | Azure Policy によって実行されるすべての効果アクション操作のレコードが含まれます。 ポリシー イベントの例としては、"_監査_" と "_拒否_" があります。 Policy によって実行されるすべてのアクションは、リソースに対する操作としてモデル化されます。 |


## <a name="next-steps"></a>次の手順

* [Azure アクティビティ ログをエクスポートするログ プロファイルを作成する](activity-log-export.md)
* [Azure アクティビティ ログを Event Hubs にストリーミングする](activity-logs-stream-event-hubs.md)
* [Azure アクティビティ ログをストレージにアーカイブする](archive-activity-log.md)

