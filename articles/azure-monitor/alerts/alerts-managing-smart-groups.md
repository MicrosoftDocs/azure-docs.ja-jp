---
title: スマート グループを管理する
description: アラート インスタンスに対して作成されたスマート グループを管理します
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 3b736fb7e4002f44ddb5d9a34b0ae3c19e21e35a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042659"
---
# <a name="manage-smart-groups"></a>スマート グループを管理する

[スマート グループ](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)では、機械学習アルゴリズムを使用して、同時発生または類似性に基づいてアラートがグループ化されるので、ユーザーは、各アラートを個別に管理するのではなく、スマート グループを管理することができます。 この記事では、Azure Monitor でスマート グループにアクセスしてそれを使用する手順について説明します。

1. アラート インスタンスに対して作成されたスマート グループを表示するには、次のどちらかの方法を使用できます。

     1. **[Alerts Summary]\(アラートの概要\)** ページで **[スマート グループ]** をクリックします。    
    ![[Alerts Summary]\(アラートの概要\) ページのスクリーンショット。[スマート グループ] が強調表示されています。](./media/alerts-managing-smart-groups/sg-alerts-summary.jpg)
    
     1. [すべてのアラート] ページで [スマート グループ別のアラート] をクリックします。   
     ![スクリーンショットには、[すべてのアラート] ページが表示され、[スマート グループ別のアラート] が強調表示されています。](./media/alerts-managing-smart-groups/sg-all-alerts.jpg)

2. アラート インスタンスに対して作成されたすべてのスマート グループのリスト ビューが表示されます。 複数のアラートを選別する代わりに、スマート グループを処理できるようになります。   
![[すべてのアラート] ページのスクリーンショット。](./media/alerts-managing-smart-groups/sg-list.jpg)

3. いずれかのスマート グループをクリックすると詳細ページが開き、グループ化の理由とメンバーのアラートを見ることができます。 この集計により、複数のアラートを選別するのではなく、1 つのスマート グループを扱うことができるようになります。   
![[詳細] ページのスクリーンショット。](./media/alerts-managing-smart-groups/sg-details.jpg)