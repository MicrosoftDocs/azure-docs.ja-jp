---
title: Azure Monitor ログを使用した Azure Functions の監視
description: Azure Functions で Azure Monitor ログを使用して関数の実行を監視する方法について説明します。
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: b6ce9e77421df0563810fd7f8255720c1fd2d0c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591072"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Azure Monitor ログを使用した Azure Functions の監視

Azure Functions は [Azure Monitor ログ](../azure-monitor/logs/data-platform-logs.md)と統合されており、関数を監視することができます。 この記事では、システム生成ログとユーザー生成ログを Azure Monitor ログに送信するように Azure Functions を構成する方法について説明します。

Azure Monitor Logs を使うと、同じワークスペース内の異なるリソースのログを統合できます。また、それを[クエリ](../azure-monitor/logs/log-query-overview.md)を使って分析し、収集したデータをすばやく取得、統合、分析できます。  Azure portal で [Log Analytics](../azure-monitor/logs/log-query-overview.md) を使用してクエリを作成およびテストした後、これらのツールを使用してデータを直接分析できるほか、クエリを保存して[視覚化](../azure-monitor/visualizations.md)または[アラート ルール](../azure-monitor/alerts/alerts-overview.md)に利用することができます。

Azure Monitor では、Azure Data Explorer で使用される [Kusto クエリ言語](/azure/kusto/query/)のバージョンを使用します。それは、単純なログ検索に適していますが、集計、結合、スマート分析などの高度な機能も備えています。 [さまざまなレッスン](../azure-monitor/logs/get-started-queries.md)を利用すれば、クエリ言語はすぐに覚えることができます。

> [!NOTE]
> 現在、Azure Monitor Logs との統合は、Windows Consumption、Premium、Dedicated ホスティング プランで実行されている v2 および v3 の関数アプリについてはパブリック プレビュー段階にあります。

## <a name="setting-up"></a>設定

1. [Azure portal](https://portal.azure.com) で関数アプリの **[監視]** セクションから **[診断設定]** を選択し、 **[診断設定を追加する]** をクリックします。

   :::image type="content" source="media/functions-monitor-log-analytics/diagnostic-settings-add.png" alt-text="[診断設定] を選択します":::

1. **[診断設定]** ページの **[カテゴリの詳細]** と **[ログ]** で、**FunctionAppLogs** を選択します。

   **[FunctionAppLogs]** テーブルには、目的のログが含まれています。

1. **[宛先の詳細]** で、 **[Log Analytics への送信]** を選択して、**Log Analytics ワークスペース** を選択します。 

1. **診断設定名** を入力して、 **[保存]** を選択します。

   :::image type="content" source="media/functions-monitor-log-analytics/choose-table.png" alt-text="診断設定を追加します":::

## <a name="user-generated-logs"></a>ユーザー生成ログ

カスタム ログを生成するには、お使いの言語に固有のログ記録ステートメントを使用します。 サンプル コード スニペットを次に示します。


# <a name="c"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>ログのクエリ

生成されたログをクエリするには、次の操作を行います。
 
1. 関数アプリから、 **[診断設定]** を選択します。 

1. **[診断設定]** から、関数ログの送信先として構成した Log Analytics ワークスペースを選択します。 

1. **[Log Analytics ワークスペース]** ページで、 **[ログ]** を選択します。

   Azure Functions によって、すべてのログは、 **[LogManagement]** の **FunctionAppLogs** テーブルに書き込まれます。 

   :::image type="content" source="media/functions-monitor-log-analytics/querying.png" alt-text="Log Analytics ワークスペース内のクエリ ウィンドウ":::

いくつかのサンプル クエリを次に示します。

### <a name="all-logs"></a>すべてのログ

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="specific-function-logs"></a>特定の関数のログ

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>例外

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>次のステップ

- 「[Azure Functions の概要](functions-overview.md)」を確認してください。
- [Azure Monitor ログ](../azure-monitor/logs/data-platform-logs.md)の詳細について学習します。
- [クエリ言語](../azure-monitor/logs/get-started-queries.md)の詳細について学習します。
