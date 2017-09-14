---
title: "Azure Application Insights でのユーザー、セッション、イベント分析 | Microsoft Docs"
description: "Web アプリのユーザーの統計分析について説明します。"
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 4c0040db9e232b2bb1506282cad57175c61a5b81
ms.contentlocale: ja-jp
ms.lasthandoff: 08/17/2017

---

# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Application Insights でのユーザー、セッション、およびイベントの分析

ユーザーが Web アプリをいつ使い、どのページに最も興味があり、ユーザーがどこにいて、どのようなブラウザーやオペレーティング システムを使っているかを確認しましょう。 [Azure Application Insights](app-insights-overview.md) サービスを使用してビジネスおよび使用状況テレメトリを分析します。

## <a name="get-started"></a>作業開始

Application Insights ポータルのユーザー、セッション、またはイベント ブレードにデータがまだ表示されていない場合は、[利用状況ツールの使用方法を確認](app-insights-usage-overview.md)してください。

## <a name="the-users-sessions-and-events-segmentation-tool"></a>ユーザー、セッション、およびイベント セグメンテーション ツール

3 つの使用状況ブレードは同じツールを使用して、これら 3 つの視点から、Web アプリのテレメトリを詳細に分析します。 データをフィルター処理および分割することによって、さまざまなページや機能の対象の使用状況に関する洞察を得ることができます。

* **ユーザー ツール**: アプリとその機能を使用したユーザーの数。  ブラウザーの Cookie に格納されている匿名 ID を使用してユーザーがカウントされます。 複数のブラウザーまたはコンピューターを使用する 1 人のユーザーは、複数のユーザーとしてカウントされます。
* **セッション ツール**: アプリの特定のページおよび機能を含むユーザー アクティビティのセッション数。 セッションは、ユーザーの非アクティブ状態が 30 分続いた後、または継続した 24 時間の使用の後、カウントされます。
* **イベント ツール**: アプリの特定のページと機能が使用された回数。 ページ ビューは、([インストルメント化した場合](app-insights-javascript.md)) ブラウザーがアプリからページを読み込むときにカウントされます。 

    カスタム イベントは、アプリで実行される操作の 1 回の出現を表します。通常、ボタン クリック、タスクの完了などのユーザー操作です。 コードをアプリに挿入して、[カスタム イベントを生成](app-insights-api-custom-events-metrics.md#trackevent)します。

![使用状況ツール](./media/app-insights-usage-segmentation/users.png)

## <a name="querying-for-certain-users"></a>特定のユーザーのクエリ実行 

[ユーザー] ツールの上部にあるクエリ オプションを調整して、さまざまなユーザー グループを調査します。 

* 次を使用した: カスタム イベントとページ ビューを選択します。 
* 期間: 時間範囲を選択します。 
* By (フィルター方法): 期間またはブラウザーや市などのプロパティごとにデータのバケットする方法を選択します。 
* 次で分割: データの分割またはセグメント化に使用するプロパティを選択します。 
* フィルターの追加: ブラウザー、市などのプロパティに基づいて特定のユーザー、セッション、またはイベントに対するクエリ実行を制限します。 
 
## <a name="saving-and-sharing-reports"></a>レポートの保存と共有 
[ユーザー] レポートは、個人利用のためだけに [My Reports (マイ レポート)] セクションに保存するか、[共有レポート] セクションのこの Application Insights リソースにアクセスできる他のすべてのユーザーと共有できます。  
 
レポートの保存時、またはそのプロパティの編集時に [現在の相対時間の範囲] を選択してレポートを保存すると、レポートで固定された期間でデータが継続的に更新されます。  
 
データの固定セットでレポートを保存するには、[現在の絶対時間の範囲] を選択します。 Application Insights 内のデータは 90 日間のみ保存されることに注意してください。このため、絶対時間の範囲が指定されたレポートを保存してから 90 日以上が経過すると、レポートは空で表示されます。 
 
## <a name="example-instances"></a>例インスタンス

[例] インスタンス セクションには、現在のクエリに一致するいくつかの個々のユーザー、セッション、またはイベントに関する情報が表示されます。 個々の動作を集計と共に検討し調査することで、ユーザーの実際のアプリの使用方法に関する洞察を得ることができます。 
 
## <a name="insights"></a>洞察 

[洞察] サイドバーは、一般的なプロパティを共有するユーザーの大規模なクラスターを示します。 これらのクラスターでは、ユーザーがアプリをどのように使用しているかについて驚くような傾向を明らかにします。 たとえば、アプリのすべての使用における 40% のユーザーが 1 つの機能を使用していることがわかります。  


## <a name="next-steps"></a>次のステップ
- 使用状況を把握できるようにするには、[カスタム イベント](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-api-custom-events-metrics#trackevent)または[ページ ビュー](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)の送信を開始します。
- カスタム イベントまたはページ ビューを既に送信した場合は、使用状況ツールを見て、自分のサービスがユーザーにどのように使用されているかを把握します。
    - [ファネル](usage-funnels.md)
    - [保持](app-insights-usage-retention.md)
    - [ユーザー フロー](app-insights-usage-flows.md)
    - [ブック](app-insights-usage-workbooks.md)
    - [ユーザー コンテキストの追加](app-insights-usage-send-user-context.md)


