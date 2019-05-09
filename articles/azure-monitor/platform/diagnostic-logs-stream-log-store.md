---
title: Azure Monitor の Log Analytics ワークスペースに Azure 診断ログをストリーミングする
description: Azure Monitor の Log Analytics ワークスペースに Azure 診断ログをストリーミングする方法を説明します。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 33d8f2e7c65a786d1ecb389574fe186efb6fb705
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630785"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics-workspace-in-azure-monitor"></a>Azure Monitor の Log Analytics ワークスペースに Azure 診断ログをストリーミングする

ポータル、PowerShell コマンドレット、または Azure CLI を使用して、Azure Monitor の Log Analytics ワークスペースに、**[Azure 診断ログ](diagnostic-logs-overview.md)** をほぼリアルタイムでストリーミングすることができます。

## <a name="what-you-can-do-with-diagnostics-logs-in-a-log-analytics-workspace"></a>Log Analytics ワークスペースの診断ログを使用してできること

Azure Monitor では、柔軟なログ クエリと分析のツールが提供されており、Azure リソースから生成された生のログ データから分析情報を得ることができます。 いくつかの機能の例を次に示します。

* **ログ クエリ** - ログ データに対する高度なクエリを記述して、各種ソースからのログを相互に関連付けます。また、Azure ダッシュボードにピン留め可能なグラフを生成することもできます。
* **アラート** - 特定のクエリにイベントが一致したことを検出し、Azure Monitor アラートを使用してメールまたは Webhook 呼び出しで通知します。
* **高度な分析** - 機械学習とパターン マッチング アルゴリズムを適用して、ログから潜在的な問題を特定します。

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics-workspace"></a>Log Analytics ワークスペースへの診断ログのストリーミングを有効にする

診断ログのストリーミングは、プログラム、ポータル、または [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) を使用して有効にすることができます。 どの方法でも、Log Analytics ワークスペースと、そこに送信するログのカテゴリおよびメトリックを指定する診断設定を作成します。 診断**ログ カテゴリ**とは、リソースから得られるログの種類です。

設定を構成するユーザーが両方のサブスクリプションに対して適切な RBAC アクセスを持っている限り、Log Analytics ワークスペースはログを出力するリソースと同じサブスクリプションに属している必要はありません。

> [!NOTE]
> 診断設定を使用した多ディメンション メトリックの送信は現在サポートされていません。 ディメンションを含むメトリックは、ディメンション値間で集計され、フラット化された単一ディメンションのメトリックとしてエクスポートされます。
>
> *例*: イベント ハブの "受信メッセージ" メトリックは、キュー単位のレベルで調査およびグラフ化できます。 ただし、診断設定を使用してエクスポートすると、メトリックは、イベント ハブ内のすべてのキューのすべての受信メッセージとして表されます。
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>ポータルを使用して診断ログをストリーミングする
1. ポータルで、Azure Monitor に移動し、**[設定]** メニューの **[診断設定]** をクリックします。


2. 必要に応じて、リソース グループまたはリソースの種類で一覧をフィルタリングして、診断設定を行うリソースをクリックします。

