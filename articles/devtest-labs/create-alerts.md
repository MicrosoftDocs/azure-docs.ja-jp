---
title: Azure DevTest Labs でラボのアクティビティ ログ アラートを作成する
description: この記事では、Azure DevTest Labs でラボのアクティビティ ログ アラートを作成する手順について説明します。
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: f774e3291961c58f55a9ed24026535e076235b98
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588772"
---
# <a name="create-activity-log-alerts-for-labs-in-azure-devtest-labs"></a>Azure DevTest Labs でラボのアクティビティ ログ アラートを作成する
この記事では Azure DevTest Labs でラボのアクティビティ ログ アラート (VM が作成されたとき、VM が削除されたときなど) を作成する方法について説明します。

## <a name="create-alerts"></a>アラートを作成する
この例では、サブスクリプションの所有者に電子メールを送信するアクションを使用して、ラボでのすべての管理操作に関するアラートを作成します。 

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Azure portal の検索バーで、「**モニター**」と入力し、結果一覧から **[モニター]** を選択します。 

    :::image type="content" source="./media/activity-logs/search-monitor.png" alt-text="モニターの検索":::        
1. 左側のメニューで **[アラート]** を選択し、ツールバーで **[新しいアラート ルール]** を選択します。 

    :::image type="content" source="./media/activity-logs/alerts-page.png" alt-text="[アラート] ページ":::    
1. **[アラート ルールの作成]** ページで、 **[リソースの選択]** をクリックします。 

    :::image type="content" source="./media/activity-logs/select-resource-link.png" alt-text="アラート対象のリソースを選択します":::        
1. **[Filter by resource type]** \(リソースの種類別でフィルターを適用\) に **[DevTest Labs]** を選択し、一覧からラボを選択して、 **[完了]** を選択します。

    :::image type="content" source="./media/activity-logs/select-lab-resource.png" alt-text="リソースとしてラボを選択します":::
1. **[アラート ルールの作成]** ページに戻って、 **[条件の選択]** をクリックします。 

    :::image type="content" source="./media/activity-logs/select-condition-link.png" alt-text="[条件の選択] リンク":::    
1. **[シグナル ロジックの構成]** ページで、DevTest Labs でサポートされているシグナルを選択します。 

    :::image type="content" source="./media/activity-logs/select-signal.png" alt-text="シグナルを選択する":::
1. **[イベント レベル]** ([詳細]、[情報]、[警告]、[エラー]、[重大]、[すべて])、 **[状態]** ([失敗]、[開始]、[成功])、およびイベントを **開始した主体** によってフィルター処理します。 
1. **[完了]** を選択して、条件の構成を完了します。 

    :::image type="content" source="./media/activity-logs/configure-signal-logic-done.png" alt-text="[シグナル ロジックの構成] - [完了]":::
1. スコープ (ラボ) とアラートの条件を指定しました。 次に、アクション グループ、および条件が満たされたときに実行されるアクションを指定する必要があります。 **[アラート ルールの作成]** ページに戻って、 **[アクション グループの選択]** を選択します。 

    :::image type="content" source="./media/activity-logs/select-action-group-link.png" alt-text="[アクション グループの選択] リンク":::
1. ツール バーの **[アクション グループの作成]** リンクを選択します。 

    :::image type="content" source="./media/activity-logs/create-action-group-link.png" alt-text="[アクション グループの作成] リンク":::
1. **[アクション グループの追加]** ページで、以下の操作を行います。
    1. アクション グループの **名前** を入力します。
    1. アクション グループの **短い名前** を入力します。 
    1. アラートを作成する **[リソース グループ]** を選択します。 
    1. **アクションの名前** を入力します。 
    1. **[アクションの種類]** を選択します (この例では、 **[Email Azure Resource Manager Role]** \(Azure Resource Manager ロールに電子メールを送信する\))。 

        :::image type="content" source="./media/activity-logs/add-action-group.png" alt-text="[アクション グループの追加] ページ":::
    1. **[Email Azure Resource Manager Role]** \(Azure Resource Manager ロールに電子メールを送信する\) ページで、ロールを選択します。 この例では **[所有者]** です。 **[OK]** をクリックします。 

        :::image type="content" source="./media/activity-logs/select-role.png" alt-text="ロールの選択":::            
    1. **[アクション グループの追加]** ページで、 **[OK]** を選択します。 
1. 次に、 **[アラート ルールの作成]** ページで、アラート ルールの名前を入力して、 **[OK]** を選択します。 

    :::image type="content" source="./media/activity-logs/create-alert-rule-done.png" alt-text="[アラート ルールの作成] - 完了":::

## <a name="view-alerts"></a>アラートを表示する 
1. **[アラート]** に (この例では) すべての管理操作に対するアラートが表示されます。 アラートは、表示されるまでに時間がかかることがあります。 

    :::image type="content" source="./media/activity-logs/alerts.png" alt-text="ダッシュボードのアラートが表示されている画面キャプチャ。":::
1. 列 ( **[合計アラート数]** など) の数値を選択すると、発生したアラートが表示されます。 

    :::image type="content" source="./media/activity-logs/all-alerts.png" alt-text="[すべてのアラート]":::
1. アラートを選択すると、その詳細が表示されます。 

    :::image type="content" source="./media/activity-logs/alert-details.png" alt-text="アラートの詳細":::
1. この例では、次の例に示されている内容の電子メールも受け取ります。 

    :::image type="content" source="./media/activity-logs/alert-email.png" alt-text="アラートの電子メール":::

## <a name="next-steps"></a>次のステップ
- さまざまなアクションの種類を使用したアクション グループの作成の詳細については、「[Azure Portal でのアクション グループの作成および管理](../azure-monitor/alerts/action-groups.md)」を参照してください。
- アクティビティ ログの詳細については、「[Azure アクティビティ ログ](../azure-monitor/essentials/activity-log.md)」を参照してください。
- アクティビティ ログ アラートの設定の詳細については、「[アクティビティ ログ アラート](../azure-monitor/alerts/activity-log-alerts.md)」を参照してください。

