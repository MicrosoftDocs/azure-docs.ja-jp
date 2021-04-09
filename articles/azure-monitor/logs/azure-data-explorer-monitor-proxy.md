---
title: Azure Data Explorer を使用して Azure Monitor のデータのクエリを実行する (プレビュー)
description: Azure Data Explorer を使用して、Azure Data Explorer と、Azure Monitor の Log Analytics ワークスペースおよび従来の Application Insights アプリケーション間で製品をまたぐクエリを実行します。
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 65dba60a798b1157a44a7a198b8eba7de1e8fe81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031261"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Azure Data Explorer を使用して Azure Monitor でデータのクエリを実行する (プレビュー)

Azure Data Explorer では、Azure Data Explorer、[Application Insights (AI)](../app/app-insights-overview.md)、[Log Analytics (LA)](./data-platform-logs.md) 間のクロス サービス クエリがサポートされています。 Azure Data Explorer ツールを使用して Log Analytics/Application Insights ワークスペースに対してクエリを実行し、クロス サービス クエリでその内容を参照することができます。 この記事では、クロス サービス クエリを作成する方法と、Azure Data Explorer の Web UI に Log Analytics/Application Insights ワークスペースを追加する方法について説明します。

Azure Data Explorer のクロス サービス クエリのフローは次のとおりです。:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="Azure Data Explorer プロキシのフロー":::

> [!NOTE]
> * Azure Data Explorer クライアント ツールから直接、または Azure Data Explorer クラスターでクエリを実行することによって間接的に、Azure Data Explorer から Azure Monitor データに対してクエリを実行する機能は、プレビュー モードです。
>* 不明な点がある場合は、[クロス サービス クエリ](mailto:adxproxy@microsoft.com) チームにお問い合わせください。

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Azure Data Explorer クライアント ツールに Log Analytics/Application Insights ワークスペースを追加する

