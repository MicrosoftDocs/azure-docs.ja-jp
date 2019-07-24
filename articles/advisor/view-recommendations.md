---
title: 重要な Azure Advisor の推奨事項を表示する
description: Azure Advisor の推奨事項を表示したり、ノイズを減らすためにフィルター処理したりします。
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: 9f599a63fd5f52420f1b79e769d4f7bca9683b32
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2019
ms.locfileid: "59053288"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>重要な Azure Advisor の推奨事項を表示する

Azure Advisor は、Azure のデプロイの最適化に役立つ推奨事項を提供します。 Advisor 内では、推奨事項を重要なもののみに絞り込むために役立ついくつかの機能にアクセスすることができます。

## <a name="configure-subscriptions-and-resource-groups"></a>サブスクリプションとリソース グループを構成する

Advisor では、個人や組織にとって重要なサブスクリプションとリソース グループを選択することができます。 選択したサブスクリプションとリソースについての推奨事項のみが表示されます。 既定では、すべてが選択されています。 構成設定はサブスクリプションまたはリソース グループに適用されるため、そのサブスクリプションまたはリソース グループにアクセスできるすべてのユーザーに同じ設定が適用されます。 構成設定は、Azure portal またはプログラムで変更できます。

Azure portal で変更を行うには、次の手順に従います。

1. Azure portal で [Azure Advisor](https://aka.ms/azureadvisordashboard) を開きます。

1. メニューから **[構成]** を選択します。

   ![Advisor 構成メニュー](./media/view-recommendations/configuration.png)

1. Advisor の推奨事項を受け取るサブスクリプションまたはリソース グループの **[含める]** チェック ボックスをオンにします。 このボックスが無効になっている場合は、そのサブスクリプションまたはリソース グループの構成を変更するアクセス許可がない可能性があります。 「[Azure Advisor でのアクセス許可](permissions.md)」を参照してください。

1. 変更を行った後、**[適用]** をクリックします。

## <a name="filtering-your-view-in-the-azure-portal"></a>Azure portal のビューをフィルター処理する

構成設定は、変更されるまではアクティブのままになります。 推奨事項のビューを 1 つの表示に制限する場合は、Advisor パネルの上部に表示されるドロップダウン リストを使用できます。 [概要]、[高可用性]、[セキュリティ]、[パフォーマンス]、[コスト]、および [All Recommendation] (すべての推奨事項) の各パネルから、表示したいサブスクリプション、リソースの種類、および推奨事項の状態を選択できます。

   ![Advisor のフィルター メニュー](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>推奨事項の却下と延期

Azure Advisor では、1 つのリソースに関する推奨事項を却下または延期することができます。 推奨事項を却下する場合、手動でアクティブ化しない限り再表示されません。 ただし、推奨事項の延期では、推奨事項が自動的に再度アクティブ化されるまでの期間を指定することができます。 延期は、Azure portal またはプログラムで実行できます。

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Azure portal で 1 つの推奨事項を延期する 

1. Azure portal で [Azure Advisor](https://aka.ms/azureadvisordashboard) を開きます。
1. 推奨事項のカテゴリを選択して、推奨事項を表示します
1. 推奨事項の一覧にある推奨事項を選択します
1. 延期または却下する推奨事項で [延期] または [却下] を選択します

     ![Advisor のフィルター メニュー](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Azure portal での複数の推奨事項を延期または却下する

1. Azure portal で [Azure Advisor](https://aka.ms/azureadvisordashboard) を開きます。
1. 推奨事項のカテゴリを選択して、推奨事項を表示します。
1. 推奨事項の一覧にある推奨事項を選択します。
1. 推奨事項を延期または却下するすべてのリソースの行の左側にあるチェック ボックスをオンにします。
1. テーブルの左上にある **[延期]** または **[却下]** を選択します。

     ![Advisor のフィルター メニュー](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> 推奨事項を却下または延期するには、共同作成者または所有者のアクセスが必要です。 「Azure Advisor でのアクセス許可」を参照してください。

> [!NOTE]
> 選択ボックスが無効になっている場合は、推奨事項がまだ読み込み中である可能性があります。 すべての推奨事項が読み込まれるのを待ってから、延期または却下を試みてください。

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>延期または却下した推奨事項を再アクティブ化する

延期または破棄された推奨事項をアクティブ化することができます。 このアクションは、Azure portal またはプログラムで実行できます。 Azure Portal で次の操作を行います。

1. Azure portal で [Azure Advisor](https://aka.ms/azureadvisordashboard) を開きます。

1. [概要] パネルのフィルターを **[延期]** に変更します。 その後、Advisor で、延期または却下した推奨事項が表示されます。

    ![Advisor のフィルター メニュー](./media/view-recommendations/activate-postponed.png)

1. カテゴリを選択して、**[延期]** および **[却下]** の推奨事項を表示します。

1. 推奨事項の一覧にある推奨事項を選択します。 これにより、あらかじめ選択されている **[延期と却下]** タブで推奨事項が開いて、この推奨事項が延期または却下されたリソースが表示されます。

1. 行の末尾にある **[アクティブ化]** をクリックします。 クリックすると、そのリソースの推奨事項がアクティブになるため、このテーブルから削除されます。 これで推奨事項が **[アクティブ]** タブに表示されます。
 
     ![Advisor のフィルター メニュー](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>次の手順

この記事では、Azure Advisor で重要な推奨事項を表示する方法について説明します。 Advisor の詳細については、次を参照してください。 

- [Azure Advisor とは](advisor-overview.md)
- [Advisor を使い始めるには](advisor-get-started.md)
- [Azure Advisor でのアクセス許可](permissions.md)



