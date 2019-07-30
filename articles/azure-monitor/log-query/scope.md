---
title: Azure Monitor Log Analytics のログ クエリのスコープ | Microsoft Docs
description: Azure Monitor Log Analytics のログ クエリのスコープと時間範囲について説明します。
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: a948b80f6524339f0908a2fb19c4a83d70b3b140
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296858"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics のログ クエリのスコープと時間範囲
[Azure portal の [Log Analytics]](get-started-portal.md) で[ログ クエリ](log-query-overview.md)を実行するとき、クエリによって評価されるデータのセットは、選択したスコープと時間範囲によって異なります。 この記事では、スコープと時間範囲、および要件に応じてそれぞれを設定する方法について説明します。 さまざまな種類のスコープの動作についても説明します。


## <a name="query-scope"></a>クエリ スコープ
クエリ スコープは、クエリによって評価されるレコードを定義します。 これには通常、単一の Log Analytics ワークスペースまたは Application Insights アプリケーション内のすべてのレコードが含まれます。 Log Analytics では、特定の監視対象 Azure リソースにスコープを設定することもできます。 これにより、リソース所有者は、そのリソースが複数のワークスペースに書き込む場合でも、自分のデータのみに焦点を絞ることができます。

スコープは常に Log Analytics ウィンドウの左上に表示されます。 アイコンは、スコープが Log Analytics ワークスペースか、それとも Application Insights アプリケーションかを示します。 アイコンなしはその他の Azure リソースを示します。

![Scope (スコープ)](media/scope/scope.png)

スコープは Log Analytics の起動に使用する方法によって決まり、スコープをクリックして変更できる場合もあります。 次の表は、使用されるさまざまな種類のスコープとそれぞれの詳細の一覧です。

| クエリ スコープ | スコープ内のレコード | 選択方法 | スコープの変更 |
|:---|:---|:---|:---|
| Log Analytics ワークスペース | Log Analytics ワークスペース内のすべてのレコード。 | **[Azure Monitor]** メニューまたは **[Log Analytics ワークスペース]** メニューの **[ログ]** を選択します。  | スコープを他のリソースの種類に変更できます。 |
| Application Insights アプリケーション | Application Insights アプリケーション内のすべてのレコード。 | Application Insights の **[概要]** ページから **[分析]** を選択します。 | スコープを別の Application Insights アプリケーションにのみ変更できます。 |
| リソース グループ | リソース グループ内のすべてのリソースによって作成されるレコード。 複数の Log Analytics ワークスペースからのデータを含めることができます。 | リソース グループ メニューから **[ログ]** を選択します。 | スコープは変更できません。|
| サブスクリプション | サブスクリプション内のすべてのリソースによって作成されたレコード。 複数の Log Analytics ワークスペースからのデータを含めることができます。 | サブスクリプション メニューから **[ログ]** を選択します。   | スコープは変更できません。 |
| その他の Azure リソース | リソースによって作成されるレコード。 複数の Log Analytics ワークスペースからのデータを含めることができます。  | リソース メニューから **[ログ]** を選択します。<br>または<br>**[Azure Monitor]** メニューから **[ログ]** を選択し、新しいスコープを選択します。 | スコープを同じリソースの種類にのみ変更できます。 |

### <a name="limitations-when-scoped-to-a-resource"></a>リソースをスコープにしたときの制限

クエリ スコープが Log Analytics ワークスペースまたは Application Insights アプリケーションのときは、ポータルのすべてのオプションとすべてのクエリ コマンドが使用できます。 ただし、ポータルの以下のオプションは単一のワークスペースまたはアプリケーションに関連付けられているため、リソースをスコープにした場合は使用できません。

- 保存
- クエリ エクスプローラー
- 新しいアラート ルール

リソースをスコープにした場合、そのリソースまたはリソース セットのデータがあるワークスペースはクエリ スコープに既に含まれているため、クエリで以下のコマンドを使用できません。

- [app](app-expression.md)
- [workspace](workspace-expression.md)
 


## <a name="time-range"></a>時間範囲
時間範囲は、レコードが作成された時期に基づいてクエリで評価されるレコード セットを指定します。 次の表で説明しているように、これはワークスペースまたはアプリケーション内のすべてのレコードの標準プロパティによって定義されます。

| Location | プロパティ |
|:---|:---|
| Log Analytics ワークスペース          | TimeGenerated |
| Application Insights アプリケーション | timestamp     |

Log Analytics ウィンドウの上部にあるタイム ピッカーから選択して、時間範囲を設定します。  定義済みの期間を選択するか、 **[カスタム]** を選択して特定の時間範囲を指定することができます。

![時刻の選択ツール](media/scope/time-picker.png)

上の表に示すように、標準の時間プロパティを使用するクエリにフィルターを設定すると、タイム ピッカーが **[Set in query]\(クエリで設定\)** に変わり、タイム ピッカーは無効になります。 この場合、フィルターをクエリの先頭に配置し、後続の処理ではフィルター処理されたレコードを処理するだけで済むようにすると最も効率的です。

![フィルター処理されたクエリ](media/scope/query-filtered.png)

[workspace](workspace-expression.md) または [app](app-expression.md) コマンドを使用して別のワークスペースまたはアプリケーションからデータを取得する場合、タイム ピッカーの動作が異なることがあります。 スコープが Log Analytics ワークスペースのときに **app** を使用している、またはスコープが Application Insights アプリケーションのときに **workspace** を使用している場合、Log Analytics は、フィルターで使用されるプロパティによって時間フィルターが決まることを理解していない可能性があります。

次の例では、スコープは Log Analytics ワークスペースに設定されています。  クエリは **workspace** を使用して別の Log Analytics ワークスペースからデータを取得します。 タイム ピッカーは、予想された **TimeGenerated** プロパティを使用するフィルターを認識するので、 **[Set in query]\(クエリで設定\)** に変わります。

![ワークスペースのクエリ](media/scope/query-workspace.png)

一方、クエリで **app** を使用して Application Insights アプリケーションからデータを取得する場合、Log Analytics はフィルターの **timestamp** プロパティを認識せず、タイム ピッカーは変更されません。 この場合、両方のフィルターが適用されます。 この例では、クエリの **where** 句で 7 日を指定した場合でも、過去 24 時間以内に作成されたレコードしかクエリに含まれません。

![アプリのクエリ](media/scope/query-app.png)

## <a name="next-steps"></a>次の手順

- [Azure portal 内での Log Analytics の使用に関するチュートリアル](get-started-portal.md)を参照します。
- [クエリの作成に関するチュートリアル](get-started-queries.md)を参照します。