3. 選択したリソースの設定が存在しない場合は、設定を作成するように求められます。 [診断を有効にする] をクリックします。

   ![診断設定の追加 - 既存の設定が存在しない](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   リソースに既存の設定が存在する場合は、このリソースで構成済みの設定の一覧が表示されます。 [診断設定の追加] をクリックします。

   ![診断設定の追加 - 既存の設定が存在する](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. 設定に名前を付け、**[Log Analytics への送信]** チェック ボックスをオンにして、Log Analytics ワークスペースを選択します。

   ![診断設定の追加 - 既存の設定が存在する](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. **[Save]** をクリックします。

しばらくすると、このリソースの設定一覧に新しい設定が表示され、新しいイベント データが生成されるとすぐに、診断ログがそのワークスペースにストリーミングされます。 イベントが生成されてから、それが Log Analytics に表示されるまでに 15 分ほどかかる場合があるので注意してください。

### <a name="via-powershell-cmdlets"></a>PowerShell コマンドレットの使用

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Azure PowerShell コマンドレット](../../azure-monitor/platform/powershell-quickstart-samples.md)を使用してストリーミングを有効にするには、次のパラメーターを指定して `Set-AzDiagnosticSetting` コマンドレットを使用します。

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

workspaceID プロパティに指定するのは、ワークスペースの完全な Azure リソース ID です。Log Analytics ポータルに表示されるワークスペース ID/キーではないので注意してください。

### <a name="via-azure-cli"></a>Azure CLI の使用

[Azure CLI](../../azure-monitor/platform/cli-samples.md) を使ってストリーミングを有効にするには、[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) コマンドを使います。

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

`--logs` パラメーターとして渡された JSON 配列にディクショナリを追加することによって、診断ログに他のカテゴリを追加できます。

`--resource-group` 引数は、`--workspace` がオブジェクト ID でない場合にのみ必要です。

## <a name="how-do-i-query-the-data-from-a-log-analytics-workspace"></a>Log Analytics ワークスペースのデータのクエリを実行する方法

Azure Monitor ポータルの [ログ] ブレードでは、AzureDiagnostics テーブルでの Log Management ソリューションの一部として診断ログのクエリを実行することができます。 他にも、[Azure リソース向けの監視ソリューションがいくつか](../../azure-monitor/insights/solutions.md)存在します。それをインストールすることで、Azure Monitor に送信されているログ データからすぐに分析情報を得ることができます。

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>既知の制限事項: AzureDiagnostics の列の制限
多くのリソースの送信するデータ型はすべて同じテーブル ("_AzureDiagnostics_") に送信されるため、収集されるさまざまなデータ型のスキーマのスーパーセットとなります。 たとえば、次のデータ型の収集に対して診断設定を作成した場合、すべて同じワークスペースに送信されます。
- リソース 1 の監査ログ (スキーマは列 A、B、C から構成される)  
- リソース 2 のエラー ログ (スキーマは列 D、E、F から構成される)  
- リソース 3 のデータ フロー ログ (スキーマは列 G、H、I から構成される)  
 
AzureDiagnostics テーブルは次のようになります。サンプル データが入力されています。  
 
| ResourceProvider | Category | A | b | C | D | E | F | G | H | I |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
| Microsoft.Resource2 | ErrorLogs | | | | q1 | w1 | e1 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
| Microsoft.Resource2 | ErrorLogs | | | | q2 | w2 | e2 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
| Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
| ... |
 
Azure Log のテーブルの列数は 500 を超えないという明示的な制限があります。 この上限に到達すると、最初の 500 列から外れるデータを含む行は、取り込み時に破棄されます。 AzureDiagnostics テーブルは特にこの制限の影響を受けやすくなっています。 これは一般的に、さまざまなデータ ソースが大量に同じワークスペースに送信されること、あるいは非常に冗長なデータ ソースが同じワークスペースに送信されることが理由です。 
 
#### <a name="azure-data-factory"></a>Azure Data Factory  
Azure Data Factory は非常に詳細なログ セットに起因し、この制限によって特に影響を受けることが判明しているリソースです。 具体的には以下のとおりです。  
- *パイプラインのアクティビティに対して定義されているユーザー パラメーター*: アクティビティに対して、一意の名前が付けられたユーザー パラメーターごとに新しい列が作成されます。 
- *アクティビティの入力と出力*: これはアクティビティによって異なり、冗長性に起因して大量の列を生成します。 
 
以下に提案する大まかな対処方法と同様に、ADF ログをそれぞれのワークスペースに分離させ、ワークスペースで収集される他のログ タイプにこのログが影響を与える可能性を最小限に抑えることをお勧めします。 Azure Data Factory 用に選別されたログを 2019 年 4 月中旬までに利用できるようにする予定です。
 
#### <a name="workarounds"></a>対処方法
短期的対処。500 列の制限が再定義されるまで、冗長なデータ型を個々のワークスペースに分離し、上限に到達する可能性を減らすことをお勧めします。
 
長期的対処。Azure Diagnostics は、まばらな統合スキーマからデータ型別のテーブルと動的な型の組み合わせに方針を移行しています。それにより、Azure Diagnostics メカニズム経由で Azure ログに入ってくるデータが大幅に使いやすくなります。 これは既に、[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) ログや [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor) ログなど、一部の Azure リソース タイプで確認することができます。 Azure でこれらの選別されたログがサポートされる新しいリソース タイプの最新情報は [Azure Updates](https://azure.microsoft.com/updates/) ブログでご覧ください。


## <a name="next-steps"></a>次の手順

* [Azure 診断ログの詳細を確認する](diagnostic-logs-overview.md)

