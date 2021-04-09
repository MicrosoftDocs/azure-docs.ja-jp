---
title: Azure DevTest Labs のアクティビティ ログ | Microsoft Docs
description: この記事では、Azure DevTest Labs でアクティビティ ログを表示する手順について説明します。
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 51bdfc6c3857a3e59d75094b4c847c80c58de045
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100582775"
---
# <a name="view-activity-logs-for-labs-in-azure-devtest-labs"></a>Azure DevTest Labs でアクティビティ ログを表示する 
1 つまたは複数のラボを作成したら、いつ、どのように、誰によってラボがアクセス、変更、および管理されるのかを監視するのが一般的です。 Azure DevTest Labs では Azure Monitor (具体的には **アクティビティ ログ**) を使用して、このようなラボに対する操作に関する情報が提供されます。 

この記事では、Azure DevTest Labs でラボに関するアクティビティ ログを表示する方法について説明します。

## <a name="view-activity-log-for-a-lab"></a>ラボに関するアクティビティ ログを表示する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[すべてのサービス]** を選択し、 **[DEVOPS]** セクションの **[DevTest Labs]** を選択します。 **[DEVOPS]** セクションで **[DevTest Labs]** の隣の [*] (星) を選択した場合。 この操作により、次に簡単にアクセスできるように左側のナビゲーション メニューに **[DevTest Labs]** が追加されます。 その後は、左側のナビゲーション メニューの **[DevTest Labs]** を選択できます。

    ![[すべてのサービス] - [DevTest Labs] を選択する](./media/devtest-lab-create-lab/all-services-select.png)
1. ラボの一覧でラボを選択します。
1. ラボのホーム ページで、左側のメニューから **[構成とポリシー]** を選択します。 

    左側のメニューで :::image type="content" source="./media/activity-logs/configuration-policies-link.png" alt-text="[構成とポリシー]"::: を選択します。
1. **[構成とポリシー]** ページの **[管理]** で、左側のメニューから **[アクティビティ ログ]** を選択します。 ラボで実行された操作のエントリが表示されます。 

    :::image type="content" source="./media/activity-logs/activity-log.png" alt-text="アクティビティ ログ":::    
1. イベントを選択すると、その詳細が表示されます。 **[概要]** ページには、操作名、タイム スタンプ、操作の実行者などの情報が表示されます。 
    
    :::image type="content" source="./media/activity-logs/stop-vm-event.png" alt-text="VM の停止イベント - 概要":::        
1. **[JSON]** タブに切り替えると、詳細が表示されます。 次の例では、VM の名前と VM 上で実行された操作 (停止) を確認できます。

    :::image type="content" source="./media/activity-logs/stop-vm-event-json.png" alt-text="VM の停止イベント - JSON":::           
1. **[変更履歴 (プレビュー)]** タブに切り替えると、変更の履歴が表示されます。 次の例では、VM 上で実行された変更を確認できます。 

    :::image type="content" source="./media/activity-logs/change-history.png" alt-text="VM の停止イベント - 変更履歴":::             
1. 変更履歴リストで変更を選択すると、変更の詳細が表示されます。 

    :::image type="content" source="./media/activity-logs/change-details.png" alt-text="VM の停止イベント - 変更の詳細":::             

アクティビティ ログの詳細については、「[Azure アクティビティ ログ](../azure-monitor/essentials/activity-log.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- アクティビティ ログの **アラート** 設定の詳細については、「[アラートを作成する](create-alerts.md)」を参照してください。
- アクティビティ ログの詳細については、「[Azure アクティビティ ログ](../azure-monitor/essentials/activity-log.md)」を参照してください。

