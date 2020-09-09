---
title: Azure Monitor Log Analytics のログ クエリのスコープ | Microsoft Docs
description: Azure Monitor Log Analytics のログ クエリのスコープと時間範囲について説明します。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2020
ms.openlocfilehash: 2840e5b8ff16d44f76aaafcf68264c65e4401ff7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "83199022"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics のログ クエリのスコープと時間範囲
[Azure portal の [Log Analytics]](get-started-portal.md) で[ログ クエリ](log-query-overview.md)を実行するとき、クエリによって評価されるデータのセットは、選択したスコープと時間範囲によって異なります。 この記事では、スコープと時間範囲、および要件に応じてそれぞれを設定する方法について説明します。 さまざまな種類のスコープの動作についても説明します。


## <a name="query-scope"></a>クエリ スコープ
クエリ スコープは、クエリによって評価されるレコードを定義します。 これには通常、単一の Log Analytics ワークスペースまたは Application Insights アプリケーション内のすべてのレコードが含まれます。 Log Analytics では、特定の監視対象 Azure リソースにスコープを設定することもできます。 これにより、リソース所有者は、そのリソースが複数のワークスペースに書き込む場合でも、自分のデータのみに焦点を絞ることができます。

スコープは常に Log Analytics ウィンドウの左上に表示されます。 アイコンは、スコープが Log Analytics ワークスペースか、それとも Application Insights アプリケーションかを示します。 アイコンなしはその他の Azure リソースを示します。

![Scope](media/scope/scope.png)

スコープは Log Analytics の起動に使用する方法によって決まり、スコープをクリックして変更できる場合もあります。 次の表は、使用されるさまざまな種類のスコープとそれぞれの詳細の一覧です。

> [!IMPORTANT]
> APM 2.1 を使用している場合、Application Insights アプリケーションは他のすべてのログ データと共に Log Analytics ワークスペースに格納され、Application Insights スコープは使用できません。 Application Insights のメニューから **[ログ]** を選択すると、 **[その他の Azure リソース]** スコープと同じように動作し、Application Insights テーブル内のそのアプリケーションのデータのみが使用できるようになります。

| クエリ スコープ | スコープ内のレコード | 選択方法 | スコープの変更 |
|:---|:---|:---|:---|
| Log Analytics ワークスペース | Log Analytics ワークスペース内のすべてのレコード。 | **[Azure Monitor]** メニューまたは **[Log Analytics ワークスペース]** メニューの **[ログ]** を選択します。  | スコープを他のリソースの種類に変更できます。 |
| Application Insights アプリケーション | Application Insights アプリケーション内のすべてのレコード。 | Application Insights の **[概要]** ページから **[分析]** を選択します。 | スコープを別の Application Insights アプリケーションにのみ変更できます。 |
| Resource group | リソース グループ内のすべてのリソースによって作成されるレコード。 複数の Log Analytics ワークスペースからのデータを含めることができます。 | リソース グループ メニューから **[ログ]** を選択します。 | スコープは変更できません。|
| サブスクリプション | サブスクリプション内のすべてのリソースによって作成されたレコード。 複数の Log Analytics ワークスペースからのデータを含めることができます。 | サブスクリプション メニューから **[ログ]** を選択します。   | スコープは変更できません。 |
| その他の Azure リソース | リソースによって作成されるレコード。 複数の Log Analytics ワークスペースからのデータを含めることができます。  | リソース メニューから **[ログ]** を選択します。<br>OR<br>**[Azure Monitor]** メニューから **[ログ]** を選択し、新しいスコープを選択します。 | スコープを同じリソースの種類にのみ変更できます。 |

### <a name="limitations-when-scoped-to-a-resource"></a>リソースをスコープにしたときの制限

クエリ スコープが Log Analytics ワークスペースまたは Application Insights アプリケーションのときは、ポータルのすべてのオプションとすべてのクエリ コマンドが使用できます。 ただし、ポータルの以下のオプションは単一のワークスペースまたはアプリケーションに関連付けられているため、リソースをスコープにした場合は使用できません。

- 保存
- クエリ エクスプローラー
- 新しいアラート ルール

リソースをスコープにした場合、そのリソースまたはリソース セットのデータがあるワークスペースはクエリ スコープに既に含まれているため、クエリで以下のコマンドを使用できません。

- [app](app-expression.md)
- [workspace](workspace-expression.md)
 

## <a name="query-limits"></a>クエリの制限
複数の Log Analytics ワークスペースにデータを書き込む Azure リソースのビジネス要件がある場合があります。 ワークスペースはリソースと同じリージョンに存在する必要はなく、1 つのワークスペースでさまざまなリージョンのリソースからデータを収集できます。  

スコープをリソースまたはリソース セットに設定する機能は、分散データを自動的に 1 つのクエリに統合できるため、Log Analytics の非常に強力な機能です。 しかし、複数の Azure リージョンにわたるワークスペースからデータを取得する必要があると、パフォーマンスが大幅に低下する可能性があります。

Log Analytics は、特定の数のリージョンが使用されているときに警告またはエラーを発行して、複数のリージョンのワークスペースにまたがるクエリから過剰なオーバーヘッドを防ぐのに役立ちます。 スコープに 5 つ以上のリージョンのワークスペースが含まれている場合、クエリで警告が表示されます。 それでも実行されますが、完了するまでに時間がかかることがあります。

![クエリ警告](media/scope/query-warning.png)

スコープに 20 以上のリージョンのワークスペースが含まれている場合、クエリの実行はブロックされます。 この場合、ワークスペース リージョンの数を減らし、クエリを再実行するように求められます。 ドロップダウンには、クエリのスコープ内のすべてのリージョンが表示されます。クエリの実行を再試行する前に、リージョンの数を減らす必要があります。

![クエリ失敗](media/scope/query-failed.png)


## <a name="time-range"></a>時間の範囲
時間範囲は、レコードが作成された時期に基づいてクエリで評価されるレコード セットを指定します。 次の表で説明しているように、これはワークスペースまたはアプリケーション内のすべてのレコードの標準プロパティによって定義されます。

| 場所 | プロパティ |
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

## <a name="next-steps"></a>次のステップ

- [Azure portal 内での Log Analytics の使用に関するチュートリアル](get-started-portal.md)を進めます。
- [クエリの作成に関するチュートリアル](get-started-queries.md)を進めます。