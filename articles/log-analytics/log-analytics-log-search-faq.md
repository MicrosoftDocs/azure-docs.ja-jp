---
title: "Log Analytics の新しいログ検索についてよく寄せられる質問 | Microsoft Docs"
description: "この記事では、よく寄せられる新しいクエリ言語への Log Analytics のアップグレードに関する質問について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/27/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: d7bd0d780c265cc15ad09a73ede8c5a886005e37
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---

# <a name="log-analytics-new-log-search-faq-and-known-issues"></a>Log Analytics の新しいログ検索についての FAQ と既知の問題

この記事では、[新しいクエリ言語への Log Analytics](log-analytics-log-search-upgrade.md) のアップグレードに関してよく寄せられる質問と既知の問題について説明します。  ワークスペースのアップグレードを決定する前に、この記事全体を確認してください。


## <a name="alerts"></a>アラート

### <a name="question-i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>質問: 警告ルールが多数あります。 アップグレード後、そのルールを新しい言語で作成し直す必要がありますか。  
いいえ。アップグレード中、警告ルールは新しい検索言語に自動変換されます。  


## <a name="computer-groups"></a>コンピューター グループ

### <a name="question-im-getting-errors-when-trying-to-use-computer-groups--has-their-syntax-changed"></a>質問: コンピューター グループを使おうとするとエラーが発生します。  構文が変更されたのですか。
はい。コンピューター グループに使用する構文は、ワークスペースがアップグレードされたときに変更されました。  詳しくは、「[Log Analytics のログ検索におけるコンピューター グループ](log-analytics-computer-groups.md)」のページをご覧ください。

### <a name="known-issue-groups-imported-from-active-directory"></a>既知の問題: Active Directory からインポートされたグループ
現在、Active Directory からインポートされたコンピューター グループを使用するクエリは作成できません。  この問題が解決されるまでの回避策として、インポートされた Active Directory グループを使用した新しいコンピューター グループを作成して、クエリで使用します。

インポートされた Active Directory グループを含んだ新しいコンピューター グループを作成するクエリの例は、次のとおりです。

    ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "AD Group Name" and TimeGenerated >= ago(24h) | distinct Computer


## <a name="dashboards"></a>ダッシュボード

### <a name="question-can-i-still-use-dashboards-in-an-upgraded-workspace"></a>質問: アップグレードされたワークスペースでこれまでどおりダッシュボードを使用できますか。
ワークスペースがアップグレードされる前に **[マイ ダッシュボード]** に追加したタイルは引き続き使用できますが、タイルを編集したり、新しいダッシュボードを追加したりすることはできません。  これまでどおり、[ビュー デザイナー](log-analytics-view-designer.md)を使用したビューの作成および編集と、ダッシュボードの作成を Azure Portal から行えます。


## <a name="log-searches"></a>ログ検索

### <a name="question-i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>質問: アップグレードされたワークスペース以外で検索を保存しました。 その検索を、新しい検索言語に自動変換できますか。
ログ検索ページで言語コンバーター ツールを使用して、1 つずつ変換できます。  ワークスペースをアップグレードしないで、複数の検索を自動的に変換することはできません。

