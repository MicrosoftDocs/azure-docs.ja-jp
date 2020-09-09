---
title: チュートリアル - Azure の予算を作成して管理する
description: このチュートリアルでは、使用する Azure サービスのコストの計画とアカウントについて説明します。
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: fab6422300dd3db7dd0d02055721bd57354668bf
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322281"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>チュートリアル:Azure の予算を作成して管理する

Cost Management での予算は、組織のアカウンタビリティを計画および推進するのに役立ちます。 予算では、特定の期間中に消費またはサブスクライブする Azure サービスを考慮することができます。 コストを事前に管理するために支出を他のユーザーに通知したり、支出の時間変化を監視したりするのに役立ちます。 作成した予算のしきい値を超えた場合は、通知のみがトリガーされます。 どのリソースも影響を受けることはなく、消費が停止されることはありません。 予算を使用して、コストを分析するときに支出を比較および追跡できます。

コストと使用状況データは通常 8 時間から 24 時間以内に利用できるようになり、予算は 12 時間から 14 時間ごとにこれらのコストに対して評価されます。 [コストと使用状況データの更新](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data#cost-and-usage-data-updates-and-retention)の詳細を把握するようにしてください。 予算のしきい値が満たされたとき、通常、評価後、1 時間以内に電子メールの通知が届きます。

将来の有効期限日を選択した場合、期間 (月、四半期、または年) の最後に、予算は自動的に同じ予算金額にリセットされます。 同じ予算金額でリセットされるため、予算を計上される通貨金額が将来の期間では異なる場合は、別の予算を作成する必要があります。

このチュートリアルの例では、Azure Enterprise Agreement (EA) サブスクリプションの予算を作成および編集する手順を説明します。

[Azure portal を使用してサブスクリプションに予算を適用する](https://www.youtube.com/watch?v=UrkHiUx19Po) ビデオを見て、Azureで予算を作成して支出を監視する方法を確認してください。 他の動画を視聴するには、[Cost Management の YouTube チャンネル](https://www.youtube.com/c/AzureCostManagement)にアクセスしてください。

>[!VIDEO https://www.youtube.com/embed/UrkHiUx19Po]

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure portal で予算を作成する
> * PowerShell で予算を作成して編集する
> * Azure Resource Manager テンプレートを使用して予算を作成する

## <a name="prerequisites"></a>前提条件

予算は、次の種類の Azure アカウントの種類とスコープに対してサポートされています。

- Azure ロールベースのアクセス制御 (Azure RBAC) のスコープ
    - 管理グループ
    - サブスクリプション
- Enterprise Agreement のスコープ
    - 請求先アカウント
    - 部署
    - 登録アカウント
- 個々の契約
    - 請求先アカウント
- Microsoft Customer Agreement のスコープ
    - 請求先アカウント
    - 請求プロファイル
    - 請求書セクション
    - Customer
- AWS スコープ
    - 外部アカウント
    - 外部サブスクリプション


予算を表示するには、少なくとも Azure アカウントの読み取りアクセス権が必要です。

新しいサブスクリプションをご利用の場合、予算の作成など、Cost Management の機能をすぐには使用できません。 すべての Cost Management 機能を使用できるようになるまでに、最大 48 時間かかる場合があります。

Azure EA サブスクリプションの場合、予算を表示するには読み取りアクセス権が必要です。 予算を作成し、管理するには、共同作成者のアクセス許可が必要です。

ユーザーおよびグループごとの予算については、サブスクリプションに従い、次の Azure アクセス許可 (スコープ) がサポートされています。 スコープの詳細については、「[Understand and work with scopes (スコープを理解して使用する)](understand-work-scopes.md)」を参照してください。

- 所有者 – サブスクリプションに対する予算を作成、変更、削除できます。
- 共同作成者と Cost Management 共同作成者 - 自分の予算を作成、変更、削除できます。 他のユーザーによって作成された予算の予算金額を変更できます。
- 閲覧者と Cost Management 閲覧者 - 自分がアクセス許可を持っている予算を表示できます。

Cost Management データに対するアクセス許可の割り当てについて詳しくは、「[Cost Management のデータへのアクセス許可を割り当てる](../../cost-management/assign-access-acm-data.md)」をご覧ください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

- Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="create-a-budget-in-the-azure-portal"></a>Azure portal で予算を作成する

月、四半期、または年の期間の Azure サブスクリプション予算を作成できます。

予算を作成または表示するには、Azure portal で目的のスコープを開き、メニューの **[予算]** を選択します。 たとえば、 **[サブスクリプション]** に移動し、一覧からサブスクリプションを選択して、メニューの **[予算]** を選択します。 [予算] で別のスコープ (管理グループなど) に切り替えるには、 **[スコープ]** ピルを使用します。 スコープの詳細については、「[Understand and work with scopes (スコープを理解して使用する)](understand-work-scopes.md)」を参照してください。

予算を作成すると、予算に対する現在の支出のシンプルなビューが表示されます。

**[追加]** を選択します。

![既に作成されている予算の一覧を示す例](./media/tutorial-acm-create-budgets/budgets01.png)

**[予算を作成する]** ウィンドウで、表示されているスコープが正しいことを確認します。 追加するフィルターを選択します。 フィルターを使用すると、サブスクリプション内のリソース グループや仮想マシンのようなサービスなど、特定のコストに関する予算を作成できます。 コスト分析で使用できるフィルターは、予算にも適用できます。

スコープとフィルターを特定したら、予算の名前を入力します。 次に、予算のリセット期間を月、四半期、年から選択します。 このリセット期間によって、予算によって分析される時間枠が決まります。 予算によって評価されるコストは、新しい期間が開始されるごとに 0 から開始されます。 四半期ごとの予算を作成するときは、毎月の予算と同様に動作します。 違いは、四半期の予算金額が四半期の 3 か月に均等に分割されることです。 年間予算金額はカレンダー年の 12 か月すべてに均等に分割されます。

従量課金制、MSDN、または Visual Studio サブスクリプションがある場合、請求書の請求期間がカレンダー月と合わない場合があります。 それらのサブスクリプションの種類とリソース グループの場合、請求期間またはカレンダー月に合わせた予算を作成できます。 請求期間に合わせた予算を作成するには、**請求月**、**請求四半期**、または**請求年**のリセット期間を選択します。 カレンダー月に合わせた予算を作成するには、**月**、**四半期**、または**年**のリセット期間を選択します。

次に、予算が無効になりコストの評価を停止する有効期限を特定します。

これまでに予算で選択したフィールドに基づいてグラフが表示され、予算に使用するしきい値の選択に役立ちます。 推奨される予算は、将来の期間に発生する可能性がある最大の予想コストに基づいています。 予算金額は変更できます。

![月額コスト データを使用した予算の作成を示す例 ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

予算金額を構成したら、 **[次へ]** を選択して予算アラートを構成します。 予算では、少なくとも 1 つのコストしきい値 (予算の %) とそれに対応するメール アドレスが必要です。 必要に応じて、1 つの予算に最大で 5 つのしきい値と 5 つのメール アドレスを含めることができます。 予算のしきい値が満たされたとき、通常、評価後、1 時間以内に電子メールの通知が届きます。

メールを受信したい場合は、迷惑メール フォルダーに振り分けられることのないよう、承認済みの差出人一覧に azure-noreply@microsoft.com を追加してください。 通知の詳細については、[コスト アラートの使用](../../cost-management/cost-mgt-alerts-monitor-usage-spending.md)に関するページを参照してください。

以下の例では、予算の 90% に達すると、メール アラートが生成されます。 Budgets API を使用して予算を作成する場合、アラートを受信するためにユーザーにロールを割り当てることもできます。 ユーザーへのロールの割り当ては、Azure portal ではサポートされていません。 Azure Budgets API の詳細については、[Budgets API](/rest/api/consumption/budgets) を参照してください。

アラート上限では、指定した予算しきい値が 0.01 から 1000% までの範囲でサポートされます。

![アラート条件を示す例](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

作成した予算は、コストの分析に表示されます。 支出の傾向と照らして予算を表示することは、[コストと支出の分析](../../cost-management/quick-acm-cost-analysis.md)を始めるときの最初のステップの 1 つです。

![コスト分析に表示される予算と支出の例](./media/tutorial-acm-create-budgets/cost-analysis.png)

前の例では、サブスクリプションの予算を作成しました。 リソース グループに対する予算を作成することもできます。 リソース グループに対する予算を作成する場合は、 **[コストの管理と請求]** &gt; **[サブスクリプション]** に移動してサブスクリプションを選択し、 **[リソース グループ]** を選択してリソース グループを選択し、 **[予算]** を選択して予算を**追加**します。

### <a name="create-a-budget-for-combined-azure-and-aws-costs"></a>Azure と AWS の統合コストに対する予算を作成する

リンクされた統合アカウントと共に、管理グループをコネクタに割り当てることで、Azure と AWS のコストを 1 つにまとめることができます。 同じ管理グループに、ご利用の Azure サブスクリプションを割り当てます。 そのうえで、統合したコストの予算を作成します。

1. Cost Management で **[予算]** を選択します。
1. **[追加]** を選択します。
1. **[スコープの変更]** を選択し、管理グループを選択します。
1. 完了するまで、予算の作成を続行します。

## <a name="costs-in-budget-evaluations"></a>予算評価におけるコスト

予算コスト評価に予約インスタンスと購入データが含まれるようになりました。 料金が適用される場合は、評価に料金が組み込まれるときにアラートが表示されることがあります。 [Azure portal](https://portal.azure.com) にサインインし、新しいコストを考慮するように予算のしきい値が適切に構成されているかどうか、確認することをお勧めします。 Azure で課金される料金は変更されません。 予算が、より完全なコストのセットに対して評価されるようになりました。 料金が適用されない場合、予算の動作は変わりません。

ファースト パーティの Azure 使用料金に対してのみ予算が評価されるように新しいコストをフィルター処理する場合は、予算に次のフィルターを追加します。

- パブリッシャーの種類: Azure
- 料金タイプ: 使用法

予算コストの評価は、実際のコストに基づいています。 償却費は含まれていません。 予算で使用できるフィルター オプションの詳細については、「[グループ化とフィルタリングのオプションの概要](group-filter.md)」を参照してください。

## <a name="trigger-an-action-group"></a>アクション グループをトリガーする

サブスクリプション スコープまたはリソース グループ スコープの予算を作成または編集するときに、アクション グループを呼び出すように予算を構成できます。 予算しきい値に達すると、アクション グループはさまざまなアクションを実行できます。 アクション グループは現在、サブスクリプションとリソース グループのスコープに対してのみサポートされています。 アクション グループの詳細については、「[Azure portal でのアクション グループの作成および管理](../../azure-monitor/platform/action-groups.md)」をご覧ください。 アクション グループで予算ベースの自動化を使用する方法の詳細については、「[Azure Budgets でのコストの管理](../manage/cost-management-budget-scenario.md)」をご覧ください。

アクション グループを作成または更新するには、予算の作成または編集時に **[アクション グループの管理]** を選択します。

![[アクション グループの管理] を示す予算作成の例](./media/tutorial-acm-create-budgets/manage-action-groups01.png)

次に、 **[アクション グループの追加]** を選択し、アクション グループを作成します。

![[アクション グループの追加] ボックスの画像](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

アクション グループが作成されたら、ボックスを閉じて予算に戻ります。

個々のしきい値に達したときにアクション グループを使用するように予算を構成します。 最大 5 つのしきい値がサポートされています。

![アクション グループのアラートの条件の選択を示す例](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

次の例は、50%、75%、100% に設定された予算しきい値を示しています。 それぞれ、指定のアクション グループ内の指定されたアクションをトリガーするように構成されています。

![さまざまなアクション グループとアクションの種類で構成されたアラートの条件を示す例](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

予算とアクション グループの統合は、共通アラート スキーマが無効になっているアクション グループに対してのみ機能します。 スキーマの無効化の詳細については、「[共通アラート スキーマを有効にする方法](../../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)」を参照してください。

## <a name="create-and-edit-budgets-with-powershell"></a>PowerShell で予算を作成して編集する

EA ユーザーは、Azure PowerShell モジュールを使用してプログラムから予算を作成、編集することができます。  最新バージョンの Azure PowerShell をダウンロードするには、次のコマンドを実行します。

```azurepowershell-interactive
install-module -name Az
```

次に示したのは、予算を作成するコマンドの例です。

```azurepowershell-interactive
#Sign into Azure Powershell with your account

Connect-AzAccount

#Select a subscription to to monitor with a budget

select-AzSubscription -Subscription "Your Subscription"

#Create an action group email receiver and corresponding action group

$email1 = New-AzActionGroupReceiver -EmailAddress test@test.com -Name EmailReceiver1
$ActionGroupId = (Set-AzActionGroup -ResourceGroupName YourResourceGroup -Name TestAG -ShortName TestAG -Receiver $email1).Id

#Create a monthly budget that sends an email and triggers an Action Group to send a second email. Make sure the StartDate for your monthly budget is set to the first day of the current month. Note that Action Groups can also be used to trigger automation such as Azure Functions or Webhooks.

New-AzConsumptionBudget -Amount 100 -Name TestPSBudget -Category Cost -StartDate 2020-02-01 -TimeGrain Monthly -EndDate 2022-12-31 -ContactEmail test@test.com -NotificationKey Key1 -NotificationThreshold 0.8 -NotificationEnabled -ContactGroup $ActionGroupId
```

## <a name="create-a-budget-with-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して予算を作成する

Azure Resource Manager テンプレートを使用して予算を作成できます。 テンプレートの使用については、[Azure Resource Manager テンプレートを使用した予算の作成](quick-create-budget-template.md)に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

作成した予算が不要になった場合は、詳細を確認して予算を削除します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure portal で予算を作成する
> * PowerShell で予算を作成して編集する
> * Azure Resource Manager テンプレートを使用して予算を作成する

次のチュートリアルに進んで、コスト管理データの定期的なエクスポートを作成してください。

> [!div class="nextstepaction"]
> [データをエクスポートし、管理する](tutorial-export-acm-data.md)
