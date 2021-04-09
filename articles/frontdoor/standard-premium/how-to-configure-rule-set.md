---
title: 'Azure Front Door: Front Door ルール セットを構成する'
description: この記事では、ルール セットを構成する方法についてのガイダンスを提供します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 6863c492059ccee152ecf3d03a09e61793576bcb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715600"
---
# <a name="configure-a-rule-set-with-azure-front-door-standardpremium-preview"></a>Azure Front Door Standard/Premium (プレビュー) でルール セットを構成する

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

この記事では、Azure portal でルール セットを作成し、最初に設定する一連のルールを作成する方法について説明します。 その後、ルール セット ページまたはエンドポイント マネージャーから、ルール セットをルートに関連付ける方法について説明します。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

* ルール セットを構成する前に、まず Azure Front Door Standard/Premium を作成する必要があります。 詳細については、「[クイックスタート : Azure Front Door Standard/Premium プロファイルを作成する](create-front-door-portal.md)」を参照してください。

## <a name="configure-rule-set-in-azure-portal"></a>Azure portal でルール セットを構成する

1. Front Door プロファイル内で、 **[設定]** の下にある **[ルール セット]** を選択します。 **[追加]** を選択し、ルール セットの名前を指定します。

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set-1.png" alt-text="ルール セットのランディング ページのスクリーンショット。":::
    
1. **[ルールの追加]** を選択して、最初のルールを作成します。 ルール名を指定します。 その後、 **[条件の追加]** または **[アクションの追加]** を選択してルールを定義します。 1 つのルールに対して、最大で 10 個の条件と 5 つのアクションを追加できます。 この例では、サーバー変数を使用して、URL に *contoso* を含む要求に対し、8Geo-country* という応答ヘッダーを追加します。

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set.png" alt-text="[ルール セットの構成] ページのスクリーンショット。":::
    
    > [!NOTE]
    > * 条件またはアクションをルールから削除するには、特定の条件またはアクションの右側にあるごみ箱を使用します。
    > * すべての受信トラフィックに適用されるルールを作成するには、条件を指定しないでください。
    > * 特定のルールが満たされた場合に残りのルールの評価を停止するには、 **[残りの規則の評価を停止する]** をオンにします。 このオプションをオンにした場合、ルール セット内の残りのすべてのルールは、条件一致が満たされているかどうかに関わらず、実行されません。  

1. ルール セット内のルールの優先順位を決定するには、矢印ボタンを使用して、ルールの優先順位を上下に移動します。 リストは昇順になっているので、最も重要なルールが最初に表示されます。

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-rule-set-change-orders.png" alt-text="ルール セットの優先順位のスクリーンショット。" lightbox="../media/how-to-configure-rule-set/front-door-rule-set-change-orders-expanded.png":::

1. 1 つまたは複数のルールを作成したら、 **[保存]** を選択してルール セットの作成を完了します。

1. 次に、ルール セットをルートに関連付けて、有効にします。 [ルール セット] ページでルールを関連付ける方法と、エンドポイント マネージャーに移動して関連付けを作成する方法があります。
 
    **[ルール セット] ページ**: 
    
    1. 関連付けるルール セットを選択します。
    
    1. *[関連付けなし]* リンクを選択します。
     

    1. 次に、 **[ルートを関連付け]** ページで、ルール セットに関連付けるエンドポイントとルートを選択します。 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set.png" alt-text="ルートの作成ページのスクリーンショット。":::    
        
    1. 選択されたルートに複数のルール セットがある場合は、 *[次へ]* を選択してルール セットの順序を変更します。 ルール セットは、上から下への順に実行されます。 順序を変更するには、ルール セットを選択し、上または下に移動します。 次に、 *[関連付け]* を選択します。
    
        > [!Note]
        > このページでは、1 つのルール セットを 1 つのルートにのみ関連付けることができます。 ルール セットを複数のルートに関連付けるには、エンドポイント マネージャーを使用してください。
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-2.png" alt-text="ルール セットの順序のスクリーンショット。":::
    
    1. これで、ルール セットがルートに関連付けられました。 応答ヘッダーを見ると、Geo-country が追加されているのが確認できます。
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-3.png" alt-text="ルートに関連付けられたルールのスクリーンショット。":::

   **エンドポイント マネージャー**: 
    
    1. エンドポイント マネージャーに移動し、ルール セットに関連付けるエンドポイントを選択します。
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1.png" alt-text="エンドポイント マネージャーでエンドポイントを選択している画面のスクリーンショット。" lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1-expanded.png":::

    1. *[エンドポイントの編集]* を選択します。  
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2.png" alt-text="エンドポイント マネージャーで [エンドポイントの編集] を選択している画面のスクリーンショット。" lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2-expanded.png":::

    1. ルートを選択します。 
    
         :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-3.png" alt-text="ルートを選択している画面のスクリーンショット。":::
    
    1. *[ルートの更新]* ページの *[ルール]* で、ルートに関連付けるルール セットをドロップダウンから選択します。 その後、ルール セットを上下に移動することで、順序を変更できます。 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-4.png" alt-text="ルートの更新ページのスクリーンショット。":::
    
    1. 次に、 *[更新]* または *[追加]* を選択して、関連付けを完了します。

## <a name="delete-a-rule-set-from-your-azure-front-door-profile"></a>Azure Front Door プロファイルからルール セットを削除する

前の手順では、ルール セットを構成し、それをルートに関連付けました。 ルール セットと Front Door の関連付けを解除する場合は、次の手順を完了することで、ルール セットを削除できます。

1. **[設定]** の下にある **[ルール セット]** ページに移動して、関連付けられているすべてのルートからルール セットの関連付けを解除します。

1. ルートを展開し、3 つのドットを選択します。 次に、 *[ルートの編集]* を選択します。

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-1.png" alt-text="ルール セットでルートが展開された画面のスクリーンショット。":::

1. [ルート] ページの [ルール] セクションに移動して、ルール セットを選択し、 *[削除]* ボタンを選択します。 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2.png" alt-text="[ルートの更新] ページでルール セットを削除している画面のスクリーンショット。" lightbox="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2-expanded.png":::

1. *[更新]* を選択すると、ルール セットとルートの関連付けが解除されます。

1. ルートの状態が *[関連付けなし]* と表示されるまで、手順 2 ～ 5 を繰り返し、このルール セットに関連付けられている他のルートの関連付けを解除します。

1. *[関連付けなし]* になっているルール セットについては、右側にある 3 つのドットをクリックして *[削除]* を選択することで、ルール セットを削除できます。 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-3.png" alt-text="ルール セットを削除する方法のスクリーンショット。":::

1. これで、ルール セットが削除されました。

## <a name="next-steps"></a>次のステップ

[ルール セットを使用したセキュリティ ヘッダー](how-to-add-security-headers.md)を追加する方法について学習する。