### <a name="question-why-are-my-query-results-not-sorted"></a>質問: クエリの結果がソートされていないのはなぜですか。
既定では、新しいクエリ言語では結果の並べ替えは行われません。  [sort 演算子](https://go.microsoft.com/fwlink/?linkid=856079)を使用して、1 つまたは複数のプロパティで結果を並べ替えます。

### <a name="known-issue-search-results-in-a-list-may-include-properties-with-no-data"></a>既知の問題: 一覧の検索結果にデータのないプロパティが表示されることがある
一覧のログ検索の結果に、データのないプロパティが表示されることがあります。  アップグレードの前に、これらのプロパティが含まれないようにします。  この問題を修正し、空のプロパティが表示されないようにする予定です。

### <a name="known-issue-selecting-a-value-in-a-chart-doesnt-display-detailed-results"></a>既知の問題: グラフの値を選択すると詳細な結果が表示されない
アップグレードの前にグラフの値を選択すると、選択した値に一致するレコードの詳細な一覧が返されます。  アップグレード後は、集計行が一行だけ返されます。  現在、この問題は調査中です。

## <a name="log-search-api"></a>Log Search API

### <a name="question-does-the-log-search-api-get-updated-after-i-upgrade"></a>質問: アップグレード後に Log Search API は更新されますか。
[Log Search API](log-analytics-log-search-api.md) は、まだ新しい検索言語にアップグレードされていません。  ワークスペースをアップグレードした後でも、引き続き従来のクエリ言語をこの API とあわせて使用します。  更新版の Log Search API のドキュメントは、API が更新されたときに利用できるようになります。


## <a name="portals"></a>ポータル

### <a name="question-should-i-use-the-new-advanced-analytics-portal-or-keep-using-the-log-search-portal"></a>質問: 新しい Advanced Analytics ポータルを使用する必要はありますか。またはログ検索ポータルを使用し続ける必要はありますか。
「[Azure Log Analytics でログ クエリを作成および編集するためのポータル](log-analytics-log-search-portals.md)」で、2 つのポータルを比較できます。  利点はそれぞれに異なるため、要件に最適なものを選択できます。  Advanced Analytics ポータルでクエリを記述し、ビュー デザイナーなどの他の場所に貼り付ける点は共通です。  実行にあたっては、[考慮すべき問題](log-analytics-log-search-portals.md#advanced-analytics-portal)をお読みください。


## <a name="power-bi"></a>Power BI

### <a name="question-does-anything-change-with-powerbi-integration"></a>質問: PowerBI 統合に変更はありますか。
はい。  ワークスペースのアップグレードが完了すると、Power BI への Log Analytics データのエクスポート プロセスは動作しなくなります。  また、アップグレード前に作成した既存のスケジュールすべてが無効になります。  アップグレード後、Azure Log Analytics では Application Insights と同じプラットフォームが使用されるため、[Application Insights クエリを Power BI にエクスポートするプロセス](../application-insights/app-insights-export-power-bi.md#export-analytics-queries)と同じプロセスを使用して、Power BI に Log Analytics クエリをエクスポートします。

### <a name="known-issue-power-bi-request-size-limit"></a>既知の問題: Power BI の要求サイズの制限
現在、Power BI にエクスポートできる Log Analytics クエリには、8 MB のサイズ制限が設けられています。  この制限は、まもなく緩和される予定です。


##<a name="powershell-cmdlets"></a>PowerShell コマンドレット

### <a name="question-does-the-log-search-powershell-cmdlet-get-updated-after-i-upgrade"></a>質問: アップグレード後にログ検索の PowerShell コマンドレットは更新されますか。
[Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/Get-AzureRmOperationalInsightsSearchResults) は、まだ新しい検索言語にアップグレードされていません。  ワークスペースをアップグレードした後でも、引き続き従来のクエリ言語をこのコマンドレットとあわせて使用します。  更新版のドキュメントは、コマンドレットが更新されたときに利用できるようになります。


## <a name="resource-manager-templates"></a>Resource Manager テンプレート

### <a name="question-can-i-create-an-upgraded-workspace-with-a-resource-manager-template"></a>質問: アップグレードされたワークスペースを、リソース マネージャー テンプレートを使用して作成できますか。
はい。  API のプレビュー バージョン 2017-03-15 を使用し、次の例のように、テンプレートに**機能**セクションを含める必要があります。

    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2017-03-15-preview",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceRegion')]",
            "properties": {
                "sku": {
                    "name": "Free"
                },
                "features": {
                    "legacy": 0,
                    "searchVersion": 1
                }
            }
        }
    ],



## <a name="solutions"></a>解決方法

### <a name="question-will-my-solutions-continue-to-work"></a>質問: ソリューションは引き続き機能しますか。
すべてのソリューションは、アップグレードされたワークスペースでこれまでどおり機能しますが、新しいクエリ言語に変換すると、パフォーマンスが向上します。  既知の問題の中には、このセクションで説明されている解決方法が存在するものがあります。

### <a name="known-issue-capacity-and-performance-solution"></a>既知の問題: Capacity and Performance のソリューション
[Capacity and Performance](log-analytics-capacity.md) ビューの何か所かは、空になっている場合があります。  この問題の修正は、まもなく利用できるようになります。

### <a name="known-issue-device-health-solution"></a>既知の問題: デバイスの正常性ソリューション
[デバイスの正常性ソリューション](https://docs.microsoft.com/windows/deployment/update/device-health-monitor)は、アップグレードされたワークスペースから情報を収集しません。  この問題の修正は、まもなく利用できるようになります。

### <a name="known-issue-application-insights-connector"></a>既知の問題: Application Insights Connector
[Application Insights Connector ソリューション](log-analytics-app-insights-connector.md)のパースペクティブは、現在、アップグレード後のワークスペースではサポートされていません。  この問題の修正は、現在分析中です。

## <a name="upgrade-process"></a>アップグレード プロセス

### <a name="question-i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>質問: ワークスペースが複数あります。 すべてのワークスペースを同時にアップグレードできますか。  
いいえ。  アップグレードはワークスペースごとに 1 つずつ適用されます。 現時点では、多数のワークスペースを一度にアップグレードすることはできません。 ワークスペースをアップグレードすると、そのワークスペースの他のユーザーも影響を受けることに注意してください。  

### <a name="question-will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>質問: アップグレードすると、ワークスペースで収集された既存のログ データは変更されますか。  
いいえ。 ワークスペース検索で使用できるログ データは、アップグレードの影響を受けません。 保存した検索、警告、およびビューは、新しい検索言語に自動的に変換されます。  

### <a name="question-what-happens-if-i-dont-upgrade-my-workspace"></a>質問: ワークスペースをアップグレードしないとどうなりますか。  
従来のログ検索は、数か月後に廃止される予定です。 その時点でアップグレードされていないワークスペースは、自動的にアップグレードされます。

### <a name="question-i-didnt-choose-to-upgrade-but-my-workspace-has-been-upgraded-anyway-what-happened"></a>質問: アップグレードするように選択しなかったにもかかわらず、ワークスペースがアップグレードされました。 なぜでしょうか?  
このワークスペースの他の管理者が、ワークスペースをアップグレードした可能性があります。 新しい言語が一般公開された時点で、すべてのワークスペースが自動的にアップグレードされることに注意してください。  

### <a name="question-i-have-upgraded-by-mistake-and-now-need-to-cancel-it-and-restore-everything-back-what-should-i-do"></a>質問: 誤ってアップグレードしてしまいました。アップグレードをキャンセルして、すべてを復元する必要があります。 どうすればよいですか。  
ご安心ください。  アップグレード前のワークスペースのスナップショットが作成されているため、復元できます。 ただし、アップグレード後に保存した検索、警告、またはビューは失われることに注意してください。  ワークスペース環境を復元するには、「[アップグレード後の復元](log-analytics-log-search-upgrade.md#can-i-go-back-after-i-upgrade)」の手順に従ってください。


## <a name="views"></a>ビュー

### <a name="question-how-do-i-create-a-new-view-with-view-designer"></a>質問: ビュー デザイナーで新しいビューを作成するにはどうすればいいですか。
アップグレードの前に、メインのダッシュボードのタイルから、ビュー デザイナーで新しいビューを作成できます。  ワークスペースがアップグレードされると、このタイルは削除されます。  左側のメニュー ボタンで緑色の + ボタンをクリックすると、OMS ポータルのビュー デザイナーで新しいビューを作成できます。

### <a name="known-issue-see-all-option-for-line-charts-in-views-doesnt-result-in-a-line-chart"></a>既知の問題: ビューの折れ線グラフの [すべて表示] オプションを表示すると折れ線グラフにならない
ビューの折れ線グラフの下部にある *[すべて表示]* オプションをクリックすると、表が表示されます。  アップグレードする前は、折れ線グラフが表示されます。  この問題は、適用可能な変更を行うために分析中です。


## <a name="next-steps"></a>次のステップ

- [新しい Log Analytics クエリ言語へのワークスペースのアップグレード](log-analytics-log-search-upgrade.md)について確認します。

