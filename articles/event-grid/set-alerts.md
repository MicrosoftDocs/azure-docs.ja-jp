---
title: Azure Event Grid メトリックとアクティビティ ログ操作のアラートを設定する
description: この記事では、Azure Event Grid メトリックとアクティビティ ログ操作に関するアラートを作成する方法について説明します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 48cb402e31435cb3e9390e8aeb461fcc5f90702f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572023"
---
# <a name="set-alerts-on-azure-event-grid-metrics-and-activity-logs"></a>Azure Event Grid メトリックとアクティビティ ログに関するアラートを設定する
この記事では、Azure Event Grid メトリックとアクティビティ ログ操作に関するアラートを作成する方法について説明します。 Azure Event Grid リソース (トピックとドメイン) の公開と配信の両方のメトリックに対してアラートを作成できます。 システムのトピックの場合は、[ **[メトリック]** ページを使用してアラートを作成してください](#create-alerts-using-the-metrics-page)。

## <a name="create-alerts-on-dead-lettered-events"></a>配信不能なイベントに関するアラートを作成する
次の手順は、カスタム トピックの **配信不能イベント** メトリックに関するアラートを作成する方法を示しています。 この例では、トピックの配信不能イベントの数が 10 を超えた場合に、Azure リソース グループの所有者に電子メールが送信されます。 

1. トピックの **[Event Grid Topic]** \(Event Grid トピック\) ページで、左側のメニューの **[アラート]** を選択してから、 **[+New alert rule]** \(+ 新しいアラート ルール\) を選択します。 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="[アラート] ページ - [New alert rule]\(新しいアラート ルール\) ボタン":::
2. **[アラート ルールの作成]** ページで、リソースにトピックが選択されていることを確認します。 次に、 **[条件の選択]** をクリックします。 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="[アラート] ページ - [条件の選択]":::    
3. **[シグナル ロジックの構成]** ページで、以下の操作を行います。
    1. メトリックまたはアクティビティ ログ エントリを選択します。 この例では、 **[配信不能イベント]** が選択されています。 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="配信不能イベントの選択":::        
    2. ディメンションを選択します (省略可能)。 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="シグナル ロジックを構成する":::        

        > [!NOTE]
        > **EventSubscriptionName** の **[+]** ボタンを選択して、イベントをフィルター処理するためのイベント サブスクリプション名を指定します。 
    3. 下へスクロールします。 **[アラート ロジック]** セクションで、**演算子** と **集計の種類** を選択し、**しきい値** を入力して、 **[完了]** を選択します。 この例では、配信不能イベントの合計数が 10 を超えると、アラートがトリガーされます。 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="アラート ロジック":::                
4. **[アラート ルールの作成]** ページに戻って、 **[アクション グループの選択]** をクリックします。

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="[アクション グループの選択] ボタン":::
5. ツールバーの **[アクション グループの作成]** を選択して、新しいアクション グループを作成します。 既存のアクション グループを選択することもできます。        
6. **[アクション グループの追加]** ページで、以下の操作を行います。
    1. **アクション グループの名前** を入力します。
    1. アクション グループの **短い名前** を入力します。
    1. アクション グループを作成する **Azure サブスクリプション** を選択します。
    1. アクション グループを作成する **Azure リソース グループ** を選択します。
    1. **アクションの名前** を入力します。 
    1. **アクションの種類** を選択します。 この例では、 **[電子メールの Azure Resource Manager のロール]** が選択されています。具体的には、**所有者** ロールです。 
    1. **[OK]** を選択してページを閉じます。 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="[アクション グループの追加] ページ":::                   
7. **[アラート ルールの作成]** ページに戻り、アラート ルールの名前を入力してから、 **[アラート ルールの作成]** を選択します。

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="アラート ルール名":::  
8. これで、トピックの **[アラート]** ページに、アラートがまだない場合にアラート ルールを管理するためのリンクが表示されます。 アラートがある場合は、ツールバーの **[アラート ルールの管理]** を選択します。  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="アラートの管理":::

## <a name="create-alerts-on-other-metrics-or-activity-log-operations"></a>他のメトリックまたはアクティビティ ログ操作に関するアラートを作成する
前のセクションでは、配信不能なイベントに関するアラートを作成する方法について説明しました。 他のメトリックまたはアクティビティ ログ操作に関するアラートを作成する手順も同様です。 

たとえば、配信できなかったイベントに関するアラートを作成するには、[**シグナル ロジックの構成]** ページで **[配信できなかったイベント]** を選択します。 

:::image type="content" source="./media/set-alerts/delivery-failed-events.png" alt-text="[配信できなかったイベント] を選択する":::


## <a name="create-alerts-using-the-metrics-page"></a>[メトリック] ページを使用してアラートを作成する
また、 **[メトリック]** ページを使用してアラートを作成することもできます。 手順は同じです。 システム トピックの場合は、 **[アラート]** ページを使用できないため、アラートの作成には **[メトリック]** ページのみを使用できます。 

:::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="[メトリック] ページ - アラートの作成ボタン":::   
    

> [!NOTE]
> この記事では、アラート作成時のすべての手順と設定については説明していません。 アラートの概要については、[アラートの概要](../azure-monitor/alerts/alerts-metric.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* イベント配信と再試行については、「[Event Grid によるメッセージ配信と再試行](delivery-and-retry.md)」をご覧ください。
* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。
