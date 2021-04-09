---
title: Azure Monitor を使用した Azure Data Explorer のクロスリソース クエリ
description: Azure Monitor を使用して、Azure Data Explorer、Log Analytics ワークスペース、および Azure Monitor の従来の Application Insights アプリケーションの間で製品にまたがるクエリを実行します。
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: a800f78df26ce76144994bb9da2cac6271323eb4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419424"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>Azure Monitor を使用した Azure Data Explorer のクロスリソース クエリ
Azure Monitor では、Azure Data Explorer、[Application Insights](../app/app-insights-overview.md)、および [Log Analytics](../logs/data-platform-logs.md) 間のクロスサービス クエリがサポートされています。 それにより、Log Analytics または Application Insights ツールを使用して Azure Data Explorer クラスターに対してクエリを実行し、それをクロスサービス クエリで参照することができます。 この記事では、クロスサービス クエリを作成する方法について説明します。

次の図は、Azure Monitor のクロスサービス フローを示しています。

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="ユーザー、Azure Monitor、プロキシ、および Azure Data Explorer 間のクエリのフローを示す図。":::

>[!NOTE]
> Azure Monitor のクロスサービス クエリは、パブリック プレビュー段階にあります。 不明な点がある場合は、[サービス チーム](mailto:ADXProxy@microsoft.com)にお問い合わせください。

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Log Analytics または Application Insights リソースと Azure Data Explorer のクロスクエリを実行する

Kusto クエリをサポートするクライアント ツールを使用して、クロスリソース クエリを実行できます。 これらのツールの例には、Log Analytics Web UI、Workbooks、PowerShell、REST API などがあります。

Azure Data Explorer クラスターの識別子をクエリの `adx` パターン内に入力し、その後にデータベース名とテーブルを入力します。

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="クロスサービス クエリの例を示すスクリーンショット。":::

> [!NOTE]
>* データベース名は大文字と小文字が区別されます。
>* アラートとしてのクロスリソース クエリはサポートされていません。
>* クラスター内の Timestamp 列の特定はサポートされていません。Log Analytics クエリ API は時間フィルターを渡しません

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>Azure Data Explorer クラスター テーブルを Log Analytics ワークスペースと結合する

クラスター テーブルを Log Analytics ワークスペースと結合するには、`union` コマンドを使用します。

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="union コマンドを使用したクロスサービス クエリの例を示すスクリーンショット。":::

> [!Tip]
> 次の短縮形を使用できます。*ClusterName*/*InitialCatalog*。 たとえば、`adx('help/Samples')` は `adx('help.kusto.windows.net/Samples')` に変換されます。

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>一方のテナントの Azure Data Explorer クラスターのデータを他方の Azure Monitor リソースと結合する

サービス間のクロス テナント クエリはサポートされていません。 両方のリソースにまたがるクエリを実行するには、1 つのテナントにサインインします。

Azure Data Explorer リソースがテナント A にあり、Log Analytics ワークスペースがテナント B にある場合は、次の方法のいずれかを使用します。

*  Azure Data Explorer を使用すると、異なるテナントにプリンシパルのロールを追加できます。 テナント B のユーザー ID を、許可されているユーザーとして Azure Data Explorer クラスターに追加します。 Azure Data Explorer クラスター上の [TrustedExternalTenant](/powershell/module/az.kusto/update-azkustocluster) プロパティにテナント B が含まれていることを確認します。テナント B でクロスクエリ全体を実行します。
*  [Lighthouse](../../lighthouse/index.yml) を使用して、Azure Monitor リソースをテナント A に射影します。

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>さまざまなテナントから Azure Data Explorer クラスターに接続する

Kusto Explorer によって、ユーザー アカウントの所属元であるテナントに自動的にサインインされます。 同じユーザー アカウントを使用して他のテナントのリソースにアクセスするには、接続文字列に `TenantId` を明示的に指定する必要があります。

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>次のステップ
* [クエリを作成する](/azure/data-explorer/write-queries)
* [Azure Data Explorer を使用して Azure Monitor のデータのクエリを実行する](/azure/data-explorer/query-monitor-data)
* [Azure Monitor でクロスリソース ログ クエリを実行する](../logs/cross-workspace-query.md)