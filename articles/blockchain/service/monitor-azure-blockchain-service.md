---
title: Azure Blockchain Service (ABS) の監視
description: Azure Monitor を使用した Azure Blockchain Service の監視
ms.date: 01/08/2020
ms.topic: how-to
ms.reviewer: v-umha
ms.openlocfilehash: 7300a5dcfb0150e6182636dcb71bacfa68c787db
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076924"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Azure Monitor を使用して Azure Blockchain Service を監視する  

お客様が Azure Blockchain Service (ABS) で運用レベルのブロックチェーン シナリオを実行する場合、可用性、パフォーマンス、および操作に関するリソースを監視することが重要になります。 この記事では、Azure Blockchain Service によって生成される監視データと、Azure Monitor のさまざまな機能と統合を使用し、分析してアラートを作成して、運用レベルの環境を管理する方法について説明します。  

## <a name="what-is-azure-monitor"></a>Azure Monitor とは

Azure Blockchain Service では、監視データの作成に Azure Monitor が使用されます。これは、Azure リソースを監視するための機能が一式そろっている Azure のフル スタック監視サービスです。 Azure Monitor の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../../azure-monitor/insights/monitor-azure-resource.md)」を参照してください。
 

以下のセクションでは、この記事を基に、Azure Blockchain Service から収集される特定のデータについて説明します。また、Azure のツールを使用してデータの収集を構成し、このデータを分析するための例を示します。

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Azure Blockchain Service から収集されたデータを監視する  

Azure Blockchain Service は、他の Azure リソースと同じ種類の監視データを収集します。これについては、[Azure リソースの監視データ](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)に関する記事を参照してください。 Azure Blockchain Service によって作成されるログとメトリックの詳細については、「[Azure Blockchain Service データのリファレンスを監視する](#monitor-azure-blockchain-service-data-reference)」を参照してください。

各 Azure Blockchain Service メンバー リソースの Azure portal の概要ページには、ハンドルされている要求と処理されたブロックを含むトランザクションの簡単なビューが含まれています。 このデータの一部は、Azure Blockchain Service メンバー リソースを作成すると自動的に収集され、分析に使用できますが、追加の構成で追加のデータ収集を有効にすることができます。

## <a name="diagnostic-settings"></a>診断設定  

プラットフォーム メトリックとアクティビティ ログは自動的に収集されますが、リソース ログを収集したり、それらを Azure Monitor の外部に転送したりするには、診断設定を作成する必要があります。 Azure portal、CLI、または PowerShell を使用して診断設定を作成するプロセスの詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../../azure-monitor/platform/diagnostic-settings.md)」を参照してください。

診断設定を作成するときは、収集するログのカテゴリを指定します。 Azure Blockchain Service のカテゴリを以下に示します。

**[Blockchain proxy logs]\(ブロックチェーン プロキシ ログ\)** : NGNIX プロキシ ログを監視する場合は、このカテゴリを選択します。 すべての顧客トランザクションの詳細は、監査とデバッグの目的で使用できます。  

**[Blockchain application logs]\(ブロックチェーン アプリケーション ログ\)** : マネージド サービスによってホストされるブロックチェーン アプリケーションのログを取得するには、このカテゴリを選択します。 たとえば、ABS-Quorum メンバーの場合、これらのログは Quorum 自体からのログです。  

**[Metric Requests]\(メトリック要求\)** :Azure Cosmos DB から診断設定の送信先にメトリック データを収集するオプションを選択します。これは、Azure Metrics で自動的に収集されます。 リソース ログを使用してメトリック データを収集し、両方の種類のデータをまとめて分析し、Azure Monitor の外部にメトリック データを送信します。

## <a name="analyze-metric-data"></a>メトリック データを分析する  

メトリックス エクスプローラーを使用して Azure Blockchain Service のメトリックを分析し、ABS リソース ブレードの [監視] セクションの [メトリック] タブに移動できます。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../../azure-monitor/platform/metrics-getting-started.md)」を参照してください。 Azure Blockchain Service の完全なメトリックは、Azure Blockchain Service の標準メトリックの名前空間に含まれています。

フィルターを追加するとき、またはメトリックを分割するときに、**ノード**のディメンションを使用できます。これにより、基本的に、ABS メンバーのトランザクション ノードおよび検証ノードごとにメトリック値が提供されます。

