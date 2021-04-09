---
title: Azure Monitor で Log Analytics ワークスペースの正常性を監視する
description: Operation テーブル内のデータを使用して、Log Analytics ワークスペースの正常性を監視する方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 9eda0acc15badfe7bb2e754d887786aa990d6e24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034967"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Azure Monitor で Log Analytics ワークスペースの正常性を監視する
Azure Monitor で Log Analytics ワークスペースのパフォーマンスと可用性を維持するには、発生する問題を事前に検出できるようにする必要があります。 この記事では、[Operation](/azure/azure-monitor/reference/tables/operation) テーブル内のデータを使用して、Log Analytics ワークスペースの正常性を監視する方法について説明します。 このテーブルはすべての Log Analytics ワークスペースに含まれているもので、その中にはお使いのワークスペースで発生したエラーと警告が入っています。 このデータを定期的に確認し、ワークスペース内に重要なインシデントがあるときは、アラートを作成して事前に通知する必要があります。

## <a name="_logoperation-function"></a>_LogOperation 関数

Azure Monitor ログでは、問題が発生したワークスペース内の [Operation](/azure/azure-monitor/reference/tables/operation) テーブルに問題の詳細を送信します。 **_LogOperation** システム関数は **Operation** テーブルに基づいており、分析とアラート作成のための簡略化された情報セットを提供します。

## <a name="columns"></a>[列]

**_LogOperation** 関数では、次の表に示した列が返されます。

| 列 | 説明 |
|:---|:---|
| TimeGenerated | インシデントが発生した時刻 (UTC)。 |
| カテゴリ  | 操作カテゴリ グループ。 これを使用すると、操作の種類をフィルター処理し、より正確なシステム監査およびアラートを作成できます。 カテゴリの一覧については、以下のセクションをご覧ください。 |
| 操作  | 操作の種類の説明。 Log Analytics の制限、操作の種類、またはプロセスの一部のいずれかを示すことができます。 |
| Level | 問題の重大度レベル:<br>- 情報: 特別な注意は必要ありません。<br>- 警告: プロセスが想定どおりに完了しなかったため、注意が必要です。<br>- エラー: プロセスが失敗したため、早急の対応が必要です。 
| 詳細 | 操作の詳細な説明には特定のエラー メッセージが含まれます (存在する場合)。 |
| _ResourceId | 操作に関連する Azure リソースのリソース ID。  |
| Computer | コンピューター名 (操作が Azure Monitor エージェントに関連している場合)。 |
| CorrelationId | 連続する関連操作をグループ化するために使用されます。 |


## <a name="categories"></a>Categories

次の表では、_LogOperation 関数から得られるカテゴリについて説明します。 

| カテゴリ | 説明 |
|:---|:---|
| データの取り込み           | データ インジェスト プロセスに含まれている操作。 詳細については、以下を参照してください。 |
| エージェント               | エージェントのインストールに関する問題を示します。 |
| データ コレクション     | データ コレクション プロセスに関連する操作。 |
| ソリューションのターゲット設定  | *ConfigurationScope* 型の操作が処理されました。 |
| 評価ソリューション | 評価プロセスが実行されました。 |