1. Log Analytics または Application Insights クラスターに接続する前に、Azure Data Explorer ネイティブ クラスター (*help* クラスターなど) が左側のメニューに表示されていることを確認します。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Azure Data Explorer ネイティブ クラスター":::

 Azure Data Explorer の UI (https://dataexplorer.azure.com/clusters) ) で、 **[クラスターの追加]** を選択します。

2. **[クラスターの追加]** ウィンドウで、LA または AI クラスターの URL を追加します。

    * LA の場合: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * AI の場合: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * **[追加]** を選択します。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="クラスターを追加する":::
 
>[!NOTE]
>複数の Log Analytics/Application insights ワークスペースに接続を追加する場合は、それぞれに異なる名前を付けます。 そうしないと、左側のウィンドウですべてが同じ名前になります。

 接続が確立されると、Log Analytics または Application Insights のワークスペースが、ネイティブの Azure Data Explorer クラスターとともに左側のペインに表示されます。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics と Azure Data Explorer のクラスター":::
 
> [!NOTE]
> マップできる Azure Monitor ワークスペースの数は、100 に制限されています。

## <a name="create-queries-using-azure-monitor-data"></a>Azure Monitor データを使用してクエリを作成する

Kusto クエリをサポートするクライアント ツールを使用してクエリを実行できます。例:Kusto Explorer、Azure Data Explorer の Web UI、Jupyter Kqlmagic、Flow、PowerQuery、PowerShell、Lens、REST API。

> [!NOTE]
> クロス サービス クエリ機能は、データの取得のみに使用されます。 詳細については、「[関数のサポート](#function-supportability)」を参照してください。

> [!TIP]
> * データベース名は、クロス サービス クエリで指定したリソースと同じ名前にする必要があります。 名前は大文字と小文字が区別されます。
> * クロス クラスター クエリでは、Application Insights アプリと Log Analytics ワークスペースの名前付けが正しいことを確認してください。
> * 名前に特殊文字が含まれている場合は、クロス サービス クエリの URL エンコードで置き換えられます。
> * 名前に [KQL 識別子の名前規則](/azure/data-explorer/kusto/query/schema-entities/entity-names)を満たしていない文字が含まれている場合は、ダッシュ **-** 文字で置き換えられます。

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Azure Data Explorer クライアント ツールからの Log Analytics/Application Insights ワークスペースでの直接クエリ

Log Analytics または Application Insights のワークスペースでクエリを実行します。 左側のペインでワークスペースが選択されていることを確認します。
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Log Analytics ワークスペースにクエリを実行する":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>Log Analytics または Application Insights と Azure Data Explorer ネイティブ クラスターのクロス クエリ

クロス クラスター サービス クエリを実行する場合は、左側のペインで Azure Data Explorer ネイティブ クラスターが選択されていることを確認してください。 次の例では、[union](/azure/data-explorer/kusto/query/unionoperator) と Log Analytics ワークスペースを使用して、Azure Data Explorer クラスター テーブルを結合する方法を示します。

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="Azure Data Explorer からのクロス サービス クエリ":::

union の代わりに [`join` 演算子](/azure/data-explorer/kusto/query/joinoperator)を使用するには、それを Azure Data Explorer ネイティブ クラスターに対して実行するための [`hint`](/azure/data-explorer/kusto/query/joinoperator#join-hints) が必要になる場合があります。

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>一方のテナントの Azure Data Explorer クラスターのデータを他方の Azure Monitor リソースと結合する

サービス間のクロス テナント クエリはサポートされていません。 両方のリソースにまたがるクエリを実行するためには、1 つのテナントにサインインします。

Azure Data Explorer リソースがテナント 'A' にあり、Log Analytics ワークスペースがテナント 'B' にある場合は、次の 2 つの方法のいずれかを使用します。

1. Azure Data Explorer を使用すると、異なるテナントにプリンシパルのロールを追加できます。 Azure Data Explorer クラスターにテナント 'B' のユーザー ID を許可されているユーザーとして追加します。 Azure Data Explorer クラスター上の *['TrustedExternalTenant'](/powershell/module/az.kusto/update-azkustocluster)* プロパティにテナント 'B' が含まれていることを検証します。 テナント 'B' でクロスクエリを完全に実行します。

2. [Lighthouse](../../lighthouse/index.yml) を使用して、Azure Monitor リソースをテナント 'A' に射影します。
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>さまざまなテナントから Azure Data Explorer クラスターに接続する

Kusto Explorer では、ユーザー アカウントが最初に属しているテナントに自動的にサインインされます。 同じユーザー アカウントを使用して他のテナントのリソースにアクセスするには、接続文字列に `tenantId` を明示的に指定する必要があります: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="function-supportability"></a>関数のサポート

Azure Data Explorer クロス サービス クエリでは、Application Insights と Log Analytics の両方の関数がサポートされています。
この機能により、クロス クラスター クエリで Azure Monitor の表形式関数を直接参照できます。
次のコマンドがクロス サービス クエリでサポートされています。

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

次の図は、Azure Data Explorer の Web UI から表形式関数にクエリを実行する例を示しています。
関数を使用するには、[クエリ] ウィンドウで名前を実行します。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Azure Data Explorer の Web UI から表形式関数のクエリを実行する":::

## <a name="additional-syntax-examples"></a>その他の構文例

Log Analytics または Application Insights のクラスターを呼び出す場合は、次の構文オプションを使用できます。

|構文の説明  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| このサブスクリプションで定義されているリソースのみを含むクラスター内のデータベース (**クロス クラスター クエリの場合に推奨**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| このサブスクリプション内のすべてのアプリ/ワークスペースを含むクラスター    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|サブスクリプション内のすべてのアプリ/ワークスペースを含み、このリソース グループのメンバーであるクラスター    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|このサブスクリプションで定義されているリソースのみを含むクラスター      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>次のステップ

- [Log Analytics ワークスペースと Application Insights のデータ構造](data-platform-logs.md)の詳細をご覧ください。
- [Azure Data Explorer のクエリの記述](/azure/data-explorer/write-queries)に関するページをご覧ください。