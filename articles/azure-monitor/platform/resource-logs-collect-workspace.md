---
title: Log Analytics ワークスペースで Azure リソース ログを収集する
description: Azure Monitor の Log Analytics ワークスペースに Azure リソース ログをストリーミングする方法を説明します。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 83b91be52694076373d950e0ad785ef22671ef4f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894529"
---
# <a name="collect-azure-resource-logs-in-log-analytics-workspace-in-azure-monitor"></a>Azure Monitor の Log Analytics ワークスペースで Azure リソース ログを収集する
Azure の[リソース ログ](resource-logs-overview.md)からは、Azure リソースの内部操作で頻繁に見られるデータが豊富に提供されます。 この記事では、Log Analytics ワークスペースでリソース ログを収集する方法について説明します。このリソース ログの収集では、Azure Monitor Logs で収集された他の監視データと組み合わせて分析できるほか、アラートや視覚化などの Azure Monitor の他の機能を活用することもできます。 


## <a name="what-you-can-do-with-resource-logs-in-a-workspace"></a>ワークスペースのリソース ログを使用して行えること
Log Analytics ワークスペースにリソース ログを収集すると、ご利用の Azure リソースすべてのログをまとめて分析できるほか、[Azure Monitor ログ](data-platform-logs.md)で利用できる次のような機能をすべて活用できます。

* **ログ クエリ** - 強力なクエリ言語を使用して[ログ クエリ](../log-query/log-query-overview.md)を作成することで、診断データをすばやく分析して分析情報を取得できるほか、Azure Monitor の他のソースから収集したデータと組み合わせて分析することができます。
* **アラート** - [Azure Monitor のログ アラート](alerts-log.md)を使用して、ご利用のリソース ログで識別された重大な状態およびパターンの事前通知を取得します。
* **視覚化** - ログ クエリの結果を Azure ダッシュボードにピン留めするか、対話型のレポートの一部としてブックに含めます。

## <a name="prerequisites"></a>前提条件
ワークスペースがまだない場合は、[新しいワークスペースを作成](../learn/quick-create-workspace.md)する必要があります。 設定を構成するユーザーが両方のサブスクリプションに対して適切な RBAC アクセスを持っている限り、ワークスペースはログを送信するリソースと同じサブスクリプションに属している必要はありません。

## <a name="create-a-diagnostic-setting"></a>診断設定の作成
既定では、リソース ログは収集されません。 Azure リソース用の診断設定を作成して、Log Analytics ワークスペースやその他の送信先でそれらを収集します。 詳細については、「[Azure でログとメトリックを収集するための診断設定を作成する](diagnostic-settings.md)」を参照してください。

## <a name="collection-mode"></a>収集モード
Log Analytics ワークスペースで収集されたデータは、「[Azure Monitor Logs の構造](../log-query/logs-structure.md)」の説明に従って、テーブルに格納されます。 リソース ログで使用されるテーブルは、リソースで使用されているコレクションの種類によって異なります。

- Azure 診断 - 書き込まれたすべてのデータが _AzureDiagnostics_ テーブルに含められます。
- リソース固有 - データは、リソースのカテゴリごとに個別のテーブルに書き込まれます。

### <a name="azure-diagnostics-mode"></a>Azure Diagnostics モード 
このモードでは、任意の[診断設定](diagnostic-settings.md)に基づくすべてのデータが、_AzureDiagnostics_ テーブルに収集されます。 これは、ほとんどの Azure サービスで現在使用されている従来の方法です。

複数の種類のリソースから同じテーブルにデータが送信されるため、そのスキーマは、収集されるすべての種類のデータ型のスキーマのスーパーセットになります。

次の例を検討してください。ここでは次のデータ型について、同じワークスペースで診断設定が収集されています。

- サービス 1 の監査ログ (スキーマは列 A、B、C から構成される)  
- サービス 1 のエラー ログ (スキーマは列 D、E、F から構成される)  
- サービス 2 の監査ログ (スキーマは列 G、H、I から構成される)  

AzureDiagnostics テーブルは次のようになります。  

| ResourceProvider    | カテゴリ     | A  | b  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Service1 | AuditLogs    | x1 | y1 | z1 |    |    |    |    |    |    |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q1 | w1 | e1 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | l1 |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q2 | w2 | e2 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | l3 |
| Microsoft.Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>リソース固有
このモードでは、診断設定で選択されたカテゴリごとに、個別のテーブルが選択されたワークスペース内に作成されます。 この方法をお勧めします。その理由は、ログ クエリのデータの操作がはるかに簡単になり、スキーマとその構造の探しやすさが向上し、取り込み遅延とクエリ時間の両方でパフォーマンスが向上し、さらに特定のテーブルで RBAC 権限を付与できることにあります。 すべての Azure サービスは、最終的にはリソース固有モードに移行します。 