### <a name="ingestion"></a>データの取り込み
インジェスト操作は、Azure Log Analytics ワークスペースの制限に達したことに関する通知など、データ インジェストの処理中に発生した問題です。 このカテゴリ内のエラー状態はデータ損失を示唆する可能性があるため、それらを監視することが特に重要です。 これらの操作の詳細については、以下の表をご覧ください。 Log Analytics ワークスペースに関するサービスの制限については、「[Azure Monitor サービスの制限](../service-limits.md#log-analytics-workspaces)」をご覧ください。


| 操作 | Level | 詳細 | 関連記事 |
|:---|:---|:---|:---|
| カスタム ログ | エラー   | カスタム フィールドの列数の上限に達しました。 | [Azure Monitor サービスの制限](../service-limits.md#log-analytics-workspaces) |
| カスタム ログ | エラー   | カスタム ログのインジェストに失敗しました。 | |
| Metadata。 | エラー | 構成エラーが検出されました。 | |
| データ コレクション | エラー   | 設定された日数より前に要求が作成されたため、データが削除されました。 | [Azure Monitor ログで使用量とコストを管理する](./manage-cost-storage.md#alert-when-daily-cap-reached)
| データ コレクション | ［情報］    | コレクション マシンの構成が検出されました。| |
| データ コレクション | ［情報］    | データ コレクションが新しい日で開始されました。 | [Azure Monitor ログで使用量とコストを管理する](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| データ コレクション | 警告 | 1 日の上限に達したため、データ コレクションが停止しました。| [Azure Monitor ログで使用量とコストを管理する](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| データ処理 | エラー   | JSON 形式が無効です。 | [HTTP データ コレクター API を使用して Azure Monitor にログ データを送信する (パブリック プレビュー)](../logs/data-collector-api.md#request-body) | 
| データ処理 | 警告 | 値が許容される最大サイズにトリミングされました。 | [Azure Monitor サービスの制限](../service-limits.md#log-analytics-workspaces) |
| データ処理 | 警告 | サイズの上限に達したため、フィールド値がトリミングされました。 | [Azure Monitor サービスの制限](../service-limits.md#log-analytics-workspaces) | 
| インジェスト率 | ［情報］ | インジェスト率が上限の 70% に近づいています。 | [Azure Monitor サービスの制限](../service-limits.md#log-analytics-workspaces) |
| インジェスト率 | 警告 | インジェスト率の上限に近づいています。 | [Azure Monitor サービスの制限](../service-limits.md#log-analytics-workspaces) |
| インジェスト率 | エラー   | インジェスト率の上限に達しました。 | [Azure Monitor サービスの制限](../service-limits.md#log-analytics-workspaces) |
| ストレージ | エラー   | 使用されている資格情報が無効なため、ストレージ アカウントにアクセスできません。  |



   

## <a name="alert-rules"></a>アラート ルール
Azure Monitor で[ログ クエリ アラート](../alerts/alerts-log-query.md)を使用すると、Log Analytics ワークスペースで問題が検出されたときに事前に通知されるようにできます。 コストを最小限に抑えながら、適切なタイミングで問題に対処できるようにする方法を使用する必要があります。 お使いのサブスクリプションは、評価される頻度に応じて、アラート ルールごとに料金が発生します。

推奨される方法は、問題のレベルに基づいて、2 つのアラート ルールから始めることです。 エラーには 5 分おきなどの短い頻度を使用し、警告には 24 時間などの長い頻度を使用します。 エラーはデータ損失の可能性を示しているため、損失を最小限に抑えるために迅速に対応する必要があります。 警告は通常、迅速な対応を必要としない問題を示しているため、毎日確認することができます。

ログ アラート ルールを作成するには、「[Azure Monitor を使用してログ アラートを作成、表示、管理する](../alerts/alerts-log.md)」に記載されているプロセスを使用します。 次のセクションでは、各ルールについて詳しく説明します。


| クエリ | しきい値 | 期間 | 頻度 |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

これらのアラート ルールでは、エラーまたは警告が発生したすべての操作に対して同じように対応します。 アラートを生成する操作に慣れてきたら、特定の操作に対して別の対応を取ることをお勧めします。 たとえば、特定の操作に対して別のユーザーに通知を送信することができます。 

特定の操作用のアラート ルールを作成するには、 **[カテゴリ]** と **[操作]** 列が含まれるクエリを使用します。 

次の例では、インジェストのボリューム率が上限の 80% に達したときに警告アラートが作成されます。

- ターゲット:Log Analytics ワークスペースを選択する
- 条件:
  - シグナル名: カスタム ログ検索
  - 検索クエリ: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - ベース: 結果の数
  - 条件: より大きい
  - しきい値: 0
  - 期間: 5 (分)
  - 頻度: 5 (分)
- アラート ルール名: 1 日のデータ制限に達しました
- 重大度: 警告 (重大度 1)


次の例では、データ コレクションが 1 日の上限に達したときに警告アラートが作成されます。 

- ターゲット:Log Analytics ワークスペースを選択する
- 条件:
  - シグナル名: カスタム ログ検索
  - 検索クエリ: `_LogOperation | where Category == "Ingestion" | where Operation == "Data Collection" | where Level == "Warning"`
  - ベース: 結果の数
  - 条件: より大きい
  - しきい値: 0
  - 期間: 5 (分)
  - 頻度: 5 (分)
- アラート ルール名: 1 日のデータ制限に達しました
- 重大度: 警告 (重大度 1)



## <a name="next-steps"></a>次の手順

- [ログ アラート](../alerts/alerts-log.md)の詳細を確認します。
- お使いのワークスペースに関する[クエリの監査データを収集](./query-audit.md)します。