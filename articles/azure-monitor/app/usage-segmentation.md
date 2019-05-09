---
title: Azure Application Insights でのユーザー、セッション、イベント分析 | Microsoft Docs
description: Web アプリのユーザーの統計分析について説明します。
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 7d378c2f72035c3584e1f5cd3c1f0fb9a5d5c2ed
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119869"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Application Insights でのユーザー、セッション、およびイベントの分析

ユーザーが Web アプリをいつ使い、どのページに最も興味があり、ユーザーがどこにいて、どのようなブラウザーやオペレーティング システムを使っているかを確認しましょう。 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) サービスを使用してビジネスおよび使用状況テレメトリを分析します。

![Application Insights ユーザーのスクリーンショット](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>作業開始

Application Insights ポータルのユーザー、セッション、またはイベント ブレードにデータがまだ表示されていない場合は、[利用状況ツールの使用方法を確認](usage-overview.md)してください。

## <a name="the-users-sessions-and-events-segmentation-tool"></a>ユーザー、セッション、およびイベント セグメンテーション ツール

3 つの使用状況ブレードは同じツールを使用して、これら 3 つの視点から、Web アプリのテレメトリを詳細に分析します。 データをフィルター処理および分割することによって、さまざまなページや機能の対象の使用状況に関する洞察を得ることができます。

* **ユーザー ツール**:アプリとその機能を使用したユーザーの数。  ブラウザーの Cookie に格納されている匿名 ID を使用してユーザーがカウントされます。 複数のブラウザーまたはコンピューターを使用する 1 人のユーザーは、複数のユーザーとしてカウントされます。
* **セッション ツール**:アプリの特定のページおよび機能を含むユーザー アクティビティのセッション数。 セッションは、ユーザーの非アクティブ状態が 30 分続いた後、または継続した 24 時間の使用の後、カウントされます。
* **イベント ツール**:アプリの特定のページと機能が使用された回数。 ページ ビューは、([インストルメント化した場合](../../azure-monitor/app/javascript.md)) ブラウザーがアプリからページを読み込むときにカウントされます。 

    カスタム イベントは、アプリで実行される操作の 1 回の出現を表します。通常、ボタン クリック、タスクの完了などのユーザー操作です。 コードをアプリに挿入して、[カスタム イベントを生成](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)します。

## <a name="querying-for-certain-users"></a>特定のユーザーのクエリ実行

[ユーザー] ツールの上部にあるクエリ オプションを調整して、さまざまなユーザー グループを調査します。

* 表示:分析するユーザーのコーホートを選択します。
* 次を使用した:カスタム イベントとページ ビューを選択します。
* 期間:時間範囲を選択します。
* By (フィルター方法):期間またはブラウザーや市などのプロパティごとにデータのバケットする方法を選択します。
* 次で分割:データの分割またはセグメント化に使用するプロパティを選択します。 
* フィルターの追加:ブラウザー、市などのプロパティに基づいて特定のユーザー、セッション、またはイベントに対するクエリ実行を制限します。 
 
## <a name="saving-and-sharing-reports"></a>レポートの保存と共有 
[ユーザー] レポートは、個人利用のためだけに [My Reports (マイ レポート)] セクションに保存するか、[共有レポート] セクションのこの Application Insights リソースにアクセスできる他のすべてのユーザーと共有できます。

ユーザー、セッション、またはイベント レポートへのリンクを共有するには、ツールバーの **[共有]** をクリックし、リンクをコピーします。

ユーザー、セッション、またはイベント レポート内のデータのコピーを共有するには、ツールバーの **[共有]** をクリックし、**Word アイコン**をクリックして、データを含む Word 文書を作成します。 または、メイン グラフの上の **Word アイコン**をクリックします。

## <a name="meet-your-users"></a>ユーザーを確認する

**[Meet your users]\(ユーザーを確認する\)** セクションには、現在のクエリに一致する 5 人のサンプル ユーザーについての情報が表示されます。 個々の動作を集計と共に検討し調査することで、ユーザーの実際のアプリの使用方法に関する洞察を得ることができます。

## <a name="next-steps"></a>次の手順

- 使用状況を把握できるようにするには、[カスタム イベント](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent)または[ページ ビュー](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)の送信を開始します。
- カスタム イベントまたはページ ビューを既に送信した場合は、使用状況ツールを見て、自分のサービスがユーザーにどのように使用されているかを把握します。
    - [ファネル](usage-funnels.md)
    - [保持](usage-retention.md)
    - [ユーザー フロー](usage-flows.md)
    - [ブック](../../azure-monitor/app/usage-workbooks.md)
    - [ユーザー コンテキストの追加](usage-send-user-context.md)