上記の例では、3 つのテーブルが作成されることになります。
 
- テーブル *Service1AuditLogs* は次のようになります。

    | リソース プロバイダー | カテゴリ | A | b | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | y1 | z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- テーブル *Service1ErrorLogs* は次のようになります。  

    | リソース プロバイダー | カテゴリ | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  q1 | w1 | e1 |
    | Service1 | ErrorLogs |  q2 | w2 | e2 |
    | ... |

- テーブル *Service2AuditLogs* は次のようになります。  

    | リソース プロバイダー | カテゴリ | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | j1 | k1 | l1|
    | Service2 | AuditLogs | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>コレクション モードを選択する
ほとんどの Azure リソースでは、ユーザーに選択肢を与えることなく、**Azure 診断**または**リソース固有モード**でワークスペースにデータが書き込まれます。 使用されるモードの詳細については、[各サービスのドキュメント](diagnostic-logs-schema.md)を参照してください。 最終的には、すべての Azure サービスで、リソース固有モードが使用されるようになります。 この移行の一環として、一部のリソースでは診断設定でモードを選択できるようになります。 新しい診断設定ではリソース固有モードを指定してください。これにより、データの管理が容易になり、後日行う移行が複雑になるのを回避できる場合があります。
  
   ![診断設定のモード セレクター](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> 現在、Azure portal で診断設定を構成する場合は、**Azure 診断**または**リソース固有**モードを選択できるだけとなっています。 CLI、PowerShell、または Rest API を使用して設定を構成する場合、既定では **Azure 診断**となります。

既存の診断設定をリソース固有モードに変更できます。 この場合、既に収集されたデータは、ワークスペースに対するご利用の保有期間設定に従って削除されるまで、_AzureDiagnostics_ テーブル内に残ります。 新しいデータは、専用のテーブルに収集されます。 両方のテーブルのデータに対してクエリを実行するには、[union](https://docs.microsoft.com/azure/kusto/query/unionoperator) 演算子を使用します。

リソース固有モードをサポートしている Azure サービスに関するお知らせは、[Azure の更新情報](https://azure.microsoft.com/updates/)を引き続きご覧ください。

### <a name="column-limit-in-azurediagnostics"></a>AzureDiagnostics での列の制限
Azure Monitor ログ内のいずれのテーブルに対しても、500 のプロパティ制限があります。 この上限に到達すると、最初の 500 プロパティから外れるデータを含む行は、取り込み時に破棄されます。 *AzureDiagnostics* テーブルは、このテーブルへの書き込みを行うすべての Azure サービスのプロパティを含んでいるため、特にこの制限の影響を受けやすくなっています。

複数のサービスからリソースログを収集している場合、_AzureDiagnostics_ は、この制限を超えることがあり、データが失われる可能性があります。 すべての Azure サービスでリソース固有モードがサポートされるようになるまでは、複数のワークスペースに書き込むようにリソースを構成して、500 列の制限に達する可能性を低くする必要があります。

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory サービスの場合は、非常に詳細なログセットであるため、多数の列の書き込みが行われ、_AzureDiagnostics_ が制限を超える可能性があることがわかっています。 リソース固有モードを有効にする前に構成した診断設定の場合、任意のアクティビティに対して、一意の名前が付けられたユーザー パラメーターごとに新しい列が作成されます。 アクティビティの入力と出力がその性質上詳細であるため、より多くの列が作成されます。
 
ソース固有モードを使用するように、ご利用のログをできるだけ早く移行する必要があります。 今すぐ実行できない場合は、暫定措置として Azure Data Factory ログをそれぞれに固有のワークスペースに分離して、これらのログがワークスペースに収集される他のログの種類に影響を与える可能性を最小限に抑えるようにします。


## <a name="next-steps"></a>次の手順

* Azure リソース ログの詳細については、[Azure リソース ログの概要](resource-logs-overview.md)を記載したページを参照してください。
* Log Analytics ワークスペースにリソース ログを収集するように診断設定を作成するには、「[Azure でログとメトリックを収集するための診断設定を作成する](diagnostic-settings.md)」を参照してください。
