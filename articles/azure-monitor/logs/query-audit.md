---
title: Azure Monitor ログ クエリでの監査クエリ
description: Azure Monitor で実行されるログ クエリに関するテレメトリが用意されているログ クエリの監査ログの詳細。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/03/2020
ms.openlocfilehash: 28dfac7de8e73adf577b0a13e5fbd8740b1e3b06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047419"
---
# <a name="audit-queries-in-azure-monitor-logs-preview"></a>Azure Monitor ログでの監査クエリ (プレビュー)
ログ クエリの監査ログには、Azure Monitor で実行されるログ クエリに関するテレメトリが用意されています。 これには、クエリがいつ実行されたか、誰が実行したか、どのツールが使用されたか、クエリ テキスト、クエリの実行を説明するパフォーマンス統計などの情報が含まれています。


## <a name="configure-query-auditing"></a>クエリの監査を構成する
クエリの監査は、Log Analytics ワークスペースの[診断設定](../essentials/diagnostic-settings.md)を使用して有効にします。 これにより、現在のワークスペースまたはサブスクリプション内のその他のワークスペース、Azure Event Hubs (Azure の外部に送信するため)、または Azure Storage (アーカイブのため) に監査データを送信することができます。 

### <a name="azure-portal"></a>Azure portal
Azure portal で、次のいずれかの場所にある Log Analytics ワークスペースの診断設定にアクセスします。

- **[Azure Monitor]** メニューから **[診断設定]** を選択し、ワークスペースを見つけて選択します。

    [![[Azure Monitor] の [診断設定]](media/query-audit/diagnostic-setting-monitor.png) ](media/query-audit/diagnostic-setting-monitor.png#lightbox) 

- **[Log Analytics ワークスペース]** メニューからワークスペースを選択し、 **[診断設定]** を選択します。

    [![[Log Analytics ワークスペース] の [診断設定]](media/query-audit/diagnostic-setting-workspace.png) ](media/query-audit/diagnostic-setting-workspace.png#lightbox) 

### <a name="resource-manager-template"></a>Resource Manager テンプレート
Resource Manager テンプレートの例は、「[Log Analytics ワークスペースの診断設定](../essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-log-analytics-workspace)」から入手できます。

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
| RequestContext        | クエリが実行を要求された対象のリソースの一覧。 最大 3 つの文字列配列 (ワークスペース、アプリケーション、リソース) が含まれます。 サブスクリプションまたはリソース グループを対象とするクエリは、*resources* と示されます。 RequestTarget によって暗黙的に示されたターゲットが含まれます。<br>解決できる場合は、各リソースのリソース ID が含まれます。 リソースへのアクセス時にエラーが返された場合は、解決できない可能性があります。 この場合は、クエリの特定のテキストが使用されます。<br>クエリであいまいな名前 (複数のサブスクリプションに存在するワークスペース名など) が使用されている場合は、このあいまいな名前が使用されます。 |
| RequestContextFilters | クエリの呼び出しの一部として指定されたフィルターのセット。 次の文字列配列が最大 3 つ含まれます。<br>- ResourceTypes - クエリのスコープを制限するリソースの種類<br>- Workspaces - クエリを制限するワークスペースの一覧<br>- WorkspaceRegions - クエリを制限するワークスペース リージョンの一覧 |
| ResponseCode          | クエリの送信時に返された HTTP 応答コード。 |
| ResponseDurationMs    | 応答が返された時刻。  |
| ResponseRowCount     | クエリによって返される行の合計数。 |
| StatsCPUTimeMs       | 計算、解析、データの取得に使用された合計コンピューティング時間。 クエリが状態コード 200 で返された場合にのみ設定されます。 |
| StatsDataProcessedKB | クエリを処理するためにアクセスされたデータの量。 対象のテーブルのサイズ、使用された期間、適用されたフィルター、参照されている列の数の影響を受けます。 クエリが状態コード 200 で返された場合にのみ設定されます。 |
| StatsDataProcessedStart | クエリを処理するためにアクセスされた最も古いデータの時刻。 クエリの明示的な期間と適用されるフィルターの影響を受けます。 これは、データのパーティション分割のために、明示的な期間よりも長くなる場合があります。 クエリが状態コード 200 で返された場合にのみ設定されます。 |
| StatsDataProcessedEnd  |クエリを処理するためにアクセスされた最も新しいデータの時刻。 クエリの明示的な期間と適用されるフィルターの影響を受けます。 これは、データのパーティション分割のために、明示的な期間よりも長くなる場合があります。 クエリが状態コード 200 で返された場合にのみ設定されます。 |
| StatsWorkspaceCount | クエリによってアクセスされたワークスペースの数。 クエリが状態コード 200 で返された場合にのみ設定されます。 |
| StatsRegionCount | クエリによってアクセスされたリージョンの数。 クエリが状態コード 200 で返された場合にのみ設定されます。 |

## <a name="considerations"></a>考慮事項

- クエリは、ユーザー コンテキストで実行された場合にのみログに記録されます。 Azure 内のサービス間のものは、ログに記録されません。 この例外に含まれる 2 つの主なクエリのセットとしては、課金計算と自動アラート実行があります。 アラートの場合、記録されないのはスケジュールされたアラート クエリそのものだけであり、アラート作成画面でのアラートの初回実行はユーザー コンテキストで実行されるため、監査の目的に使用できます。 
- Azure Data Explorer プロキシからのクエリでパフォーマンス統計を使用することはできません。 これらのクエリのその他すべてのデータは、引き続き設定されます。
- [文字列リテラルを難読化する](/azure/data-explorer/kusto/query/scalar-data-types/string#obfuscated-string-literals)文字列に対する *h* ヒントは、クエリ監査ログに影響しません。 クエリは、文字列が難読化されることなく、送信されたとおりにキャプチャされます。 このデータを表示するためのコンプライアンス権限を持つユーザーのみが、Log Analytics ワークスペースで利用可能なさまざまな Kubernetes RBAC または Azure RBAC モードを使用してそれを実行できるようにする必要があります。
- 複数のワークスペースのデータを含むクエリの場合、クエリは、ユーザーがアクセスできるワークスペースでのみキャプチャされます。

## <a name="costs"></a>コスト  
Azure Diagnostic 拡張機能には料金はかかりませんが、取り込まれたデータの料金が発生する場合があります。 データの収集先については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [診断設定](../essentials/diagnostic-settings.md)の詳細を確認します。
- [ログ クエリの最適化](query-optimization.md)の詳細を確認します。
