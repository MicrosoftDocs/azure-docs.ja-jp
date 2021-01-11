---
title: アラートとスマート グループの状態を管理する
description: アラートとスマート グループ インスタンスの状態を管理します
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: a22d7b7b962b36f93ee804c64b3bc6b08ecceb0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667518"
---
# <a name="manage-alert-and-smart-group-states"></a>アラートとスマート グループの状態を管理する

Azure Monitor のアラートは[アラート状態と監視条件](https://aka.ms/azure-alerts-overview)を持つようになり、スマート グループも同様に[スマート グループの状態](https://aka.ms/smart-groups)を持つようになりました。 状態の変更は、アラートまたはスマート グループにそれぞれ関連付けられた履歴にキャプチャされるようになりました。 この記事では、アラートおよびスマート グループの両方について、状態を変更するプロセスを説明します。

## <a name="change-the-state-of-an-alert"></a>アラートの状態を変更する

1. 次のさまざまな方法で、アラートの状態を変更できます。 
    * [すべてのアラート] ページで、状態を変更するアラートの横にあるチェック ボックスをクリックし、[状態の変更]\(Change State\)  をクリックします。   
    ![監視](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * 特定のアラート インスタンスについての [アラートの詳細] ページで、[状態の変更]\(Change State\)  をクリックすることができます   
    ![監視](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * 特定のアラート インスタンスについての [アラートの詳細] ページの [Smart Group] (スマート グループ) ペインで、目的のアラートの横にあるチェック ボックスをクリックすることができます    
    ![監視](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * スマート グループの [詳細] ページのメンバー アラートの一覧で、状態を変更するアラートの横にあるチェック ボックスをクリックし、[状態の変更]\(Change State\) をクリックすることができます。   
    ![監視](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. [状態の変更] (Change State) をクリックすると、ポップアップが表示され、状態 (新規/確認済み/終了) を選択して必要に応じてコメントを入力できます。   
![監視](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. これが完了すると、それぞれのアラートの履歴に状態の変更が記録されます。 この情報は、それぞれの詳細ページを開き、履歴のセクションを確認することによって表示できます。    
![監視](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>スマート グループの状態を変更する
1. 次のさまざまな方法で、スマート グループの状態を変更できます。
    1. スマート グループのリスト ページで、状態を変更するスマート グループの横にあるチェック ボックスをクリックし、[状態の変更]\(Change State\) をクリックします  
    ![監視](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. スマート グループの [詳細] ページで、状態の変更をクリックすることができます        
    ![監視](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. [状態の変更] (Change State) をクリックすると、ポップアップが表示され、状態 (新規/確認済み/終了) を選択して必要に応じてコメントを入力できます。 
![Monitoring](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  スマート グループの状態を変更しても、個々のメンバーのアラートの状態は変更されません。

1. これが完了すると、それぞれのスマート グループの履歴に状態の変更が記録されます。 この情報は、それぞれの詳細ページを開き、履歴のセクションを確認することによって表示できます。     
![監視](./media/alerts-managing-alert-states/state-sg-history.jpg)

