---
title: Azure Monitor ログ クエリでの監査クエリ
description: Azure Monitor で実行されるログ クエリに関するテレメトリが用意されているログ クエリの監査ログの詳細。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/25/2020
ms.openlocfilehash: cb38dcba2f61a432decb56164b816688ad3192d8
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88893641"
---
# <a name="audit-queries-in-azure-monitor-logs-preview"></a>Azure Monitor ログでの監査クエリ (プレビュー)
ログ クエリの監査ログには、Azure Monitor で実行されるログ クエリに関するテレメトリが用意されています。 これには、クエリがいつ実行されたか、誰が実行したか、どのツールが使用されたか、クエリ テキスト、クエリの実行を説明するパフォーマンス統計などの情報が含まれています。

## <a name="current-limitations"></a>現在の制限
パブリック プレビュー段階では、次の制限事項が適用されます。

- ワークスペース中心のクエリのみがログに記録されます。 クエリがリソース中心のモードで実行される場合、またはワークスペースベースとして構成されていない Application Insights に対して実行される場合は、ログに記録されません。


## <a name="configure-query-auditing"></a>クエリの監査を構成する
クエリの監査は、Log Analytics ワークスペースの[診断設定](../platform/diagnostic-settings.md)を使用して有効にします。 これにより、現在のワークスペースまたはサブスクリプション内のその他のワークスペース、Azure Event Hubs (Azure の外部に送信するため)、または Azure Storage (アーカイブのため) に監査データを送信することができます。 

### <a name="azure-portal"></a>Azure portal
Azure portal で、次のいずれかの場所にある Log Analytics ワークスペースの診断設定にアクセスします。

- **[Azure Monitor]** メニューから **[診断設定]** を選択し、ワークスペースを見つけて選択します。

    [![[Azure Monitor] の [診断設定]](media/log-query-audit/diagnostic-setting-monitor.png) ](media/log-query-audit/diagnostic-setting-monitor.png#lightbox) 

- **[Log Analytics ワークスペース]** メニューからワークスペースを選択し、 **[診断設定]** を選択します。

    [![[Log Analytics ワークスペース] の [診断設定]](media/log-query-audit/diagnostic-setting-workspace.png) ](media/log-query-audit/diagnostic-setting-workspace.png#lightbox) 

### <a name="resource-manager-template"></a>Resource Manager テンプレート
Resource Manager テンプレートの例は、「[Log Analytics ワークスペースの診断設定](../samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-log-analytics-workspace)」から入手できます。

## <a name="audit-data"></a>監査データ
監査レコードは、クエリが実行されるたびに作成されます。 Log Analytics ワークスペースにデータを送信すると、*LAQueryLogs* というテーブルに格納されます。 監査データの各レコードのプロパティについて次の表で説明します。

| フィールド | 説明 |
|:---|:---|
| TimeGenerated         | クエリが送信された UTC 時刻。 |
| CorrelationId         | クエリを識別する一意の ID。 トラブルシューティングのシナリオで、Microsoft に問い合わせるときに使用できます。 |
| AADObjectId           | クエリを開始したユーザー アカウントの Azure Active Directory ID。  |
| AADTenantId           | クエリを開始したユーザー アカウントのテナントの ID。  |
| AADEmail              | クエリを開始したユーザー アカウントのテナントのメール アドレス。  |
| AADClientId           | クエリを開始するために使用されたアプリケーションの ID と解決済みの名前。 |
| RequestClientApp      | クエリを開始するために使用されたアプリケーションの解決済みの名前。 |
| QueryTimeRangeStart   | クエリに対して選択された時間範囲の開始。 これは一部のシナリオでは設定されない場合があります。たとえば、クエリが Log Analytics から開始され、時間範囲が時間ピッカーではなくクエリ内で指定されている場合などです。 |
| QueryTimeRangeEnd     | クエリに対して選択された時間範囲の終了。 これは一部のシナリオでは設定されない場合があります。たとえば、クエリが Log Analytics から開始され、時間範囲が時間ピッカーではなくクエリ内で指定されている場合などです。  |
| QueryText             | 実行されたクエリのテキスト。 |
| RequestTarget         | クエリを送信するために API URL が使用されました。  |
| RequestContext        | クエリが実行を要求された対象のリソースの一覧。 最大 3 つの文字列配列 (ワークスペース、アプリケーション、リソース) が含まれます。 サブスクリプションまたはリソース グループを対象とするクエリは、*resources* と示されます。 RequestTarget によって暗黙的に示されたターゲットが含まれます。 |
| RequestContextFilters | クエリの呼び出しの一部として指定されたフィルターのセット。 次の文字列配列が最大 3 つ含まれます。<br>- ResourceTypes - クエリのスコープを制限するリソースの種類<br>- Workspaces - クエリを制限するワークスペースの一覧<br>- WorkspaceRegions - クエリを制限するワークスペース リージョンの一覧 |
| ResponseCode          | クエリの送信時に返された HTTP 応答コード。 |
| ResponseDurationMs    | 応答が返された時刻。  |
| StatsCPUTimeMs       | 計算、解析、データの取得に使用された合計コンピューティング時間。 クエリが状態コード 200 で返された場合にのみ設定されます。 |
| StatsDataProcessedKB | クエリを処理するためにアクセスされたデータの量。 対象のテーブルのサイズ、使用された期間、適用されたフィルター、参照されている列の数の影響を受けます。 クエリが状態コード 200 で返された場合にのみ設定されます。 |
| StatsDataProcessedStart | クエリを処理するためにアクセスされた最も古いデータの時刻。 クエリの明示的な期間と適用されるフィルターの影響を受けます。 これは、データのパーティション分割のために、明示的な期間よりも長くなる場合があります。 クエリが状態コード 200 で返された場合にのみ設定されます。 |
| StatsDataProcessedEnd  |クエリを処理するためにアクセスされた最も新しいデータの時刻。 クエリの明示的な期間と適用されるフィルターの影響を受けます。 これは、データのパーティション分割のために、明示的な期間よりも長くなる場合があります。 クエリが状態コード 200 で返された場合にのみ設定されます。 |
| StatsWorkspaceCount | クエリによってアクセスされたワークスペースの数。 クエリが状態コード 200 で返された場合にのみ設定されます。 |
| StatsRegionCount | クエリによってアクセスされたリージョンの数。 クエリが状態コード 200 で返された場合にのみ設定されます。 |



## <a name="next-steps"></a>次のステップ

- [診断設定](../platform/diagnostic-settings.md)の詳細を確認します。
- [ログ クエリの最適化](query-optimization.md)の詳細を確認します。