## <a name="analyze-log-data"></a>ログ データの分析

ログ検索バーに入力して Azure Blockchain Service メンバーを監視するときに役立つクエリをいくつか紹介します。 これらのクエリは[新しい言語](../../azure-monitor/log-query/log-query-overview.md)で使用できます。

Blockchain アプリケーション ログのエラー状態のクエリを実行するには、次のクエリを使用します。

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Blockchain プロキシ ログのエラー状態のクエリを実行するには、次のクエリを使用します  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
Azure ログで使用できる時間フィルターを使用して、特定の時間範囲のクエリをフィルター処理できます。

## <a name="monitor-azure-blockchain-service-data-reference"></a>Azure Blockchain Service データのリファレンスを監視する  

この記事では、Azure Blockchain Service のパフォーマンスと可用性を分析する目的で収集されるログとメトリック データのリファレンスを提供します。  

### <a name="resource-logs"></a>リソース ログ

すべてのリソース ログは、ブロックチェーン サービスに固有の一意のプロパティがほとんどない最上位の共通スキーマを共有します。 記事「[リソース ログの上位スキーマ](../../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema)」を参照してください。Azure Blockchain Service 固有のプロパティの詳細については、以下で説明します  

次の表に、Azure Monitor ログまたは Azure Storage で Azure Blockchain プロキシ ログが収集される場合のそれらのプロパティを示します。  


| プロパティ名  | 説明 |
|:---|:---|
| time | 操作が発生した日時 (UTC)。 |
| resourceID  | ログが有効になっている Azure Blockchain Service リソース。  |
| category  |Azure Blockchain Service の場合、使用できる値は **Proxylogs** と **Applicationlogs** です。 |
| operationName  | このイベントで表される操作の名前。   |
| ログ レベル  | 既定では、Azure Blockchain Service により、 **[情報提供]** ログ レベルが有効になります。   |
| NodeLocation  | ブロックチェーン メンバーがデプロイされる Azure リージョン。  |
| BlockchainNodeName  | 操作が実行される Azure Blockchain Service メンバーのノードの名前。   |
| EthMethod  | 基盤となるブロックチェーン プロトコルによって呼び出されるこのメソッドは、Quorum では、eth_sendTransactions、eth_getBlockByNumber などになります。  |
| エージェント  | Web ブラウザー Mozilla、Edge など、ユーザーに代わって機能するユーザー エージェント。値の例を次に示します。"Mozilla/5.0 (Linux x64) node.js/8.16.0 v8/6.2.414.77"  |
| コード   | HTTP エラー コード。 通常、4XX と 5XX はエラー状態です。  |
| NodeHost  | ノードの DNS 名。   |
| RequestMethodName | 呼び出された HTTP メソッド。考えられる値は、メンバーの作成の PUT、既存のメンバーの詳細の取得の GET、メンバーの削除の DELETE、メンバーの更新の PATCH です。   |
| BlockchainMemberName  | ユーザーによって指定された Azure Blockchain Service メンバー名。  |
| コンソーシアム | ユーザーによって指定されたコンソーシアム名。   |
| Remote  | 要求の送信元クライアントの IP。  |
| RequestSize  | 要求のサイズ (バイト単位)。  |
| RequestTime  | 要求の期間 (ミリ秒単位)。|




次の表に、Azure Blockchain アプリケーション ログのプロパティを示します。


| プロパティ名  | 説明 |
|:---|:---|
| time | 操作が発生した日時 (UTC)。 |
| resourceID  | ログが有効になっている Azure Blockchain Service リソース。|
| category  |Azure Blockchain Service の場合、使用できる値は **Proxylogs** と **Applicationlogs** です。  |
| operationName  | このイベントで表される操作の名前。   |
| ログ レベル  | 既定では、Azure Blockchain Service により、 **[情報提供]** ログ レベルが有効になります。   |
| NodeLocation  | ブロックチェーン メンバーがデプロイされる Azure リージョン。  |
| BlockchainNodeName  | 操作が実行される Azure Blockchain Service メンバーのノードの名前。   |
| BlockchainMessage    | このフィールドには、データ プレーン ログであるブロックチェーン アプリケーション ログが含まれます。 ABS-Quorum の場合、これには Quorum ログが含まれます。 情報提供、エラー、警告、および実行されたアクションに関する詳細情報を提供する文字列であるログ エントリの種類に関する情報があります。   |
| TenantId    | Azure Blockchain Service のリージョン固有のテナント。 このフィールドの形式は https://westlake-rp-prod.<region>.cloudapp.azure.com です。cloudapp.azure.com です。このリージョンにはデプロイされたメンバーの Azure リージョンを指定します。       |
| SourceSystem   | システムによってログが作成されます。この場合は **Azure** です。    |



