---
title: Azure Data Explorer を使用して Azure Monitor でデータのクエリを実行する (プレビュー)
description: このトピックでは、Application Insights と Log Analytics でのクロス積クエリ用の Azure Data Explorer プロキシを作成することによって、Azure Monitor でデータのクエリを実行します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: f363e59e6faa6b115eb40a2a5d35432f02299d52
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810034"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Azure Data Explorer を使用して Azure Monitor でデータのクエリを実行する (プレビュー)

Azure Data Explorer プロキシ クラスター (ADX プロキシ) は、[Azure Monitor](/azure/azure-monitor/) サービスで Azure Data Explorer、[Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)、および [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) の間のクロス積クエリを実行できるようにするエンティティです。 Azure Monitor Log Analytics ワークスペースまたは Application Insights アプリをプロキシ クラスターとしてマップできます。 その後、Azure Data Explorer ツールを使用してプロキシ クラスターにクエリを実行し、それをクロス クラスター クエリで参照できます。 この記事では、プロキシ クラスターに接続し、プロキシ クラスターを Azure Data Explorer の Web UI に追加した後、Azure Data Explorer から AI アプリまたは LA ワークスペースに対してクエリを実行する方法を示します。

Azure Data Explorer プロキシのフロー: 

![ADX プロキシのフロー](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>前提条件

> [!NOTE]
> ADX プロキシはプレビュー モードです。 この機能を有効にするには、[ADXProxy](mailto:adxproxy@microsoft.com) チームまでお問い合わせください。

## <a name="connect-to-the-proxy"></a>プロキシに接続する

1. Log Analytics または Application Insights クラスターに接続する前に、Azure Data Explorer ネイティブ クラスター (*help* クラスターなど) が左側のメニューに表示されていることを確認します。

    ![ADX ネイティブ クラスター](media/adx-proxy/web-ui-help-cluster.png)

1. Azure Data Explorer の UI (https://dataexplorer.azure.com/clusters) ) で、 **[クラスターの追加]** を選択します。

1. **[クラスターの追加]** ウィンドウで、次の操作を行います。

    * LA または AI クラスターに URL を追加します。 次に例を示します。`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * **[追加]** を選択します。

    ![クラスターを追加する](media/adx-proxy/add-cluster.png)

    複数のプロキシ クラスターに接続を追加する場合は、それぞれに異なる名前を付けます。 そうしないと、左側のウィンドウですべてが同じ名前になります。

1. 接続が確立されると、LA または AI クラスターが ADX ネイティブ クラスターと共に左側のウィンドウに表示されます。 

    ![Log Analytics クラスターと Azure Data Explorer クラスター](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>クエリを実行する

Kusto Explorer、ADX Web Explorer、Jupyter Kqlmagic、または REST API を使用して、プロキシ クラスターにクエリを実行できます。 

> [!TIP]
> * データベース名は、プロキシ クラスターで指定したリソースと同じ名前にする必要があります。 名前は大文字と小文字が区別されます。
> * クロス クラスター クエリでは、[アプリとワークスペースの名前付け](#application-insights-app-and-log-analytics-workspace-names)が正しいことを確認してください。

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Azure Data Explorer ネイティブ クラスターにクエリを実行する 

Azure Data Explorer クラスター (*help* クラスター内の *StormEvents* テーブルなど) にクエリを実行します。 クエリを実行する場合は、左側のウィンドウで Azure Data Explorer ネイティブ クラスターが選択されていることを確認してください。

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![StormEvents テーブルにクエリを実行する](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>LA または AI クラスターにクエリを実行する

LA または AL クラスターにクエリを実行する場合は、左側のウィンドウで LA または AI クラスターが選択されていることを確認してください。 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![LA ワークスペースにクエリを実行する](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>ADX プロキシから LA または AI クラスターにクエリを実行する  

プロキシから LA または AI クラスターにクエリを実行する場合は、左側のウィンドウで ADX ネイティブ クラスターが選択されていることを確認してください。 次の例は、ADX ネイティブ クラスターを使用した LA ワークスペースのクエリを示しています

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Azure Data Explorer プロキシからクエリを実行する](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>ADX プロキシからの LA または AI クラスターと ADX クラスターのクロス クエリ 

プロキシからクロス クラスター クエリを実行する場合は、左側のウィンドウで ADX ネイティブ クラスターが選択されていることを確認してください。 次の例は、ADX クラスター テーブルの LA ワークスペースとの (`union` を使用した) 組み合わせを示しています。

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Azure Data Explorer プロキシからのクロス クエリ](media/adx-proxy/cross-query-adx-proxy.png)

union の代わりに [`join` 演算子](/azure/kusto/query/joinoperator)を使用するには、それを (プロキシに対してではなく) Azure Data Explorer ネイティブ クラスターに対して実行するためのヒントが必要になる場合があります。 

## <a name="additional-syntax-examples"></a>その他の構文例

Application Insights (AI) または Log Analytics (LA) クラスターを呼び出す場合は、次の構文オプションを使用できます。

|構文の説明  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| このサブスクリプションで定義されているリソースのみを含むクラスター内のデータベース (**クロス クラスター クエリの場合に推奨**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| このサブスクリプション内のすべてのアプリ/ワークスペースを含むクラスター    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|サブスクリプション内のすべてのアプリ/ワークスペースを含み、このリソース グループのメンバーであるクラスター    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|このサブスクリプションで定義されているリソースのみを含むクラスター      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

### <a name="application-insights-app-and-log-analytics-workspace-names"></a>Application Insights アプリと Log Analytics ワークスペースの名前

* 名前に特殊文字が含まれている場合は、プロキシ クラスター名の URL エンコードで置き換えられます。 
* 名前に [KQL 識別子の名前規則](/azure/kusto/query/schema-entities/entity-names)を満たしていない文字が含まれている場合は、ダッシュ **-** 文字で置き換えられます。

## <a name="next-steps"></a>次の手順

[クエリを作成する](write-queries.md)