---
title: アラートとスマート グループの状態を管理する
description: アラートとスマート グループ インスタンスの状態を管理します
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: cac926c3c56c7f58e5237b820e0328999ffe2c52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033573"
---
# <a name="manage-alert-and-smart-group-states"></a>アラートとスマート グループの状態を管理する

Azure Monitor のアラートは[アラート状態と監視条件](./alerts-overview.md)を持つようになり、スマート グループも同様に[スマート グループの状態](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)を持つようになりました。 状態の変更は、アラートまたはスマート グループにそれぞれ関連付けられた履歴にキャプチャされるようになりました。 この記事では、アラートおよびスマート グループの両方について、状態を変更するプロセスを説明します。

## <a name="change-the-state-of-an-alert"></a>アラートの状態を変更する

1. 次のさまざまな方法で、アラートの状態を変更できます。 
    * [すべてのアラート] ページで、状態を変更するアラートの横にあるチェック ボックスをクリックし、[状態の変更]/(Change State/)  をクリックします。   
    ![スクリーンショットに、[状態の変更] が選択されている [すべてのアラート] ページが示されています。](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * 特定のアラート インスタンスについての [アラートの詳細] ページで、[状態の変更]/(Change State/)  をクリックすることができます   
    ![スクリーンショットに、[アラートの状態の変更] が選択されている [アラートの詳細] ページが示されています。](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * 特定のアラート インスタンスについての [アラートの詳細] ページの [Smart Group] (スマート グループ) ペインで、目的のアラートの横にあるチェック ボックスをクリックすることができます    
    ![スクリーンショットに、一部のインスタンスにチェック マークが付けられたハートビート アラートの [アラートの詳細] ページが示されています。](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * スマート グループの [詳細] ページのメンバー アラートの一覧で、状態を変更するアラートの横にあるチェック ボックスをクリックし、[状態の変更]/(Change State/) をクリックすることができます。   
    ![スクリーンショットに、状態を変更するアラートを選択できる [スマート グループの詳細] ページが示されています。](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. [状態の変更] (Change State) をクリックすると、ポップアップが表示され、状態 (新規/確認済み/終了) を選択して必要に応じてコメントを入力できます。   
![スクリーンショットに、[詳細] のアラートの変更ダイアログ ボックスが示されています。](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. これが完了すると、それぞれのアラートの履歴に状態の変更が記録されます。 この情報は、それぞれの詳細ページを開き、履歴のセクションを確認することによって表示できます。    
![スクリーンショットに、状態変更の履歴が示されています。](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>スマート グループの状態を変更する
1. 次のさまざまな方法で、スマート グループの状態を変更できます。
    1. スマート グループのリスト ページで、状態を変更するスマート グループの横にあるチェック ボックスをクリックし、[状態の変更]/(Change State/) をクリックします  
    ![スクリーンショットに、スマートグループの [状態の変更] ページが示されていいます。](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. スマート グループの [詳細] ページで、状態の変更をクリックすることができます        
    ![スクリーンショットに、[スマート グループの状態の変更] が選択されたスマート グループの [詳細] ページが示されています。](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. [状態の変更] (Change State) をクリックすると、ポップアップが表示され、状態 (新規/確認済み/終了) を選択して必要に応じてコメントを入力できます。 
![スクリーンショットに、スマート グループの [状態の変更] ダイアログ ボックスが示されています。](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  スマート グループの状態を変更しても、個々のメンバーのアラートの状態は変更されません。

1. これが完了すると、それぞれのスマート グループの履歴に状態の変更が記録されます。 この情報は、それぞれの詳細ページを開き、履歴のセクションを確認することによって表示できます。     
![スクリーンショットに、スマート グループの変更の履歴が示されています。](./media/alerts-managing-alert-states/state-sg-history.jpg)