### <a name="metrics"></a>メトリック

Azure Blockchain Service について収集されたプラットフォーム メトリックを次の表に示します。 すべてのメトリックは、名前空間 **Azure Blockchain Service** 標準メトリックに格納されます。

(Azure Blockchain Service など) Azure Monitor がサポートするすべてのメトリックの一覧については、「[Azure Monitor のサポートされるメトリック](../../azure-monitor/platform/metrics-supported.md)」を参照してください。

### <a name="blockchain-metrics"></a>Blockchain のメトリック

Azure Blockchain Service メンバー リソースについて収集される Blockchain メトリックの一覧を次の表に示します。


| メトリックの名前 | ユニット  |  集計の種類| 説明   |
|---|---|---|---|
| 保留中のトランザクション   | Count  |  Average | 抽出を待機しているトランザクションの数。   |
| 処理されたブロック   | Count  | SUM  |  各時間間隔で処理されたブロックの数。 現在、ブロック サイズは 5 秒です。そのため、各ノードでは 1 分で 12 ブロック、5 分で 60 ブロックが処理されます。   |
|処理されたトランザクション    | Count  | SUM  | 1 ブロックで処理されたトランザクションの数。    |
|キューに登録されたトランザクション    |  Count | Average  | すぐに抽出できないトランザクションの数。 この原因として、順不同で到着し、将来のトランザクションが以前のトランザクションの到着を待機する場合があります。 また、1 回だけ使用される同じ番号 (nonce) と同じガス値を持つ 2 つのトランザクションがあり、そのため 2 つ目のトランザクションを抽出できない場合があります。   |

### <a name="connection-metrics"></a>接続のメトリック  

Azure Blockchain Service メンバー リソース用に収集されるさまざまな接続メトリックを次の表に示します。 これらは NGINX プロキシ メトリックです。


| メトリックの名前 | ユニット  |  集計の種類| 説明 |
|---|---|---|---|
| 承認された接続   | Count  |  SUM | 承認されたクライアント接続の合計数。   |
| アクティブな接続  | Count  | Average  |  待機中の接続を含むアクティブなクライアント接続の現在の数。    |
|処理された接続    | Count  | SUM  | 処理された接続の合計数。 通常、リソースの制限に達していない限り、このパラメーター値は承認された接続数と同じです。     |
|処理された要求     |  Count | SUM  | クライアント要求の合計数。  |


### <a name="performance-metrics"></a>パフォーマンスのメトリック

Azure Blockchain メンバー リソースの各ノードについて収集されるパフォーマンス メトリックを次の表に示します。  


| メトリックの名前 | ユニット  |  集計の種類| 説明   |
|---|---|---|---|
| CPU 使用率 (%)   | パーセント  |  Max | CPU 使用率の割合。     |
| IO 読み取りバイト数   | キロバイト   | SUM  |  ブロックチェーン メンバー リソースのすべてのノード全体の IO 読み取りバイト数。      |
|IO 書き込みバイト数     | キロバイト   | SUM  | ブロックチェーン メンバー リソースのノード全体の IO 書き込みバイト数。     |
|メモリの制限       |  GB (ギガバイト)   | Average    | ブロックチェーン プロセスで使用可能なノードあたりの最大メモリ。 |
|メモリ使用量     | GB (ギガバイト)  |  Average | すべてのノード全体で使用されている平均のメモリ量。  |
| メモリ使用率 (%)     | パーセント   | Average  |  すべてのノード全体で使用されている平均のメモリの割合。       |
|ストレージの使用状況      | GB (ギガバイト)   | Average  | すべてのノード全体で使用されている平均のストレージ サイズ (GB)。       |


## <a name="next-steps"></a>次の手順

ブロックチェーン データをキャプチャして Azure Event Grid に変換する [Blockchain Data Manager](./data-manager.md) の詳細を確認します。
