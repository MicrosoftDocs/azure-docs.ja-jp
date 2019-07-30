---
title: Azure Monitor の Log Analytics ワークスペースに Azure 診断ログをストリーミングする
description: Azure Monitor の Log Analytics ワークスペースに Azure 診断ログをストリーミングする方法を説明します。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: e8e6276a38f06b5c6ebb24c89f3733b9fd7220f7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612841"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics-workspace-in-azure-monitor"></a>Azure Monitor の Log Analytics ワークスペースに Azure 診断ログをストリーミングする

ポータル、PowerShell コマンドレット、または Azure CLI を使用して、Azure Monitor の Log Analytics ワークスペースに、 **[Azure 診断ログ](diagnostic-logs-overview.md)** をほぼリアルタイムでストリーミングすることができます。

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
1. ポータルで、Azure Monitor に移動し、 **[設定]** メニューの **[診断設定]** をクリックします。


2. 必要に応じて、リソース グループまたはリソースの種類で一覧をフィルタリングして、診断設定を行うリソースをクリックします。

3. 選択したリソースの設定が存在しない場合は、設定を作成するように求められます。 [診断を有効にする] をクリックします。

   ![診断設定の追加 - 既存の設定が存在しない](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   リソースに既存の設定が存在する場合は、このリソースで構成済みの設定の一覧が表示されます。 [診断設定の追加] をクリックします。

   ![診断設定の追加 - 既存の設定が存在する](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. 設定に名前を付け、 **[Log Analytics への送信]** チェック ボックスをオンにして、Log Analytics ワークスペースを選択します。

   ![診断設定の追加 - 既存の設定が存在する](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. **[Save]** をクリックします。

しばらくすると、このリソースの設定一覧に新しい設定が表示され、新しいイベント データが生成されるとすぐに、診断ログがそのワークスペースにストリーミングされます。 イベントが生成されてから、それが Log Analytics に表示されるまでに最大 15 分かかる可能性があります。

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

### <a name="examples"></a>例

```Kusto
// Resources that collect diagnostic logs into this Log Analytics workspace, using Diagnostic Settings
AzureDiagnostics
| distinct _ResourceId
```
```Kusto
// Resource providers collecting diagnostic logs into this Log Analytics worksapce, with log volume per category
AzureDiagnostics
| summarize count() by ResourceProvider, Category
```
```Kusto
// Resource types collecting diagnostic logs into this Log Analytics workspace, with number of resources onboarded
AzureDiagnostics
| summarize ResourcesOnboarded=dcount(_ResourceId) by ResourceType
```
```Kusto
// Operations logged by specific resource provider, in this example - KeyVault
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.KEYVAULT"
| distinct OperationName
```

## <a name="azure-diagnostics-vs-resource-specific"></a>Azure Diagnostics とリソース固有  
Azure Diagnostics の構成で Log Analytics の宛先を有効にした後、データは、明確に異なる 2 つ方法でワークスペースに表示されます。  
- **Azure Diagnostics** - これは、現在、大部分の Azure サービスで使用されている従来の方法です。 このモードでは、特定のワークスペースを指しているすべての診断設定からのすべてのデータが _AzureDiagnostics_ テーブルに表示されます。 
<br><br>同じテーブル (_AzureDiagnostics_) に多数のリソースからデータが送信されるため、このテーブルのスキーマが、収集されるすべての異なるデータ型のスキーマのスーパーセットになります。 たとえば、次のデータ型を収集するための診断設定を作成した場合、すべてが同じワークスペースに送信されます。
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

- **リソース固有** - このモードでは、診断設定の構成で選択されたカテゴリごとに、個別のテーブルが選択されたワークスペース内に作成されます。 このより新しい方法では、すべてのカテゴリ用のテーブルに比べ、関心領域の明確な分離によって、目的のものをはるかに簡単に見つけることができます。 さらに、動的な型のサポートによる利点が提供されます。 このモードは、一部の Azure リソースの種類 ([Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) ログや [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor) ログなど) で、すぐに確認できます。 最終的には、すべてのデータ型がリソース固有モードに移行される予定です。 

    上記の例では、3 つのテーブルが作成されることになります。 
    - _AuditLogs_ テーブルは次のようになります。

        | ResourceProvider | Category | A | b | C |
        | -- | -- | -- | -- | -- |
        | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
        | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
        | ... |

    - _ErrorLogs_ テーブルは次のようになります。  

        | ResourceProvider | Category | D | E | F |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource2 | ErrorLogs | q1 | w1 | e1 |
        | Microsoft.Resource2 | ErrorLogs | q2 | w2 | e2 |
        | ... |

    - _DataFlowLogs_ テーブルは次のようになります。  

        | ResourceProvider | Category | G | H | I |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource3 | DataFlowLogs | j1 | k1 | l1|
        | Microsoft.Resource3 | DataFlowLogs | j3 | k3 | l3|
        | ... |

    リソース固有モードを使用するその他のメリットとして、取り込みの待機時間とクエリ時間の両方でのパフォーマンスの向上、スキーマとそれらの構造の検出可能性の向上、特定のテーブルに対する RBAC 権限の付与などがあります。

### <a name="selecting-azure-diagnostic-vs-resource-specific-mode"></a>Azure Diagnostics とリソース固有モードの選択
ほとんどの Azure リソースでは、Azure Diagnostics を使用するかリソース固有モードを使用するかを選択する必要はありません。データは、リソースで使用するように選択されている方法で自動的に送信されます。 使用されるモードの詳細については、Log Analytics へのデータの送信を有効にしているリソースのドキュメントを参照してください。 

前のセクションで述べたように、Azure Monitor の最終的な目標は、Azure のすべてのサービスでリソース固有モードを使用することです。 この移行を促進し、その一環としてデータの消失がないことを確認するために、一部の Azure サービスでは、Log Analytics へのオンボード時にモードを選択できるようになっています。  
   ![診断設定のモード セレクター](media/diagnostic-logs-stream-log-store/diagnostic-settings-mode-selector.png)

困難である可能性がある移行が完了するまでは、新たに作成する診断設定では、リソースを中心とするモードを使用することを**強く**お勧めします。  

既存の診断設定については、特定の Azure リソースによって有効になった後、Azure Diagnostics からリソース固有モードにさかのぼって切り替えることができます。 以前に取り込まれたデータは、ワークスペースのデータ保持設定の構成に従って期限切れになるまでは _AzureDiagnostics_ テーブルで引き続き使用できますが、新しいデータは、すべて専用のテーブルに送信されます。 つまり、古いデータと新しいデータの両方を対象とする必要があるすべてのクエリでは、古いデータが完全に期限切れになるまでは、クエリで [union](https://docs.microsoft.com/azure/kusto/query/unionoperator) 演算子を使用して、これら 2 つのデータ セットを結合する必要があります。

リソース固有モードでログをサポートする新しい Azure サービスの最新情報については、「[Azure の更新情報](https://azure.microsoft.com/updates/)」ブログをご覧ください。

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>既知の制限事項: AzureDiagnostics の列の制限
Azure Log のテーブルの列数は 500 を超えないという明示的な制限があります。 この上限に到達すると、最初の 500 列から外れるデータを含む行は、取り込み時に破棄されます。 AzureDiagnostics テーブルは特にこの制限の影響を受けやすくなっています。 これは、通常は、多種多様なデータ ソースが同じワークスペースに送信されること、または複数の冗長なデータ ソースが同じワークスペースに送信されることで発生します。 

#### <a name="azure-data-factory"></a>Azure Data Factory  
Azure Data Factory は非常に詳細なログ セットであるため、この制限によって特に影響を受けることが判明しているリソースです。 特に、リソース固有モードが有効になる前に構成されている、または下位互換性を保つという理由でリソース固有モードが明示的に選択されている診断設定には、以下が適用されます。  
- *パイプラインのアクティビティに対して定義されているユーザー パラメーター*: アクティビティに対して、一意の名前が付けられたユーザー パラメーターごとに新しい列が作成されます。 
- *アクティビティの入力と出力*: これらは、アクティビティによって異なり、その冗長性によって多数の列が生成されます。 
 
次に示す対処方法の提案とともに、できるだけ早くリソース固有モードを使用するようにログを移行することをお勧めします。 今すぐ実行できない場合は、暫定措置として ADF ログをそれぞれに固有のワークスペースに分離して、これらのログがワークスペースに収集される他のログの種類に影響を与える可能性を最小限に抑えるようにします。 
 
#### <a name="workarounds"></a>対処方法
短期的には、すべての Azure サービスでリソース固有モードが有効になるまでは、リソース固有モードがサポートされていないすべてのサービスで、これらのサービスによって発行される冗長なデータの種類を別々のワークスペースに分離して、制限に達する可能性を削減することをお勧めします。  
 
長期的には、Azure Diagnostics で、リソース固有モードをサポートするすべての Azure サービスに対応する予定です。 この 500 列という制限による影響の可能性を削減するために、できるだけ早くこのモードに移行することをお勧めします。  


## <a name="next-steps"></a>次の手順

* [Azure 診断ログの詳細を確認する](diagnostic-logs-overview.md)

