---
title: Azure AD でログ分析ウィザードを構成する | Microsoft Docs
description: ログ分析の構成方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: markvi
author: MarkusVi
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca84fa57cb3a26337038275d1b7491154915c90e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574371"
---
# <a name="tutorial-configure-the-log-analytics-wizard"></a>チュートリアル:ログ分析ウィザードを構成する


このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 監査ログとサインイン ログ用に Log Analytics ワークスペースを構成する
> * Kusto クエリ言語 (KQL) を使用してクエリを実行する
> * 特定のアカウントが使用されたときにアラートを送信するアラート ルールを作成する
> * クイックスタート テンプレートを使用してカスタム ブックを作成する
> * 既存のブック テンプレートにクエリを追加する

## <a name="prerequisites"></a>前提条件

- P1 ライセンス管理者が少なくとも 1 人いる Azure サブスクリプション。Azure サブスクリプションを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/free/)できます。

- Azure AD テナント。

- Azure AD テナントの "グローバル管理者" または "セキュリティ管理者" であるユーザー。


以下の記事の内容を確認します。

- [チュートリアル:Azure リソースからリソース ログを収集して分析する](../../azure-monitor/essentials/tutorial-resource-logs.md)

- [アクティビティ ログを Log Analytics と統合する方法](./howto-integrate-activity-logs-with-log-analytics.md)

- [Azure AD で緊急アクセス用アカウントを管理する](../roles/security-emergency-access.md)

- [KQL クイック リファレンス](/azure/data-explorer/kql-quick-reference)

- [Azure Monitor ブック](../../azure-monitor/visualize/workbooks-overview.md)



## <a name="configure-a-workspace"></a>ワークスペースを設定する 

この手順では、監査ログとサインイン ログ用に Log Analytics ワークスペースを構成する方法を示します。
Log Analytics ワークスペースの構成は、主に 2 つの手順で構成されています。
 
1. Log Analytics ワークスペースの作成
2. 診断設定の設定

**ワークスペースを構成するには:** 


