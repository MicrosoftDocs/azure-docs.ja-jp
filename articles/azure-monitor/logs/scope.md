---
title: Azure Monitor Log Analytics のログ クエリのスコープ
description: Azure Monitor Log Analytics のログ クエリのスコープと時間範囲について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/09/2020
ms.openlocfilehash: 43e4e861905352c2818dfb08b8cb442bd70481c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047181"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics のログ クエリのスコープと時間範囲
[Azure portal の [Log Analytics]](../logs/log-analytics-tutorial.md) で[ログ クエリ](../logs/log-query-overview.md)を実行するとき、クエリによって評価されるデータのセットは、選択したスコープと時間範囲によって異なります。 この記事では、スコープと時間範囲、および要件に応じてそれぞれを設定する方法について説明します。 さまざまな種類のスコープの動作についても説明します。


## <a name="query-scope"></a>クエリ スコープ
クエリ スコープは、クエリによって評価されるレコードを定義します。 これには通常、単一の Log Analytics ワークスペースまたは Application Insights アプリケーション内のすべてのレコードが含まれます。 Log Analytics では、特定の監視対象 Azure リソースにスコープを設定することもできます。 これにより、リソース所有者は、そのリソースが複数のワークスペースに書き込む場合でも、自分のデータのみに焦点を絞ることができます。

スコープは常に Log Analytics ウィンドウの左上に表示されます。 アイコンは、スコープが Log Analytics ワークスペースか、それとも Application Insights アプリケーションかを示します。 アイコンなしはその他の Azure リソースを示します。

![ポータルに表示されるスコープ](media/scope/scope.png)

スコープは Log Analytics の起動に使用する方法によって決まり、スコープをクリックして変更できる場合もあります。 次の表は、使用されるさまざまな種類のスコープとそれぞれの詳細の一覧です。

> [!IMPORTANT]
> Application Insights でワークスペース ベースのアプリケーションを使用している場合、そのデータは、その他のすべてのログ データと共に Log Analytics ワークスペースに格納されます。 下位互換性のために、アプリケーションをスコープとして選択すると従来の Application Insights エクスペリエンスが得られます。 Log Analytics ワークスペースでこのデータを表示するには、このワークスペースにスコープを設定します。

| クエリ スコープ | スコープ内のレコード | 選択方法 | スコープの変更 |
|:---|:---|:---|:---|
| Log Analytics ワークスペース | Log Analytics ワークスペース内のすべてのレコード。 | **[Azure Monitor]** メニューまたは **[Log Analytics ワークスペース]** メニューの **[ログ]** を選択します。  | スコープを他のリソースの種類に変更できます。 |
| Application Insights アプリケーション | Application Insights アプリケーション内のすべてのレコード。 | アプリケーションの **[Application Insights]** メニューで **[ログ]** を選択します。 | スコープを別の Application Insights アプリケーションにのみ変更できます。 |
| Resource group | リソース グループ内のすべてのリソースによって作成されるレコード。 複数の Log Analytics ワークスペースからのデータを含めることができます。 | リソース グループ メニューから **[ログ]** を選択します。 | スコープは変更できません。|
| サブスクリプション | サブスクリプション内のすべてのリソースによって作成されたレコード。 複数の Log Analytics ワークスペースからのデータを含めることができます。 | サブスクリプション メニューから **[ログ]** を選択します。   | スコープは変更できません。 |
| その他の Azure リソース | リソースによって作成されるレコード。 複数の Log Analytics ワークスペースからのデータを含めることができます。  | リソース メニューから **[ログ]** を選択します。<br>OR<br>**[Azure Monitor]** メニューから **[ログ]** を選択し、新しいスコープを選択します。 | スコープを同じリソースの種類にのみ変更できます。 |

### <a name="limitations-when-scoped-to-a-resource"></a>リソースをスコープにしたときの制限

クエリ スコープが Log Analytics ワークスペースまたは Application Insights アプリケーションのときは、ポータルのすべてのオプションとすべてのクエリ コマンドが使用できます。 ただし、ポータルの以下のオプションは単一のワークスペースまたはアプリケーションに関連付けられているため、リソースをスコープにした場合は使用できません。

- 保存
- クエリ エクスプローラー
- 新しいアラート ルール

リソースをスコープにした場合、そのリソースまたはリソース セットのデータがあるワークスペースはクエリ スコープに既に含まれているため、クエリで以下のコマンドを使用できません。

- [app](../logs/app-expression.md)
- [workspace](../logs/workspace-expression.md)
 

## <a name="query-scope-limits"></a>クエリ スコープの制限
スコープをリソースまたはリソース セットに設定する機能は、分散データを自動的に 1 つのクエリに統合できるため、Log Analytics の非常に強力な機能です。 しかし、複数の Azure リージョンにわたるワークスペースからデータを取得する必要があると、パフォーマンスが大幅に低下する可能性があります。

Log Analytics は、特定の数のリージョンが使用されているときに警告またはエラーを発行して、複数のリージョンのワークスペースにまたがるクエリから過剰なオーバーヘッドを防ぐのに役立ちます。 スコープに 5 つ以上のリージョンのワークスペースが含まれている場合、クエリで警告が表示されます。 それでも実行されますが、完了するまでに時間がかかることがあります。

![クエリ警告](media/scope/query-warning.png)

スコープに 20 以上のリージョンのワークスペースが含まれている場合、クエリの実行はブロックされます。 この場合、ワークスペース リージョンの数を減らし、クエリを再実行するように求められます。 ドロップダウンには、クエリのスコープ内のすべてのリージョンが表示されます。クエリの実行を再試行する前に、リージョンの数を減らす必要があります。

![クエリ失敗](media/scope/query-failed.png)


## <a name="time-range"></a>時間の範囲
時間範囲は、レコードが作成された時期に基づいてクエリで評価されるレコード セットを指定します。 次のテーブルで指定しているように、これはワークスペースまたはアプリケーション内のすべてのレコードの **TimeGenerated** 列によって定義されます。 クラシック Application Insights アプリケーションの場合は、時間範囲に **timestamp** 列が使用されます。


Log Analytics ウィンドウの上部にあるタイム ピッカーから選択して、時間範囲を設定します。  定義済みの期間を選択するか、 **[カスタム]** を選択して特定の時間範囲を指定することができます。

![時刻の選択ツール](media/scope/time-picker.png)

上の表に示すように、標準の時間列を使用するクエリにフィルターを設定すると、タイム ピッカーが **[Set in query]\(クエリに設定\)** に変わり、タイム ピッカーは無効になります。 この場合、フィルターをクエリの先頭に配置し、後続の処理ではフィルター処理されたレコードを処理するだけで済むようにすると最も効率的です。

![フィルター処理されたクエリ](media/scope/query-filtered.png)

[workspace](../logs/workspace-expression.md) または [app](../logs/app-expression.md) コマンドを使用して別のワークスペースまたはクラシック アプリケーションからデータを取得する場合、タイム ピッカーの動作が異なることがあります。 スコープが Log Analytics ワークスペースのときに **app** を使用している場合、またはスコープがクラシック Application Insights アプリケーションのときに **workspace** を使用している場合、Log Analytics では、フィルターで使用される列によって時間フィルターが決まることが理解されない可能性があります。

次の例では、スコープは Log Analytics ワークスペースに設定されています。  クエリは **workspace** を使用して別の Log Analytics ワークスペースからデータを取得します。 タイム ピッカーは、予想された **TimeGenerated** 列を使用するフィルターを認識するので、 **[Set in query]\(クエリに設定\)** に変わります。

![ワークスペースのクエリ](media/scope/query-workspace.png)

ただし、クエリで **app** を使用してクラシック Application Insights アプリケーションからデータを取得する場合、Log Analytics でフィルターの **timestamp** 列が認識されず、タイム ピッカーは変更されません。 この場合、両方のフィルターが適用されます。 この例では、クエリの **where** 句で 7 日を指定した場合でも、過去 24 時間以内に作成されたレコードしかクエリに含まれません。

![アプリのクエリ](media/scope/query-app.png)

## <a name="next-steps"></a>次のステップ

- [Azure portal 内での Log Analytics の使用に関するチュートリアル](../logs/log-analytics-tutorial.md)を進めます。
- [クエリの作成に関するチュートリアル](../logs/get-started-queries.md)を進めます。