1. [Azure Portal](https://portal.azure.com) にグローバル管理者としてサインインします。

2. "**Log Analytics ワークスペース**" を検索します。

    ![リソース サービスとドキュメントを検索する](./media/tutorial-log-analytics-wizard/search-services.png)

3. Log Analytics ワークスペース ページで、 **[追加]** をクリックします。

    ![スクリーンショットは、Log Analytics ワークスペース ページの [追加] ボタンを示しています。](./media/tutorial-log-analytics-wizard/add.png)

4.  **[Log Analytics ワークスペースの作成]** ページで、次の手順を実行します。

    ![ログ分析ワークスペースを作成する](./media/tutorial-log-analytics-wizard/create-log-analytics-workspace.png)

    1. サブスクリプションを選択します。

    2. リソース グループを選択します。
 
    3. **[名前]** ボックスに、名前を入力します (例: MytestWorkspace1)。

    4. 自分のリージョンを選択します。

5. **[Review + Create]\(レビュー + 作成\)** をクリックします。

    ![確認と作成](./media/tutorial-log-analytics-wizard/review-create.png)

6. **[作成]** をクリックして、デプロイが成功するまで待ちます。 新しいワークスペースを表示するには、ページの更新が必要な場合があります。

    ![作成](./media/tutorial-log-analytics-wizard/create-workspace.png)

7. **Azure Active Directory** を検索します。

    ![Azure Search 内の Azure Active Directory を示すスクリーンショット。](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

8. **[監視]** セクションで、 **[診断設定]** をクリックします。

    ![[監視] で選択された [診断設定] を示すスクリーンショット。](./media/tutorial-log-analytics-wizard/diagnostic-settings.png)

9. **[診断設定]** ページで、 **[診断設定を追加する]** をクリックします。

    ![診断設定を追加する](./media/tutorial-log-analytics-wizard/add-diagnostic-setting.png)

10. **[診断設定]** ページで、次の手順を実行します。

    ![診断設定を選択する](./media/tutorial-log-analytics-wizard/select-diagnostics-settings.png)

    1. **[カテゴリの詳細]** で、 **[AuditLogs]** と **[SigninLogs]** を選択します。

    2. **[宛先の詳細]** で、 **[Log Analytics への送信]** を選択し、新しい Log Analytics ワークスペースを選択します。 
   
    3. **[保存]** をクリックします。 

## <a name="run-queries"></a>クエリを実行する  

この手順では、**Kusto クエリ言語 (KQL)** を使用してクエリを実行する方法を示します。


**クエリを実行するには:**


1. [Azure Portal](https://portal.azure.com) にグローバル管理者としてサインインします。

2. **Azure Active Directory** を検索します。

    ![Azure Search 内の Azure Active Directory を示すスクリーンショット。](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. **[監視]** セクションで、 **[ログ]** をクリックします。

4. **[ログ]** ページで **[Get Started]\(開始\)** をクリックします。

5. **検索* ボックスに、クエリを入力します。

6. **[実行]** をクリックします。  


### <a name="kql-query-examples"></a>KQL クエリの例

入力データから 10 個のエントリをランダムに取得する:

`SigninLogs | take 10`

条件付きアクセスが成功したサインインを確認する

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus` 


成功した数をカウントする

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus | count`


成功したサインインの数をユーザー別および日別に集計する:

`SigninLogs | where ConditionalAccessStatus == "success" | summarize SuccessfulSign-ins = count() by UserDisplayName, bin(TimeGenerated, 1d)` 


ユーザーが特定の期間に特定の操作を行った回数を表示する:

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | summarize count() by OperationName, Identity`


操作名で結果をピボットする

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | project OperationName, Identity | evaluate pivot(OperationName)`


内部結合を使用して監査ログとサインイン ログをマージする:

`AuditLogs |where OperationName contains "Add User" |extend UserPrincipalName = tostring(TargetResources[0].userPrincipalName) | |project TimeGenerated, UserPrincipalName |join kind = inner (SigninLogs) on UserPrincipalName |summarize arg_min(TimeGenerated, *) by UserPrincipalName |extend SigninDate = TimeGenerated` 


クライアント アプリの種類別にサインイン数を表示する:

`SigninLogs | summarize count() by ClientAppUsed`

日別にサインイン数をカウントする:

`SigninLogs | summarize NumberOfEntries=count() by bin(TimeGenerated, 1d)`

5 つのエントリをランダムに取得し、結果に表示する列を射影する:

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `


上位の 5 つを降順で取得し、表示する列を射影する

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `

値を他の 2 つの列に結合して新しい列を作成する:

`SigninLogs | limit 10 | extend RiskUser = strcat(RiskDetail, "-", Identity) | project RiskUser, ClientAppUsed`




## <a name="create-an-alert-rule"></a>アラート ルールを作成する  

この手順では、緊急用アカウントが使用されたときにアラートを送信する方法を示します。

**アラート ルールを作成するには:**


1. [Azure Portal](https://portal.azure.com) にグローバル管理者としてサインインします。

2. **Azure Active Directory** を検索します。

    ![Azure Search 内の Azure Active Directory を示すスクリーンショット。](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. **[監視]** セクションで、 **[ログ]** をクリックします。

4. **[ログ]** ページで **[Get Started]\(開始\)** をクリックします。

5. **検索** ボックスに、「`SigninLogs |where UserDisplayName contains "BreakGlass" | project UserDisplayName`」と入力します。

6. **[実行]** をクリックします。  

7. ツール バーの **[新しいアラート ルール]** をクリックします。

    ![新しいアラート ルール](./media/tutorial-log-analytics-wizard/new-alert-rule.png)

8. **[アラート ルールの作成]** ページで、スコープが正しいことを確認します。

9. **[条件]** で、 **[Whenever the average custom log search is greater than <logic undefined> count]\(平均カスタム ログ検索が &lt;logic undefined&gt; の数より大きい場合\)** をクリックします。

    ![既定の条件](./media/tutorial-log-analytics-wizard/default-condition.png)

10. **[シグナル ロジックの構成]** ページの **[アラート ロジック]** セクションで、次の手順を実行します。

    ![アラート ロジック](./media/tutorial-log-analytics-wizard/alert-logic.png)

    1. **[基準]** として、 **[結果の数]** を選択します。

    2. **[演算子]** として、 **[Greater than]\(次の値より大きい\)** を選択します。

    3. **[しきい値]** として、 **[0]** を選択します。 

11. **[シグナル ロジックの構成]** ページの **[評価基準]** セクションで、次の手順を実行します。

    ![評価基準](./media/tutorial-log-analytics-wizard/evaluated-based-on.png)

    1. **[期間 (分単位)]** として、 **[5]** を選択します。

    2. **[頻度 (分単位)]** として、 **[5]** を選択します。

    3. **[Done]** をクリックします。 

12. **[アクション グループ]** で、 **[アクション グループの選択]** をクリックします。 

    ![アクション グループ](./media/tutorial-log-analytics-wizard/action-group.png)

13. **[このアラート ルールにアタッチするアクション グループを選択する]** で、 **[アクション グループの作成]** をクリックします。 

    ![アクション グループの作成](./media/tutorial-log-analytics-wizard/create-action-group.png)

14. **[アクション グループの作成]** ページで、次の手順を実行します。

    ![インスタンスの詳細](./media/tutorial-log-analytics-wizard/instance-details.png)

    1. **[アクション グループ名]** ボックスに「**My action group**」と入力します。

    2. **[表示名]** ボックスに「**My action**」と入力します。

    3. **[Review + create]\(レビュー + 作成\)** をクリックします。 

    4. **Create** をクリックしてください。


15. **[アクションをカスタマイズする]** で、次の手順を実行します。

    ![アクションをカスタマイズする](./media/tutorial-log-analytics-wizard/customize-actions.png)

    1. **[メールの件名]** を選択します。

    2. **[件名]** ボックスに「`Breakglass account has been used`」と入力します

16. **[アラート ルールの詳細]** で、次の手順を実行します。

    ![アラート ルールの詳細](./media/tutorial-log-analytics-wizard/alert-rule-details.png)

    1. **[アラート ルール名]** ボックスに「`Breakglass account`」と入力します

    2. **[説明]** ボックスに「`Your emergency access account has been used`」と入力します

17. **[アラート ルールの作成]** をクリックします。   


## <a name="create-a-custom-workbook"></a>カスタム ブックを作成する

この手順では、クイックスタート テンプレートを使用して新しいブックを作成する方法を示します。




1. [Azure Portal](https://portal.azure.com) にグローバル管理者としてサインインします。

2. **Azure Active Directory** を検索します。

    ![Azure Search 内の Azure Active Directory を示すスクリーンショット。](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. **[監視]** セクションで、 **[ブック]** をクリックします。

    ![スクリーンショットは、Azure portal メニューの、[Workbooks] が選択されている [監視] を示しています。](./media/tutorial-log-analytics-wizard/workbooks.png)

4. **[クイックスタート]** セクションで、 **[Empty]\(空\)** をクリックします。

    ![クイック スタート](./media/tutorial-log-analytics-wizard/quick-start.png)

5. **[追加]** をクリックします。

    ![ブックの追加](./media/tutorial-log-analytics-wizard/add-workbook.png)

6. **[テキストの追加]** をクリックします。

    ![テキストの追加](./media/tutorial-log-analytics-wizard/add-text.png)


7. ボックスに「`# Client apps used in the past week`」と入力し、 **[編集が完了しました]** をクリックします。

    ![ブックのテキスト](./media/tutorial-log-analytics-wizard/workbook-text.png)

8. 新しいブックで、 **[追加]** をクリックし、 **[クエリの追加]** をクリックします。

    ![クエリの追加](./media/tutorial-log-analytics-wizard/add-query.png)

9. クエリのボックスに「`SigninLogs | where TimeGenerated > ago(7d) | project TimeGenerated, UserDisplayName, ClientAppUsed | summarize count() by ClientAppUsed`」と入力します。

10. [**クエリの実行**] をクリックします。

    ![[クエリの実行] ボタンを示すスクリーンショット。](./media/tutorial-log-analytics-wizard/run-workbook-query.png)

11. ツール バーの **[視覚化]** で、 **[円グラフ]** をクリックします。

    ![円グラフ](./media/tutorial-log-analytics-wizard/pie-chart.png)

12. **[編集が完了しました]** をクリックします。

    ![編集が完了しました](./media/tutorial-log-analytics-wizard/done-workbook-editing.png)



## <a name="add-a-query-to-a-workbook-template"></a>ブック テンプレートにクエリを追加する  

この手順では、既存のブック テンプレートにクエリを追加する方法を示します。 この例は、条件付きアクセスの成功と失敗の分布を示すクエリに基づいています。


1. [Azure Portal](https://portal.azure.com) にグローバル管理者としてサインインします。

2. **Azure Active Directory** を検索します。

    ![Azure Search 内の Azure Active Directory を示すスクリーンショット。](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. **[監視]** セクションで、 **[ブック]** をクリックします。

    ![スクリーンショットは、[Workbooks] が選択されている、メニュー [監視] を示しています。ブックが選択されているメニューの監視を示しています。](./media/tutorial-log-analytics-wizard/workbooks.png)

4. **[条件付きアクセス]** セクションで、 **[Conditional Access Insights and Reporting]\(条件付きアクセスに関する分析情報とレポート\)** をクリックします。

    ![[Conditional Access Insights and Reporting]\(条件付きアクセスに関する分析情報とレポート\) オプションを示すスクリーンショット。](./media/tutorial-log-analytics-wizard/conditional-access-template.png)

5. ツール バーで、 **[編集]** をクリックします。

    ![[編集] ボタンを示すスクリーンショット。](./media/tutorial-log-analytics-wizard/edit-workbook-template.png)

6. ツール バーで、3 つのドット、 **[追加]** 、 **[クエリの追加]** の順にクリックします。

    ![ブック クエリを追加する](./media/tutorial-log-analytics-wizard/add-custom-workbook-query.png)

7. クエリのボックスに「`SigninLogs | where TimeGenerated > ago(20d) | where ConditionalAccessPolicies != "[]" | summarize dcount(UserDisplayName) by bin(TimeGenerated, 1d), ConditionalAccessStatus`」と入力します。

8. [**クエリの実行**] をクリックします。

    ![このクエリを実行する [クエリの実行] ボタンを示すスクリーンショット。](./media/tutorial-log-analytics-wizard/run-workbook-insights-query.png)

9. **[期間]** をクリックし、 **[クエリに設定します]** を選択します。

10. **[視覚化]** をクリックし、 **[横棒グラフ]** を選択します。 

11. **[詳細設定]** をクリックし、グラフのタイトルとして「`Conditional Access status over the last 20 days`」と入力し、 **[編集が完了しました]** をクリックします。 

    ![グラフのタイトルを設定する](./media/tutorial-log-analytics-wizard/set-chart-title.png)








## <a name="next-steps"></a>次のステップ

次の記事に進んで、Azure portal を使用してデバイス ID を管理する方法を学習します。
> [!div class="nextstepaction"]
> [Monitoring](overview-monitoring.md)